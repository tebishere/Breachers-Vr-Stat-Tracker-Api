# Breachers Lookup API

A free, public JSON API for Breachers player stats and game data. It's the same API that powers the Breachers Lookup website, so you can use it in your own bots, apps, and sites.

> Unofficial fan project. Not affiliated with the game's developers. Data comes from BreachersTracker.

## Base URL

```
https://tickit-o.onrender.com
```

No API key is needed. All endpoints are `GET` and return JSON.

## Endpoints

### Health check

```
GET /health
```

Returns OK when the server is running.

### Search players

```
GET /api/search?name={username}
```

Finds players matching a username. Each result includes the player's ID, which you need for the stats endpoints below.

```bash
curl "https://tickit-o.onrender.com/api/search?name=Username"
```

### Player stats

```
GET /api/player/{id}/casual
GET /api/player/{id}/competitive
```

Get a player's stats in casual or competitive mode. `{id}` is the ID from the search results.

```bash
curl "https://tickit-o.onrender.com/api/player/PLAYER_ID/competitive"
```

### Game data

```
GET /api/catalog    Full game catalog
GET /api/maps       All maps
GET /api/weapons    All weapons
GET /api/gadgets    All gadgets
GET /api/ranks      All ranks
```

```bash
curl "https://tickit-o.onrender.com/api/maps"
```

## Example (JavaScript)

```js
const BASE = "https://tickit-o.onrender.com";

async function getPlayer(name) {
  const search = await fetch(`${BASE}/api/search?name=${encodeURIComponent(name)}`)
    .then(r => r.json());

  // Pick the player ID from the first result, then:
  // const stats = await fetch(`${BASE}/api/player/${id}/competitive`).then(r => r.json());
  return search;
}
```

## Errors

| Code | Meaning |
| ---- | ------- |
| 404 | Nothing found for that request |
| 429 | Too many requests. Wait and try again |
| 5xx | Server error. Try again in a moment |

## Good to know

- **Slow first request:** The server may take up to a minute to wake up after being idle. Allow a long timeout and retry.
- **Cache your results:** The official site caches responses for 10 minutes. Please do the same to avoid overloading the server.
- **Be kind:** Don't spam requests or try to hammer the server. Heavy abuse may get blocked.

## Credit

If you build something with this, please credit the project and link back.

## Support

Questions or bugs? Join the Discord: https://discord.gg/BwYsyQxQ9R
