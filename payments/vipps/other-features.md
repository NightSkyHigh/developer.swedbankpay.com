---
title: Swedbank Pay Payments Vipps Other Features
sidebar:
  navigation:
  - title: Checkout
    items:
    - url: /checkout/
      title: Introduction
    - url: /checkout/payment
      title: Checkout Payment
    - url: /checkout/after-payment
      title: Checkout After Payment
    - url: /checkout/other-features
      title: Checkout Other Features
  - title: Payments
    items:
    - url: /payments/
      title: Introduction
    - url: /payments/credit-card
      title: Credit Card Payments
    - url: /payments/credit-card/redirect
      title: Credit Card Redirect
    - url: /payments/credit-card/seamless-view
      title: Credit Card Seamless View
    - url: /payments/credit-card/after-payment
      title: Credit Card After Payments
    - url: /payments/credit-card/other-features
      title: Credit Card Other Features
    - url: /payments/invoice
      title: Invoice Payments
    - url: /payments/invoice/redirect
      title: Invoice Redirect
    - url: /payments/invoice/seamless-view
      title: Invoice Seamless View
    - url: /payments/invoice/after-payment
      title: Invoice After Payment
    - url: /payments/invoice/other-features
      title: Invoice Other Features
    - url: /payments/mobile-pay
      title: Mobile Pay Payments
    - url: /payments/mobile-pay/redirect
      title: Mobile Pay Redirect
    - url: /payments/mobile-pay/seamless-view
      title: Mobile Pay Seamless View
    - url: /payments/mobile-pay/after-payment
      title: Mobile Pay After Payment
    - url: /payments/mobile-pay/other-features
      title: Mobile Pay Other Features
    - url: /payments/swish
      title: Swish Payments
    - url: /payments/swish/redirect
      title: Swish Redirect
    - url: /payments/swish/seamless-view
      title: Swish Seamless View
    - url: /payments/swish/after-payment
      title: Swish After Payment
    - url: /payments/swish/other-features
      title: Swish Other Features
    - url: /payments/vipps
      title: Vipps Payments
    - url: /payments/vipps/redirect
      title: Vipps Redirect
    - url: /payments/vipps/seamless-view
      title: Vipps Seamless View
    - url: /payments/vipps/after-payment
      title: Vipps After After Payment
    - url: /payments/vipps/other-features    
      title: Vipps Other Features
    - url: /payments/direct-debit
      title: Direct Debit Payments
    - url: /payments/direct-debit/redirect
      title: Direct Debit Redirect
    - url: /payments/direct-debit/seamless-view
      title: Direct Debit Seamless View
    - url: /payments/direct-debit/after-payment
      title: Direct Debit After Payments
    - url: /payments/direct-debit/other-features
      title: Direct Debit Other Features
    - url: /payments/credit-account
      title: Credit Account
    - url: /payments/credit-account/after-payment
      title: Credit Account After Payment
    - url: /payments/credit-account/other-features
      title: Credit Account Other Features
  - title: Resources
    items:
    - url: /resources/
      title: Introduction
    - url: /resources/test-data
      title: Test Data
    - url: /resources/demoshop
      title: Demoshop
---


  - title: Checkout
    items:
    - url: /checkout/
      title: Introduction
    - url: /checkout/payment
      title: Checkout Payment
    - url: /checkout/after-payment
      title: Checkout After Payment
    - url: /checkout/other-features
      title: Checkout Other Features

{% include alert.html type="warning"
                      icon="warning"
                      header="Site under development"
                      body="The Developer Portal is under construction and should not be used to integrate against Swedbank Pay's APIs yet." %}


{% include payment-menu-styling.md %}

{% include settlement-reconciliation.md %}

{% include one-click-payments.md %}

{% include payment-link.md %}

{% include recurring-card-payments.md %}

{% include subsite.md %}

## Problem messages

When performing unsuccessful operations, the eCommerce API will respond with a problem message. We generally use the problem message type and status code to identify the nature of the problem. The problem name and description will often help narrow down the specifics of the problem.

For general information about problem messages and error handling, [visit error handling and problem details][technical-reference-problems].  

### Error types from Vipps (Init-call)

All Vipps error types will have the following URI in front of type: `https://api.payex.com/psp/errordetail/vipps/<errorType>`

{:.table .table-striped}
| **Type** | **Status** | Note 
| *VIPPS_ERROR* | 403 | All errors

### Error types from Vipps (Callback)

All Vipps error types will have the following URI in front of type: `https://api.payex.com/psp/errordetail/vipps/<errorType>`

{:.table .table-striped}
| **Type** | **Status** | Note 
| *VIPPS_DECLINED* | 400 | Any status that is not YES

### Error types from Acquirer

All Vipps error types will have the following URI in front of type: `https://api.payex.com/psp/errordetail/vipps/<errorType>`

{:.table .table-striped}
| **Type** | **Status** | Note 
| *CARD_BLACKLISTED* | 400 | 
| *PAYMENT_TOKEN_ERROR* | 403 | 
| *CARD_DECLINED* | 403 | 
| *ACQUIRER_ERROR* | 403 | 
| *ACQUIRER_CARD_BLACKLISTED* | 403 | 
| *ACQUIRER_CARD_EXPIRED* | 403 | 
| *ACQUIRER_CARD_STOLEN* | 403 | 
| *ACQUIRER_INSUFFICIENT_FUNDS* | 403 | 
| *ACQUIRER_INVALID_AMOUNT* | 403 | 
| *ACQUIRER_POSSIBLE_FRAUD* | 403 | 
| *FRAUD_DETECTED* | 403 | 
| *BAD_REQUEST* | 500 | 
| *INTERNAL_SERVER_ERROR* | 500 | 
| *BAD_GATEWAY* | 502 | 
| *ACQUIRER_GATEWAY_ERROR* | 502 | 
| *ACQUIRER_GATEWAY_TIMEOUT* | 504 | 
| *UNKNOWN_ERROR* | 500 | 

[technical-reference-problems]: #