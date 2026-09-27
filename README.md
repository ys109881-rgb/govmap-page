# govmap-page

A GovMap (the Israeli government map portal) page hosted on GitHub Pages and embedded in an iframe by the "ת"ת קרית מלך" CRM (Electron).

GovMap API tokens are bound to a domain, so the map runs here, at
`https://ys109881-rgb.github.io/govmap-page/`, and the app controls it via `postMessage`.

## Setup

1. In the GovMap personal area (User options → API management), issue a token for the domain `ys109881-rgb.github.io`.
2. Put the token in `index.html`: `const GOVMAP_TOKEN = '...';`
3. Commit and push. GitHub Pages publishes the page within about a minute.

The token is visible in the page source; that is how GovMap works, since the protection is the domain binding.

## Message protocol

App → page:
```js
{ source: 'tat-app', id: 1, command: 'zoomToXY', params: { x: 180553, y: 574243, level: 9, marker: true } }
```
Page → app:
```js
{ source: 'govmap-page', type: 'ready' }
{ source: 'govmap-page', type: 'result', id: 1, ok: true, result: ... }   // or ok: false, error: '...'
{ source: 'govmap-page', type: 'event', event: 'click', data: { x, y } }
{ source: 'govmap-page', type: 'error', error: '...' }
```

Commands: `zoomToXY`, `setMapMarker`, `clearMapMarker`, `setBackground`, `search`, `geocode`, `searchAndLocate`, `displayGeometries`, `clearGeometriesByName`, `ping`.
Coordinates are Israeli Transverse Mercator (ITM, EPSG:2039).

Commands are accepted only from the parent window, and only from the origins in `ALLOWED_ORIGINS` (`file://` = the installed app, `http://localhost:5173` = development).
