# Risk Controls

This project should not place live orders unless all mandatory risk controls are implemented, tested, and enabled.

## Mandatory controls

- **Mode gate**: live trading must require `BINANCE_ENABLE_LIVE_TRADING=true` and `BINANCE_TRADING_MODE=live`.
- **API key scope**: use read-only keys for data collection and separate trading keys for execution.
- **Symbol allowlist**: reject orders for symbols that are not explicitly enabled.
- **Max order notional**: cap the value of each order.
- **Max daily notional**: cap total traded value per day.
- **Max position exposure**: cap exposure per symbol and globally.
- **Max drawdown**: stop trading when realized or mark-to-market drawdown breaches limits.
- **Stale data guard**: reject signals when market data or account data is stale.
- **Exchange filter validation**: validate tick size, step size, minimum quantity, minimum notional, and other exchange filters before order submission.
- **Idempotency and reconciliation**: do not assume a timed-out order failed; reconcile status before retrying.
- **Kill switch**: provide a single operational control to cancel open orders and stop new order placement.
- **Audit logging**: record every signal, risk decision, order request, response, fill, cancellation, and exception.

## Recommended defaults

| Setting | Recommended default |
| --- | --- |
| Trading mode | `testnet` |
| Live trading | `false` |
| Max order notional | Very small during testnet/live rollout |
| Symbol list | Explicit allowlist only |
| Order types | Limit orders first |
| Strategy schedule | Disabled until configured |

## Pre-live checklist

- Backtest results are reproducible from committed code and immutable data snapshots.
- Paper-trading metrics match expected strategy behavior.
- Testnet execution handles partial fills, cancellations, rejects, disconnects, and timeouts.
- Alerts are configured for connectivity, latency, rejected orders, stale data, drawdown, and position drift.
- Real API keys are stored only in the deployment secret manager or local untracked environment files.
