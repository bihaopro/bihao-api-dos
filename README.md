## API Guide


- All requests are http request, and use **POST** method
- Http request content type is **application/json**
- All request will be verified by signature, with `accessKey` and `secretKey` provided for each account in bihao.pro
- Signature parameter `sign`, will be calculated by sort each key ,and then concat key and value, with concat secretKey last. Then do double MD5 digest. The algorithm is:
   ```js
   sign=MD5(MD5(key1+value1+key2+value2+...+secretKey))
   ```
- Bihao.pro production environment api prefix is https://www.bihao.pro/v1/
- All response data will be as follow format
    ```js
    {
        "data": {},
        "code": String,
        "msg": String
    }
    ```
    `data` is response data, `code` indicates result status, `msg` is result message.
- Response data is UTF-8 encoded

## API Interfaces


### 1. Ticker

URL: /ticker

Get latest market ticker

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|symbol| String|Yes | Trade Pair, for example BTC_USDT, currency is uppercase and concat by underline `_`|
|sign| String| Yes | Signature|


*Response Example*:
```json
{
    "data": {
        "date": 1525966177,
        "ticker": {
            "buy": "0.10000000",
            "high": "123.00000000",
            "last": "0.12345000",
            "low": "0.10000000",
            "sell": "141.00000000",
            "vol": 0
        }
    },
    "code": "10000",
    "msg": "Success
}
```

*Parameter Description*:
1. `date`: last time of the request
2. `buy`: first buy price
3. `sell`: first sell price
4. `high`: the highest price in last 24 hours
5. `low`: the lowest price in last 24 hours
6. `vol`: trade volumen in last 24 hours

### 2. Depth

URL: /depth

Get market orderbook depth

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|symbol| String|Yes | Trade Pair, for example BTC_USDT, currency is uppercase and concat by underline `_`|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "data": {
        "asks": [
           ["2.2000", "20.00"],
           ["2.2000", "200.00"],
           ["2.1000", "100.00"]
        ],
        "bids": [
           ["2.0000", "20.0"],
           ["1.9000", "10.0"],
           ["1.8000", "20.0"],
           ["1.5000", "10.0"]
        ],
        "current_price": "2.00"
    },
    "code": "10000",
    "msg": "Success"
}
```

*Parameter Description*:
1. `asks`: sell order[price, amount] array, sort by price desc
2. `bids`: buy order[price, amount] array, sort by price desc 
3. `current_price`: the latest market price


### 3. User Info

URL: /userinfo

Get user information, include user balance

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "data": {
        "balance": [
            {
                "free": 0,
                "freezed": 0,
                "currency": "BTC"
            },
            {
                "free": 0,
                "freezed": 0,
                "currency": "ETH"
            },
            {
                "free": 0,
                "freezed": 0,
                "currency": "USDT"
            }
        ],
        "nameauth": 0
    },
    "code": "10000",
    "msg": "Success"
}
```

*Parameter Description*:

1. `free`: user avilable balance
2. `freezed`: user locked balance
3. `currency`: user hold currency
4. `nameauth`: user verification information, 0: unverified, 1: verified, 2: verified failed

### 4. New Order

URL: /orders

User set new order in orderbook

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|symbol| String|Yes | Trade Pair, for example BTC_USDT, currency is uppercase and concat by underline `_`|
|num| String| Yes| Order amount|
|price| String| Yes| Order price|
|type| String|Yes| Order type, sell or buy|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "data": {
        "order_id": "123"
    },
    "code": "10000",
    "msg": "Success"
}
```

*Parameter Description*:

1. `order_id`: new created order id 

### 5. Cancel Order

URL: /cancel_order

User cancel order

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|order_id| String| Yes| Order to be cancelled|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "code": "10020",
    "msg": "Success"
}
```

### 6. Order Information

URL: /order_info

Get User pending order detail information

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|order_id| String| Yes| Order to be query|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "data": {
        "id": "1",
        "price": "10.00000000",
        "num": "2.00000000",
        "trade_num": "2.00000000",
        "fee": "0.10000000",
        "type": "buy",
        "add_time": "1524662244",
        "trade_time": "1524666228",
        "status": "2",
        "trade_pair": "BTC/CNY",
        "digit_num": "6"
    },
    "code": "10000",
    "msg": "Success"
}
```

*Parameter Description*:
1. `id`: order id
2. `price`: order price
3. `num`: order amount
4. `trade_num`: order traded amount
5. `fee`: order fee
6. `type`: order type, sell or buy
7. `add_time`: order created time
8. `trade_time`: order last trade time
9. `status`: order status, 0: pending, 1: partially filled, 2: full filled, -1: cancelled
10. `trade_pair`: order trade pair, for example BTC_USDT
11. `digit_num`: order currency digital number


### 7. Order History

URL: /order_history

Get user history order information, limit latest two days

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|status| Integer|Yes| Order status, 0: pending order, 1: partially filled order, 2: filled order, -1: to be confirm, -2: cancelled|
|since| Long| Yes| From which to query order|
|size| Integer| Yes| limit size of each page, 200 at most|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "data": {
        "orders": [
             {
                "id": "134",
                "price": "1.00000000",
                "num": "243.99980000",
                "trade_num": "243.00000000",
                "fee": "0.10000000",
                "type": "sell",
                "add_time": "1524677232",
                "trade_time": "1524678034",
                "status": "1",
                "trade_pair": "BTC/CNY",
                "digit_num": "6"
            },
            {
                "id": "135",
                "price": "1.00000000",
                "num": "243.99980000",
                "trade_num": "243.00000000",
                "fee": "0.10000000",
                "type": "sell",
                "add_time": "1524677232",
                "trade_time": "1524678034",
                "status": "1",
                "trade_pair": "BTC/CNY",
                "digit_num": "6"
            }        
        ]
    },
    "code": "10000",
    "msg": "Success"
}
```

