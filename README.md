# Binance Quant Trading System

This repository is the starting point for a Binance-focused quantitative trading system. The first milestone is to build a safe research and paper-trading foundation before any live trading is enabled.

> Risk notice: this project is engineering infrastructure, not financial advice. Live trading should stay disabled until strategies, risk controls, exchange connectivity, and operational monitoring have been validated.

## Product direction

The system should be designed around four principles:

1. **Safety first**: default to Binance Spot Testnet or paper trading, use least-privilege API keys, and require explicit configuration before live order placement.
2. **Research before execution**: collect data, backtest strategies, and compare simulated results against realistic fees, slippage, and latency assumptions.
3. **Modular architecture**: separate data ingestion, strategy generation, risk checks, execution, persistence, and monitoring.
4. **Operational visibility**: every order decision should be traceable through logs, metrics, alerts, and audit records.

## Suggested architecture

```text
market data -> feature pipeline -> strategy engine -> risk engine -> execution gateway
      |                 |                  |              |                 |
      v                 v                  v              v                 v
 historical store   feature store      signals log   risk/audit log   order/events store
```

Core modules:

- **Market data**: REST bootstrap for historical candles/order-book snapshots plus WebSocket streams for live ticks, depth, and account events.
- **Strategy engine**: pluggable strategies that emit target positions or order intents, not raw exchange orders.
- **Risk engine**: validates every intent against account exposure, max drawdown, symbol filters, notional limits, kill switches, and trading mode.
- **Execution gateway**: owns Binance request signing, retries, idempotency keys, order state reconciliation, and timeout handling.
- **Persistence**: stores market data, strategy signals, orders, fills, balances, and risk decisions for replay and audit.
- **Monitoring**: tracks latency, dropped streams, rejected orders, drawdown, position drift, and stale data.

## Recommended first milestones

1. Define configuration and secret handling with `.env.example`.
2. Implement a read-only Binance Spot Testnet connectivity check.
3. Build data ingestion for `exchangeInfo`, klines, ticker streams, and user data events.
4. Add a backtesting harness with fee and slippage models.
5. Implement paper trading with the same strategy/risk interfaces used by live trading.
6. Add live trading only after testnet, backtest, paper trading, and monitoring are stable.

## Safety defaults

- Start with `BINANCE_TRADING_MODE=testnet`.
- Keep `BINANCE_ENABLE_LIVE_TRADING=false` unless explicitly approved.
- Never commit real API keys, secrets, account IDs, or production environment files.
- Use exchange filters from `exchangeInfo` before submitting any order.
- Treat exchange timeout responses as unknown order state until reconciled through account/order queries or user data events.

## Repository setup notes

Configure a GitHub remote before pushing this branch:

```bash
git remote add origin <github-repository-url>
git push -u origin work
```

See [`docs/PROJECT_PLAN.md`](docs/PROJECT_PLAN.md) for the proposed build plan, [`docs/RISK_CONTROLS.md`](docs/RISK_CONTROLS.md) for mandatory risk controls, and [`docs/API_NOTES.md`](docs/API_NOTES.md) for Binance API assumptions to verify during implementation.
