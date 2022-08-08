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

# V1: Districts

## Get Districts

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
|-----------------|---------|-----------------------------------------------------------------------------------------------------------------------------------|
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


# V1: Game Info

## Get Game Info

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

| Parameter                       | type    | Description                                                                                                                       |
|---------------------------------|---------|-----------------------------------------------------------------------------------------------------------------------------------|
| num_toons                       | integer | The number of toons online; player count is limited to the number of toons in-game and does not include those in the main menu    |
| production_closed               | boolean | Whether or not logging into the game is currently possible for a regular user                                                     |
| production_closed_reason        | integer | An english reason for the game being closed                                                                                       |
| version                         | boolean | Current version of the game; pulled from the latest version in [CorporateClash/Changelogs](https://github.com/CorporateClash/Changelogs/blob/master/releases/all.json) |