*Parameter Description*:

1. `id`: order id
2. `currency_id`: currency id
3. `currency_trade_id`: partition currency id
4. `price`: order price
5. `num`: order amount
6. `trade_num`: traded order amount
7. `fee`: order free
8. `type`: order type, sell or buy
9. `add_time`: order created time
10. `trade_time`: last order trade time
11. `status`: order status, 0: pending order, 1: partially filled order, 2: filled order, -1: to be confirm, -2: cancelled
12. `trade_pair`: order trade symbol
13. `digit_num`: digit for trade currency


### 8. Trade History

URL: /trade_history

Get user trade history information

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|symbol| String|Yes | Trade Pair, for example BTC_USDT, currency is uppercase and concat by underline `_`|
|since| Long | Yes | From which unix time as start time, UNIX TIME|
|size| Interger | No | trade size of each page, default is 200|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "data": {
        "trades": [
        {
           "trade_no": "t123456",
           "order_id": "123",
           "matched_id": "124",
           "price": "1.00000000",
           "num": "1.00000000",
           "money": "1.00000000",
           "fee": "0.00100000",
           "type": "sell",
           "add_time": "1526884311",
           "trade_pair": "SWT/USDT"
       },
       {
           "trade_no": "t123457",
           "order_id": "125",
           "matched_id": "126",
           "price": "1.00000000",
           "num": "10.00000000",
           "money": "10.00000000",
           "fee": "0.01000000",
           "type": "buy",
           "add_time": "1526639996",
           "trade_pair": "BTC/USDT"
           }
       ]
    },
    "code": "10000",
    "msg": "Success"
}
```

*Parameter Description*:

1. `trade_no`: trade no, unique
2. `order_id`: maker order id, which one create the order to matched
3. `matched_id`: matched order id
4. `price`: order price
5. `num`: order amount
6. `fee`: order fee
7. `type`: order type, sell or buy
8. `add_time`: order created time
9. `trade_pair`: order trade pair, for example BTC_USDT

### 9. KLine data

URL: /kline

Get Kline/candlestick bars for a symbol

*Request parameters*:

|Param|Type|Required|Description|
|-------------|-------------|-----|----|
|api_key| String|Yes | User account's `accessKey`|
|symbol| String|Yes | Trade Pair, for example BTC_USDT, currency is uppercase and concat by underline `_`|
|type| String| Yes| KLine period, 1 min, 15 min, 30 min, 1 day, 2 day, 3 day, 1 week, 3 week, 1 month, 6 month|
|size|String| No| Size of each request |
|since|String| No| Request from which time, unix time|
|sign| String| Yes | Signature|

*Response Example*:

```json
{
    "data": [
       [
           "1525639499",
           "10",
           "2",
           "2.0000",
           "1.0000",
           "1.5000"
       ],
       [
           "1525639499",
           "20",
           "2",
           "2.0000",
           "1.0000",
           "1.5000"
       ]
    ],
    "code": "10000",
    "msg": "Success"
}
```

*Parameter Description*:

1. `1525639499`: trade timestamp
2. `10`: trade volume
3. `2.0000`: open price in last 24 hours
4. `2.0000`: high price in last 24 hours
5. `1.0000`: low price in last 24 hours
6. `1.5000`: closed price in last 24 hours


## Error Code

| Code | Error Detail Information|
|-------------|-------------|
|10000| Success|
|10001| Signature failure|
|10002| api_key is empty|
|10003| required parameters are emtpy, TODO INVALID|
|10004| invalid parameters, TODO INVALID|
|10005| symbol not exists|
|10006| order_id not exists |
|10007| order time not start|
|10008| symbol orderbook not exists|
|10009| order amount not valid|
|10010| order amount exceeds maxium|
|10011| order amount uder minium|
|10012| price under limit down price|
|10013| price exceeds up price|
|10014| account balance not enough|
|10015| new order fail, contact administrator, TODO INVALID|
|10016| price or amount invalid|
|10017| exceed maxium trade amount|
|10018| under minium trade amount|
|10019| order id not exits|
|10020| cancel order success, TODO INVALID|
|10021| cancel order failure, TODO INVALID|
|10401| The type parameter can not be empty|
|20000| Server error|
|20002| Trading stops Trading|
|20003| Not in the transaction time|
|20004| The currency is locked|
|20005| Price or quantity must be numeric|
|20006| The currency has been down|
|20007| The currency has been closed|
|20008| The amount of purchase must be greater than 0|
|20009| The quantity is less than the maximum quantity|
|20010| Quantity is greater than minimum quantity|
|20011| The transaction password is incorrect|
|20012| Insufficient balance|




