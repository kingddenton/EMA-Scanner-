# Binance OI Monitor

A browser-based USDⓈ-M USDT perpetual OI monitoring prototype.

## Current functionality

- Discovers trading USDT perpetuals.
- Editable 24-hour quote-volume and OI-notional eligibility thresholds (default $2 million each).
- OI notional is calculated as current OI quantity × mark price.
- Monitors the highest-volume qualifying contracts (default 20).
- Polls current OI through Binance REST with timeout and retry handling.
- Receives mark prices through Binance's mark-price WebSocket and reconnects after disconnects.
- Records OI quantity, response timestamp, local receipt time, mark price and OI notional.
- Displays rolling 30-second bullish (OI up + price up) and bearish (OI down + price down) conditions.
- Reports OI request failures, last snapshot age, oldest mark-price update age, polling cycle duration and observed OI value-change gaps.
- Exports raw snapshots to CSV.

## Initial freshness experiment

Suggested starting configuration: 20 coins, 5-second polling target, 30 minutes, $2m minimum 24h quote volume and $2m minimum OI notional.

The 5-second value is a polling target, not a claim that Binance recalculates OI every five seconds. Use the per-coin changed-value gap and timestamp diagnostics to measure what the feed actually delivers.

## Important limitations

- The current OI REST response timestamp is the response timestamp; it is not a separate timestamp for the underlying OI calculation.
- Network errors can still occur. The app retries transient REST errors and records failures.
- Mark-price WebSocket reconnection is attempted automatically, but a browser/network may still block or interrupt the connection.
- Do not scale to the full eligible universe until the 20-coin test has been repeated successfully and Binance request-weight limits have been reviewed.
- This is a monitoring tool only; it does not place orders.

## Binance endpoints

- `GET /fapi/v1/exchangeInfo`
- `GET /fapi/v1/ticker/24hr`
- `GET /fapi/v1/premiumIndex`
- `GET /fapi/v1/openInterest?symbol=...`
- `wss://fstream.binance.com/stream?streams=<symbol>@markPrice@1s`
