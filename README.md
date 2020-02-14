# http-localhost-4000
script/bootstrap script/server


# RESTful API for Bitkub (2020-01-21)

# Releases
* 2020-01-21 Depth API
* 2020-01-14 Trading credit balance API
* 2020-01-07 Place ask by fiat API
* 2020-01-06 Status API
* 2019-12-03 Limit API
* 2019-11-26 Websocket token API
* 2019-11-12 Fiat withdrawal and fiat deposit/withdrawal history
* 2019-10-28 Crypto withdrawal and crypto deposit/withdrawal history
* 2019-05-25 Ticker API now allows symbol query
* 2019-03-23 Added order info API
* 2018-12-16 Updated documentation
* 2018-08-09 V1 Release

# Table of contents
* [Base URL](#base-url)
* [Endpoint types](#endpoint-types)
* [Constructing the request](#constructing-the-request)
* [API documentation](#api-documentation)
* [Error codes](#error-codes)

# Base URL
* The base URL is: https://api.bitkub.com

# Endpoint types
### Non-secure endpoints
All non-secure endpoints do not need authentication and use the method GET.
* [GET /api/status](#get-apistatus)
* [GET /api/servertime](#get-apiservertime)
* [GET /api/market/symbols](#get-apimarketsymbols)
* [GET /api/market/ticker](#get-apimarketticker)
* [GET /api/market/trades](#get-apimarkettrades)
* [GET /api/market/bids](#get-apimarketbids)
* [GET /api/market/asks](#get-apimarketasks)
* [GET /api/market/books](#get-apimarketbooks)
* [GET /api/market/trading-view](#get-apimarkettradingview)
* [GET /api/market/depth](#get-apimarketdepth)

### Secure endpoints
All secure endpoints require [authentication](#constructing-the-request) and use the method POST.
* [POST /api/market/wallet](#post-apimarketwallet)
* [POST /api/market/balances](#post-apimarketbalances)
* [POST /api/market/place-bid](#post-apimarketplace-bid)
* [POST /api/market/place-ask](#post-apimarketplace-ask)
* [POST /api/market/place-ask-by-fiat](#post-apimarketplace-ask-by-fiat)
* [POST /api/market/cancel-order](#post-apimarketcancel-order)
* [POST /api/market/my-open-orders](#post-apimarketmy-open-orders)
* [POST /api/market/my-order-history](#post-apimarketmy-order-history)
* [POST /api/market/order-info](#post-apimarketorder-info)
* [POST /api/crypto/addresses](#post-apicryptoaddresses)
* [POST /api/crypto/withdraw](#post-apicryptowithdraw)
* [POST /api/crypto/deposit-history](#post-apicryptodeposit-history)
* [POST /api/crypto/withdraw-history](#post-apicryptowithdraw-history)
* [POST /api/fiat/accounts](#post-apifiataccounts)
* [POST /api/fiat/withdraw](#post-apifiatwithdraw)
* [POST /api/fiat/deposit-history](#post-apifiatdeposit-history)
* [POST /api/fiat/withdraw-history](#post-apifiatwithdraw-history)
* [POST /api/market/wstoken](#post-apimarketwstoken)
* [POST /api/user/limits](#post-apiuserlimits)
* [POST /api/user/trading-credits](#post-apiusertrading-credits)

# Constructing the request
### Request header
Authentication requires API KEY and API SECRET. Every request to the server must contain the following in the request header:
* Accept: application/json
* Content-type: application/json
* X-BTK-APIKEY: {YOUR API KEY}

### Payload
The payload is always JSON. **Always include timestamp in the payload; use `ts` as the key name for timestamp**.

### Signature
Generate the signature from the JSON payload using HMAC SHA-256. Use the API SECRET as the secret key for generating the HMAC variant of JSON payload.

#### Example payload:
```javascript
{"sym":"THB_BTC","amt":0.1,"rat":10000,"typ":"limit","ts":1529490685}
```

#### Example payload with signature:
```javascript
{"sym":"THB_BTC","amt":0.1,"rat":10000,"typ":"limit","ts":1529490685,"sig":"d0c66fabb816c46953270e4a442836ca449711e143c8658dd03103c90b2d0fb7"}
```

#### Example cURL:
```javascript
curl -X POST \
  https://api.bitkub.com/api/market/place-ask \
  -H 'accept: application/json' \
  -H 'content-type: application/json' \
  -H 'x-btk-apikey: 6da634977495306b2206eee7772532cb' \
  -d '{"sym":"THB_BTC","amt":0.1,"rat":10000,"typ":"limit","ts":1529490685,"sig":"d0c66fabb816c46953270e4a442836ca449711e143c8658dd03103c90b2d0fb7"}'
```

### Nonce
You can secure your request even further by including `nonce` in the request payload. Nonce is a numeric value which is incremental in each request (nonce value has to be higher in each new request). Use `non` as the key name for nonce.

#### Example payload (with nonce):
```javascript
{"sym":"THB_BTC","amt":0.1,"rat":10000,"typ":"limit","ts":1529490685,"non":1}
```

# API documentation
Refer to the following for description of each endpoint

### GET /api/status

#### Description:
Get endpoint status.

#### Query:
-

#### Response:
```javascript
[
  {
    "name": "Non-secure endpoints",
    "status": "ok",
    "message": ""
  },
  {
    "name": "Secure endpoints",
    "status": "ok",
    "message": ""
  }
]
```

### GET /api/servertime

#### Description:
Get server timestamp.

#### Query:
-

#### Response:
```javascript
1529999999
```

### GET /api/market/symbols

#### Description:
List all available symbols.

#### Query:
-

#### Response:
```javascript
{
  error: 0,
  result: [
    {
      id: 1,
      symbol: "THB_BTC",
      info: "Thai Baht to Bitcoin"
    },
    {
      id: 2,
      symbol: "THB_ETH",
      info: "Thai Baht to Ethereum"
    }
  ]
}
```

### GET /api/market/ticker

#### Description:
Get ticker information.

#### Query:
* `sym` **string** The symbol (optional)

#### Response:
```javascript
{
  THB_BTC: {
    id: 1,
    last: "216415.00",
    lowestAsk: "216678.00",
    highestBid: "215000.00",
    percentChange: "1.91",
    baseVolume: "71.02603946",
    quoteVolume: "15302897.99",
    isFrozen: "0",
    high24hr: "221396.00",
    low24hr: "206414.00"
  },
  THB_ETH: {
    id: 2,
    last: "11878.00",
    lowestAsk: "12077.00",
    highestBid: "11893.00",
    percentChange: "-0.49",
    baseVolume: "455.17839270",
    quoteVolume: "5505664.42",
    isFrozen: "0",
    high24hr: "12396.00",
    low24hr: "11645.00"
  }
}
```

### GET /api/market/trades

#### Description:
List recent trades.

#### Query:
* `sym`		**string** The symbol
* `lmt`		**int** No. of limit to query recent trades

#### Response:
```javascript
{
  "error": 0,
  "result": [
    [
      1529516287, // timestamp
      "10000.00", // rate
      "0.09975000", // amount
      "BUY" // side
    ]
  ]
}
```

### GET /api/market/bids

#### Description:
List open buy orders.

#### Query:
* `sym` **string** The symbol
* `lmt` **int** No. of limit to query open buy orders

#### Response:
```javascript
{
  "error": 0,
  "result": [
    [
      1, // order id
      1529453033, // timestamp
      "997.50", // volume
      "10000.00", // rate
      "0.09975000" // amount
    ]
  ]
}

```

### GET /api/market/asks

#### Description:
List open sell orders.

#### Query:
* `sym` **string** The symbol
* `lmt` **int** No. of limit to query open sell orders

#### Response:
```javascript
{
  "error": 0,
  "result": [
    [
      680, // order id
      1529491094, // timestamp
      "997.50", // volume
      "10000.00", // rate
      "0.09975000" // amount
    ]
  ]
}
```

### GET /api/market/books

#### Description:
List all open orders.

#### Query:
* `sym` **string** The symbol
* `lmt` **int** No. of limit to query open orders

#### Response:
```javascript
{
  "error": 0,
  "result": {
    "bids": [
      [
        1, // order id
        1529453033, // timestamp
        "997.50", // volume
        "10000.00", // rate
        "0.09975000" // amount
      ]
    ],
    "asks": [
      [
        680, // order id
        1529491094, // timestamp
        "997.50", // volume
        "10000.00", // rate
        "0.09975000" // amount
      ]
    ]
  }
}
```

### GET /api/market/tradingview
#### Description:
Get tradingview data for displaying tradingview graph.

#### Query:
* `sym`		**string**		The symbol
* `int`		**int**		Chart interval in seconds (e.g. 60, 900, 3600, 86400)
* `frm`		**int**		Timestamp of the starting time (e.g. 1574477162)
* `to`		**int**		Timestamp of the ending time (e.g. 1575773222)

#### Response:
```javascript
{
  "error": 0,
  "result": [
    o: [
      207500
    ],
    c: [
      205000
    ],
    h: [
      207500
    ],
    l: [
      205000
    ],
    s: "ok",
    v: [
      0.11205317
    ],
    t: [
      1530460800
    ]
  ]
}
```

### GET /api/market/depth

#### Description:
Get depth information.

#### Query:
* `sym` **string** The symbol
* `lmt` **int** Depth size

#### Response:
```javascript
{
  "asks": [
    [
      262600,
      0.61905798
    ],
    [
      263000,
      0.00210796
    ],
    [
      263200,
      0.89555545
    ],
    [
      263422.5,
      0.03796183
    ],
    [
      263499,
      0.4123439
    ]
  ],
  "bids": [
    [
      262510,
      0.38038703
    ],
    [
      262100.01,
      1.22519999
    ],
    [
      262100,
      0.00381533
    ],
    [
      262024.88,
      0.00352718
    ],
    [
      262001,
      0.09999961
    ]
  ]
}
```

### POST /api/market/wallet

#### Description:
Get user available balances (for both available and reserved balances please use [POST /api/market/balances](#post-apimarketbalances)).

#### Query:
-

#### Response:
```javascript
{
  "error": 0,
  "result": {
    "THB": 188379.27,
    "BTC": 8.90397323,
    "ETH": 10.1
  }
}
```

### POST /api/market/balances

#### Description:
Get balances info: this includes both available and reserved balances.

#### Query:
-

#### Response:
```javascript
{
  "error": 0,
  "result": {
    "THB":  {
      "available": 188379.27,
      "reserved": 0
    },
    "BTC": {
      "available": 8.90397323,
      "reserved": 0
    },
    "ETH": {
      "available": 10.1,
      "reserved": 0
    }
  }
}
```

### POST /api/market/place-bid

#### Description:
Create a buy order.

#### Query:
* `sym`		**string**		The symbol
* `amt`		**float**		Amount you want to spend with no trailing zero (e.g 1000.00 is invalid, 1000 is ok)
* `rat`		**float**		Rate you want for the order with no trailing zero (e.g 1000.00 is invalid, 1000 is ok)
* `typ`		**string**		Order type: limit or market

#### Response:
```javascript
{
  "error":0,
  "result":
  {
    "id": 1, // order id
    "typ": "limit", // order type
    "amt": 1000, // spending amount
    "rat": 15000, // rate
    "fee": 2.5, // fee
    "cre": 2.5, // fee credit used
    "rec": 0.06666666, // amount to receive
    "ts": 1533834547 // timestamp
  }
}
```

### POST /api/market/place-ask

#### Description:
Create a sell order.

#### Query:
* `sym`		**string**		The symbol
* `amt`		**float**		Amount you want to sell with no trailing zero (e.g 0.10000000 is invalid, 0.1 is ok)
* `rat`		**float**		Rate you want for the order with no trailing zero (e.g 1000.00 is invalid, 1000 is ok)
* `typ`		**string**		Order type: limit or market

#### Response:
```javascript
{
  "error": 0,
  "result": {
    "id": 1, // order id
    "typ": "limit", // order type
    "amt": 1.00000000, // selling amount
    "rat": 15000, // rate
    "fee": 37.5, // fee
    "cre": 37.5, // fee credit used
    "rec": 15000, // amount to receive
    "ts": 1533834844 // timestamp
  }
}
```

### POST /api/market/place-ask-by-fiat

#### Description:
Create a sell order by specifying the fiat amount you want to receive (selling amount of cryptocurrency is automatically calculated). If order type is `market`, currrent highest bid will be used as rate.

#### Query:
* `sym`		**string**		The symbol
* `amt`		**float**		Fiat amount you want to receive with no trailing zero (e.g 1000.00 is invalid, 1000 is ok)
* `rat`		**float**		Rate you want for the order with no trailing zero (e.g 1000.00 is invalid, 1000 is ok)
* `typ`		**string**		Order type: limit or market

#### Response:
```javascript
{
  "error": 0,
  "result": {
    "id": 1, // order id
    "typ": "limit", // order type
    "amt": 0.0000422, // selling amount resulted from calculation
    "rat": 236999, // rate
    "fee": 0.03, // fee
    "cre": 0.03, // fee credit used
    "rec": 10, // fiat amount to receive
    "ts": 1578390814 // timestamp
  }
}
```

### POST /api/market/cancel-order

### Description:
Cancel an open order.

### Query:
* `sym`		**string**		The symbol
* `id`		**int**		Order id you wish to cancel
* `sd`		**string**		Order side: buy or sell

### Response:
```javascript
{
  "error": 0
}
```

### POST /api/market/my-open-orders

### Description:
List all open orders of the given symbol.

### Query:
* `sym`		**string**		The symbol

### Response:
```javascript
{
  "error": 0,
  "result": [
    {
      "id": 2, // order id
      "side": "SELL", // order side
      "type": "limit", // order type
      "rate": 15000, // rate
      "fee": 35.01, // fee
      "credit": 35.01, // credit used
      "amount": 0.93333334, // amount
      "receive": 14000, // amount to receive
      "parent_id": 1, // parent order id
      "super_id": 1, // super parent order id
      "ts": 1533834844 // timestamp
    }
  ]
}
```

### POST /api/market/my-order-history

### Description:
List all orders that have already matched.

### Query:
* `sym` **string** The symbol
* `p` **int** Page (optional)
* `lmt` **int** Limit (optional)

### Response:
```javascript
{
  "error": 0,
  "result": [
    {
      "txn_id": "ETHBUY0000000197",
      "order_id": 240,
      "parent_order_id": 0,
      "super_order_id": 0,
      "taken_by_me": true,
      "side": "buy",
      "type": "limit",
      "rate": 13335.57,
      "fee": 0.34,
      "credit": 0.34,
      "amount": 0.00999987,
      "ts": 1531513395
    }
  ],
  "pagination": {
      "page": 2,
      "last": 3,
      "next": 3,
      "prev": 1
  }
}
```

### POST /api/market/order-info

### Description:
Get information regarding the specified order.

### Query:
* `sym`		**string**		The symbol
* `id`		**int**		Order id
* `sd`		**string**		Order side: buy or sell

### Response:
```javascript
{
    "error": 0,
    "result": {
        "id": 289, // order id
        "first": 289, // first order id
        "parent": 0, // parent order id
        "last": 316, // last order id
        "amount": 4000, // order amount
        "rate": 291000, // order rate
        "fee": 10, // order fee
        "credit": 10, // order fee credit used
        "filled": 3999.97, // filled amount
        "total": 4000, // total amount
        "status": "filled", // order status: filled, unfilled, or cancelled
        "history": [
            {
                "amount": 98.14848,
                "credit": 0.25,
                "fee": 0.25,
                "id": 289,
                "rate": 291000,
                "timestamp": 1525944169
            },
            {
                "amount": 87.3,
                "credit": 0.22,
                "fee": 0.22,
                "id": 290,
                "rate": 291000,
                "timestamp": 1525947677
            },
            {
                "amount": 11.64,
                "credit": 0.03,
                "fee": 0.03,
                "id": 301,
                "rate": 291000,
                "timestamp": 1525947712
            },
            {
                "amount": 116.4,
                "credit": 0.3,
                "fee": 0.3,
                "id": 302,
                "rate": 291000,
                "timestamp": 1525947746
            },
            {
                "amount": 10.185,
                "credit": 0.03,
                "fee": 0.03,
                "id": 303,
                "rate": 291000,
                "timestamp": 1525948237
            },
            {
                "amount": 10.185,
                "credit": 0.03,
                "fee": 0.03,
                "id": 315,
                "rate": 291000,
                "timestamp": 1525948253
            },
            {
                "amount": 3666.13731,
                "credit": 9.17,
                "fee": 9.17,
                "id": 316,
                "rate": 291000,
                "timestamp": 1525977397
            }
        ]
    }
}
```

### POST /api/crypto/addresses

### Description:
List all crypto addresses.

### Query (URL):
* `p` **int** Page (optional)
* `lmt` **int** Limit (optional)

### Response:
```javascript
{
   "error":0,
   "result":[
      {
         "currency":"BTC",
         "address":"3BtxdKw6XSbneNvmJTLVHS9XfNYM7VAe8k",
         "tag":0,
         "time":1570893867
      }
   ],
   "pagination":{
      "page":1,
      "last":1
   }
}
```

### POST /api/crypto/withdraw

### Description:
Make a withdrawal to a **trusted** address.

### Query:
* `cur`		**string**		Currency for withdrawal (e.g. BTC, ETH)
* `amt`		**float**		Amount you want to withdraw
* `adr`		**string**		Address to which you want to withdraw
* `mem`		**string**		(Optional) Memo or destination tag to which you want to withdraw

### Response:
```javascript
{
    "error": 0,
    "result": {
        "txn": "BTCWD0000012345", // local transaction id
        "adr": "4asyjKw6XScneNvhJTLVHS9XfNYM7VBf8x", // address
        "mem": "", // memo
        "cur": "BTC", // currency
        "amt": 0.1, // withdraw amount
        "fee": 0.0002, // withdraw fee
        "ts": 1569999999 // timestamp
    }
}
```

### POST /api/crypto/deposit-history

### Description:
List crypto deposit history.

### Query (URL):
* `p` **int** Page (optional)
* `lmt` **int** Limit (optional)

### Response:
```javascript
{
   "error":0,
   "result":[
      {
         "hash":"XRPWD0000100276",
         "currency":"XRP",
         "amount":5.75111474,
         "address":null,
         "confirmations":1,
         "status":"complete",
         "time":1570893867
      }
   ],
   "pagination":{
      "page":1,
      "last":1
   }
}
```

### POST /api/crypto/withdraw-history

### Description:
List crypto withdrawal history.

### Query (URL):
* `p` **int** Page (optional)
* `lmt` **int** Limit (optional)

### Response:
```javascript
{
   "error":0,
   "result":[
      {
         "txn_id":"XRPWD0000100276",
         "hash":"send_internal",
         "currency":"XRP",
         "amount":"5.75111474",
         "fee":0.01,
         "address":"rpXTzCuXtjiPDFysxq8uNmtZBe9Xo97JbW",
         "status":"complete",
         "time":1570893493
      }
   ],
   "pagination":{
      "page":1,
      "last":1
   }
}
```

### POST /api/fiat/accounts

### Description:
List all approved bank accounts.

### Query (URL):
* `p` **int** Page (optional)
* `lmt` **int** Limit (optional)

### Response:
```javascript
{
   "error":0,
   "result":[
      {
         "id":"7262109099",
         "bank":"Kasikorn Bank",
         "name":"Somsak",
         "time":1570893867
      }
   ],
   "pagination":{
      "page":1,
      "last":1
   }
}
```

### POST /api/fiat/withdraw

### Description:
Make a withdrawal to an **approved** bank account.

### Query:
* `id`		**string**	Bank account id
* `amt`		**float**		Amount you want to withdraw

### Response:
```javascript
{
    "error": 0,
    "result": {
        "txn": "THBWD0000012345", // local transaction id
        "acc": "7262109099", // bank account id
        "cur": "THB", // currency
        "amt": 21, // withdraw amount
        "fee": 20, // withdraw fee
        "rec": 1, // amount to receive
        "ts": 1569999999 // timestamp
    }
}
```

### POST /api/fiat/deposit-history

### Description:
List fiat deposit history.

### Query (URL):
* `p` **int** Page (optional)
* `lmt` **int** Limit (optional)

### Response:
```javascript
{
   "error":0,
   "result":[
      {
         "txn_id":"THBDP0000012345",
         "currency":"THB",
         "amount":5000.55,
         "status":"complete",
         "time":1570893867
      }
   ],
   "pagination":{
      "page":1,
      "last":1
   }
}
```

### POST /api/fiat/withdraw-history

### Description:
List fiat withdrawal history.

### Query (URL):
* `p` **int** Page (optional)
* `lmt` **int** Limit (optional)

### Response:
```javascript
{
   "error":0,
   "result":[
      {
         "txn_id":"THBWD0000012345",
         "currency":"THB",
         "amount":"21",
         "fee":20,
         "status":"complete",
         "time":1570893493
      }
   ],
   "pagination":{
      "page":1,
      "last":1
   }
}
```

### POST /api/market/wstoken

### Description:
Get the token for websocket authentication.

### Query (URL):
-

### Response:
```javascript
{
   "error":0,
   "result":"BYGoc1Pt81s1ouhZD095UtMdwWU2ZU0tVPYZSZ22WPU8GcMC9jOldV3e9aBJoDWLsfqxWH8jkZYI9ID4EZeeueEFNDL1OznPcS0z1Da19sSF0MlBbqpgT3TQpyp2oea9"
}
```

### POST /api/user/limits

### Description:
Check deposit/withdraw limitations and usage.

### Query (URL):
-

### Response:
```javascript
{
   "error":0,
   "result":{ 
       "limits":{ // limitations by kyc level
          "crypto":{ 
             "deposit":0.88971929, // BTC value equivalent
             "withdraw":0.88971929 // BTC value equivalent
          },
          "fiat":{ 
             "deposit":200000, // THB value equivalent
             "withdraw":200000 // THB value equivalent
          }
       },
       "usage":{ // today's usage
          "crypto":{ 
             "deposit":0, // BTC value equivalent
             "withdraw":0, // BTC value equivalent
             "deposit_percentage":0,
             "withdraw_percentage":0,
             "deposit_thb_equivalent":0, // THB value equivalent
             "withdraw_thb_equivalent":0 // THB value equivalent
          },
          "fiat":{ 
             "deposit":0, // THB value equivalent
             "withdraw":0, // THB value equivalent
             "deposit_percentage":0,
             "withdraw_percentage":0
          }
       },
       "rate":224790 // current THB rate used to calculate
    }
}
```


### POST /api/user/trading-credits

### Description:
Check trading credit balance.

### Query (URL):
-

### Response:
```javascript
{
   "error":0,
   "result":1000
}
```

# Error codes
Refer to the following descriptions:

Code | Description
------------ | ------------
0 | No error
1 | Invalid JSON payload
2 | Missing X-BTK-APIKEY
3 | Invalid API key
4 | API pending for activation
5 | IP not allowed
6 | Missing / invalid signature
7 | Missing timestamp
8 | Invalid timestamp
9 | Invalid user
10 | Invalid parameter
11 | Invalid symbol
12 | Invalid amount
13 | Invalid rate
14 | Improper rate
15 | Amount too low
16 | Failed to get balance
17 | Wallet is empty
18 | Insufficient balance
19 | Failed to insert order into db
20 | Failed to deduct balance
21 | Invalid order for cancellation
22 | Invalid side
23 | Failed to update order status
24 | Invalid order for lookup (or cancelled)
30 | Limit exceeds
40 | Pending withdrawal exists
41 | Invalid currency for withdrawal
42 | Address is not in whitelist
43 | Failed to deduct crypto
44 | Failed to create withdrawal record
45 | Nonce has to be numeric
46 | Invalid nonce
47 | Withdrawal limit exceeds
48 | Invalid bank account
49 | Bank limit exceeds
50 | Pending withdrawal exists

# Websocket API for Bitkub (2018-11-15)

# Table of contents
* [Websocket endpoint](#websocket-endpoint)
* [Stream name](#stream-name)
* [Symbols](#symbols)
* [Websocket API documentation](#web-socket-api-documentation)
* [Stream Demo](#stream-demo)
* [Live Order Book](#live-order-book)

# Websocket endpoint
* The websocket endpoint is: **wss://api.bitkub.com/websocket-api/[\<streamName\>](#stream-name)**

# Stream name
Stream name requires 3 parts: **service name**, **service type**, and **symbol**, delimited by **dot (.)**, and is **case-insensitive**.

#### Stream name format:
```javascript
<serviceName>.<serviceType>.<symbol>
```

#### Stream name example:
```javascript
market.trade.thb_btc
```
Above example stream name provides real-time data from the **market** service, type **trade**, of symbol **THB_BTC**.



### Multiple streams subscription:
You can combine multiple streams by using **comma (,)** as the delimeter.

#### Multiple stream names format:
```javascript
<streamName>,<streamName>,<streamName>
```

#### Multiple stream names example:
```javascript
market.trade.thb_btc,market.ticker.thb_btc,market.trade.thb_eth,market.ticker.thb_eth
```
Above subscription provides real-time data from trade and ticker streams of symbols THB_BTC and THB_ETH.



# Symbols
Refer to [RESTful API](https://github.com/bitkub/bitkub-official-api-docs/blob/master/restful-api.md#get-apimarketsymbols) for all available symbols.



# Websocket API documentation
Refer to the following for description of each stream

### Trade stream
#### Name:
market.trade.\<symbol\>

#### Description:
The trade stream provides real-time data on matched orders. Each trade contains buy order id and sell order id. Order id is unique by the order side (buy/sell) and symbol.

#### Response:
```javascript
{
  "stream": "market.trade.thb_eth", // stream name
  "sym":"THB_ETH", // symbol
  "txn": "ETHSELL0000074282", // transaction id
  "rat": "5977.00", // rate matched
  "amt": 1.556539, // amount matched
  "bid": 2048451, // buy order id
  "sid": 2924729, // sell order id
  "ts": 1542268567 // trade timestamp
}
```

### Ticker stream
#### Name:
market.ticker.\<symbol\>

#### Description:
The ticker stream provides real-time data on ticker of the specified symbol. Ticker for each symbol is re-calculated on trade order creation, cancellation, and fulfillment.

#### Response:
```javascript
{
  "stream": "market.ticker.thb_bch", // stream name
  "id": 6, // symbol id
  "last": "15425.00",
  "lowestAsk": "17799.00",
  "highestBid": "15425.00",
  "percentChange": "-10.15",
  "baseVolume": "55.76940380",
  "quoteVolume": "905856.89",
  "isFrozen": "0",
  "high24hr": "17980.00",
  "low24hr": "15409.00"
 }
```

# Stream Demo
The demo page is available [here](https://api.bitkub.com/websocket-api?streams=) for testing streams subscription.

# Live Order Book
#### Description:
Use symbol id to get real-time data of order book: **wss://api.bitkub.com/websocket-api/[\<symbol-id\>](#symbols)**.

#### Authentication:
Authentication is required in order to access certain data. Send the **[websocket token](https://github.com/bitkub/bitkub-official-api-docs/blob/master/restful-api.md#post-apimarketwstoken)** to the server via the established socket connection. The message is in **JSON** format.

#### Sample authentication message (JSON string):
```javascript
{
    "auth": "BYGoc1Pt81s1ouhZD095UtMdwWU2ZU0tVPYZSZ22WPU8GcMC9jOldV3e9aBJoDWLsfqxWH8jkZYI9ID4EZeeueEFNDL1OznPcS0z1Da19sSF0MlBbqpgT3TQpyp2oea9"
}
```

#### Message data:
```javascript
{
    "data": (data),
    "event": (event type)
}
```
There are 3 event types: **bidschanged**, **askschanged**, and **tradeschanged**
* **bidschanged** occurs when any buy order has changed (opened/closed/cancelled). Data is array of buy orders after the change (max. 20 orders).
* **askschanged** occurs when any sell order has changed (opened/closed/cancelled). Data is array of sell orders after the change (max. 20 orders).
* **tradeschanged** occurs when buy and sell orders have been matched. Data is array containing 3 arrays: array of latest trades, array of buy orders, and array of sell orders (each max. 20 orders). You get this event as the initial data upon successful subscription.

#### Example response (bidschanged or askschanged):
```javascript
{
   "data":[
      [
         121.82, // vol
         112510.1, // rate
         0.00108283, // amount
         0, // reserved, always 0
         false, // is new order
         false // user is owner (available when authenticated)
      ]
   ],
   "event":"bidschanged"
}
```

#### Example response (tradeschanged):
```javascript
{
   "data":[
      [
         [
            1550320593, // timestamp
            113587, // rate
            0.12817027, // amount
            "BUY", // side
            0, // reserved, always 0
            0, // reserved, always 0
            true, // is new order
            false, // user is buyer (available when authenticated)
            false // user is seller (available when authenticated)
         ]
      ],
      [
         [
            121.82, // vol
            112510.1, // bid rate
            0.00108283, // bid amount
            0, // reserved, always 0
            false, // is new order
            false // user is owner (available when authenticated)
         ]
      ],
      [
         [
            51247.13, // vol
            113699, // ask rate
            0.45072632, // ask amount
            0, // reserved, always 0
            false, // is new order
            false // user is owner (available when authenticated)
         ]
      ]
   ],
   "event":"tradeschanged"
}
```

