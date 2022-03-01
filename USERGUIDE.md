# REST User Guide

## Table of Contents
* [Introduction](#introduction)
* [Generate API key](#generate-api-key)
* [Setup](#setup)
    * [Authorization](#authorization)
    * [Swagger](#swagger)
* [Text messages](#text-messages)
    * [Send an SMS](#send-an-sms)
    * [Send an SMS with payment](#send-an-sms-with-payment)
    * [Schedule an SMS for later sending](#schedule-an-sms-for-later-sending)
    * [Edit a scheduled SMS](#edit-a-scheduled-sms)
    * [Delete a scheduled SMS](#delete-a-scheduled-sms)
* [Payment transactions](#payment-transactions)
    * [Create a Strex payment transaction](#create-a-strex-payment-transaction)
    * [Create a Strex payment transaction with one-time password](#create-a-strex-payment-transaction-with-one-time-password)
    * [Reverse a Strex payment transaction](#reverse-a-strex-payment-transaction)
* [One-click](#one-click)
    * [One-click config](#one-click-config)
    * [Recurring transaction](#recurring-transaction)
* [Lookup](#lookup)
    * [Address lookup for mobile number](#address-lookup-for-mobile-number)
* [Keywords](#keywords)
    * [Create a keyword](#create-a-keyword)
    * [Delete a keyword](#delete-a-keyword)
* [Forwards](#forwards)
    * [SMS forward](#sms-forward)
    * [DLR forward](#dlr-forward)
    * [DLR status codes](#dlr-status-codes)
* [Encoding and SMS length](#encoding-and-sms-length)

## Introduction
The Target365 REST API gives you direct access to our online services like sending and receiving SMS, address lookup and Strex payment transactions. We encourage using our SDK's which provides an appropriate abstraction level for C# development and is officially support by Target365. The SDK's also implements very high security (ECDsaP256 HMAC) compared to the REST API secret key.
* https://github.com/Target365/sdk-for-net
* https://github.com/Target365/sdk-for-java
* https://github.com/Target365/sdk-for-node
* https://github.com/Target365/sdk-for-php
* https://github.com/Target365/sdk-for-python

## Generate API key
* Log in to your account at StrexConnect at https://www.strexconnect.no
* Select "API KEYS" from the top menu
* Click "Add api key" and enter a name
* Click "Save"
* Copy the "API key" string

## Setup
### Authorization
* Add a header called "X-ApiKey" with the key from above to your requests.

### Swagger
* Swagger for the API is located here: https://test.target365.io/api/swagger.json
    * https://generator.swagger.io/?url=https%3A%2F%2Ftest.target365.io%2Fapi%2Fswagger.json

## Text messages

### Send an SMS
This example sends an SMS to 98079008 (+47 for Norway) from "Target365" with the text "Hello world from SMS!".

#### Request
```
POST https://test.target365.io/api/out-messages
Content-Type: application/json

{
    "transactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "sender": "Target365",
    "recipient": "+4798079008",
    "content": "Hello World from SMS!"
}
```
<details>
   <summary>curl example</summary>

```
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "transactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "sender": "Target365",
    "recipient": "+4798079008",
    "content": "Hello World from SMS!"
}' 'https://test.target365.io/api/out-messages'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Send an SMS with payment
This example sends an SMS with payment to 98079008 (+47 for Norway) from "Target365" with the text "Hello world from SMS!" and the price 1 krone.

#### Request
```
POST https://test.target365.io/api/out-messages
 
Content-Type: application/json  
{  
    "transactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",  
    "sender": "2002",  
    "recipient": "+4798079008",  
    "content": "Hello World from SMS!",  
    "strex":  
    { 
      "merchantId" : "YOUR_MERCHANT_ID",  
      "price": 1,  
      "serviceCode": "14002",  
      "invoiceText": "Donation test"  
    }  
}
```
<details>
   <summary>curl example</summary>

```
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{  
    "transactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",  
    "sender": "2002",  
    "recipient": "+4798079008",  
    "content": "Hello World from SMS!",  
    "strex":  
    { 
      "merchantId" : "YOUR_MERCHANT_ID",  
      "price": 1,  
      "serviceCode": "14002",  
      "invoiceText": "Donation test"  
    }  
}' 'https://test.target365.io/api/out-messages'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

   
### Schedule an SMS for later sending
This example sets up a scheduled SMS. Scheduled messages can be updated or deleted before the time of sending.

#### Request
```
POST https://test.target365.io/api/out-messages
Content-Type: application/json

{
    "transactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "sender": "Target365",
    "recipient": "+4798079008",
    "content": "Hello World from SMS!",
    "sendTime" : "2021-04-12T13:27:50Z"
}
```
<details>
   <summary>curl example</summary>

```
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "transactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "sender": "Target365",
    "recipient": "+4798079008",
    "content": "Hello World from SMS!",
    "sendTime" : "2021-04-12T13:27:50Z"
}' 'https://test.target365.io/api/out-messages'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Edit a scheduled SMS
This example shows how you can get a previously created scheduled SMS and post it again with a new sendtime and content.

#### Requests
```
GET https://test.target365.io/api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3
```
```
POST https://test.target365.io/api/out-messages
Content-Type: application/json

{
    "transactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "sender": "Target365",
    "recipient": "+4798079008",
    "content": "Hello World from SMS an hour later!",
    "sendTime" : "2021-04-14T13:27:50Z"
}
```

### Delete a scheduled SMS
This example deletes a previously created scheduled SMS.

#### Request
```
DELETE https://test.target365.io/api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3
```
<details>
   <summary>curl example</summary>

```
curl -XDELETE -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" 'https://test.target365.io/api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3'
```
</details>

#### Response
```
200 Ok

```

#### Response codes
* 204	Out-message deleted successfully.
* 404	Out-message not found.
* 409	Out-message could not be deleted.

## Payment transactions
If your service requires a minimum age of the End User, each payment transaction should be defined with minimum age. Both StrexTransaction and OutMessage have a property named “Age”. If not set or present in the request, there is no age limit.

### Create a Strex payment transaction
This example creates a 1 NOK Strex payment transaction that the end user will confirm by replying "OK" to an SMS from Strex.
You can use message_prefix and message_suffix custom properties to influence the start and end of the SMS sent by Strex.

#### Request
```
POST https://test.target365.io/api/strex/transactions
Content-Type: application/json

{
    "transactionId": "8502b85f-fac2-47cc-8e55-a20ab8680427",
    "shortNumber": "2002",
    "recipient": "+4798079008",
    "merchantId" : "YOUR_MERCHANT_ID",
    "price": 1,
    "serviceCode": "ServiceCodes.NonCommercialDonation",
    "invoiceText": "Donation test",
    "smsConfirmation": true,
    "properties": {
      "message_prefix": "Dear customer...",
      "message_suffix": "Best regards..."
    }
}
```
<details>
   <summary>curl example</summary>

```
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "transactionId": "8502b85f-fac2-47cc-8e55-a20ab8680427",
    "shortNumber": "2002",
    "recipient": "+4798079008",
    "merchantId" : "YOUR_MERCHANT_ID",
    "price": 1,
    "serviceCode": "14002",
    "invoiceText": "Donation test",
    "smsConfirmation": true,
    "properties": {
      "message_prefix": "Dear customer...",
      "message_suffix": "Best regards..."
    }
}' 'https://test.target365.io/api/strex/transactions'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/strex/transactions/8502b85f-fac2-47cc-8e55-a20ab8680427

```

#### Response codes
* 201	Transaction posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Create a Strex payment transaction with one-time password
This example creates a Strex one-time password sent to the end user and get completes the payment by using the one-time password. You can use MessagePrefix and MessageSuffix to influence the start and end of the SMS sent by Strex.

#### Request
```
POST https://test.target365.io/api/strex/one-time-passwords
Content-Type: application/json

{
    "transactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "merchantId": "YOUR_MERCHANT_ID",
    "recipient": "+4798079008",
    "messagePrefix": "Dear customer...",
    "messageSuffix": "Best Regards...",
    "recurring": false
}
```
<details>
   <summary>curl example</summary>

```
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "transactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "merchantId": "YOUR_MERCHANT_ID",
    "recipient": "+4798079008",
    "messagePrefix": "Dear customer...",
    "messageSuffix": "Best Regards...",
    "recurring": false,
}' 'https://test.target365.io/api/strex/one-time-passwords'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/strex/one-time-passwords/3202b85f-fac2-432a-8e55-a20ab8680211

```

#### Response codes
* 201	Posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

 Get input from end user (eg. via web site) and post payment transaction using same TransactionId as above.

#### Request
```
POST https://test.target365.io/api/strex/transactions
Content-Type: application/json

{
    "transactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "shortNumber": "2002",
    "recipient": "+4798079008",
    "merchantId": "YOUR_MERCHANT_ID",
    "price": 1,
    "serviceCode": "14002",
    "invoiceText": "Donation test",
    "oneTimePassword": "ONE_TIME_PASSWORD_FROM_USER"
}
```
<details>
   <summary>curl example</summary>

```
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "transactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "shortNumber": "2002",
    "recipient": "+4798079008",
    "merchantId: "YOUR_MERCHANT_ID",
    "price": 1,
    "serviceCode": "14002",
    "invoiceText": "Donation test",
    "oneTimePassword": "ONE_TIME_PASSWORD_FROM_USER"
}' 'https://test.target365.io/api/strex/transactions'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/strex/transactions/3202b85f-fac2-432a-8e55-a20ab8680211

```

#### Response codes
* 201	Transaction posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Reverse a Strex payment transaction
This example reverses a previously billed Strex payment transaction. The original transaction will not change, but a reversal transaction will be created that counters the previous transaction by a negative Price. The reversal is an asynchronous operation that usually takes a few seconds to finish.

#### Request
```
DELETE https://test.target365.io/api/strex/transactions/3202b85f-fac2-432a-8e55-a20ab8680211
```
<details>
   <summary>curl example</summary>

```
curl -XDELETE -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" 'https://test.target365.io/api/strex/transactions/3202b85f-fac2-432a-8e55-a20ab8680211'
```
</details>

#### Response
```
200 Ok

```

#### Response
```JSON
{
    "transactionId": "-3202b85f-fac2-432a-8e55-a20ab8680211"
}
```
#### Response codes
* 201	Strex transaction reversal has been registered and is processed asynchronously. Transaction id of the reversal transaction will be the same as the original transaction id prefixed with '-'.
* 401	Request was unauthorized.
* 404	Strex transaction not found.
* 409	Strex transaction is not reversable.

## One-click

Please note:

* The OneClick service will not stop same MSISDN to order several times as long as transactionID is unique. If end users order or subscribe several times to same service it's the merchants responsibility to refund the end user.

* Recurring billing is initiated by merchants, see section [Payment transactions](#payment-transactions) for more info.

* Since the one-click flow ends by redirecting the end user to an external merchant-controlled URL we recommend that merchants implement a mechanism to check status on all started transactions. If there’s any issue for the end user on their way to the last page they might have finished the payment, but not been able to get their product.

### One-click config
This example sets up a one-click config which makes it easier to handle campaigns in one-click where most properties like merchantId, price et cetera are known in advance. You can redirect the end-user to the one-click campaign page by redirecting to http://betal.strex.no/{YOUR-CONFIG-ID} for PROD and http://test-strex.target365.io/{YOUR-CONFIG-ID} for TEST-environment. You can also set the TransactionId by adding ?id={YOUR-TRANSACTION-ID} to the URL.

#### Request
```
PUT https://test.target365.io/api/one-click/configs/Test1
Content-Type: application/json

{
    "configId": "Test1",
    "shortNumber": "2002",
    "price": 99,
    "merchantId": "YOUR_MERCHANT_ID",
    "serviceCode": "14002",
    "onlineText": "Buy directly",
    "offlineText": "Buy with PIN-code",
    "invoiceText": "Donation test",
    "redirectUrl": "https://your-return-url.com?id={TransactionId}",
    "recurring": true,
    "subscriptionInterval": "monthly",
    "subscriptionPrice": 99,
    "subscriptionStartSms": "Thanks for donating 99kr each month."
}
```

{TransactionId} in RedirectUrl is replaced by actual transaction id.

If Recurring is set to 'false', the following parameters are optional:

* SubscriptionInterval - Possible values are "weekly", "monthly", "yearly"

* SubscriptionPrice - How much the subscriber will be charged each interval

This parameter is optional:

* SubscriptionStartSms - SMS that will be sent to the user when subscription starts.

<details>
   <summary>curl example</summary>

```
curl -XPUT -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "configId": "Test1",
    "shortNumber": "2002",
    "price": 99,
    "merchantId": "YOUR_MERCHANT_ID",
    "serviceCode": "14002",
    "onlineText": "Buy directly",
    "offlineText": "Buy with PIN-code",
    "invoiceText: "Donation test",
    "redirectUrl": "https://your-return-url.com?id={TransactionId}", // {TransactionId} is replaced by actual transaction id
    "recurring": true,
    "subscriptionInterval": "monthly",
    "subscriptionPrice": 99,
    "subscriptionStartSms": "Thanks for donating 99kr each month."
}' 'https://test.target365.io/api/one-click/configs/Test1'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/one-click/configs/Test1

```

#### Response codes
* 201	Config posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Recurring transaction
This example sets up a recurring transaction for one-click. After creation you can immediately get the transaction to get the status code - the server will wait up to 20 seconds for the async transaction to complete.
![Recurring sequence](https://github.com/Target365/sdk-for-net/raw/master/oneclick-recurring-flow.png "Recurring sequence diagram")

#### POST request
```
POST https://test.target365.io/api/strex/transactions
Content-Type: application/json

{
    "transactionId": "5402b85f-fac2-212a-8e55-a20ab8680765",
    "recipient": "RECIPIENT_FROM_SUBSCRIPTION_TRANSACTION",
    "shortNumber": "2002",
    "merchantId": "YOUR_MERCHANT_ID",
    "price": 1,
    "serviceCode": "14002",
    "invoiceText": "Donation test"
}

```
<details>
   <summary>curl example</summary>


```
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "transactionId": "5402b85f-fac2-212a-8e55-a20ab8680765",
    "recipient": "RECIPIENT_FROM_SUBSCRIPTION_TRANSACTION",
    "shortNumber": "2002",
    "merchantId": "YOUR_MERCHANT_ID",
    "price": 1,
    "serviceCode": "14002",
    "invoiceText": "Donation test"
}' 'https://test.target365.io/api/strex/transactions'```
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/strex/transactions/5402b85f-fac2-212a-8e55-a20ab8680765

```

#### GET request
```
GET https://test.target365.io/api/strex/transactions/5402b85f-fac2-212a-8e55-a20ab8680765
```
<details>
   <summary>curl example</summary>

```
curl -XGET -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" 'https://test.target365.io/api/strex/transactions/5402b85f-fac2-212a-8e55-a20ab8680765'
```
</details>

#### Response
```JSON
{
  "created": "2018-11-02T12:00:00Z",
  "invoiceText": "Thank you for your donation",
  "lastModified": "2018-11-02T12:00:00Z",
  "merchantId": "mer_test",
  "price": 10.5,
  "recipient": "+4798079008",
  "serviceCode": "14002",
  "shortNumber": "2001",
  "transactionId": "8502b85f-fac2-47cc-8e55-a20ab8680427",
  "statusCode": "Ok",
  "detailedStatusCode": "Delivered"
}
```

// TODO: Check transaction.StatusCode

## Lookup

### Address lookup for mobile number
This example looks up address information for the norwegian mobile number 98079008. Lookup information includes registered name and address.

#### Request
```
GET https://test.target365.io/api/lookup?msisdn=+4798079008
```

<details>
   <summary>curl example</summary>

```
curl -XGET -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" 'https://test.target365.io/api/lookup?msisdn=+4798079008'
```
</details>

#### Response
```JSON
{
  "msisdn": "98079008",
  "firstName": "Hans Olav",
  "lastName": "Stjernholm",
  "streetName": "Teststreet",
  "streetNumber": "1",
  "streetLetter": "a",
  "zipCode": "1234",
  "city": "Test City",
  "gender": "M",
  "dateOfBirth": "2000-01-01",
  "age": 20
}
```

#### Response codes
* 200	Lookup performed successfully.
* 400	Request had invalid payload.
* 401	Request was unauthorized.
* 404	Information on phone number not found.

## Keywords

### Create a keyword
This example creates a new keyword on short number 2002 that forwards incoming SMS messages to 2002 that starts with "HELLO" to the URL  "https://your-site.net/api/receive-sms".

#### Request
```
POST https://test.target365.io/api/keywords
Content-Type: application/json

{
    "shortNumberId": "NO-2002",
    "keywordText": "HELLO",
    "mode": "Text",
    "forwardUrl": "https://your-site.net/api/receive-sms",
    "enabled": true
}

```

<details>
   <summary>curl example</summary>

```
curl -XGET -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "shortNumberId": "NO-2002",
    "keywordText": "HELLO",
    "mode": "Text",
    "forwardUrl": "https://your-site.net/api/receive-sms",
    "enabled": true
}' 'https://test.target365.io/api/keywords'
```
</details>

#### Response
```
201 Created
Location: https://test.target365.io/api/keywords/546654745b85f-fac2-212a-8e55-a202348680778

```

#### Response codes
* 201	Keyword created. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Delete a keyword
This example deletes a keyword.

#### Request
```
DELETE https://test.target365.io/api/keywords/{keywordId}
```

<details>
   <summary>curl example</summary>

```
curl -XDELETE -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" 'https://test.target365.io/api/keywords/{keywordId}'
```
</details>

#### Response
```
200 Ok

```

#### Response codes
* 204	Keyword deleted.
* 404	Keyword not found.

## Forwards

### SMS forward
This example shows how SMS messages are forwarded to the keywords ForwardUrl. All sms forwards expects a response with status code 200 (OK). If the request times out or response status code differs the forward will be retried several times.
#### Request
```
POST https://your-site.net/api/receive-sms HTTP/1.1
Content-Type: application/json
Host: your-site.net

{
  "transactionId":"00568c6b-7baf-4869-b083-d22afc163059",
  "created":"2019-02-07T21:11:00+00:00",
  "sender":"+4798079008",
  "recipient":"2002",
  "content":"HELLO"
}
```

#### Response
```
HTTP/1.1 200 OK
Date: Thu, 07 Feb 2019 21:13:51 GMT
Content-Length: 0
```

### DLR forward
This example shows how delivery reports (DLR) are forwarded to the outmessage DeliveryReportUrl. All DLR forwards expect a response with status code 200 (OK). If the request times out or response status code differs the forward will be retried 10 times with exponentially longer intervals for about 15 hours.
#### Request
```
POST https://your-site.net/api/receive-dlr HTTP/1.1
Content-Type: application/json
Host: your-site.net

{
    "correlationId": null,
    "transactionId": "client-specified-id-5c88e736bb4b8",
    "price": null,
    "sender": "Target365",
    "recipient": "+4798079008",
    "operatorId": "no.telenor",
    "statusCode": "Ok",
    "detailedStatusCode": "Delivered",
    "delivered": true,
    "billed": null,
    "smscTransactionId": "16976c7448d",
    "smscMessageParts": 1
}
```

#### Response
```
HTTP/1.1 200 OK
Date: Thu, 07 Feb 2019 21:13:51 GMT
Content-Length: 0
```

### DLR status codes
Delivery reports contains two status codes, one overall called `StatusCode` and one detailed called `DetailedStatusCode`.

#### StatusCode values
|Value|Description|
|:---|:---|
|Queued|Message is queued|
|Sent|Message has been sent|
|Failed|Message has failed|
|Ok|message has been delivered/billed|
|Reversed|Message billing has been reversed|

#### DetailedStatusCode values
|Value|Description|
|:---|:---|
|None|Message has no status|
|Delivered|Message is delivered to destination|
|Expired|Message validity period has expired|
|Undelivered|Message is undeliverable|
|UnknownError|Obsolete. Replaced by OtherError|
|OtherError|Billing is not done, and MT is not delivered. OtherError is a collective status for many different errors, but they are few in number and we have arranged it like this to avoid hundreds of different error-details. If you have a large number of this status, please contact us so we may analyze your traffic.|   
|Rejected|Message has been rejected|
|UnknownSubscriber|Unknown subscriber|
|SubscriberUnavailable|Subscriber unavailable|
|SubscriberBarred|Subscriber barred|
|InsufficientFunds|Insufficient funds|
|RegistrationRequired|Registration required|
|UnknownAge|Unknown age|
|DuplicateTransaction|Duplicate transaction|
|SubscriberLimitExceeded|Subscriber limit exceeded|
|MaxPinRetry|Max pin retry reached|
|InvalidAmount|Invalid amount|
|OneTimePasswordExpired|One-time password expired|
|OneTimePasswordFailed|One-time password failed|
|SubscriberTooYoung|Subscriber too young|
|TimeoutError|Timeout error|

## Encoding and SMS length
When sending SMS messages, we'll automatically send messages in the most compact encoding possible. If you include any non GSM-7 characters in your message body, we will automatically fall back to UCS-2 encoding (which will limit message bodies to 70 characters each).

Additionally, for long messages--greater than 160 GSM-7 characters or 70 UCS-2 characters--we will split the message into multiple segments. Six (6) bytes is also needed to instruct receiving device how to re-assemble messages, which for multi-segment messages, leaves 153 GSM-7 characters or 67 UCS-2 characters per segment.

Note that this may cause more message segments to be sent than you expect - a body with 152 GSM-7-compatible characters and a single unicode character will be split into three (3) messages because the unicode character changes the encoding into less-compact UCS-2. This will incur charges for three outgoing messages against your account.

Norwegian operators support different numbers of segments; Ice 16 segments, Telia 20 segments and Telenor 255 segments.
