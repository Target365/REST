# REST User Guide

## Table of Contents
* [Introduction](#introduction)
* [Generate API key](#generate-api-key)
* [Setup](#setup)
    * [Authorization](#authorization)
    * [Swagger](#swagger)
* [Text messages](#text-messages)
    * [Send an SMS](#send-an-sms)
    * [Schedule an SMS for later sending](#schedule-an-sms-for-later-sending)
    * [Edit a scheduled SMS](#edit-a-scheduled-sms)
    * [Delete a scheduled SMS](#delete-a-scheduled-sms)
* [Payment transactions](#payment-transactions)
    * [Create a Strex payment transaction](#create-a-strex-payment-transaction)
    * [Create a Strex payment transaction with one-time password](#create-a-strex-payment-transaction-with-one-time-password)
    * [Reverse a Strex payment transaction](#reverse-a-strex-payment-transaction)
* [One-click](#one-click)
    * [One-click config](#one-click-config)
    * [One-time transaction](#one-time-transaction)
    * [Setup subscription transaction](#setup-subscription-transaction)
    * [Recurring transaction](#recurring-transaction)
* [Lookup](#lookup)
    * [Address lookup for mobile number](#address-lookup-for-mobile-number)
* [Keywords](#keywords)
    * [Create a keyword](#create-a-keyword)
    * [Delete a keyword](#delete-a-keyword)
* [Forwards](#forwards)
    * [SMS forward](#sms-forward)
    * [SMS forward using the SDK](#sms-forward-using-the-sdk)
    * [DLR forward](#dlr-forward)
    * [DLR forward using the SDK](#dlr-forward-using-the-sdk)
    * [DLR status codes](#dlr-status-codes)

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

#### HTTP POST /api/out-messages
```JSON
{
    "TransactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "Sender": "Target365",
    "Recipient": "+4798079008",
    "Content": "Hello World from SMS!"
}
```

```CURL
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "TransactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "Sender": "Target365",
    "Recipient": "+4798079008",
    "Content": "Hello World from SMS!"
}' 'https://test.target365.io/api/out-messages'
```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Schedule an SMS for later sending
This example sets up a scheduled SMS. Scheduled messages can be updated or deleted before the time of sending.

#### HTTP POST /api/out-messages
```JSON
{
    "TransactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "Sender": "Target365",
    "Recipient": "+4798079008",
    "Content": "Hello World from SMS!",
    "SendTime" : "2021-04-12T13:27:50Z"
}
```

```CURL
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "TransactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "Sender": "Target365",
    "Recipient": "+4798079008",
    "Content": "Hello World from SMS!",
    "SendTime" : "2021-04-12T13:27:50Z"
}' 'https://test.target365.io/api/out-messages'
```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Edit a scheduled SMS
This example shows how you can get a previously created scheduled SMS and post it again with a new sendtime and content.

#### HTTP GET /api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

#### HTTP POST /api/out-messages
```JSON
{
    "TransactionId": "8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3",
    "Sender": "Target365",
    "Recipient": "+4798079008",
    "Content": "Hello World from SMS an hour later!",
    "SendTime" : "2021-04-14T13:27:50Z"
}
```

### Delete a scheduled SMS
This example deletes a previously created scheduled SMS.
#### HTTP DELETE /api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

```CURL
curl -XDELETE -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" 'https://test.target365.io/api/out-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3'
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

#### HTTP POST /api/strex/transactions
```JSON
{
    "TransactionId": "8502b85f-fac2-47cc-8e55-a20ab8680427",
    "ShortNumber": "2002",
    "Recipient": "+4798079008",
    "MerchantId" : "YOUR_MERCHANT_ID",
    "Price": 1,
    "ServiceCode": "ServiceCodes.NonCommercialDonation",
    "InvoiceText": "Donation test",
    "SmsConfirmation": true,
    "properties": {
      "message_prefix": "Dear customer...",
      "message_suffix": "Best regards..."
    }
}
```

```CURL
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "TransactionId": "8502b85f-fac2-47cc-8e55-a20ab8680427",
    "ShortNumber": "2002",
    "Recipient": "+4798079008",
    "MerchantId" : "YOUR_MERCHANT_ID",
    "Price": 1,
    "ServiceCode": "14002",
    "InvoiceText": "Donation test",
    "SmsConfirmation": true,
    "properties": {
      "message_prefix": "Dear customer...",
      "message_suffix": "Best regards..."
    }
}' 'https://test.target365.io/api/strex/transactions'
```

#### Response codes
* 201	Transaction posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Create a Strex payment transaction with one-time password
This example creates a Strex one-time password sent to the end user and get completes the payment by using the one-time password. You can use MessagePrefix and MessageSuffix to influence the start and end of the SMS sent by Strex.

#### HTTP POST /api/strex/one-time-passwords
```JSON
{
    "TransactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "MerchantId": "YOUR_MERCHANT_ID",
    "Recipient": "+4798079008",
    "MessagePrefix": "Dear customer...",
    "MessageSuffix": "Best Regards...",
    "Recurring": false
}
```

```CURL
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "TransactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "MerchantId": "YOUR_MERCHANT_ID",
    "Recipient": "+4798079008",
    "MessagePrefix": "Dear customer...",
    "MessageSuffix": "Best Regards...",
    "Recurring": false,
}' 'https://test.target365.io/api/strex/one-time-passwords'
```

#### Response codes
* 201	Posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

 Get input from end user (eg. via web site) and post payment transaction using same TransactionId as above.

#### HTTP POST /api/strex/transactions
```JSON
{
    "TransactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "ShortNumber": "2002",
    "Recipient": "+4798079008",
    "MerchantId": "YOUR_MERCHANT_ID",
    "Price": 1,
    "ServiceCode": "14002",
    "InvoiceText": "Donation test",
    "OneTimePassword": "ONE_TIME_PASSWORD_FROM_USER"
}
```

```CURL
curl -XPOST -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "TransactionId": "3202b85f-fac2-432a-8e55-a20ab8680211",
    "ShortNumber": "2002",
    "Recipient": "+4798079008",
    "MerchantId: "YOUR_MERCHANT_ID",
    "Price": 1,
    "ServiceCode": "14002",
    "InvoiceText": "Donation test",
    "OneTimePassword": "ONE_TIME_PASSWORD_FROM_USER"
}' 'https://test.target365.io/api/strex/transactions'
```

#### Response codes
* 201	Transaction posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Reverse a Strex payment transaction
This example reverses a previously billed Strex payment transaction. The original transaction will not change, but a reversal transaction will be created that counters the previous transaction by a negative Price. The reversal is an asynchronous operation that usually takes a few seconds to finish.

#### HTTP DELETE /api/strex/transactions/3202b85f-fac2-432a-8e55-a20ab8680211

```CURL
curl -XDELETE -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" 'https://test.target365.io/api/strex/transactions/3202b85f-fac2-432a-8e55-a20ab8680211'
```

Response:
```JSON
{
    "TransactionId": "-3202b85f-fac2-432a-8e55-a20ab8680211"
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

#### HTTP PUT api/one-click/configs/Test1
```JSON
{
    "ConfigId": "Test1",
    "ShortNumber": "2002",
    "Price": 99,
    "MerchantId": "YOUR_MERCHANT_ID",
    "ServiceCode": "14002",
    "OnlineText": "Buy directly",
    "OfflineText": "Buy with PIN-code",
    "InvoiceText": "Donation test",
    "RedirectUrl": "https://your-return-url.com?id={TransactionId}",
    "Recurring": true,
    "SubscriptionInterval": "monthly",
    "SubscriptionPrice": 99,
    "SubscriptionStartSms": "Thanks for donating 99kr each month."
}
```

{TransactionId} in RedirectUrl is replaced by actual transaction id.

If Recurring is set to 'false', the following parameters are optional:

* SubscriptionInterval - Possible values are "weekly", "monthly", "yearly"

* SubscriptionPrice - How much the subscriber will be charged each interval

This parameter is optional:

* SubscriptionStartSms - SMS that will be sent to the user when subscription starts.

```CURL
curl -XPUT -H 'X-ApiKey: <KeyString>' -H "Content-type: application/json" -d '{
    "ConfigId": "Test1",
    "ShortNumber": "2002",
    "Price": 99,
    "MerchantId": "YOUR_MERCHANT_ID",
    "ServiceCode": "14002",
    "OnlineText": "Buy directly",
    "OfflineText": "Buy with PIN-code",
    "InvoiceText: "Donation test",
    "RedirectUrl": "https://your-return-url.com?id={TransactionId}", // {TransactionId} is replaced by actual transaction id
    "Recurring": true,
    "SubscriptionInterval": "monthly",
    "SubscriptionPrice": 99,
    "SubscriptionStartSms": "Thanks for donating 99kr each month."
}' 'https://test.target365.io/api/one-click/configs/Test1'
```

#### Response codes
* 201	Config posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### One-time transaction
This example sets up a simple one-time transaction for one-click without the use of config. After creation you can redirect the end-user to the one-click landing page by redirecting to http://betal.strex.no/{YOUR-ACCOUNT-ID}/{YOUR-TRANSACTION-ID} for PROD and http://test-strex.target365.io/{YOUR-ACCOUNT-ID}/{YOUR-TRANSACTION-ID} for TEST-environment.

If the MSISDN can't be determined automatically on the landing page the end user will have to enter the MSISDN and will receice an SMS with a pin-code that must be entered. Entering the pin-code can be attempted only 3 times before the transaction is abandoned and the end user is redirected back to the redirectUrl.

![one-time sequence](https://github.com/Target365/sdk-for-net/raw/master/oneclick-simple-transaction-flow.png "One-time sequence diagram")

```C#
var transaction = new StrexTransaction
{
    TransactionId = transactionId,
    ShortNumber = "2002",
    MerchantId = "YOUR_MERCHANT_ID",
    Price = 1,
    ServiceCode = ServiceCodes.NonCommercialDonation,
    InvoiceText = "Donation test"
};

transaction.Properties["RedirectUrl"] = "https://your-return-url.com?id=" + transactionId;
await serviceClient.CreateStrexTransactionAsync(transaction);

// TODO: Redirect end-user to one-click landing page
```
### Setup subscription transaction
This example sets up a subscription transaction for one-click. After creation you can redirect the end-user to the one-click landing page by redirecting to http://betal.strex.no/{YOUR-ACCOUNT-ID}/{YOUR-TRANSACTION-ID} for PROD and http://strex-test.target365.io/{YOUR-ACCOUNT-ID}/{YOUR-TRANSACTION-ID} for TEST-environment.
![subscription sequence](https://github.com/Target365/sdk-for-net/raw/master/oneclick-subscription-flow.png "Subscription sequence diagram")
```C#
var transaction = new StrexTransaction
{
    TransactionId = transactionId,
    ShortNumber = "2002",
    MerchantId = "YOUR_MERCHANT_ID",
    Price = 1,
    ServiceCode = ServiceCodes.NonCommercialDonation,
    InvoiceText = "Donation test"
};

transaction.Properties["Recurring"] = true;
transaction.Properties["RedirectUrl"] = "https://your-return-url.com?id=" + transactionId;
await serviceClient.CreateStrexTransactionAsync(transaction);

// TODO: Redirect end-user to one-click landing page
```
### Recurring transaction
This example sets up a recurring transaction for one-click. After creation you can immediately get the transaction to get the status code - the server will wait up to 20 seconds for the async transaction to complete.
![Recurring sequence](https://github.com/Target365/sdk-for-net/raw/master/oneclick-recurring-flow.png "Recurring sequence diagram")
```C#
var transaction = new StrexTransaction
{
    TransactionId = transactionId,
    Recipient = "RECIPIENT_FROM_SUBSCRIPTION_TRANSACTION"
    ShortNumber = "2002",
    MerchantId = "YOUR_MERCHANT_ID",
    Price = 1,
    ServiceCode = ServiceCodes.NonCommercialDonation,
    InvoiceText = "Donation test"
};

await serviceClient.CreateStrexTransactionAsync(transaction);
transaction = await serviceClient.GetStrexTransactionAsync(transactionId);

// TODO: Check transaction.StatusCode
```

## Lookup

### Address lookup for mobile number
This example looks up address information for the mobile number 98079008. Lookup information includes registered name and address.
```C#
var lookup = await serviceClient.LookupAsync("+4798079008");
var firstName = lookup.FirstName;
var lastName = lookup.LastName;
```

## Keywords

### Create a keyword
This example creates a new keyword on short number 2002 that forwards incoming SMS messages to 2002 that starts with "HELLO" to the URL  "https://your-site.net/api/receive-sms".
```C#
var keyword = new Keyword
{
    ShortNumberId = "NO-2002",
    KeywordText = "HELLO",
    Mode = KeywordModes.Text,
    ForwardUrl = "https://your-site.net/api/receive-sms",
    Enabled = true
};

var keywordId = await serviceClient.CreateKeywordAsync(keyword);
```

### Delete a keyword
This example deletes a keyword.
```C#
await serviceClient.DeleteKeywordAsync(keywordId);
```

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

### SMS forward using the SDK
This example shows how to parse an SMS forward request using the SDK.
```C#
[Route("api/receive-sms")]
public async Task<HttpResponseMessage> PostInMessage(HttpRequestMessage request)
{
    var settings = new JsonSerializerSettings
    {
    	Converters = new List<JsonConverter> { new StringEnumConverter { CamelCaseText = false } },
    };
    
    var message = JsonConvert.DeserializeObject<InMessage>(await request.Content.ReadAsStringAsync(), settings);
    return request.CreateResponse(HttpStatusCode.OK);
}
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

### DLR forward using the SDK
This example shows how to parse an DLR forward request using the SDK.
```C#
[Route("api/receive-dlr")]
public async Task<HttpResponseMessage> PostDeliveryReport(HttpRequestMessage request)
{
    var settings = new JsonSerializerSettings
    {
    	Converters = new List<JsonConverter> { new StringEnumConverter { CamelCaseText = false } },
    };
    
    var message = JsonConvert.DeserializeObject<DeliveryReport>(await request.Content.ReadAsStringAsync(), settings);
    return request.CreateResponse(HttpStatusCode.OK);
}
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
|UnknownError|Unknown error|
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
