# Binance OI Monitor

A clean USDⓈ-M USDT perpetual open-interest monitoring prototype.

## Current implementation

- Discovers trading USDⓈ-M USDT perpetual contracts from Binance Futures exchange information.
- Applies editable minimum 24-hour quote-volume and current OI-notional filters (both default to 2,000,000 USDT).
- Calculates OI notional as current `openInterest` quantity multiplied by mark price.
- Builds the eligible universe using current OI snapshots.
- Selects the highest-volume qualifying coins for the initial freshness experiment (default 20).
- Polls the Binance current-OI REST endpoint at an editable target interval (default 5 seconds).
- Uses the mark-price WebSocket for price updates.
- Records local receipt time, Binance response timestamp, OI quantity, mark price, OI notional and changes.
- Shows symmetrical market-condition panels:
  - Bullish: OI rising over the rolling 30-second window AND price rising over the same window.
  - Bearish: OI falling over the rolling 30-second window AND price falling over the same window.
- Exports captured OI snapshots as CSV.

## First test

1. Open the GitHub Pages site after Pages is enabled for the repository.
2. Leave both thresholds at $2 million.
3. Leave the test size at 20, polling target at 5 seconds and duration at 30 minutes.
4. Click **Build universe & start test**.
5. Observe the per-symbol Binance timestamp age and the time between changed OI values.
6. Export snapshots after the test for further analysis.

## Important measurement caveat

The current OI REST response includes a Binance server timestamp, but that timestamp is the response's timestamp; it does not expose a separately documented timestamp for the underlying OI calculation. Therefore, repeated polling cannot prove that the exchange recalculates OI at the polling frequency. The prototype measures observed response freshness and observed value-change cadence.

## API endpoints used

- `GET /fapi/v1/exchangeInfo`
- `GET /fapi/v1/ticker/24hr`
- `GET /fapi/v1/premiumIndex`
- `GET /fapi/v1/openInterest?symbol=...`
- WebSocket mark-price streams: `<symbol>@markPrice@1s`

This is a monitoring/scanning tool only. It does not place orders.

## Current scope / next engineering checks

- Validate browser CORS and API availability in the deployed GitHub Pages environment.
- Measure actual request duration, response timestamps, changed-value cadence and missed polling cycles.
- Verify Binance request-weight limits under the complete startup universe scan.
- Add robust retry/backoff and WebSocket reconnection.
- Expand from the 20-coin experiment only after the freshness and rate-limit tests pass.
