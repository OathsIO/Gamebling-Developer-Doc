# Gamebling Wallet Developer Doc


### This doc is used for Gamebling Wallet Api Development

# API Setup

 - Please use **HTTPS**, with port **8080**
 - API endpoint: **https://api.q0studio.com:8080**
 - API Return type is JSON, it includes **status** and **result**
 - API Status code: 

| Code             | meaning				|
| ---------------- | -------------------|
| 200              | Success          |
| 401              | Page Not Found  |
| 404              | Authentication Failed  |
| 500				   | API error |

 - API Structure: **/gamebling/\<version-number>/\<api-path>**
 - API version: **v1**
 - API example full url: **https://api.q0studio.com:8080/gamebling/v1/wallet/balance\_of\_player\1**

-------

# API Authentication

### 1. Add these value to header
	X-GBling-App-Key: \<app\_key\>
	X-GBling-App-Signature: \<app\_signature\>
	X-GBling-App-TimeStamp: \<current\_timestamp\>

### 2. Create api signature 
- prepare these values: api-key, timestamp, api-path, api-secrect
- combine a string by using: api-key:timestame:api-path
- then create HMAC by using SHA256 and api-secrect
- eg: 
	- api-key: **helloworld**
	- timestamp: **1551669165**
	- api-path: **/gamebling/v1/wallet/balance\_of\_player/1**
	- api-secret: **1234567890**
	- API Signature String before hash: **helloworld:1551669165:/gamebling/v1/wallet/balance\_of\_player/1**
	- HMAC SHA256 hashed by using api-sercret: **0e119e638702705c76046f2451296a996914bf7588e96b7290054e0a6b60a5f8**

### 3. Bypass for testing purpose
- For dev server, I added a short cut for bypassing the api authentication
- by adding a parameter: **bypass=bypass**
- eg: **https://api.q0studio.com:8080/gamebling/v1/wallet/balance\_of\_player\1?bypass=bypass**
- this only works on dev server, for testing purpose only


# Enum

### Currency

| id | title|
|----|------|
| 1  | USD  |
| 2  | ETH  |

### Wallet Status

| id | title | Notes      |
|----|------ |------------
| 1  | Normal  |  normal status |
| 2  | In-Game  | when player is in game, during this status, no money in or out|
| 3  | BAN | player wallet is frozen for all purpose |


### Transaction Type

| id	| title	| math_factor
|------|--------|-----------|
|1	    |deposit	| 1        | 
|2	    |withdraw |	-1      | 
|3	    |game_win |	1      | 
|4	    |game_lose | 	-1 | 
|5	    |reward_from_system | 	1 |
|6     |transfer_from_user	 | 1 |


### Game Reward Mode Stauts
| id | title|
|----|------|
| 1  | Active  |
| 2  | Deactive  |


# API Details

## Wallet API: /gamebling/v1/wallet/\<api-end-point>

### Register for Wallet

| Endpoint | /gamebling/v1/wallet/register |
|----------|--------------------------|
| HTTP Method | POST |
| Authenticate | Mandatory |
| Body Format |  x-www-form-urlencoded|

#### POST Body

| Paramter Name | Value | Note |
|---------------|-------|------|
|gamebling_user_id| INT | gamebling_user_id is unqiue, one player can only have one wallet|


#### Curl Example

```
curl -s -XPOST \
    -dgamebling_user_id=40 \
    https://api.q0studio.com:8080/gamebling/v1/wallet/register?bypass=bypass | python -mjson.tool
```

#### Return Value

```
{
    "status": 200,
    "results": {
        "id": 19,
        "gamebling_user_id": 123,
        "status": 1,
        "created_at": "2019-08-13T11:54:01.000Z",
        "updated_at": "2019-08-13T11:54:01.000Z",
        "current_balance": []
    }
}
```


### Get All Wallets

| Endpoint | /gamebling/v1/wallet/all |
|----------|--------------------------|
| HTTP Method | GET |
| Authenticate | Mandatory | 


#### Return Value

```
{
    "status": 200,
    "results": [
        {
            "id": 1,
            "gamebling_user_id": 1,
            "status": 1,
            "created_at": "2019-08-11T16:00:04.000Z",
            "updated_at": "2019-08-13T11:02:35.000Z"
        },
        {
            "id": 2,
            "gamebling_user_id": 2,
            "status": 1,
            "created_at": "2019-08-11T16:04:18.000Z",
            "updated_at": "2019-08-13T11:02:35.000Z"
        },
        ...
}
```



### Get Wallet by Player ID

| Endpoint | /gamebling/v1/wallet/balance\_of\_player/\<player-id> |
|----------|--------------------------|
| HTTP Method | GET |
| Authenticate | Mandatory | 
| Note | player id is the id from gamebling platform |


#### Return Value

```
{
    "status": 200,
    "results": {
        "id": 1,
        "gamebling_user_id": 1,
        "status": 1,
        "created_at": "2019-08-11T16:00:04.000Z",
        "updated_at": "2019-08-13T11:02:35.000Z",
        "current_balance": [
            {
                "currency": 1,
                "currencyName": "USD",
                "currentBalance": 550
            },
            {
                "currency": 2,
                "currencyName": "ETH",
                "currentBalance": 10000
            }
        ]
    }
}
```


### Get Wallet by Wallet ID

| Endpoint | /gamebling/v1/wallet/balance\_of\_wallet/\<wallet-id> |
|----------|--------------------------|
| HTTP Method | GET |
| Authenticate | Mandatory | 
| Note | Wallet id is the id provided by oath-wallet |



