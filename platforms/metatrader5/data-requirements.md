# MetaTrader 5 Demo Data Requirements

This document defines the data format required for demonstrating the prop firm trading platform with real trader historical data.

---

## Overview

The demo data should contain a list of traders, where each trader includes their complete historical trading activity:

| Component   | Description                                                    |
| ----------- | -------------------------------------------------------------- |
| **Traders** | List of trading accounts to include in the demo                |
| **Deals**   | All executed trades/positions for each trader                  |
| **Orders**  | All order history (pending, filled, cancelled) for each trader |

---

## Data Format Specification

### Root Structure

```json
{
  "traders": [
    {
      "login": 93940,
      "deals": [...],
      "orders": [...]
    }
  ]
}
```

---

## Trader

| Field   | Type    | Required | Description                               |
| ------- | ------- | -------- | ----------------------------------------- |
| `login` | integer | Yes      | Unique trading account number (MT5 login) |

---

## Deals

Deals represent executed trades — the actual filled transactions in the market.

### Deal Fields

| Field          | Type       | Required | Description                                                                                                    |
| -------------- | ---------- | -------- | -------------------------------------------------------------------------------------------------------------- |
| `time`         | integer    | Yes      | Deal execution timestamp (Unix timestamp in seconds)                                                           |
| `deal_id`      | integer    | Yes      | Unique deal ticket number                                                                                      |
| `position_id`  | integer    | Yes      | Position ID this deal relates to                                                                               |
| `entry`        | integer    | Yes      | Deal direction: `0` = IN (open/add), `1` = OUT (close/partial), `2` = INOUT (reverse), `3` = OUT_BY (close by) |
| `action`       | integer    | Yes      | Deal type: `0` = Buy, `1` = Sell, `2` = Balance, etc. See Deal Actions below                                   |
| `profit`       | float      | Yes      | Profit/loss from the deal                                                                                      |
| `commission`   | float      | Yes      | Commission charged for the deal                                                                                |
| `storage`      | float      | Yes      | Swap/rollover cost (if available, otherwise 0.0)                                                               |
| `fee`          | float      | Yes      | Fee amount per deal (if available, otherwise 0.0)                                                              |
| `symbol`       | string     | Yes      | Trading symbol (e.g., "EURUSD", "GBPUSD")                                                                      |
| `volume`       | float      | Yes      | Deal volume in lots                                                                                            |
| `contractsize` | float      | Yes      | Contract size of the symbol                                                                                    |
| `price`        | float      | Yes      | Deal execution price                                                                                           |
| `pricesl`      | float/null | Yes      | Stop Loss price (if available, otherwise 0.0)                                                                  |
| `pricetp`      | float/null | Yes      | Take Profit price (if available, otherwise 0.0)                                                                |
| `comment`      | string     | Yes      | Deal comment/notes (if available, otherwise empty string)                                                      |
| `reason`       | integer    | Yes      | Reason for deal execution. See Deal Reasons below (if available, otherwise null)                               |

### Deal Actions (`action`)

| Value | Name                        | Description                        |
| ----- | --------------------------- | ---------------------------------- |
| 0     | DEAL_BUY                    | Buy deal                           |
| 1     | DEAL_SELL                   | Sell deal                          |
| 2     | DEAL_BALANCE                | Balance operation                  |
| 3     | DEAL_CREDIT                 | Credit operation                   |
| 4     | DEAL_CHARGE                 | Additional charges/withdrawals     |
| 5     | DEAL_CORRECTION             | Correcting operations              |
| 6     | DEAL_BONUS                  | Bonuses                            |
| 7     | DEAL_COMMISSION             | Commission                         |
| 8     | DEAL_COMMISSION_DAILY       | Daily commission                   |
| 9     | DEAL_COMMISSION_MONTHLY     | Monthly commission                 |
| 10    | DEAL_AGENT_DAILY            | Daily agent commission             |
| 11    | DEAL_AGENT_MONTHLY          | Monthly agent commission           |
| 12    | DEAL_INTERESTRATE           | Accrual of annual interest         |
| 13    | DEAL_BUY_CANCELED           | Canceled Buy deal                  |
| 14    | DEAL_SELL_CANCELED          | Canceled Sell deal                 |
| 15    | DEAL_DIVIDEND               | Dividend operations                |
| 16    | DEAL_DIVIDEND_FRANKED       | Franked (non-taxable) dividend     |
| 17    | DEAL_TAX                    | Charging a tax                     |
| 18    | DEAL_AGENT                  | Agent commission                   |
| 19    | DEAL_SO_COMPENSATION        | Stop Out compensation              |
| 20    | DEAL_SO_COMPENSATION_CREDIT | Credit funds after SO compensation |

