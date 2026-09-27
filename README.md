# govmap-page

A relay page for GovMap (the Israeli government map portal) API calls from the "ת"ת קרית מלך" CRM (an Electron desktop app).

GovMap API tokens are bound to a domain, and the desktop app has none. So the app loads this page,
`https://ys109881-rgb.github.io/govmap-page/`, in a hidden iframe and sends its requests through it via `postMessage`.
The requests reach GovMap from the `ys109881-rgb.github.io` domain.

- The token is registered in GovMap for the domain `ys109881-rgb.github.io`.
- The token is **not** stored here: the app sends it with each request (from its settings).
- Requests are accepted only from the parent window, and only from the origins in `ALLOWED_ORIGINS` (`file://` = the installed app, `http://localhost:5173` = development).

## Protocol

App → page:
```js
{ source: 'tat-app', id: 1, command: 'search', params: { apiKey, searchText: 'הרב שך 10 בני ברק', maxResults: 5 } }
```
Page → app:
```js
{ source: 'govmap-page', type: 'ready' }
{ source: 'govmap-page', type: 'result', id: 1, ok: true, result: { results: [...] } }
{ source: 'govmap-page', type: 'result', id: 1, ok: false, status: 401, error: '...' }   // status 0 = network failure
```

Coordinates in the results (`centroid`) are Israeli Transverse Mercator (ITM, EPSG:2039).
