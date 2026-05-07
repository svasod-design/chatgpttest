# Binance API Notes

These notes capture API assumptions that the project should verify during implementation. Always re-check official Binance documentation before changing exchange connectivity code because exchange behavior and limits can change.

## Spot REST

- Production REST endpoints include `https://api.binance.com`, `https://api-gcp.binance.com`, and numbered `api1` through `api4` alternatives.
- Binance documents that `api1` through `api4` may perform better but can be less stable.
- JSON timestamps are milliseconds by default, with optional microsecond support through Binance's documented time-unit controls.
- Public market-data-only access should prefer Binance's market data endpoint where appropriate.
- Exchange timeout responses must be treated as unknown order status until reconciled.

Official reference: <https://developers.binance.com/docs/binance-spot-api-docs/rest-api/general-api-information>

## Spot WebSocket API

- Production WebSocket API endpoint: `wss://ws-api.binance.com:443/ws-api/v3`.
- Spot Testnet WebSocket API endpoint: `wss://ws-api.testnet.binance.vision/ws-api/v3`.
- Binance documents that a single WebSocket API connection is valid for 24 hours, so the service must proactively reconnect.
- The server sends ping frames and expects prompt pong responses.
- Timeout responses should trigger order/account status reconciliation rather than blind retries.

Official reference: <https://developers.binance.com/docs/binance-spot-api-docs/websocket-api/general-api-information>

## Spot Testnet

- Binance Spot Testnet is the default environment for early development.
- Testnet behavior is not guaranteed to be perfectly synchronized with the live exchange.
- Testnet data can be periodically reset, so tests should not rely on long-lived testnet account state.

Official reference: <https://developers.binance.com/docs/binance-spot-api-docs/testnet>

## Implementation implications

- Keep endpoint URLs configurable by environment.
- Build reconnection and heartbeat handling into WebSocket clients from the start.
- Store enough order metadata to reconcile unknown states after timeouts or disconnects.
- Do not hard-code filters or limits; refresh symbol metadata and validate orders against current exchange rules.
