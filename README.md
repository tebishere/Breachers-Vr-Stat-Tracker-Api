# Breachers API

A free, public JSON API for Breachers player stats, game data, and map and rank images. It's the same API that powers the Breachers Lookup website, so you can use it in your own bots, apps, and sites.

> Unofficial fan project. Not affiliated with the game's developers. Player data comes from BreachersTracker.

## Base URL

```
https://tickit-o.onrender.com
```

No API key is needed. Every endpoint is a `GET` request and returns JSON, except the `/image` endpoints, which redirect to an image. CORS is open, so you can call it from browser apps.

## Quick start

```bash
# Find a player
curl "https://tickit-o.onrender.com/api/search?name=JOPY"

# Get their stats (use the player ID from the search results)
curl "https://tickit-o.onrender.com/api/player/PLAYER_ID/competitive"

# Get a map image
curl -L "https://tickit-o.onrender.com/api/maps/arctic/image" -o arctic.png
```

## Endpoints

| Endpoint | Description |
| -------- | ----------- |
| `GET /health` | Server status and version |
| `GET /api/catalog` | Maps, weapons, and gadgets in one response |
| `GET /api/maps` | All maps, with an image link for each |
| `GET /api/maps/:name` | One map and its image link |
| `GET /api/maps/:name/image` | Redirects to the map image |
| `GET /api/weapons` | All weapons |
| `GET /api/gadgets` | All gadgets |
| `GET /api/ranks` | All ranks, with an icon link for each |
| `GET /api/ranks/:rank` | One rank and its icon link |
| `GET /api/ranks/:rank/image` | Redirects to the rank icon |
| `GET /api/search?name=` | Search players by username |
| `GET /api/player/:id/casual` | A player's casual stats |
| `GET /api/player/:id/competitive` | A player's competitive stats |

### Health

```
GET /health
```

```json
{ "ok": true, "service": "breachers-api-proxy", "version": "1.4.0" }
```

### Game data

```
GET /api/catalog
GET /api/maps
GET /api/weapons
GET /api/gadgets
```

`/api/weapons` and `/api/gadgets` return a list and a count:

```json
{ "success": true, "weapons": ["Auril", "Cadillo", "..."], "count": 14 }
```

### Maps and map images

```
GET /api/maps
```

Returns every map name, plus an `images` object with a direct image link for each:

```json
{
  "success": true,
  "maps": ["Arctic", "Dam", "Factory", "..."],
  "count": 12,
  "images": {
    "Arctic": "https://.../arctic.png"
  }
}
```

```
GET /api/maps/:name
```

```json
{
  "success": true,
  "name": "Arctic",
  "image": "https://.../arctic.png",
  "imageEndpoint": "/api/maps/Arctic/image"
}
```

```
GET /api/maps/:name/image
```

Redirects to the image file, so you can use it directly in HTML:

```html
<img src="https://tickit-o.onrender.com/api/maps/arctic/image" alt="Arctic">
```

Map names are flexible. `arctic`, `Winter Outpost`, `winter-outpost`, and `WinterOutpost` all work.

**Available maps:** Arctic, Dam, Factory, Headquarters, Hideout, Hideout_Infected, Killhouse, Mall, Outpost, Ship, Skyscraper, WinterOutpost

### Ranks and rank icons

```
GET /api/ranks
GET /api/ranks/:rank
GET /api/ranks/:rank/image
```

```json
{
  "success": true,
  "name": "gold1",
  "icon": "https://.../GoldI.png",
  "imageEndpoint": "/api/ranks/gold1/image"
}
```

Use the image endpoint directly:

```html
<img src="https://tickit-o.onrender.com/api/ranks/gold1/image" alt="Gold I">
```

Rank names are flexible. `gold1`, `Gold III`, `platinum3`, and `global-champion` all work.

**Available ranks:** bronze1, bronze2, bronze3, silver1, silver2, silver3, gold1, gold2, gold3, plat1, plat2, plat3, diamond1, diamond2, diamond3, master, grandmaster, globalchampion

### Search players

```
GET /api/search?name={username}
```

Finds players matching a username. Each result includes the player's ID, which you need for the stats endpoints. The name is required and can be up to 40 characters.

### Player stats

```
GET /api/player/{id}/casual
GET /api/player/{id}/competitive
```

`{id}` is the player's ID (a UUID) from the search results.

## JavaScript example

```js
const BASE = "https://tickit-o.onrender.com";

async function getJson(path) {
  const res = await fetch(BASE + path);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}

const results = await getJson("/api/search?name=JOPY");
// Pick the player ID from the results, then:
// const stats = await getJson(`/api/player/${id}/competitive`);

const maps = await getJson("/api/maps");
console.log(maps.images.Arctic);
```

## Errors

Errors return JSON with an `error` message.

| Code | Meaning |
| ---- | ------- |
| 400 | Missing or invalid parameter, such as no `name` in a search |
| 404 | Unknown endpoint, map, or rank. The response lists valid options |
| 405 | Only `GET` is allowed |
| 429 | Rate limit exceeded. Wait a minute and try again |
| 502 | The server couldn't get data from BreachersTracker. Try again |

## Limits and good practice

- **Rate limit:** Player search and player stats are limited to 60 requests per minute per IP address. Game data, map, and rank endpoints are not limited.
- **Caching:** Search and stats responses are cached for about a minute. Cache results in your own app too, instead of calling the API repeatedly.
- **Slow first request:** The server may take up to a minute to wake up after being idle. Use a long timeout and retry.
- **Be kind:** Don't spam requests or try to get around the rate limit. Heavy abuse may get blocked.

## Self-hosting

The server is a single Node.js file with no dependencies. It needs Node 18 or newer.

```bash
npm start
```

| Variable | Default | Description |
| -------- | ------- | ----------- |
| `PORT` | `3000` | Port to listen on |
| `ALLOWED_ORIGIN` | `*` | Value for the CORS header |
| `CACHE_TTL_MS` | `60000` | How long to cache search and stats responses |
| `RATE_LIMIT` | `60` | Requests per minute per IP for search and stats |
| `MAP_IMAGE_BASE` | Breachers Lookup site | Folder URL where map images are hosted |
| `RANK_IMAGE_BASE` | empty | Folder URL for self-hosted rank icons. If empty, icons come from BreachersTracker |

## Credit

If you build something with this API, please credit the project and link back.

## Support

Questions or bugs? Join the Discord: https://discord.gg/BwYsyQxQ9R
