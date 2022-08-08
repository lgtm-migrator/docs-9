---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ

- shell
- python

toc_footers:

- <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:

- errors
- aup

search: true

code_clipboard: true

meta:

- name: description
  content: Documentation for the Corporate Clash APIs

---

# Introduction

Welcome to the Corporate Clash API Documentation! This will include documentation detailing launcher APIs and other
public APIs for your usage.

Please read the Acceptable Usage Policy at the bottom of this document before using it. We want to ensure our users'
data is safe and our services are protected from abuse, which will keep our costs down and keep Corporate Clash running!

Note: manifest documentation to allow third-party launchers to download our game files is planned.

# User-agent requirement

> Please set an appropriate user-agent header:

```
user-agent: MyCoolSite/1.0.0
```

> If you'd like to include extra info, feel free, but keep it under 100 characters:

```
user-agent: example.com/1.0.0 python-requests/2.27.1
```

Please include an identifiable user-agent with requests. We don't gate the user-agent besides preventing fake scrapers (
eg. pretending to be GoogleBot). If we can't get in touch with you and your client is abusing our API we might block it
to ensure functionality for other API consumers.

# V1: API Version 1

These APIs don't require authentication, as they're open access.

## V1: Districts

```python
import requests

r = requests.get('https://corporateclash.net/api/v1/districts.js')
out = r.json()
```

```shell
curl "https://corporateclash.net/api/v1/districts.js" \
  -H "User-Agent: my-shell/1"
```

> JSON:

```json
[
  {
    "name": "Cupcake Cove",
    "online": true,
    "population": 39,
    "invasion_online": false,
    "last_update": 1651361858,
    "cogs_attacking": "None",
    "count_defeated": 0,
    "count_total": 0,
    "remaining_time": 0
  },
  {
    "name": "Kazoo Kanyon",
    "online": true,
    "population": 37,
    "invasion_online": true,
    "last_update": 1651361858,
    "cogs_attacking": "Middleman",
    "count_defeated": 180,
    "count_total": 4913,
    "remaining_time": 2082
  }
]
```

This endpoint retrieves all districts connected to the server. 

### HTTP Request

`GET https://corporateclash.net/api/v1/districts.js`

### Return Object

Returns **an array** of district objects, defined as:

| Parameter       | type    | Description                                                                                                                       |
| --------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------- |
| name            | string  | The name of the district                                                                                                          |
| online          | boolean | Whether or not the district is online / available. This might be 'false' if the district is scheduled for/undergoing maintenance. |
| population      | integer | The amount of toons in the district                                                                                               |
| invasion_online | boolean | Whether or not an invasion is active.                                                                                             |
| last_update     | integer | The last time the district was updated. This generally requires a value change to update, eg. toons leaving/joining the district. |
| cogs_attacking  | string  | The name of the cog invading the district.                                                                                        |
| count_defeated  | integer | The number of cogs defeated for the current invasion.                                                                             |
| count_total     | integer | How many cogs need to be defeated to end the invasion early.                                                                      |
| remaining_time  | integer | The amount of seconds before the invasion ends.                                                                                   |

<aside class="success">
Please only perform this API call at a maximum of once every 5 seconds. If using client-sided via CORS, please don't try to cache bust, as the Cloudflare cache is set to 10 seconds regardless.
</aside>

## V1 Game Info

```python
import requests

r = requests.get('https://corporateclash.net/api/v1/game_info.js')
out = r.json()
```

```shell
curl "https://corporateclash.net/api/v1/game_info.js" \
  -H "User-Agent: my-shell/1"
```

> Returned JSON:

```json
{
  "num_toons": 383,
  "production_closed": false,
  "production_closed_reason": "We're performing maintenance",
  "version": "v1.2.7.9"
}
```

This endpoint retrieves info about the live game

### HTTP Request

`GET https://corporateclash.net/api/v1/game_info.js`

### Return Object

