---
title: Payment Api – Security
sidebar:
  navigation:
  - title: Payment Api
    items:
    - url: /giftcard/
      title: Introduction
    - url: /giftcard/operations
      title: Operations
    - url: /giftcard/security
      title: Security
    - url: /giftcard/payment-client
      title: Test Client
    - url: /giftcard/other-features
      title: Other Features
---

----
#### OAuth2

The Payment Api requires an OAuth2 access token for interaction.  
This application automatically handles token fetching and refreshing by using [Spring Security](https://docs.spring.io/spring-security-oauth2-boot/docs/current/reference/htmlsingle/#boot-features-security-custom-user-info-client).   
Configuration values are set in [application.yml](https://github.com/PayEx/vas-payment-api-client/blob/master/backend/src/main/resources/application.yml): 

```yaml
# "XXX" Should be replaced by value provided by Swebank Pay
# CLIENT_ID/CLIENT_SECRET/VAS_AUTH_SERVER_URL can also be set in docker-compose.yml as environment variables if running with docker
vas-payment-api:
    oauth2:
        client:
            grantType: client_credentials
            clientId: "${CLIENT_ID}:XXX"
            clientSecret: "${CLIENT_SECRET}:XXX"
            accessTokenUri: "${VAS_AUTH_SERVER_URL}:XXX"
            scope: publicapi 
            
```
And the implementation of these are located in [Oauth2RestTemplateConfiguration.java](https://github.com/PayEx/vas-payment-api-client/blob/master/backend/src/main/java/com/payex/vas/demo/config/security/Oauth2RestTemplateConfiguration.java):
```java
public class Oauth2RestTemplateConfiguration {
    //...
    @Bean
    @ConfigurationProperties("vas-payment-api.oauth2.client")
    protected ClientCredentialsResourceDetails oAuthDetails() {
        return new ClientCredentialsResourceDetails();
    }

    @Bean
    protected RestTemplate restTemplate() {
        var restTemplate = new OAuth2RestTemplate(oAuthDetails());
        restTemplate.setInterceptors(ImmutableList.of(externalRequestInterceptor()));
        restTemplate.setRequestFactory(httpRequestFactory());
        return restTemplate;
    }
    //...
}
```

#### HMAC
The API also requires HMAC authentication to be present in a request.  
In this client the HMAC value is automatically calculated by [HmacSignatureBuilder.java](https://github.com/PayEx/vas-payment-api-client/blob/master/backend/src/main/java/com/payex/vas/demo/config/security/HmacSignatureBuilder.java) and added to all outgoing requests in [ExternalRequestInterceptor.java](https://github.com/PayEx/vas-payment-api-client/blob/master/backend/src/main/java/com/payex/vas/demo/config/ExternalRequestInterceptor.java) 

HMAC is implemented using SHA-512 secure hash algorithm. 

Expected `Hmac` header format is:
```text
HmacSHA512 <user>:<nonce>:<digest>	
```
where `digest` is a Base64 formatted HMAC SHA512 digest of the following string: 
```text
METHOD\n
RESOURCE\n
USER\
NONCE\n
DATE\n
PAYLOAD\n
```

`METHOD` (mandatory) the requested method (in upper case) 
`RESOURCE` (mandatory) the path to desired resource (without hostname and any query parameters)  
`NONSE` (mandatory) a unique value for each request ([UUID](https://tools.ietf.org/rfc/rfc4122.txt)) 
`DATE`(optional) same as `Transmission-Time` if provided as seperate header. Uses [ISO8601 standard](https://en.wikipedia.org/wiki/ISO_8601)
`PAYLOAD` (optional) body of request 

Example request:

```bash
curl -X POST \
  https://stage-evc.payex.com/payment-api/api/payments/payment-account/balance \
  -H 'Accept: */*' \
  -H 'Agreement-Merchant-Id: XXX' \
  -H 'Authorization: Bearer XXX' \
  -H 'Hmac: HmacSHA512 user:21a0213e-30eb-85ab-b355-a310d31af30e:oY5Q5Rf1anCz7DRm3GyWR0dvJDnhl/psylfnNCn6FA0NOrQS3L0fvyUsQ1IQ9gQPeLUt9J3IM2zwoSfZpDgRJA==' \
  -H 'Transmission-Time: 2019-06-18T09:19:15.208257Z' \
  -H 'Session-Id: e0447bd2-ab64-b456-b17b-da274bb8428e' \
  -d '{
	"accountIdentifier": {
		"accountKey": "7013369000000000000",
		"cvc": "123",
		"expiryDate": "2019-12-31",
		"instrument": "GC"
	}
}'
```

In this example `USER` is user and `SECRET` is secret. 

The plain string to `digest` would then be:
```text
POST
/payment-api/api/payments/payment-account/balance
user
21a0213e-30eb-85ab-b355-a310d31af30e
2019-06-18T09:19:15.208257Z
{
	"accountIdentifier": {
		"accountKey": "7013360000000000000",
		"cvc": "123",
		"expiryDate": "2020-12-31",
		"instrument": "CC"
	}
}
```

The plain `digest` string is then hashed with `HmacSHA512` algorithm and the `SECRET`.
Finally we Base64 encode the hashed value. This is the final `digest` to be provided in the `Hmac` header.


Final `Hmac` header value: 
```text 
HmacSHA512 user:21a0213e-30eb-85ab-b355-a310d31af30e:oY5Q5Rf1anCz7DRm3GyWR0dvJDnhl/psylfnNCn6FA0NOrQS3L0fvyUsQ1IQ9gQPeLUt9J3IM2zwoSfZpDgRJA==
```

#### Postman example script for generation HMAC header

{:.code-header}
**In pre-request script copy/paste the following snippet:**

```javascript
var user = 'Systemtest';
var secret = 'Systemtest';
var transmissionTime = (new Date()).toISOString();
var sessionId = guid();

var hmac = generateHMAC(user, secret, transmissionTime);
console.log('hmac: ' + hmac);

//Set header values
pm.request.headers.add({key: 'Hmac', value: hmac });
pm.request.headers.add({key: 'Transmission-Time', value: transmissionTime });
pm.request.headers.add({key: 'Session-Id', value: sessionId });

function generateHMAC(user, secret, transmissionTime) {

    var algorithm = "HmacSHA512";
    var separator = ":";
    var method = request.method.toUpperCase();
    var nonce = generateNonce(); //UUID
    var date = transmissionTime;
    var uri_path = request.url.trim().replace(new RegExp('^https?://[^/]+/'), '/'); // strip hostname
    uri_path = uri_path.split("?")[0]; //Remove query paramters
    var payload = _.isEmpty(request.data) ? "" : request.data;
    var macData = method + '\n'
        + uri_path + '\n'
        + user + '\n'
        + nonce + '\n'
        + date + '\n'
        + payload + '\n';

    macData = replaceRequestEnv(macData);
    console.log('data to mac: ' + macData);

    var hash = CryptoJS.HmacSHA512(macData, secret);
    var digest = CryptoJS.enc.Base64.stringify(hash);
    return algorithm + " " + user + separator + nonce + separator + digest;
}

function replaceRequestEnv(input) { //manually set environments to they are populated before hashing
    return input.replace(/\{\{(.*?)\}\}/g, function (str, key) {
        var value = pm.environment.get(key);
        return value === null ? pm.varables.get(key) : value;
    });
}

function generateNonce() {
    return guid();
}

function guid() {
    function s4() {
        return Math.floor((1 + Math.random()) * 0x10000)
            .toString(16)
            .substring(1);
    }

    return s4() + s4() + '-' + s4() + '-' + s4() + '-' +
        s4() + '-' + s4() + s4() + s4();
}
```



### Security documentation
* [OAuth2](https://oauth.net/2/)
* [Client Credentials](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/)
* [The RESTful CookBook: HMAC](http://restcookbook.com/Basics/loggingin/)
* [HMAC - Wikipedia](https://en.wikipedia.org/wiki/HMAC)

## Test client:

* For more information how to implement the api, see [Test Client][test-client]

[test-client]: /giftcard/payment-client