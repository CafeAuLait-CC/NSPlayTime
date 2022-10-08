# NSPlayTime
Query For Game Play Histories Using MyNintendo App APIs

## Steps to acquire all histories

1. Get `client_id` and `session_token` from mitmproxy ([mitmproxy_instructions](https://github.com/frozenpandaman/splatnet2statink/wiki/mitmproxy-instructions))

```
Within mitmproxy, look for the following requests:

* https://accounts.nintendo.com/connect/1.0.0/authorize?client_id=xxx... 
	
	-- client_id can be found in the request parameters;

* https://accounts.nintendo.com/connect/1.0.0/api/session_token

	-- session_token can be found in JSON in response body:

	{
	    "code": "eyJhb....8bmYY",
	    "session_token": "eyJhbG....7HuMBF3WI"
	}
```

2. Get `access_token` and `id_token` with `client_id` and `session_token` (it seems that as of Oct. 7, 2022, both `access_token` and `id_token` can be used as the Authorization token in later steps)

```
Request:

curl POST "https://accounts.nintendo.com/connect/1.0.0/api/token" \
     -H 'Content-Type: application/json' \
     -d '{"client_id": "{CLIENT_ID}", "grant_type": "urn:ietf:params:oauth:grant-type:jwt-bearer-session-token", "session_token": "{SESSION_TOKEN}"}'


Response:

{
    "access_token": "eyJqa3U...7hRBIEYbw",
    "expires_in": 900,
    "id_token": "eyJhb...PfuvEg",
    "scope": [
        "openid",
        "user",
        "user.email",
        "user.links[].id",
        "user.mii"
    ],
    "token_type": "Bearer"
}
```

3. Query for `play_histories`

```
Request:

curl "https://mypage-api.entry.nintendo.co.jp/api/v1/users/me/play_histories" \
     -H 'Authorization: Bearer {ID_TOKEN}' \
     -H 'User-Agent: com.nintendo.znej/1.14.1 (iOS 15.6.1)'

Response:

{
	"playHistories":[...],
	"hiddenTitleList":[],
	"recentPlayHistories":[...],
	"lastUpdatedAt": "2022-10-06T09:51:08+09:00"
}
```

## Steps to acquire more detailed histories

```
Request:

curl "https://mypage-api.entry.nintendo.co.jp/api/v1/users/me/play_histories/game_titles/{titleId}?limit=16&offset=0" \
     -H 'Authorization: Bearer {ID_TOKEN}}' \
     -H 'User-Agent: com.nintendo.znej/1.14.1 (iOS 15.6.1)'

Response:

{
    "deviceType": "HAC",
    "firstPlayedAt": "2022-08-25T01:27:57+09:00",
    "imageUrl": "https://atum-img-lp1.cdn.nintendo.net/i/c/9011bed89b234849b2c3e4b224c0f078_512",
    "lastPlayedAt": "2022-08-29T12:34:00+09:00",
    "lastUpdatedAt": "2022-10-06T09:51:08+09:00",
    "playedDays": [
        {
            "minutesPlayed": 3,
            "playedDate": "2022-08-29T09:00:00+09:00"
        },
        {
            "minutesPlayed": 215,
            "playedDate": "2022-08-28T09:00:00+09:00"
        },
        {
            "minutesPlayed": 32,
            "playedDate": "2022-08-25T09:00:00+09:00"
        }
    ],
    "playedDaysOffset": 0,
    "titleId": "0100BA0018500000",
    "titleName": "スプラトゥーン3 前夜祭",
    "totalPlayedDays": 3,
    "totalPlayedMinutes": 250
}
```