| Parameter                | type    | Description                                                                                                                                                            |
| ------------------------ | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| num_toons                | integer | The number of toons online; player count is limited to the number of toons in-game and does not include those in the main menu                                         |
| production_closed        | boolean | Whether or not logging into the game is currently possible for a regular user                                                                                          |
| production_closed_reason | integer | An english reason for the game being closed                                                                                                                            |
| version                  | boolean | Current version of the game; pulled from the latest version in [CorporateClash/Changelogs](https://github.com/CorporateClash/Changelogs/blob/master/releases/all.json) |

# Launcher V1 API

These APIs are the primary method for logging users into the game.

For these APIs, we only allow client implementations that do not store the password, and store login tokens locally on users' computer. If malicious launchers are created that consume these APIs, we might shut off third party access and implement technical measures to prevent third-party usage.

## POST Register

```python
import requests

username = 'judge'
password = ''
friendly = 'my 2022 laptop'

r = requests.post("https://corporateclash.net/api/launcher/v1/register", json={
    'username': username,
    'password': password,
    'friendly': friendlyname
})

```

```shell
# curl 7.82.0 introduced the --json option which sends `application/json` for the headers `content-type` and `accept`
curl "https://corporateclash.net/api/launcher/v1/register" \
  --json '{"username": "judge", "password": "", "friendly": "my 2022 laptop"}'
```

> JSON:

```json
{
  "status": true,
  "reason": 0,
  "toonstep": false,
  "message": "success!",
  "token": "abcdefg1234567890abcdefg1234567890abcdefg1234567890",
  "id": 1
}
```

This endpoint registers a launcher with a user's account for persistent login access.

### HTTP Request

`POST https://corporateclash.net/api/launcher/v1/register`

JSON post body:

| Parameter | type   | Description                                                                                                                             |
| --------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| username  | string | The username for the account                                                                                                            |
| password  | string | The password for the account                                                                                                            |
| friendly  | string | A name the user can refer to this launcher/computer as. Will be referenced in the launcher confirmation email and website account page. |


### Return Object

Returns **an array** of district objects, defined as:

| Parameter | type     | Description                                                                                                                                                |
| --------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| status    | boolean  | Whether or not token issuance was a success                                                                                                                |
| reason    | string   | An integer indicating the failure reason/where in our backend this failed. Feel free to show it to the user, however, what these codes mean is not public. |
| toonstep  | boolean  | Whether or not this operation failed due to toonstep.                                                                                                      |
| message   | string   | Text indicicating why this operation failed or succeeded                                                                                                   |
| token     | string   | The login token to be used in future requests.                                                                                                             |
| id        | integer? | A unique ID for this launcher. It is recommended to display this to the user in case they need to audit launcher access on their account.                  |

The token and username should be stored somewhere on the user's computer. If possible, try utilizing a built-in secret store or data protection API for the launcher platform you're targeting, eg [KeyChain on MacOS](https://developer.apple.com/documentation/security/keychain_services) or the [Windows Credential Store](https://docs.microsoft.com/en-us/windows/win32/secauthn/credentials-management). Otherwise, storing it in a configuration folder for your application is fine.


## Launcher errors when token is revoked

> JSON:

```json
{
  "error": "Unauthorized",
  "bad_token": true
}
```

Your API requests will fail with a `401` status and this response if it's ever revoked via the website (eg. on the account page or via a password reset).

### Return Object


| Parameter | type    | Description                         |
| --------- | ------- | ----------------------------------- |
| error     | boolean | Error message                       |
| bad_token | boolean | Whether or not the token is invalid |



## POST Login

```python
import requests

token = 'abcdefg1234567890abcdefg1234567890abcdefg1234567890'

r = requests.post("https://corporateclash.net/api/launcher/v1/login", headers={
    'Authorization': f'Bearer {token}'
})

```

```shell
curl -X POST "https://corporateclash.net/api/launcher/v1/login" \
  -H 'Authorization: Bearer abcdefg1234567890abcdefg1234567890abcdefg1234567890'
```

> JSON:

```json
{
  "status": true,
  "success": true,
  "toonstep": false,
  "message": "Welcome back to Toontown, judge!",
  "token": "0987654321gfedcba0987654321gfedcba0987654321gfedcba",
}
```

This endpoint allows logging into the game.

### HTTP Request

`POST https://corporateclash.net/api/launcher/v1/login`

Headers to include in your post request:

| Header        | Value                             |
| ------------- | --------------------------------- |
| Authorization | The user's token in bearer format |

### Return Object


| Parameter | type    | Description                                                                       |
| --------- | ------- | --------------------------------------------------------------------------------- |
| status    | boolean | Whether or not logging in was successful                                          |
| success   | boolean | Same as status                                                                    |
| toonstep  | boolean | Whether or not this operation failed due to toonstep                              |
| message   | string  | Text indicicating why this operation failed or succeeded                          |
| token     | string  | a temporary login token that allows access into the game under the user's account |

After logging in, you should start the game process with the following environment variables:


| Environment Variable | Value                 |
| -------------------- | --------------------- |
| TT_GAMESERVER        | gs.corporateclash.net |
| TT_PLAYCOOKIE        | the `token` above     |



## GET Metadata

```python
import requests

token = 'abcdefg1234567890abcdefg1234567890abcdefg1234567890'

r = requests.get("https://corporateclash.net/api/launcher/v1/metadata", headers={
    'Authorization': f'Bearer {token}'
})

```

```shell
curl "https://corporateclash.net/api/launcher/v1/metadata" \
  -H 'Authorization: Bearer abcdefg1234567890abcdefg1234567890abcdefg1234567890'
```

> JSON:

```json
{
  "acc_launcher_friendly": "my 2022 laptop",
  "acc_launcher_authorization_id": 1,
  "production_servers": [],
  "production_servers_v2": [
    {
      "id": 13,
      "name": "AWS",
      "realm": "production",
      "base_url": "https://aws1.corporateclash.net/productionv2/",
      "v2": true
    }
  ],
  "production_windows_manifest_v2_url": "https://corporateclash.net/api/v1/launcher/manifest/v2/windows_production.js",
  "production_macos_manifest_v2_url": "https://corporateclash.net/api/v1/launcher/manifest/v2/macos_production.js",
  "production_resources_manifest_v2_url": "https://corporateclash.net/api/v1/launcher/manifest/v2/resources_production.js",
  "access_qa": false,
  "qa_windows_manifest_v2_url": "",
  "qa_macos_manifest_v2_url": "",
  "qa_resources_manifest_v2_url": "",
  "qa_manifest_url": "",
  "qa_salt": "",
  "qa_hostname": "",
  "qa_servers": [],
  "qa_servers_v2": [],
  "bad_token": false
}
```

This endpoint provides metadata. 

### HTTP Request

`POST https://corporateclash.net/api/launcher/v1/metadata`

Headers to include in your post request:

| Header        | Value                             |
| ------------- | --------------------------------- |
| Authorization | The user's token in bearer format |

### Return Object

Provides information for downloading files from the game. Further documentation of our entire asset distribution scheme will become available at a later date. 

| Parameter                            | type    | Description                                                                                                       |
| ------------------------------------ | ------- | ----------------------------------------------------------------------------------------------------------------- |
| acc_launcher_friendly                | string  | The launcher's `friendly` value, same as register                                                                 |
| acc_launcher_authorization_id        | integer | The launcher's ID, same as register                                                                               |
| production_servers                   | array   | Array of servers.                                                                                                 |
| production_servers_v2                | array   | Array of servers.                                                                                                 |
| production_windows_manifest_v2_url   | string  | Manifest URL                                                                                                      |
| production_macos_manifest_v2_url     | string  | Manifest URL                                                                                                      |
| production_resources_manifest_v2_url | string  | Manifest URL                                                                                                      |
| (qa fields)                          | (misc)  | Misc QA fields.                                                                                                   |
| bad_token                            | boolean | Whether or not the token is bad. An authentication check is performed on this API so this should always be false. |


## POST Revoke Current Token

```python
import requests

token = 'abcdefg1234567890abcdefg1234567890abcdefg1234567890'

r = requests.post("https://corporateclash.net/api/launcher/v1/revoke_self", headers={
    'Authorization': f'Bearer {token}'
})

```

```shell
curl -X POST "https://corporateclash.net/api/launcher/v1/revoke_self" \
  -H 'Authorization: Bearer abcdefg1234567890abcdefg1234567890abcdefg1234567890'
```

> JSON:

```json
{
  "status": true,
  "bad_token": false
}
```

This endpoint revokes the launcher authorization associated with the token sent in the Authorization header.

### HTTP Request

`POST https://corporateclash.net/api/launcher/v1/revoke_self`

Headers to include in your post request:

| Header        | Value                             |
| ------------- | --------------------------------- |
| Authorization | The user's token in bearer format |

### Return Object


| Parameter | type    | Description                              |
| --------- | ------- | ---------------------------------------- |
| status    | boolean | Whether or not recovation was successful |
| bad_token | boolean | Whether or not the token was bad already |

After running this, the token will no longer work for any APIs.