### Get Player Transaction History by Player ID

| Endpoint | /gamebling/v1/wallet/history\_of\_player/\<player-id> |
|----------|--------------------------|
| HTTP Method | GET |
| Authenticate | Mandatory | 
| Note | player id is the id from gamebling platform |


#### Return Value

```
{
    "status": 200,
    "results": [
        {
            "id": 1,
            "wallet_id": 1,
            "type": 1,
            "currency": 1,
            "amount": 500,
            "created_at": "2019-08-11T16:06:19.000Z",
            "updated_at": "2019-08-11T16:06:19.000Z",
            "title": "deposit",
            "currency_name": "USD"
        },
        {
            "id": 2,
            "wallet_id": 1,
            "type": 3,
            "currency": 1,
            "amount": 5,
            "created_at": "2019-08-11T16:10:02.000Z",
            "updated_at": "2019-08-11T16:10:02.000Z",
            "title": "game_win",
            "currency_name": "USD"
        },
        {
            "id": 4,
            "wallet_id": 1,
            "type": 5,
            "currency": 2,
            "amount": 10000,
            "created_at": "2019-08-11T19:31:22.000Z",
            "updated_at": "2019-08-11T19:31:22.000Z",
            "title": "reward_from_system",
            "currency_name": "ETH"
        }
    ]
}
```

### Get Player Transaction History by Wallet ID

| Endpoint | /gamebling/v1/wallet/history\_of\_wallet/\<wallet-id> |
|----------|--------------------------|
| HTTP Method | GET |
| Authenticate | Mandatory | 
| Note | Wallet id is the id provided by oath-wallet |



## Game Reward Mode API: /gamebling/v1/game\_reward/\<api-end-point>

### Get All Reward Mode
| Endpoint | /gamebling/v1/game\_reward/all|
|----------|--------------------------|
| HTTP Method | GET |
| Authenticate | Mandatory | 

### Return Value
``` 
{
    "status": 200,
    "results": [
        {
            "id": 2,
            "game_mode": 1,
            "title": "Winner-Takes-All",
            "reward_currency": 1,
            "reward_amount": 5,
            "status": 1,
            "created_at": "2019-08-11T16:00:50.000Z",
            "updated_at": "2019-08-11T20:09:38.000Z"
        },
        {
            "id": 3,
            "game_mode": 2,
            "title": "Reward-Per-Kill",
            "reward_currency": 1,
            "reward_amount": 10,
            "status": 1,
            "created_at": "2019-08-11T20:07:46.000Z",
            "updated_at": "2019-08-11T20:09:52.000Z"
        }
    ]
}
```

### Add Game Reward Mode

| Endpoint | /gamebling/v1/game_reward/add |
|----------|--------------------------|
| HTTP Method | POST |
| Authenticate | Mandatory |
| Body Format |  x-www-form-urlencoded|

#### POST Body

| Paramter Name | Value | Note |
|---------------|-------|------|
|game_mode| INT | this id should match the one in gamebling database|
|reward_currency| INT | check the currency enum id above|
|reward_amount| INT | |
|title | VARCHAR |  |


#### Curl Example

```
curl -s -XPOST \
    -dgame_mode=103 \
    -dreward_currency=1 \
    -dreward_amount=10 \
    -dtitle=HelloWorld \
    https://api.q0studio.com:8080/gamebling/v1/game_reward/add?bypass=bypass | python -mjson.tool
```

#### Return Value

```
{
    "status": 200,
    "results":  {
        "id": 37,
        "game_mode": 102,
        "reward_amount": 10,
        "reward_currency": 1,
        "status": 1,
        "title": "HelloWorld",
        "created_at": "2019-08-13T12:11:48.000Z",
        "updated_at": "2019-08-13T12:11:48.000Z"
    }
}
```


## Game API: /gamebling/v1/game/\<api-end-point>

### Game Start

| Endpoint | /gamebling/v1/game/start |
|----------|--------------------------|
| HTTP Method | POST |
| Authenticate | Mandatory |
| Body Format |  x-www-form-urlencoded|

#### POST Body

| Paramter Name | Value | Note |
|---------------|-------|------|
|players| array | this data should be an array for all the player ids, such as [1,2,3]|


#### Curl Example

```
curl -s -XPOST \
    -dplayers=[1,2] \
    https://api.q0studio.com:8080/gamebling/v1/game/start?bypass=bypass | python -mjson.tool
```

#### Return Value

```
{
    "status": 200,
    "results":  "Success"
}
```

### Game End

| Endpoint | /gamebling/v1/game/end |
|----------|--------------------------|
| HTTP Method | POST |
| Authenticate | Mandatory |
| Body Format |  x-www-form-urlencoded|

#### POST Body

| Paramter Name | Value | Note |
|---------------|-------|------|
|winners| array | this data should be an array for all the winner ids, such as [1,2,3]|
|losers| array | this data should be an array for all the loser ids, such as [1,2,3]|
|game_id| INT | this should matches the game id from gamebling database |
|game_mode_id| INT | this should matches the game_mode_id from gamebling database|



#### Curl Example

```
curl -s -XPOST \
	-dgame_id=1005 \
    -dwinners=[1,2] \
    -dlosers=[3,4] \
    -dgame_mode_id=1 \
    https://api.q0studio.com:8080/gamebling/v1/game/end?bypass=bypass | python -mjson.tool
```

#### Return Value

```
{
    "status": 200,
    "results":  "Success"
}