### Deal Entry Direction (`entry`)

| Value | Name         | Description                                           |
| ----- | ------------ | ----------------------------------------------------- |
| 0     | ENTRY_IN     | Entering market or adding volume                      |
| 1     | ENTRY_OUT    | Exit from market or partial closure                   |
| 2     | ENTRY_INOUT  | Close + reverse (netting mode only)                   |
| 3     | ENTRY_OUT_BY | Close by — simultaneous closure of opposite positions |

### Deal Reasons (`reason`)

| Value | Name                         | Description                                  |
| ----- | ---------------------------- | -------------------------------------------- |
| 0     | DEAL_REASON_CLIENT           | Manual trade via client terminal             |
| 1     | DEAL_REASON_EXPERT           | Trade by Expert Advisor                      |
| 2     | DEAL_REASON_DEALER           | Trade by dealer/manager                      |
| 3     | DEAL_REASON_SL               | Stop Loss activation                         |
| 4     | DEAL_REASON_TP               | Take Profit activation                       |
| 5     | DEAL_REASON_SO               | Stop-Out level reached                       |
| 6     | DEAL_REASON_ROLLOVER         | Position reopen for swap charging            |
| 7     | DEAL_REASON_EXTERNAL_CLIENT  | External trading system (commission charged) |
| 8     | DEAL_REASON_VMARGIN          | Variation margin charging                    |
| 9     | DEAL_REASON_GATEWAY          | MetaTrader 5 gateway                         |
| 10    | DEAL_REASON_SIGNAL           | Signal copying                               |
| 11    | DEAL_REASON_SETTLEMENT       | Forced close due to delivery date            |
| 12    | DEAL_REASON_TRANSFER         | Position relocation                          |
| 13    | DEAL_REASON_SYNC             | Sync with external system                    |
| 14    | DEAL_REASON_EXTERNAL_SERVICE | External system (no commission)              |
| 15    | DEAL_REASON_MIGRATION        | Import from MetaTrader 4                     |
| 16    | DEAL_REASON_MOBILE           | Mobile terminal trade                        |
| 17    | DEAL_REASON_WEB              | Web terminal trade                           |
| 18    | DEAL_REASON_SPLIT            | Symbol split                                 |

---

## Orders

Orders represent trade requests — pending, active, and historical orders.

### Order Fields

| Field            | Type    | Required | Description                                                                        |
| ---------------- | ------- | -------- | ---------------------------------------------------------------------------------- |
| `order_id`       | integer | Yes      | Unique order ticket number                                                         |
| `positionid`     | integer | Yes      | Position ID associated with the order                                              |
| `symbol`         | string  | Yes      | Trading symbol                                                                     |
| `type`           | integer | Yes      | Order type. See Order Types below                                                  |
| `activationtime` | integer | Yes      | Order activation time (for pending orders)                                         |
| `volumeinitial`  | integer | Yes      | Initial order volume (in units, not lots)                                          |
| `state`          | integer | Yes      | Current order state. See Order States below                                        |
| `timesetupmsc`   | integer | Yes      | Order placement time in milliseconds                                               |
| `timedonemsc`    | integer | Yes      | Order execution time in milliseconds                                               |
| `priceorder`     | float   | Yes      | Order's specified price (if available, otherwise 0.0)                              |
| `pricetrigger`   | float   | Yes      | Trigger price for Stop Limit orders (if available, otherwise 0.0)                  |
| `reason`         | integer | Yes      | Reason for order placement. See Order Reasons below (if available, otherwise null) |
| `comment`        | string  | Yes      | Order comment/notes (if available, otherwise empty string)                         |

### Order Types (`type`)

| Value | Name               | Description                        |
| ----- | ------------------ | ---------------------------------- |
| 0     | OP_BUY             | Market Buy order                   |
| 1     | OP_SELL            | Market Sell order                  |
| 2     | OP_BUY_LIMIT       | Buy Limit pending order            |
| 3     | OP_SELL_LIMIT      | Sell Limit pending order           |
| 4     | OP_BUY_STOP        | Buy Stop pending order             |
| 5     | OP_SELL_STOP       | Sell Stop pending order            |
| 6     | OP_BUY_STOP_LIMIT  | Buy Stop Limit order               |
| 7     | OP_SELL_STOP_LIMIT | Sell Stop Limit order              |
| 8     | OP_CLOSE_BY        | Close By order (hedging mode only) |

