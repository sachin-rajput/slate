---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL
  - javascript: Node
  - web: Web

toc_footers:
  - <a href='mailto:sachin.rajput@dowjones.com?Subject=Access Request for EPS-API'>Sign Up for a Developer Key</a>

includes:

search: true
---

# API Reference

<aside class="quote">
  Welcome to the documentation for Enterprise Payment Solutions! You can use our various Web & API based solutions using this documentation.
</aside>

> Some of our services are `web-based-only` meaning they only work on a browser.

<aside class="info">
  <ul>
  <li>We have language bindings in cURL and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.</li>

  <li>This document explains the integration steps for upstream applications consume Hosted Payment Page in their web site or application or process payment transactions from their application using our APIs.</li>

  <li>We currently support only CreditCards, moving forward we plan to support more payment instruments.</li>
  </ul>
</aside>

<!-- This example API documentation page was created with [Slate](https://github.com/lord/slate). -->

# Authentication

- EPS uses [AWS Cognito](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) to authenticate and authorize our consumers.

- We use the AWS Cognito: [/TOKEN](https://docs.aws.amazon.com/cognito/latest/developerguide/token-endpoint.html) endpoint which uses [OAuth 2.0 Client Credentials Grant](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/).

- EPS team will provide you with an unique `api-key` for each consumer.

- Following is the header for the AWS Cognito Token endpoint that looks like the following:

  `Authorization: Basic api-key`

<aside class="notice">
You must replace <code>api-key</code> with your personal API key.
</aside>

_Note_: The api-key is Basic Base64Encode(client_id:client_secret)

## Get Access Token

> Exchanging Client Credentials for an Access Token:

```shell
# Pay attention to the data: grant_type and scope variable
curl "http://djis-eps-dev.auth.us-east-1.amazoncognito.com/oauth2/token"
  -H "Authorization: Basic paste-api-key-here"
  -d 'grant_type=client_credentials&scope=managecreditcard/all'
```

```javascript
const eps = require("dj-eps");

let token = eps.authorize("paste-api-key-here");
```

> Make sure to replace `paste-api-key-here` with your API key.

This endpoint is used to get the access_token

### HTTP Request [[ ... View Endpoints](/api/specs/#endpoints)]

`POST http://djis-eps-dev.auth.us-east-1.amazoncognito.com/oauth2/token`

### Body [[ ... Tags Definition](/api/specs/#tags)]

| Parameter  | Default              | Description                         |
| ---------- | -------------------- | ----------------------------------- |
| grant_type | client_credentials   | Should always be client_credentials |
| scope      | managecreditcard/all | See: [Scopes](/api/specs/#scopes)   |

<aside class="notice">
EPS expects the token to be included in all API requests to the server in a header as Authorization Bearer token.
</aside>

### Success Response

> The above command returns JSON structured like this for <code>SUCCESS</code>

```json
{
  "access_token": "eyJz9sdfsdfsdfsd",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

| Parameter    | Description                                 |
| ------------ | ------------------------------------------- |
| access_token | Access Token to be used as Bearer token     |
| token_type   | Type of token returned                      |
| expires_in   | Time after which the token would be invalid |

## Scopes

- **managecreditcard/hosted:** Can only access Hosted Payment Page of EPS
- **managecreditcard/payments:** Can only access Payment API of EPS with edit permission
- **managecreditcard/all:** Can access all the Web & API services of EPS

# Payments

## Payment: Request

```shell

Make sure to replace aws-cognito-token with your token from
Authorization Call.

curl "https://eps.financedev.dowjones.io/payments/v1/api"
  -H "Authorization: Bearer aws-cognito-token"
  -H "Accept: application/json"
  -d '{
  "consumerId": "MSCGBP",
  "customerId": "EPSTest01",
  "cardId": "AP1H63VWF73V5454",
  "expirationDate": "10/2019",
  "currency": "840",
  "nameOnCard": "John Snow",
  "address1": "1 Times Square",
  "city": "New York",
  "state": "NJ",
  "postalCode": "10001",
  "country": "USA",
  "transactionChargeDesc1": "PRD-WSJ",
  "transactionType": "Sale",
  "transactionStateTax": "0",
  "transactionShipTax": "0",
  "transactionTotal": "13456",
  "transactionChargeDesc2": "Charge Desc 2",
  "transactionChargeDesc3": "Charge Desc 3",
  "transactionChargeDesc4": "Charge Desc 4",
  "transactionAltTax": "0",
  "transactionBuyerCode": "COMMS",
  "cardType": "MasterCard",
  "cvv": "123",
  "syncMode": true,
  "force": true,
  "forcePreAuth": true,
  "controlNumber": "0",
  "authorizationCode": "45667T",
  "validateType": true,
  "transactionDate": "10/2018",
  "transactionShipDate": "04/16/2019",
  "isLevel1": true,
  "strictMode": true,
  "transactionPONumber": "12345",
  "transactionInvNumber": "INV13556",
  "shipToFirstName": "FName",
  "shipToLastName": "LName",
  "shipToPostalCode": "13458",
  "shipToCompany": "Bloomberg Inc",
  "shipToStreet1": "1 Times Square",
  "shipToCity": "New York",
  "shipToStateProv": "NY",
  "shipToCountry": "USA",
  "shipFromName": "FName LName",
  "shipFromCompany": "Dow Jones & Co",
  "shipFromPostalCode": "12345",
  "shipFromStreet1": "1 Times Square",
  "shipFromCity": "New York",
  "shipFromStateProv": "NY",
  "shipFromCountry": "USA",
  "orderItemNumber": "1",
  "orderQty": "1",
  "orderDesc": "Test Product 1",
  "orderCommCode": "COMM",
  "orderId": "5d60cc2a-aae3-4563-0056-3443bh3233j5",
  "orderItemId": "HJYU",
  "orderPrice": "13458",
  "orderProductCode": "HJYU",
  "orderStateTax": "100",
  "orderVATShipAmt": "0",
  "orderVATTax": "0",
  "orderDiscAmt": "0",
  "orderDutyTax": "0",
  "orderTotal": "13458",
  "orderUnitMeasure": "NUMBER",
  "OrderItemList": []
}'
```

> Make sure to replace `paste-api-key-here` with your API key.

```javascript
const eps = require("dj-eps");

let token = eps.authorize("paste-api-key-here");

let request = {
  consumerId: "BIAR",
  customerId: "9ALL016800",
  cardId: "RTACYYD07C7N5454",
  expirationDate: "12/2019",
  nameOnCard: "John Doe",
  address1: "1 Times Square",
  city: "New York",
  state: "NY",
  postalCode: "10001",
  country: "USA",
  currency: "840",
  syncMode: "true",
  force: "false",
  authorizationCode: "0",
  validateType: "true",
  cardType: "MasterCard",
  shipToFirstName: "John",
  shipToLastName: "Doe",
  shipToStreet1: "1 Times Sq",
  shipToCity: "New York",
  shipToState: "NY",
  shipToPostalCode: "10001",
  shipToCountry: "US",
  shipFromName: "Dow Jones & Co",
  shipFromCompany: "Dow Jones & Co",
  shipFromStreet1: "P O Box 300",
  shipFromCity: "Princeton",
  shipFromState: "NJ",
  shipFromPostalCode: "08852",
  shipFromCountry: "US",
  orderId: "",
  orderItemNumber: "1",
  orderQty: "1",
  orderDesc: "Test Product",
  orderCommCode: "0",
  orderItemId: "HJYU",
  orderPrice: "13458",
  orderProductCode: "HJYU",
  orderStateTax: "100",
  orderVATShipAmt: "08540",
  orderVATTax: "0",
  orderDiscAmt: "0",
  orderDutyTax: "0",
  orderTotal: "13458",
  orderUnitMeasure: "NUMBER",
  transactionDate: "01/2018",
  transactionType: "Sale",
  transactionChargeDesc1: "whatever1111*",
  transactionChargeDesc2: "whatever2222*",
  transactionChargeDesc3: "whatever3333*",
  transactionChargeDesc4: "whatever4444*",
  transactionAltTax: "100",
  transactionInvNumber: "12345678911",
  transactionBuyerCode: "WSJ123",
  transactionShipDate: "",
  transactionStateTax: "0",
  transactionShipTax: "0",
  transactionTotal: "13458"
};

let transaction = eps.payment({ request, token });
```

This endpoint processes the payment.

<aside class="notice">
Click <a href='/api/specs/#payment-model'>here</a> to read more about the Body parameters (Payment API Model).
</aside>

### Header

Bearer Header with the AWS Cognito Token with other headers:

`Authorization: Bearer aws-cognito-token`

### HTTP Request [[ ... View Endpoints](/api/specs/#endpoints)]

`POST https://eps.financedev.dowjones.io/payments/v1/api`

### Body [[ ... Tags Definition](/api/specs/#tags)]

<aside class="notice">
For Level - II / III savings: you need to pass all <req>required</req> fields and <level>L-II/L-III</level> fields; you may leave the <fetched>auto-fetched</fetched> fields but it is recommended to pass them on the API call as well. Also, StateTax fields should be greater than 0
</aside>

<aside class="info">
All amount fields should be in "cents"
</aside>

| Parameter              | Base Rules                                                                                                                                                      | More Tags / Rules                                                                                                                                                       |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| consumerId             | <req title="This field is required">required</req>                                                                                                              |                                                                                                                                                                         |
| customerId(25)         | <req title="This field is required">required</req>                                                                                                              |                                                                                                                                                                         |
| cardId(16)             | <req title="This field is required">required</req>                                                                                                              |                                                                                                                                                                         |
| transactionChargeDesc1 | <req title="This field is required">required</req>                                                                                                              |                                                                                                                                                                         |
| transactionType        | <req title="This field is required">required</req>                                                                                                              |                                                                                                                                                                         |
| transactionTotal       | <req title="This field is required">required</req>                                                                                                              |                                                                                                                                                                         |
| expirationDate         | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| nameOnCard(50)         | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| address1(60)           | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| city(25)               | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| state                  | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| postalCode             | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| country                | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipToFirstName(50)    | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| shipToLastName(50)     | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| shipToStreet1(60)      | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| shipToCity             | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| shipToState            | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| shipToPostalCode       | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| shipToCountry          | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| currency               | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| cardType               | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| orderItemNumber        | <created title="The field value is created by the system">auto-created</created>                                                                                |                                                                                                                                                                         |
| orderPrice             | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| orderTotal             | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipFromName(50)       | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipFromCompany(50)    | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipFromStreet1(50)    | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipFromCity           | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipFromState          | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipFromPostalCode     | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| shipFromCountry        | <fetched title="The field value is fetched by the system">auto-fetched</fetched><mut title="The field is value can be overridden on the API call">mutable</mut> |                                                                                                                                                                         |
| orderProductCode       | <opt title="This field is optional">optional</opt>                                                                                                              | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| orderStateTax          | <opt title="The field is optional">optional</opt>                                                                                                               | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level> <defaults title="The field has a default value">default: "0"</defaults> |
| transactionInvNumber   | <opt title="The field is optional">optional</opt>                                                                                                               | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                                         |
| transactionStateTax    | <opt title="The field is optional">optional</opt>                                                                                                               | <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level> <defaults title="The field has a default value">default: "0"</defaults> |
| cvv                    | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| syncMode               | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| force                  | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "false"</defaults>                                                                                             |
| controlNumber          | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| authorizationCode      | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| validateType           | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "false"</defaults>                                                                                             |
| orderId                | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| orderQty               | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "1"</defaults>                                                                                                 |
| orderDesc              | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| orderCommCode          | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "5092"</defaults>                                                                                              |
| orderItemId            | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| orderVATShipAmt        | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |
| orderVATTax            | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |
| orderDiscAmt           | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |
| orderDutyTax           | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |
| orderUnitMeasure       | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "NUMBER"</defaults>                                                                                            |
| transactionDate        | <created title="The field value is created by the system">auto-created</fetched>                                                                                |                                                                                                                                                                         |
| transactionChargeDesc2 | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| transactionChargeDesc3 | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| transactionChargeDesc4 | <opt title="The field is optional">optional</opt>                                                                                                               |                                                                                                                                                                         |
| transactionAltTax      | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |
| transactionBuyerCode   | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |
| transactionShipDate    | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |
| transactionShipTax     | <opt title="The field is optional">optional</opt>                                                                                                               | <defaults title="The field has a default value">default: "0"</defaults>                                                                                                 |

## Payment: Success

<aside class="success">
On Success, OrderId is returned along with other payment information.
</aside>

> The above command returns JSON structured like this for <code>SUCCESS</code>

```json
{
  "data": {
    "status": "success",
    "statusCode": 200,
    "authCode": "tst323",
    "amount": "30000",
    "transType": "Auth",
    "token": {
      "id": "RTACYYD07C7N5454",
      "bin": "41124113",
      "expires": "5454",
      "desc": "0110",
      "indicator": "1",
      "isLevel3Eligible": false
    },
    "code": "100",
    "orderId": "5d6e8505-335a-3000-0086-0003ba9896f7",
    "dateTime": "2019-09-03T19:38:43.025Z",
    "info": "Approved."
  }
}
```

### Success Response

| Parameter              | Example                              | DESCRIPTION                                                                                       |
| ---------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------- |
| status                 | success                              | States if the transaction was successful or failure                                               |
| statusCode             | 200                                  | HTTP Return Status Code; mapped to [Status Return Codes](/api/specs/#status-codes)                |
| authCode               | tst233                               | Authorization Code returned by the bank.                                                          |
| amount                 | 12300                                | Amount in cents                                                                                   |
| transType              | Auth                                 | Transaction type                                                                                  |
| token                  |                                      | Token object containing the credit card information                                               |
| token.id               | RTACYYD07C7N5454                     | Token used for the transaction                                                                    |
| token.bin              | 41131122                             | Bin number for the card                                                                           |
| token.lastFour         | 5454                                 | Last four for the card                                                                            |
| token.expires          | 0                                    | Card Expiry                                                                                       |
| token.desc             | 0                                    | Card Descriptor                                                                                   |
| token.indicator        | 0                                    | Card Indicator                                                                                    |
| token.isLevel3Eligible | 0                                    | Card Level                                                                                        |
| code                   | 100                                  | Return Code from the payment vendor; mapped to [Response Codes](/api/specs/#response-codes) table |
| orderId                | 5d6e8505-335a-3000-0086-0003ba9896f7 | OrderId returned from the payment vendor.                                                         |
| dateTime               | 2019-09-03T19:38:43.025Z             | Datetime when the transaction took place.                                                         |
| info                   | Approved.                            | Message from the payment vendor.                                                                  |

## Payment: Failure

<aside class="warning">
On Failure, Message is returned along with code from Payment Vendor.
</aside>

> The above command returns JSON structured like this for <code>FAILURE</code>

```json
{
  "data": {
    "status": "failed",
    "statusCode": 400,
    "message": "Country code value 'USA' specified for the 'BillTo' address is not a valid country code.",
    "amount": "30000",
    "token": {
      "id": "RTACYYD07C7N5454",
      "bin": "41124113",
      "expires": "5454",
      "isLevel3Eligible": false
    },
    "code": "211",
    "dateTime": "2019-09-03T19:35:13.186Z"
  }
}
```

### Failure Response

| Parameter              | Example                                                                                    | DESCRIPTION                                                                                       |
| ---------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------- |
| status                 | failed                                                                                     | States if the transaction was successful or failure                                               |
| statusCode             | 400                                                                                        | HTTP Return Status Code; mapped to [Status Return Codes](/api/specs/#status-codes)                |
| info                   | "Country code value 'USA' specified for the 'BillTo' address is not a valid country code." | Error Message from the payment vendor.                                                            |
| amount                 | 12300                                                                                      | Amount in cents                                                                                   |
| token                  |                                                                                            | Token object containing the credit card information                                               |
| token.id               | RTACYYD07C7N5454                                                                           | Token used for the transaction                                                                    |
| token.bin              | 41131122                                                                                   | Bin number for the card                                                                           |
| token.lastFour         | 5454                                                                                       | Last four for the card                                                                            |
| token.expires          | 0                                                                                          | Card Expiry                                                                                       |
| token.isLevel3Eligible | 0                                                                                          | Card Level                                                                                        |
| code                   | 211                                                                                        | Return Code from the payment vendor; mapped to [Response Codes](/api/specs/#response-codes) table |
| dateTime               | 2019-09-03T19:38:43.025Z                                                                   | Datetime when the transaction took place.                                                         |

## Payment: Model

Description for each field in the Body of the Payment request.

| Parameter              | Description                                                                                                                                                        |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|                        |                                                                                                                                                                    |
| consumerId             | Character code of Dow Jones billing entity. Provided by EPS App Team.                                                                                              |
| customerId(25)         | Customer within the requesting entity's realm. Code must be unique within a consumer ID.                                                                           |
| cardId(16)             | **Starts with A or R only**; Credit Card Token for the transaction                                                                                                 |
| orderProductCode       | Order Product code <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                                                 |
| transactionChargeDesc1 | Description which goes on customer credit card statement                                                                                                           |
| transactionType        | Can be Auth, PreAuth, PostAuth, Credit or Void                                                                                                                     |
| transactionTotal       | Transaction amount for the transaction                                                                                                                             |
| expirationDate         | Format: MM/YYYY Credit Card Expiration Date                                                                                                                        |
| nameOnCard(50)         | Name on the Credit Card                                                                                                                                            |
| address1(60)           | Address on file for the customer                                                                                                                                   |
| city(25)               | City on file for the customer                                                                                                                                      |
| state                  | State on file for the customer                                                                                                                                     |
| postalCode             | Postal Code on file for the customer                                                                                                                               |
| country                | Country on file for the customer                                                                                                                                   |
| shipToFirstName(50)    | Ship to first name <defaults>defaults: "nameOnCard"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>     |
| shipToLastName(50)     | Ship to last name <defaults>defaults: "nameOnCard"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>      |
| shipToStreet1(60)      | Ship to street1 <defaults>defaults: "address1"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>          |
| shipToCity             | Ship to city <defaults>defaults: "city"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                 |
| shipToState            | Ship to state <defaults>defaults: "state"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>               |
| shipToPostalCode       | Ship to postal code <defaults>defaults: "postalCode"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>    |
| shipToCountry          | Ship to country <defaults>defaults: "country"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>           |
| currency               | Currency for the transaction; this feature still not available                                                                                                     |
| cardType               | Type of Credit Card                                                                                                                                                |
| orderItemNumber        | Order Item Number                                                                                                                                                  |
| orderPrice             | Order Price is same as the total charge for the transaction                                                                                                        |
| orderTotal             | Order Total is same as the total charge for the transaction                                                                                                        |
| shipFromName(50)       | Ship from name <defaults>defaults: "Dow Jones Co & Inc"</defaults>                                                                                                 |
| shipFromCompany(50)    | Ship from company name <defaults>defaults: "Dow Jones Co & Inc"</defaults>                                                                                         |
| shipFromStreet1(50)    | Ship from street1 <defaults>defaults: "PO Box 300"</defaults>                                                                                                      |
| shipFromCity           | Ship from city <defaults>defaults: "Princeton"</defaults>                                                                                                          |
| shipFromState          | Ship from state <defaults>defaults: "NJ"</defaults>                                                                                                                |
| shipFromPostalCode     | Ship from postal code <defaults>defaults: "08852"</defaults>                                                                                                       |
| shipFromCountry        | Ship from country <defaults>defaults: "USA"</defaults>                                                                                                             |
| orderStateTax          | Order State tax<defaults>defaults: "0"</defaults> <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>                  |
| transactionInvNumber   | Invoice number from the upstream system; important for reconciling <level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level> |
| transactionStateTax    | State tax for the transaction; defaulted to "0"                                                                                                                    |
| cvv                    | Security code for the credit card                                                                                                                                  |
| syncMode               | Process payment and update other upstream system; this feature still not available                                                                                 |
| force                  | Force a PreAuth transaction                                                                                                                                        |
| controlNumber          | Control number to process credit                                                                                                                                   |
| authorizationCode      | Auth Code to process credit                                                                                                                                        |
| validateType           | Check if card is valid first                                                                                                                                       |
| orderId                | OrderId to process credit                                                                                                                                          |
| orderQty               | Order quantity. Defaults to 1                                                                                                                                      |
| orderDesc              | Order Description                                                                                                                                                  |
| orderCommCode          | Order Comm Code defaults to 5092                                                                                                                                   |
| orderItemId            | Order Item Id is created by EPS                                                                                                                                    |
| orderVATShipAmt        | Order Vat ship tax                                                                                                                                                 |
| orderVATTax            | Order Vat tax                                                                                                                                                      |
| orderDiscAmt           | Order Disc tax                                                                                                                                                     |
| orderDutyTax           | Order Duty tax                                                                                                                                                     |
| orderUnitMeasure       | Order Unit Measure                                                                                                                                                 |
| transactionDate        | Generated by EPS                                                                                                                                                   |
| transactionChargeDesc2 | Extra description                                                                                                                                                  |
| transactionChargeDesc3 | Extra description                                                                                                                                                  |
| transactionChargeDesc4 | Extra description                                                                                                                                                  |
| transactionAltTax      | Transaction Alternate taxes                                                                                                                                        |
| transactionBuyerCode   | Buyer Code                                                                                                                                                         |
| transactionShipDate    | Shippping Date                                                                                                                                                     |
| transactionShipTax     | Ship Tax                                                                                                                                                           |

# Codes

## Status Codes

| Code | Meaning                                                                                                                      |
| ---- | ---------------------------------------------------------------------------------------------------------------------------- |
| 200  | The request has succeeded with the operation                                                                                 |
| 201  | The request has succeeded and a new resource has been created as a result.                                                   |
| 400  | Bad Request -- Your request is invalid.                                                                                      |
| 401  | Unauthorized -- Your API key or token is wrong.                                                                              |
| 403  | Forbidden -- The client does not have access rights to the content.                                                          |
| 404  | Not Found -- The server can not find requested resource.                                                                     |
| 405  | Method Not Allowed -- The request method is known by the server but has been disabled and cannot be used.                    |
| 406  | Not Acceptable -- You requested a format that isn't JSON.                                                                    |
| 409  | Conflict -- This response is sent when a request conflicts with the current state of the server, like resource already exist |
| 418  | I'm a teapot.                                                                                                                |
| 429  | Too Many Requests -- The user has sent too many requests in a given amount of time ("rate limiting").                        |
| 500  | Internal Server Error -- We had a problem with our server. Try again later.                                                  |
| 503  | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.                                    |

## Response Codes

| Code | Meaning                                                        |
| ---- | -------------------------------------------------------------- |
| 1    | The transaction was approved and was successful                |
| 9    | The transaction was accepted for verification                  |
| 50   | Declined (General).                                            |
| 51   | Connection timed-out                                           |
| 101  | Transaction failed to settle due to Hard Error                 |
| 209  | Token does not belong to the customer                          |
| 210  | Invalid Token provided                                         |
| 211  | Invalid Country Code                                           |
| 1014 | Invalid Transaction                                            |
| 1004 | Level III data is missing or is not valid                      |
| 1006 | BIN is not valid                                               |
| 1007 | Card number is not valid                                       |
| 1014 | Invalid Transaction                                            |
| 1026 | Invalid Issuer                                                 |
| 1050 | Declined - Insufficient funds                                  |
| 1051 | Customer card is expired.                                      |
| 1052 | Lost or stolen card                                            |
| 1053 | Pick up card. Issuer wants card returned                       |
| 1054 | Response is not valid                                          |
| 1057 | Declined - Do not honor                                        |
| 1058 | Declined - Exceeds issuer withdrawal limit                     |
| 1059 | Declined - Exceeds withdrawal limit                            |
| 1060 | Declined - Activity limit exceeded                             |
| 1057 | Declined - Do not honor                                        |
| 1067 | System error.                                                  |
| 1082 | Suspected Fraud                                                |
| 1050 | Insufficent Funds                                              |
| 2056 | Restricted card                                                |
| 2056 | Restricted card                                                |
| 2054 | Card was authorized, but approval was overwritten per merchant |
| 2026 | Lost or stolen card. Card issuer wants the card returned       |
| 2116 | New Card Issued                                                |

# Endpoints

## Dev

### AWS Cognito

`http://djis-eps-dev.auth.us-east-1.amazoncognito.com/oauth2/token`

### EPS API

`https://eps.financedev.dowjones.io/`

### EPS Swagger

`https://eps.financedev.dowjones.io/documentation`

### EPS API Reference

`https://eps.financedev.dowjones.io/api/specs`

## Stag

### AWS Cognito

`https://djis-eps-stag.auth.us-east-1.amazoncognito.com/oauth2/token`

### EPS API

`https://eps.financestag.dowjones.io/`

### EPS Swagger

`https://eps.financestag.dowjones.io/documentation`

### EPS API Reference

`https://eps.financestag.dowjones.io/api/specs`

## Prod

### AWS Cognito

`https://djis-eps-prod.auth.us-east-1.amazoncognito.com/oauth2/token`

### EPS API

`https://eps.financeprod.dowjones.io/`

### EPS Swagger

`https://eps.financeprod.dowjones.io/documentation`

### EPS API Reference

`https://eps.financeprod.dowjones.io/api/specs`

# Tags

## Tag meaning

<req title="This field is required">required</req>`: The field is required`

<fetched title="The field value is fetched by the system">auto-fetched</fetched>`: The field value is fetched by the system`

<created title="The field value is created by the system">auto-created</created>`: The field value is created by the system`

<mut title="The field is value can be overridden on the API call">mutable</mut>`: The field is value can be overridden on the API call`

<level title="The field is required for it to be considered as Level-II/III">L-II/L-III</level>`: The field is required for it to be considered as Level-II/III`

<opt title="The field is optional">optional</opt>`: The field is optional`

<defaults title="The field has a default value">defaults</defaults>`: The field has a default value`
