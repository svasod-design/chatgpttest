# Binance Quant Trading System Project Plan

## Scope

Build a spot-market quantitative trading system that can progress through research, backtesting, paper trading, Binance Spot Testnet trading, and finally controlled live trading.

Initial scope should focus on spot trading rather than futures or margin trading because spot execution has fewer liquidation and leverage risks.

## Non-goals for the first version

- No leverage, margin, futures, options, or borrowing.
- No fully autonomous live trading by default.
- No high-frequency market-making assumptions until latency, co-location, and order-book modeling are proven.
- No strategy that cannot be explained, replayed, and audited from stored data.

## Architecture phases

### Phase 0: Foundations

- Choose the runtime stack and package manager.
- Add linting, formatting, tests, and CI.
- Define configuration schema and `.env.example`.
- Create a read-only Binance client for server time and exchange metadata.

### Phase 1: Data platform

- Ingest historical klines and exchange metadata.
- Subscribe to WebSocket market data streams.
- Persist raw events before transformation.
- Add data quality checks for gaps, duplicate events, out-of-order messages, stale streams, and symbol filter changes.

### Phase 2: Research and backtesting

- Implement reusable strategy interfaces.
- Add backtest support with fees, slippage, minimum notional constraints, and exchange filters.
- Produce reproducible reports for PnL, Sharpe ratio, max drawdown, turnover, win rate, and exposure.
- Add walk-forward validation and out-of-sample evaluation.

### Phase 3: Paper trading

- Run strategies against live market data without submitting real orders.
- Simulate fills and compare simulated account state against intended positions.
- Record every signal, rejected intent, simulated order, simulated fill, and risk decision.

### Phase 4: Testnet execution

- Integrate Binance Spot Testnet order placement.
- Reconcile order state from REST queries and user data events.
- Add retry, timeout, duplicate order, and partial fill handling.
- Validate kill switches and emergency shutdown workflows.

### Phase 5: Controlled live trading

- Require explicit live-trading configuration and deployment approval.
- Start with tiny notional limits and a small allowlist of symbols.
- Enable alerts for rejected orders, stream disconnects, drawdown, stale data, position drift, and unexpected balances.
- Review audit logs after every session before increasing limits.

## Suggested module boundaries

```text
src/
  config/        configuration schema and environment loading
  exchange/      Binance REST/WebSocket clients and signing
  data/          ingestion, normalization, storage adapters
  strategy/      strategy interfaces and implementations
  risk/          pre-trade and runtime controls
  execution/     order routing, reconciliation, and idempotency
  portfolio/     balances, positions, PnL, and accounting
  monitoring/    metrics, alerts, health checks
  backtest/      simulation engine and reporting
```

## Key engineering decisions to make next

1. Runtime: Python is strong for research; TypeScript/Node is convenient for real-time services; Rust or Go can be introduced later for latency-sensitive components.
2. Storage: start with SQLite or DuckDB for research, then consider PostgreSQL plus object storage for production data.
3. Event model: define immutable event records for market data, signals, orders, fills, balances, and risk decisions.
4. Deployment: keep local development simple first, then add containerization only when the service layout stabilizes.
