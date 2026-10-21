# RESTful API for Bitkub V4 (2025-12-03)

# Announcement

- Deposit history records are available for the last 90 days only for [GET /api/v4/crypto/deposits](#get-apiv4cryptodeposits). Records older than 90 days are archived.
- Introducing the New Public API v4 for Crypto Endpoints
- Introducing the New Public API v4 for Fiat Endpoints
- Introducing the New Public API v4 for Market Endpoints: [GET /api/v4/market/open-orders](#get-apiv4marketopen-orders), [GET /api/v4/market/orders](#get-apiv4marketorders) and [GET /api/v4/market/matched](#get-apiv4marketmatched)

# Change log

- 2026-10-21 Introducing Market V4 Endpoints [GET /api/v4/market/open-orders](#get-apiv4marketopen-orders), [GET /api/v4/market/orders](#get-apiv4marketorders) and [GET /api/v4/market/matched](#get-apiv4marketmatched)
- 2026-04-07 Introducing Fiat V4 Endpoints
- 2025-05-27 Added new Crypto endpoint [GET /api/v4/crypto/compensations](#get-apiv4cryptocompensations) and update api specification for [GET /api/v4/crypto/withdraws](#get-apiv4cryptowithdraws) and [GET /api/v4/crypto/deposits](#get-apiv4cryptodeposits)
- 2025-04-08 Added new error codes: [B1016-CW] Deposit is frozen, [V1015-CW] Coin not found
- 2025-04-03 Added new Crypto endpoint [GET /api/v4/crypto/coins](#get-apiv4cryptocoins)
- 2025-02-03 Introducing Crypto V4 Endpoints

# Table of contents

- [Base URL](#base-url)
- [Endpoint types](#endpoint-types)
- [Constructing the request](#constructing-the-request)
- [API documentation](#api-documentation)
- [Error codes](#error-codes)
- [Rate limits](#rate-limits)

# Base URL

- The base URL is: https://api.bitkub.com

# Endpoint types

### Secure endpoints V4

All secure endpoints require [authentication](#constructing-the-request).

| Crypto V4 Endpoints                                           | Method | Deposit | Withdraw | Trade |
| ------------------------------------------------------------- | ------ | ------- | -------- | ----- |
| [/api/v4/crypto/addresses](#get-apiv4cryptoaddresses)         | GET    |         |          |       |
| [/api/v4/crypto/addresses](#post-apiv4cryptoaddresses)        | POST   | ✅      |          |       |
| [/api/v4/crypto/deposits](#get-apiv4cryptodeposits)           | GET    |         |          |       |
| [/api/v4/crypto/withdraws](#get-apiv4cryptowithdraws)         | GET    |         |          |       |
| [/api/v4/crypto/withdraws](#post-apiv4cryptowithdraws)        | POST   |         | ✅       |       |
| [/api/v4/crypto/coins](#get-apiv4cryptocoins)                 | GET    |         |          |       |
| [/api/v4/crypto/compensations](#get-apiv4cryptocompensations) | GET    |         |          |       |

| Fiat V4 Endpoints                                              | Method | Deposit | Withdraw | Trade |
| -------------------------------------------------------------- | ------ | ------- | -------- | ----- |
| [/api/v4/fiat/accounts](#get-apiv4fiataccounts)                | GET    |         | ✅       |       |
| [/api/v4/fiat/deposit/history](#get-apiv4fiatdeposithistory)   | GET    |         |          |       |
| [/api/v4/fiat/withdraw/history](#get-apiv4fiatwithdrawhistory) | GET    |         |          |       |
| [/api/v4/fiat/withdraw](#post-apiv4fiatwithdraw)               | POST   |         |          |       |

| Market V4 Endpoints                                         | Method | Deposit | Withdraw | Trade |
| ----------------------------------------------------------- | ------ | ------- | -------- | ----- |
| [/api/v4/market/open-orders](#get-apiv4marketopen-orders)   | GET    |         |          |       |
| [/api/v4/market/orders](#get-apiv4marketorders)             | GET    |         |          |       |
| [/api/v4/market/matched](#get-apiv4marketmatched)           | GET    |         |          |       |

# Constructing the request

### GET/POST request

- GET requests require parameters as **query string** in the URL (e.g. ?symbol=BTC&limit=10).
- POST requests require JSON payload (application/json).

### Request headers (Secure Endpoints)

Authentication requires API KEY and API SECRET. Every request to the server must contain the following in the request header:

- Accept: application/json
- Content-type: application/json
- X-BTK-APIKEY: {YOUR API KEY}
- X-BTK-TIMESTAMP: {Timestamp i.e. 1699376552354 }
- X-BTK-SIGN: [Signature](#signature)

### Signature

Generate the signature from the timestamp, the request method, API path, query parameter, and JSON payload using HMAC SHA-256. Use the API Secret as the secret key for generating the HMAC variant of JSON payload. The signature is in **hex** format. The user has to attach the signature via the Request Header
You must get a new timestamp in millisecond from [/api/v3/servertime](restful-api.md#get-apiv3servertime). The old one is in second.

#### Example string for signing a signature:

```javascript
//Example for Get Method
1699381086593GET/api/v4/crypto/addresses?symbol=ATOM

// Example for Post Method
1699376552354POST/api/v4/crypto/addresses{"symbol":"ATOM","network": "ATOM"}
```

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/addresses?symbol=ATOM' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/addresses' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a' \
--header 'Content-Type: application/json' \
--data '{
    "symbol": "ATOM",
    "network": "ATOM",
}'
```

# API documentation

Refer to the following for description of each endpoint

### GET /api/v4/crypto/addresses

#### Description:

List all crypto addresses (paginated).

#### Path Params: -

#### Query Params:

| Key     | Type   | Required | Description                      |
| ------- | ------ | -------- | -------------------------------- |
| page    | int    | false    | Page (default = 1)               |
| limit   | int    | false    | Limit (default = 100, max = 200) |
| symbol  | String | false    | e.g. ATOM                        |
| network | String | false    | e.g. ATOM                        |
| memo    | String | false    | e.g. 107467228685                |

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/addresses?symbol=ATOM' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "page": 1,
    "total_page": 1,
    "total_item": 2,
    "items": [
      {
        "symbol": "ATOM",
        "network": "ATOM",
        "address": "cosmos1jcslcmz2lpsy7uq5u2ktn459qce2chqapey7gh",
        "memo": "107467228685",
        "created_at": "2022-03-18T05:41:40.199Z"
      },
      {
        "symbol": "ATOM",
        "network": "ATOM",
        "address": "cosmos1jcslcmz2lpsy7uq5u2ktn459qce2chqapey7gh",
        "memo": "104010164476",
        "created_at": "2022-03-18T05:46:34.113Z"
      }
    ]
  }
}
```

### POST /api/v4/crypto/addresses

#### Description:

Generate a new crypto address (if an address exists; will return the existing address).

#### Required Permission: `is_deposit`

#### Path Params: -

#### Query Params: -

#### Body Params:

| Key     | Type   | Required | Description |
| ------- | ------ | -------- | ----------- |
| symbol  | String | true     | e.g. ATOM   |
| network | String | true     | e.g. ATOM   |

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/addresses' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a' \
--header 'Content-Type: application/json' \
--data '{
    "symbol": "ETH",
    "network": "ETH",
}'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": [
    {
      "symbol": "ETH",
      "network": "ETH",
      "address": "0x520165471daa570ab632dd504c6af257bd36edfb",
      "memo": ""
    }
  ]
}
```

### GET /api/v4/crypto/deposits

#### Description:

List crypto deposit history.

#### Note: Only deposit records within the last 90 days will be returned.

#### Path Params: -

#### Query Params:

| Key           | Type   | Required | Description                                                                                                                                                                |
| ------------- | ------ | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| page          | int    | false    | Page (default = 1)                                                                                                                                                         |
| limit         | int    | false    | Limit (default = 100, max = 200)                                                                                                                                           |
| symbol        | String | false    | Coin Symbol (e.g. BTC, ETH)                                                                                                                                                |
| status        | String | false    | Transaction Deposit Status (pending, rejected, complete)                                                                                                                   |
| created_start | String | false    | The start of the time range for the transaction creation timestamp. Only transactions created on or after this timestamp will be included. (e.g. 2025-01-11T10:00:00.000Z) |
| created_end   | String | false    | The end of the time range for the transaction creation timestamp. Only transactions created on or before this timestamp will be included. (e.g. 2025-01-11T10:00:00.000Z)  |

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/deposits?limit=10' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "page": 1,
    "total_page": 1,
    "total_item": 1,
    "items": [
     {
        "hash": "XRPWD0000100276",
        "symbol": "XRP",
        "network": "XRP",
        "amount": "5.75111474",
        "from_address": "0xDaCd17d1E77604aaFB6e47F5Ffa1F7E35F83fDa7",
        "to_address": "0x2b0849d47a90e3c4784a5b1130a14305a099d828",
        "confirmations": 1,
        "status": "complete",
        "created_at": "2022-03-18T05:41:40.199Z",
        "completed_at": "2022-03-18T05:45:50.199Z"
      }
    ]
  }
}
```

### GET /api/v4/crypto/withdraws

#### Description:

List crypto withdrawal history.

#### Path Params: -

#### Query Params:

| Key           | Type   | Required | Description                                                                                                                                                                |
| ------------- | ------ | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| page          | int    | false    | Page (default = 1)                                                                                                                                                         |
| limit         | int    | false    | Limit (default = 100, max = 200)                                                                                                                                           |
| symbol        | String | false    | Coin Symbol (e.g. BTC, ETH)                                                                                                                                                |
| status        | String | false    | Transaction Withdraw Status (pending, processing, reported, rejected, complete)                                                                                            |
| created_start | String | false    | The start of the time range for the transaction creation timestamp. Only transactions created on or after this timestamp will be included. (e.g. 2025-01-11T10:00:00.000Z) |
| created_end   | String | false    | The end of the time range for the transaction creation timestamp. Only transactions created on or before this timestamp will be included. (e.g. 2025-01-11T10:00:00.000Z)  |

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/withdraws?limit=10' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "page": 1,
    "total_page": 1,
    "total_item": 2,
    "items": [
       {
        "txn_id": "RDNTWD0000804050",
        "external_ref": "XX_1111111111",
        "hash": null,
        "symbol": "RDNT",
        "network": "ARB",
        "amount": "2.00000000",
        "fee": "4.36",
        "address": "0xDaCd17d1E77604aaFB6e47F5Ffa1F7E35F83fDa7",
        "memo": "",
        "status": "processing",
        "created_at": "2024-09-01T10:02:43.211Z",
        "completed_at": "2024-09-01T10:02:45.031Z"
      },
      {
        "txn_id": "BTCWD1321312683",
        "external_ref": "XX_1111111112",
        "hash": "0x8891b79c79f0842c9a654db47745fe0291fba222b290d22cabc93f8ae4490303",
        "symbol": "BTC",
        "network": "BTC",
        "amount": "0.10000000",
        "fee": "0.0025",
        "address": "0xDaCd17d1E77604aaFB6e47F5Ffa1F7E35F83fDa7",
        "memo": "",
        "status": "complete",
        "created_at": "2024-09-01T10:02:43.211Z",
        "completed_at": "2024-09-01T10:02:45.031Z"
      }
    ]
  }
}
```

### POST /api/v4/crypto/withdraws

#### Description:

Make a withdrawal to a trusted address.

#### Required Permission: `is_withdraw`

#### Path Params: -

#### Query Params: -

#### Body Params:

| Key     | Type   | Required | Description                           |
| ------- | ------ | -------- | ------------------------------------- |
| symbol  | String | true     | Symbol for withdrawal (e.g. BTC, ETH) |
| amount  | String | true     | Amount to withdraw                    |
| address | String | true     | Address to withdraw                   |
| memo    | String | false    | Memo or destination tag to withdraw   |
| network | String | true     | Network to withdraw                   |

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/withdraws' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a' \
--header 'Content-Type: application/json' \
--data '{
    "symbol": "RDNT",
    "amount": "2.00000000",
    "address": "0xDaCd17d1E77604aaFB6e47F5Ffa1F7E35F83fDa7",
    "network": "ARB"
}'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "txn_id": "RDNTWD0000804050",
    "symbol": "RDNT",
    "network": "ARB",
    "amount": "2.00000000",
    "fee": "4.36",
    "address": "0xDaCd17d1E77604aaFB6e47F5Ffa1F7E35F83fDa7",
    "memo": "",
    "created_at": "2024-09-01T10:02:43.211Z"
  }
}
```

### GET /api/v4/crypto/coins

#### Description:

Get all coins (available for deposit and withdraw).

#### Path Params: -

#### Query Params:

| Key     | Type   | Required | Description                 |
| ------- | ------ | -------- | --------------------------- |
| symbol  | String | false    | Coin Symbol (e.g. BTC, ETH) |
| network | String | false    | Network                     |

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/coin?symbol=ATOM' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "items":[
      {
        "name": "Bitcoin",
        "symbol": "BTC",
        "networks": [
          {
            "name": "Bitcoin",
            "network": "BTC",
            "address_regex": "^[13][a-km-zA-HJ-NP-Z1-9]{26,35}$|^(tb1)[0-9A-Za-z]{39,59}$",
            "memo_regex": "",
            "explorer": "https://www.blockchain.com/btc/tx/",
            "contract_address": "",
            "withdraw_min": "0.0002",
            "withdraw_fee": "0.0001",
            "withdraw_internal_min": "",
            "withdraw_internal_fee": "",
            "withdraw_decimal_places": 8,
            "min_confirm": 3,
            "decimal": 8,
            "deposit_enable": true,
            "withdraw_enable": true,
            "is_memo": false
          }
        ],
        "deposit_enable": true,
        "withdraw_enable": true
      }
    ]
  }
}
```

### GET /api/v4/crypto/compensations

#### Description:

List crypto compensations history.

#### Path Params: -

#### Query Params:

| Key           | Type   | Required | Description                                                                                                                                                                |
| ------------- | ------ | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| page          | int    | false    | Page (default = 1)                                                                                                                                                         |
| limit         | int    | false    | Limit (default = 100, max = 200)                                                                                                                                           |
| symbol        | String | false    | Coin Symbol (e.g. BTC, ETH)                                                                                                                                                |
| type          | String | false    | Compensation Type (COMPENSATE,DECOMPENSATE)                                                                                                                                |
| status        | String | false    | Transaction Compensation Status (complete)                                                                                                                                 |
| created_start | String | false    | The start of the time range for the transaction creation timestamp. Only transactions created on or after this timestamp will be included. (e.g. 2025-01-11T10:00:00.000Z) |
| created_end   | String | false    | The end of the time range for the transaction creation timestamp. Only transactions created on or before this timestamp will be included. (e.g. 2025-01-11T10:00:00.000Z)  |

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/crypto/compensations?symbol=ATOM' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "Success",
  "data": {
    "page": 1,
    "total_page": 1,
    "total_item": 2,
    "items": [
      {
        "txn_id": "XRPCP0000001234",
        "symbol": "XRP",
        "type": "DECOMPENSATE",
        "amount": "-1",
        "status": "complete",
        "created_at": "2024-02-09T12:00:00Z",
        "completed_at": "2024-02-09T13:00:00Z",
        "user_id": "1234"
      },
      {
        "txn_id": "BLUECP0000001234",
        "symbol": "BLUE",
        "type": "COMPENSATE",
        "amount": "20",
        "status": "complete",
        "created_at": "2025-04-09T18:30:04Z",
        "completed_at": "2025-04-09T18:30:04Z",
        "user_id": "1234"
      }
    ]
  }
}
```

### GET /api/v4/fiat/accounts

#### Description:

List approved bank accounts for the user.

#### Path Params: -

#### Query Params:

| Key   | Type | Required | Description                     |
| ----- | ---- | -------- | ------------------------------- |
| page  | int  | false    | Page (default = 1, min = 1)     |
| limit | int  | false    | Limit (default = 25, max = 100) |

> **Note**: `page` and `limit` must be provided together. Providing only one will return a validation error.

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/fiat/accounts?page=1&limit=25' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": "123456",
      "bank": "KBANK",
      "name": "John Doe",
      "time": 1706745600
    },
    {
      "id": "789012",
      "bank": "SCB",
      "name": "John Doe",
      "time": 1706832000
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 25
  }
}
```

### GET /api/v4/fiat/deposit/history

#### Description:

List fiat deposit transaction history for the user.

#### Path Params: -

#### Query Params:

| Key   | Type | Required | Description                     |
| ----- | ---- | -------- | ------------------------------- |
| page  | int  | false    | Page (default = 1, min = 1)     |
| limit | int  | false    | Limit (default = 25, max = 100) |

> **Note**: `page` and `limit` must be provided together. Providing only one will return a validation error.

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/fiat/deposit/history?page=1&limit=25' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "txn_id": "THBDPXXXXXXXXXX",
      "currency": "THB",
      "amount": "10000.50",
      "status": "complete",
      "time": 1706745600
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 25
  }
}
```

### GET /api/v4/fiat/withdraw/history

#### Description:

List fiat withdrawal transaction history for the user.

#### Path Params: -

#### Query Params:

| Key   | Type | Required | Description                     |
| ----- | ---- | -------- | ------------------------------- |
| page  | int  | false    | Page (default = 1, min = 1)     |
| limit | int  | false    | Limit (default = 25, max = 100) |

> **Note**: `page` and `limit` must be provided together. Providing only one will return a validation error.

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/fiat/withdraw/history?page=1&limit=25' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "txn_id": "THBDPXXXXXXXXXX",
      "currency": "THB",
      "amount": "5000.00",
      "fee": "10.00",
      "status": "complete",
      "time": 1706745600
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 25
  }
}
```

### POST /api/v4/fiat/withdraw

#### Description:

Submit a fiat withdrawal request to an **approved** bank account.

#### Required Permission: `withdraw`

#### Path Params: -

#### Query Params: -

#### Body Params:

| Key             | Type   | Required | Description                                 |
| --------------- | ------ | -------- | ------------------------------------------- |
| bank_account_no | String | true     | Approved bank account number to withdraw to |
| amount          | double | true     | Withdrawal amount (must be > 0)             |

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/fiat/withdraw' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a' \
--header 'Content-Type: application/json' \
--data '{
    "bank_account_no": "1234567890",
    "amount": 5000.00
}'
```

#### Response:

```javascript
{
  "code": 0,
  "message": "success",
  "data": {
    "txn_id": "THBDPXXXXXXXXXX",
    "bank_account_no": "******7890",
    "currency": "THB",
    "amount": "5000.00",
    "fee": "10.00",
    "receive": "4990.00",
    "time": 1706745600
  }
}
```

### GET /api/v4/market/open-orders

#### Description:

List open orders (pending or partially filled) of the user, with optional filters and cursor-based pagination.

#### Path Params: -

#### Query Params:

| Key               | Type   | Required | Description                                                                  |
| ----------------- | ------ | -------- | ---------------------------------------------------------------------------- |
| symbol            | String | false    | Trading pair symbol (e.g. BTC_THB). Required when `order_id` is provided     |
| order_id          | String | false    | Filter by order ID                                                           |
| side              | String | false    | Order side (`buy`, `sell`)                                                   |
| type              | String | false    | Order type (`limit`, `market`, `stoplimit`)                                  |
| contain_client_id | String | false    | Filter orders whose `client_id` contains this value                          |
| limit             | int    | false    | Limit (default = 10, min = 1, max = 1000)                                    |
| next_id           | String | false    | Cursor ID of the next page (`cursor.id` from previous response). Alphanumeric |
| next_ts           | int    | false    | Cursor timestamp of the next page (`cursor.ts` from previous response)       |
| start_time        | int    | false    | Start time (Unix timestamp in milliseconds)                                  |
| end_time          | int    | false    | End time (Unix timestamp in milliseconds)                                    |

> **Note**:
> - `symbol` is required when `order_id` is provided.
> - `next_id` and `next_ts` must be provided together.

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/market/open-orders?symbol=BTC_THB&limit=10' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "limit": 10,
    "has_next": true,
    "cursor": {
      "id": "68a82566596d482000f4e4edaa05m0",
      "ts": 1755850086843
    },
    "items": [
      {
        "order_id": "68a82566596d482000f4e4edaa05m0",
        "client_id": "CLIENT123",
        "symbol": "BTC_THB",
        "side": "buy",
        "type": "limit",
        "status": "partial_filled",
        "partial_filled": true,
        "price": "2500000.00",
        "order_currency": "THB",
        "order_amount": "1000.00",
        "executed_currency": "THB",
        "executed_amount": "500.00",
        "received_currency": "BTC",
        "received_amount": "0.00019950",
        "total_fee": "1.25",
        "credit_used": "0.00",
        "fee_currency": "THB",
        "net_fee_paid": "1.25",
        "avg_filled_price": "2500000.00",
        "post_only": false,
        "order_created_at": 1755850086843,
        "order_updated_at": 1755850090000,
        "quote_amount": "1000.00"
      }
    ]
  }
}
```

#### Response Fields (`data`):

| Key      | Type    | Description                                                                                              |
| -------- | ------- | -------------------------------------------------------------------------------------------------------- |
| limit    | int     | Limit applied to this request                                                                            |
| has_next | boolean | `true` if more records are available                                                                     |
| cursor   | Object  | Cursor for the next page. Pass `cursor.id` as `next_id` and `cursor.ts` as `next_ts` to get the next page |
| items    | Array   | List of orders (see below)                                                                               |

#### Order Item Fields (`data.items[]`):

| Key                | Type    | Description                                                                         |
| ------------------ | ------- | ----------------------------------------------------------------------------------- |
| order_id           | String  | Order ID                                                                            |
| client_id          | String  | Client ID given when placing the order (omitted if not set)                         |
| symbol             | String  | Trading pair symbol (e.g. BTC_THB)                                                  |
| side               | String  | Order side (`buy`, `sell`)                                                          |
| type               | String  | Order type (`limit`, `market`, `stoplimit`)                                         |
| status             | String  | Order status (see `status` query param for possible values)                         |
| partial_filled     | boolean | `true` if the order has been partially filled                                       |
| price              | String  | Order price (omitted for market orders)                                             |
| stop_price         | String  | Stop price (stop-limit orders only)                                                 |
| order_currency     | String  | Currency of `order_amount`                                                          |
| order_amount       | String  | Order amount                                                                        |
| executed_currency  | String  | Currency of `executed_amount`                                                       |
| executed_amount    | String  | Amount executed so far                                                              |
| received_currency  | String  | Currency of `received_amount`                                                       |
| received_amount    | String  | Amount received so far                                                              |
| total_fee          | String  | Total fee                                                                           |
| credit_used        | String  | Trading credit used to pay the fee                                                  |
| fee_currency       | String  | Currency of the fee                                                                 |
| net_fee_paid       | String  | Fee paid after deducting trading credit (`total_fee` - `credit_used`)               |
| avg_filled_price   | String  | Average filled price                                                                |
| post_only          | boolean | `true` if the order is post-only                                                    |
| canceled_by        | String  | Who canceled the order (omitted if the order is not canceled)                       |
| order_created_at   | int     | Order creation time (Unix timestamp in milliseconds)                                |
| order_triggered_at | int     | Time the stop-limit order was triggered in milliseconds (omitted if not triggered)  |
| order_updated_at   | int     | Last update time in milliseconds (omitted if never updated)                         |
| base_amount        | String  | Order amount in base currency (omitted if not applicable)                           |
| condition          | String  | Order condition (omitted if not applicable)                                         |
| quote_amount       | String  | Order amount in quote currency                                                      |

> **Note**: Optional fields are **omitted** from the response when they have no value, instead of being returned as `""` or `0`.

### GET /api/v4/market/orders

#### Description:

List order history of the user, with optional filters and cursor-based pagination.

#### Path Params: -

#### Query Params:

| Key        | Type   | Required | Description                                                                                                                       |
| ---------- | ------ | -------- | --------------------------------------------------------------------------------------------------------------------------------- |
| symbol     | String | false    | Trading pair symbol (e.g. BTC_THB). Required when `order_id` is provided                                                          |
| order_id   | String | false    | Filter by order ID                                                                                                                |
| side       | String | false    | Order side (`buy`, `sell`)                                                                                                        |
| type       | String | false    | Order type (`limit`, `market`, `stoplimit`)                                                                                       |
| status     | String | false    | Order status (`new`, `open`, `rejected`, `partial_filled`, `partial_filled_canceled`, `filled`, `canceled`, `untriggered`)        |
| limit      | int    | false    | Limit (default = 10, min = 1, max = 1000)                                                                                         |
| next_id    | String | false    | Cursor ID of the next page (`cursor.id` from previous response). Alphanumeric                                                     |
| next_ts    | int    | false    | Cursor timestamp of the next page (`cursor.ts` from previous response)                                                            |
| start_time | int    | false    | Start time (Unix timestamp in milliseconds)                                                                                       |
| end_time   | int    | false    | End time (Unix timestamp in milliseconds)                                                                                         |

> **Note**:
> - `symbol` is required when `order_id` is provided.
> - `next_id` and `next_ts` must be provided together.

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/market/orders?symbol=BTC_THB&status=filled&limit=10' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "limit": 10,
    "has_next": true,
    "cursor": {
      "id": "68a82566596d482000f4e4edaa05m0",
      "ts": 1755850086843
    },
    "items": [
      {
        "order_id": "68a82566596d482000f4e4edaa05m0",
        "client_id": "CLIENT123",
        "symbol": "BTC_THB",
        "side": "buy",
        "type": "limit",
        "status": "filled",
        "partial_filled": false,
        "price": "2500000.00",
        "order_currency": "THB",
        "order_amount": "1000.00",
        "executed_currency": "THB",
        "executed_amount": "1000.00",
        "received_currency": "BTC",
        "received_amount": "0.00039900",
        "total_fee": "2.50",
        "credit_used": "0.00",
        "fee_currency": "THB",
        "net_fee_paid": "2.50",
        "avg_filled_price": "2500000.00",
        "post_only": false,
        "order_created_at": 1755850086843,
        "order_updated_at": 1755850090000,
        "quote_amount": "1000.00"
      }
    ]
  }
}
```

Response fields are the same as [GET /api/v4/market/open-orders](#get-apiv4marketopen-orders).

### GET /api/v4/market/matched

#### Description:

List executed (matched) trades of the user, with optional filters and cursor-based pagination.

#### Path Params: -

#### Query Params:

| Key        | Type    | Required | Description                                                                   |
| ---------- | ------- | -------- | ----------------------------------------------------------------------------- |
| symbol     | String  | false    | Trading pair symbol (e.g. BTC_THB). Required when `order_id` is provided      |
| order_id   | String  | false    | Filter by order ID                                                            |
| txn_id     | String  | false    | Filter by transaction ID. Alphanumeric                                        |
| side       | String  | false    | Order side (`buy`, `sell`)                                                    |
| type       | String  | false    | Order type (`limit`, `market`, `stoplimit`)                                   |
| limit      | int     | false    | Limit (default = 20, min = 1, max = 100)                                      |
| next_id    | String  | false    | Cursor ID of the next page (`cursor.id` from previous response). Alphanumeric |
| next_ts    | int     | false    | Cursor timestamp of the next page (`cursor.ts` from previous response)        |
| start_time | int     | false    | Start time (Unix timestamp in milliseconds)                                   |
| end_time   | int     | false    | End time (Unix timestamp in milliseconds)                                     |
| archive    | boolean | false    | `true` to query archived trade records                                        |

> **Note**:
> - `symbol` is required when `order_id` is provided.
> - `next_id` and `next_ts` must be provided together.
> - `start_time` must be less than `end_time` when both are provided.

#### Body Params: -

#### Example cURL:

```javascript
curl --location 'https://api.bitkub.com/api/v4/market/matched?symbol=BTC_THB&limit=20' \
--header 'X-BTK-TIMESTAMP: 1699381086593' \
--header 'X-BTK-APIKEY: e286825bda3497ae2d03aa3a30c420d603060cb4edbdd3ec711910c86966e9ba' \
--header 'X-BTK-SIGN: f5884963865a6e868ddbd58c9fb9ea4bd013076e8a8fa51d38b86c38d707cb8a'
```

#### Response:

```javascript
{
  "code": "0",
  "message": "success",
  "data": {
    "limit": 20,
    "has_next": true,
    "cursor": {
      "id": "68a82566596d482000f4e4edaa05m1",
      "ts": 1755850086843
    },
    "items": [
      {
        "order_id": "68a82566596d482000f4e4edaa05m0",
        "txn_id": "68a82566596d482000f4e4edaa05m1",
        "client_id": "CLIENT123",
        "symbol": "BTC_THB",
        "type": "limit",
        "status": "filled",
        "side": "buy",
        "is_maker": true,
        "price": "2500000.00",
        "executed_currency": "THB",
        "executed_amount": "1000.00",
        "received_currency": "BTC",
        "received_amount": "0.00039900",
        "fee_rate": "0.25",
        "total_fee": "2.50",
        "credit_used": "0.00",
        "fee_currency": "THB",
        "net_fee_paid": "2.50",
        "txn_ts": 1755850086843
      }
    ]
  }
}
```

#### Response Fields (`data`):

| Key      | Type    | Description                                                                                               |
| -------- | ------- | --------------------------------------------------------------------------------------------------------- |
| limit    | int     | Limit applied to this request                                                                             |
| has_next | boolean | `true` if more records are available                                                                      |
| cursor   | Object  | Cursor for the next page. Pass `cursor.id` as `next_id` and `cursor.ts` as `next_ts` to get the next page |
| items    | Array   | List of matched trades (see below)                                                                        |

#### Matched Item Fields (`data.items[]`):

| Key               | Type    | Description                                                           |
| ----------------- | ------- | --------------------------------------------------------------------- |
| order_id          | String  | Order ID                                                              |
| txn_id            | String  | Transaction (trade) ID                                                |
| client_id         | String  | Client ID given when placing the order (omitted if not set)           |
| symbol            | String  | Trading pair symbol (e.g. BTC_THB)                                    |
| type              | String  | Order type (`limit`, `market`, `stoplimit`)                           |
| status            | String  | Order status                                                          |
| side              | String  | Order side (`buy`, `sell`)                                            |
| is_maker          | boolean | `true` if the trade was executed as maker                             |
| price             | String  | Executed price                                                        |
| executed_currency | String  | Currency of `executed_amount`                                         |
| executed_amount   | String  | Amount executed in this trade                                         |
| received_currency | String  | Currency of `received_amount`                                         |
| received_amount   | String  | Amount received in this trade                                         |
| fee_rate          | String  | Fee rate                                                              |
| total_fee         | String  | Total fee                                                             |
| credit_used       | String  | Trading credit used to pay the fee                                    |
| fee_currency      | String  | Currency of the fee                                                   |
| net_fee_paid      | String  | Fee paid after deducting trading credit (`total_fee` - `credit_used`) |
| txn_ts            | int     | Trade execution time (Unix timestamp in milliseconds)                 |

## Additional

For the use of coins and networks, please use coin or network symbol for any APIs request. Please be cautious of these cryptocurrency when you specified on the request.\
\
Please refer to the link below for the available coins and networks.\
https://www.bitkub.com/fee/cryptocurrency \
\
Note the following exceptions for the coin and network:

| Currency / Network  | Symbol  |
| ------------------- | ------- |
| Terra Classic(LUNC) | `LUNA`  |
| Terra 2.0 (LUNA)    | `LUNA2` |

# Error codes

The following is the JSON payload for the Response Error:

```javascript
{
   "code": "V1007-CW",
   "message": "Symbol not found",
   "data": {}
 }
```

## Status Codes

#### 200 (OK)

The request was processed as expected.

#### 400 (INVALID_REQUEST)

The request is not well-formed, violates schema, or has incorrect fields.

#### 401 (NOT_AUTHORIZED)

The API key doesn't match the signature or have the necessary permissions to perform the request.

#### 403 (FORBIDDEN)

The API key doesn't have the necessary permissions to complete the request.

#### 404 (NOT_FOUND)

The requested resource doesn't exist.

#### 5XX

Internal Server Error.

| Code     | Status | Message                |
| -------- | ------ | ---------------------- |
| S1000-CW | 500    | Internal service error |

### Business Error

| Code     | Status | Message                        |
| -------- | ------ | ------------------------------ |
| B1000-CW | 400    | User account is suspended      |
| B1001-CW | 400    | Network is disabled            |
| B1002-CW | 400    | CWS Wallet not found           |
| B1003-CW | 400    | Insufficient balance           |
| B1004-CW | 400    | User mismatch condition        |
| B1005-CW | 400    | Duplicate key                  |
| B1006-CW | 400    | Airdrop already transfer       |
| B1007-CW | 400    | Symbol required                |
| B1008-CW | 400    | Event Symbol mismatched        |
| B1009-CW | 400    | Pending withdrawal exists      |
| B1010-CW | 400    | User account is frozen         |
| B1011-CW | 400    | Withdrawal exceeds daily limit |
| B1012-CW | 400    | Address is not trusted         |
| B1013-CW | 400    | Withdrawal is frozen           |
| B1014-CW | 400    | Address is not whitelisted     |
| B1015-CW | 400    | Request is processing          |
| B1016-CW | 400    | Deposit is frozen              |

### Validation Error

| Code     | Status | Message                                   |
| -------- | ------ | ----------------------------------------- |
| V1000-CW | 404    | User not found                            |
| V1001-CW | 404    | Asset not found                           |
| V1002-CW | 404    | Event not found                           |
| V1003-CW | 400    | Invalid signature                         |
| V1004-CW | 401    | Signature has expired                     |
| V1005-CW | 404    | Transaction not found                     |
| V1006-CW | 400    | Invalid parameter                         |
| V1007-CW | 404    | Symbol not found                          |
| V1008-CW | 400    | Address not yet generated for this symbol |
| V1009-CW | 404    | Memo not found for this address           |
| V1010-CW | 404    | Address not found                         |
| V1011-CW | 400    | Address already exists                    |
| V1012-CW | 400    | Destination address not active            |
| V1015-CW | 404    | Coin not found                            |

### Authentication Error

| Code     | Status | Message             |
| -------- | ------ | ------------------- |
| A1000-CW | 401    | Unauthorized Access |
| A1001-CW | 403    | Permission denied   |

### Market Error Codes

Error codes for [Market V4 Endpoints](#secure-endpoints-v4).

| Code     | Status | Message                                       |
| -------- | ------ | --------------------------------------------- |
| V1000-MK | 400    | Invalid parameter                             |
| V1001-MK | 404    | Symbol not found                              |
| V1002-MK | 400    | next_id and next_ts must be provided together |
| V1003-MK | 400    | symbol is required when order_id is provided  |
| V1004-MK | 400    | start_time must be less than end_time         |
| V1006-MK | 400    | invalid side — must be buy or sell            |
| V1008-MK | 400    | invalid order type                            |
| V1010-MK | 400    | invalid status value                          |
| V1012-MK | 400    | limit must be at least 1                      |
| V1013-MK | 400    | limit exceeds maximum allowed value           |
| V1015-MK | 400    | start_time must be greater than zero          |
| V1017-MK | 400    | end_time must be greater than zero            |
| B1000-MK | 404    | Order not found                               |
| A1000-MK | 401    | Unauthorized                                  |
| A1001-MK | 403    | IP address not allowed                        |
| A1002-MK | 401    | API key is missing                            |
| A1003-MK | 401    | Invalid API key                               |
| A1004-MK | 401    | Signature is missing                          |
| A1005-MK | 401    | Invalid signature                             |
| A1006-MK | 401    | Invalid timestamp                             |
| A1007-MK | 401    | Timestamp expired                             |
| A1008-MK | 429    | Rate limit exceeded                           |
| A1009-MK | 401    | Invalid access token                          |
| A1010-MK | 401    | Access token expired                          |
| S1000-MK | 500    | Internal server error                         |

# Rate limits

If the request rate exceeds the limit in any endpoints, the request will be blocked for 30 seconds. When blocked, HTTP response is 429 Too Many Requests. The limits apply to individual user accessing the API. **_The rate limit is applied to each endpoint regardless the API version._**

| Endpoint          | Rate Limit        |
| ----------------- | ----------------- |
| /api/v4/crypto/\* | 250 req / 10 secs |