### Order States (`state`)

| Value | Name                       | Description                       |
| ----- | -------------------------- | --------------------------------- |
| 0     | ORDER_STATE_STARTED        | Order placed, awaiting processing |
| 1     | ORDER_STATE_PLACED         | Order accepted and active         |
| 2     | ORDER_STATE_CANCELED       | Order cancelled by client         |
| 3     | ORDER_STATE_PARTIAL        | Partially filled                  |
| 4     | ORDER_STATE_FILLED         | Fully filled                      |
| 5     | ORDER_STATE_REJECTED       | Rejected by broker                |
| 6     | ORDER_STATE_EXPIRED        | Expired                           |
| 7     | ORDER_STATE_REQUEST_ADD    | Request to place being processed  |
| 8     | ORDER_STATE_REQUEST_MODIFY | Request to modify being processed |
| 9     | ORDER_STATE_REQUEST_CANCEL | Request to cancel being processed |

### Order Reasons (`reason`)

| Value | Name                          | Description                      |
| ----- | ----------------------------- | -------------------------------- |
| 0     | ORDER_REASON_CLIENT           | Manual order via client terminal |
| 1     | ORDER_REASON_EXPERT           | Expert Advisor order             |
| 2     | ORDER_REASON_DEALER           | Dealer/manager order             |
| 3     | ORDER_REASON_SL               | Stop Loss activation             |
| 4     | ORDER_REASON_TP               | Take Profit activation           |
| 5     | ORDER_REASON_SO               | Stop-Out reached                 |
| 6     | ORDER_REASON_ROLLOVER         | Position reopen for swaps        |
| 7     | ORDER_REASON_EXTERNAL_CLIENT  | External trading system          |
| 8     | ORDER_REASON_VMARGIN          | Variation margin                 |
| 9     | ORDER_REASON_GATEWAY          | MetaTrader 5 gateway             |
| 10    | ORDER_REASON_SIGNAL           | Signal copying                   |
| 11    | ORDER_REASON_SETTLEMENT       | Settlement/delivery              |
| 12    | ORDER_REASON_TRANSFER         | Position transfer                |
| 13    | ORDER_REASON_SYNC             | External system sync             |
| 14    | ORDER_REASON_EXTERNAL_SERVICE | External service                 |
| 15    | ORDER_REASON_MIGRATION        | MT4 migration                    |
| 16    | ORDER_REASON_MOBILE           | Mobile terminal                  |
| 17    | ORDER_REASON_WEB              | Web terminal                     |
| 18    | ORDER_REASON_SPLIT            | Symbol split                     |

---

## Volume Note

- **Deals**: Volume is in **lots** (e.g., `1.02` = 1.02 lots)
- **Orders**: Volume is in **units** (e.g., `102000` = 1.02 lots = 102,000 units)

The relationship is: `volume_initial = volume_initial_ext / 100000`

---

## Data Extraction Checklist

To prepare demo data, ensure:

- [ ] Each trader has a valid `login` number
- [ ] Include **all deals** from the account history (filtered fields only)
- [ ] Include **all orders** from the account history (filtered fields only)
- [ ] Timestamps are in Unix format (seconds since 1970) or milliseconds
- [ ] Numeric fields use appropriate types (float for prices, integer for IDs)
- [ ] Use default values for optional fields:
  - `storage`: 0.0 if not available
  - `fee`: 0.0 if not available
  - `pricesl`: 0.0 if not available
  - `pricetp`: 0.0 if not available
  - `comment`: empty string if not available
  - `reason`: null if not available
  - `priceorder`: 0.0 if not available
  - `pricetrigger`: 0.0 if not available
- [ ] Enum fields use the integer values (not string names)

---

## Example JSON Structure

For a complete working example with metadata envelope, see [`examples/sample-data.json`](examples/sample-data.json).

The example file includes:
- Metadata envelope (version, platform, timestamp, purpose)
- Sample trader account with `login: 93940`
- Sample deal with all required fields
- Sample order with execution details

---

## Contact

For questions regarding this data format, please reach out to the Stackorithm representative or info@stackorithm.co.

_Document Version: 2.0_  
_Platform: MetaTrader 5_  
_Last Updated: February 2026_
_Copyright © 2026 Stackorithm LLC. All rights reserved._
