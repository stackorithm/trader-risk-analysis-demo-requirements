# Tradovate Demo Data Requirements

This document defines the data format required for demonstrating the prop firm trading platform with real trader historical data from Tradovate.

---

## Overview

The demo data should contain a list of traders (accounts), where each trader includes their complete historical trading activity:

| Component     | Description                                                    |
| ------------- | -------------------------------------------------------------- |
| **Traders**   | List of trading accounts to include in the demo                |
| **Fills**     | All executed trades/fills for each trader                      |
| **Orders**    | All order history (pending, filled, cancelled) for each trader |
| **Positions** | Historical position data                                       |
| **Contracts** | Contract/instrument details for mapping symbols                |
| **Products**  | Product details for contract size mapping                      |

---

## Data Format Specification

### Root Structure

```json
{
  "traders": [
    {
      "accountId": 12345,
      "fills": [...],
      "orders": [...],
      "positions": [...],
      "contracts": [...]
    }
  ]
}
```

---

## Trader (Account)

Represents a trading account in Tradovate.

| Field       | Type    | Required | Description                |
| ----------- | ------- | -------- | -------------------------- |
| `accountId` | integer | Yes      | Unique account identifier  |

---

## Fills

Fills represent executed trades — the actual filled transactions in the market.

### Fill Fields

Source: [Tradovate API Documentation](https://api.tradovate.com/)

| Field           | Type    | Required | Description                      |
| --------------- | ------- | -------- | -------------------------------- |
| `id`            | integer | Yes      | Unique fill ID                   |
| `orderId`       | integer | Yes      | Associated order ID              |
| `contractId`    | integer | Yes      | Contract ID traded               |
| `timestamp`     | string  | Yes      | Fill timestamp (ISO 8601 format) |
| `tradeDate`     | object  | Yes      | Trade date (year, month, day)    |
| `action`        | string  | Yes      | "Buy" or "Sell"                  |
| `qty`           | integer | Yes      | Quantity filled                  |
| `price`         | float   | Yes      | Fill price                       |
| `active`        | boolean | Yes      | Whether fill is active           |
| `finallyPaired` | integer | Optional | Pairing identifier               |

### Fill Fields Extended (from Execution Report)

For complete trade data, also extract from `/executionReport/list` endpoint:

| Field             | Type    | Required | Description                           |
| ----------------- | ------- | -------- | ------------------------------------- |
| `id`              | integer | Yes      | Unique execution report ID            |
| `commandId`       | integer | Yes      | Command ID that generated this report |
| `accountId`       | integer | Yes      | Account ID                            |
| `contractId`      | integer | Yes      | Contract ID                           |
| `timestamp`       | string  | Yes      | Execution timestamp (ISO 8601 format) |
| `tradeDate`       | object  | Yes      | Trade date (year, month, day)         |
| `orderId`         | integer | Yes      | Order ID                              |
| `execType`        | string  | Yes      | Execution type (see values below)     |
| `execRefId`       | string  | Optional | Reference ID for execution            |
| `ordStatus`       | string  | Yes      | Order status                          |
| `action`          | string  | Yes      | "Buy" or "Sell"                       |
| `cumQty`          | integer | Yes      | Cumulative quantity executed          |
| `avgPx`           | float   | Yes      | Average execution price               |
| `lastQty`         | integer | Yes      | Last fill quantity                    |
| `lastPx`          | float   | Yes      | Last fill price                       |
| `rejectReason`    | string  | Optional | Rejection reason if rejected          |
| `text`            | string  | Optional | Additional text/comment               |
| `exchangeOrderId` | string  | Optional | Exchange order ID                     |

### Execution Type Values (`execType`)

| Value            | Description            |
| ---------------- | ---------------------- |
| `New`            | New order accepted     |
| `PartialFill`    | Order partially filled |
| `Fill`           | Order fully filled     |
| `Canceled`       | Order canceled         |
| `Rejected`       | Order rejected         |
| `PendingCancel`  | Cancel pending         |
| `Stopped`        | Order stopped          |
| `Expired`        | Order expired          |
| `PendingReplace` | Replace pending        |
| `PendingNew`     | New order pending      |

---

## Orders

Orders represent trade requests — pending, active, and historical orders.

### Order Fields

| Field                 | Type    | Required | Description                       |
| --------------------- | ------- | -------- | --------------------------------- |
| `id`                  | integer | Yes      | Unique order ID                   |
| `accountId`           | integer | Yes      | Account ID                        |
| `contractId`          | integer | Yes      | Contract ID                       |
| `timestamp`           | string  | Yes      | Order timestamp (ISO 8601 format) |
| `action`              | string  | Yes      | "Buy" or "Sell"                   |
| `ordStatus`           | string  | Yes      | Order status (see below)          |
| `executionProviderId` | integer | Optional | Execution provider ID             |
| `ocoId`               | integer | Optional | OCO (One Cancels Other) order ID  |
| `parentId`            | integer | Optional | Parent order ID                   |
| `linkedId`            | integer | Optional | Linked order ID                   |
| `admin`               | boolean | Optional | Admin order flag                  |

### Order Status Values (`ordStatus`)

| Value             | Description                      |
| ----------------- | -------------------------------- |
| `New`             | Order placed, awaiting execution |
| `PartiallyFilled` | Partially filled                 |
| `Filled`          | Fully filled                     |
| `Canceled`        | Canceled by client               |
| `Rejected`        | Rejected by broker               |
| `PendingNew`      | Pending placement                |
| `Expired`         | Expired                          |
| `Stopped`         | Stopped                          |
| `Suspended`       | Suspended                        |

### OrderVersion Fields

For detailed order information, use `/orderVersion/list` or `/orderVersion/deps?masterid={orderId}`:

| Field           | Type    | Required | Description                          |
| --------------- | ------- | -------- | ------------------------------------ |
| `id`            | integer | Yes      | Unique OrderVersion ID               |
| `orderId`       | integer | Yes      | Associated order ID                  |
| `orderQty`      | integer | Yes      | Order quantity                       |
| `orderType`     | string  | Yes      | Order type (e.g., "Limit", "Market") |
| `price`         | float   | Yes      | Limit price                          |
| `stopPrice`     | float   | Optional | Stop price for stop orders           |
| `maxShow`       | integer | Optional | Maximum number to show               |
| `pegDifference` | float   | Optional | Difference for peg orders            |
| `timeInForce`   | string  | Yes      | Time in force (e.g., "Day", "GTC")   |
| `expireTime`    | string  | Optional | Expiration time (ISO 8601 format)    |
| `text`          | string  | Optional | Additional text/comment              |

### Time In Force Values

| Value | Description               |
| ----- | ------------------------- |
| `Day` | Valid for the trading day |
| `GTC` | Good Until Canceled       |
| `GTD` | Good Until Date           |
| `IOC` | Immediate or Cancel       |
| `FOK` | Fill or Kill              |
| `ATO` | At The Open               |
| `ATC` | At The Close              |

---

## Positions

Positions represent historical position data derived from fills. Use `/position/deps?masterid={accountId}` endpoint for complete position history.

### Position Fields (Historical/Detailed)

Use `/position/deps?masterid={accountId}` for more detailed position data:

| Field         | Type    | Required | Description                          |
| ------------- | ------- | -------- | ------------------------------------ |
| `id`          | integer | Yes      | Unique position ID                   |
| `accountId`   | integer | Yes      | Account ID                           |
| `contractId`  | integer | Yes      | Contract ID                          |
| `timestamp`   | string  | Yes      | Position timestamp (ISO 8601 format) |
| `tradeDate`   | object  | Yes      | Trade date (year, month, day)        |
| `netPos`      | integer | Yes      | Net position quantity                |
| `netPrice`    | float   | Yes      | Net position average price           |
| `bought`      | integer | Yes      | Total bought quantity                |
| `boughtValue` | float   | Yes      | Total bought value                   |
| `sold`        | integer | Yes      | Total sold quantity                  |
| `soldValue`   | float   | Yes      | Total sold value                     |
| `prevPos`     | integer | Yes      | Previous position quantity           |
| `prevPrice`   | float   | Yes      | Previous position average price      |

---

## Contracts

Contracts represent the instruments traded. Required to map contract IDs to symbol names.

### Contract Fields

| Field                | Type    | Required | Description                      |
| -------------------- | ------- | -------- | -------------------------------- |
| `id`                 | integer | Yes      | Unique contract ID               |
| `name`               | string  | Yes      | Contract name (e.g., "MESM5")    |
| `contractMaturityId` | integer | Optional | Contract maturity ID             |
| `productId`          | integer | Optional | Associated product ID            |
| `exchangeId`         | integer | Optional | Exchange ID                      |
| `status`             | string  | Optional | Contract status (e.g., "Active") |

---

## Products

Products represent the underlying instrument details. Required for contract size mapping.

### Product Fields

| Field           | Type    | Required | Description                    |
| --------------- | ------- | -------- | ------------------------------ |
| `id`            | integer | Yes      | Unique product ID              |
| `name`          | string  | Yes      | Product name (e.g., "MES")     |
| `valuePerPoint` | float   | Yes      | Contract size                  |
| `tickSize`      | float   | Yes      | Minimum tick size              |

---

## Data Extraction Checklist

To prepare demo data, ensure:

- [ ] Each trader has a valid `accountId`
- [ ] Include **all fills** from the account history
- [ ] Include **all orders** from the account history
- [ ] Include **all positions** (historical - derived from fills)
- [ ] Include **all contracts** referenced in fills/orders
- [ ] Include **all products** referenced in contracts
- [ ] Timestamps are in ISO 8601 format (e.g., "2024-08-24T14:15:22Z")
- [ ] Numeric fields use appropriate types (float for prices, integer for quantities)
- [ ] Map string action values ("Buy"/"Sell") appropriately
- [ ] Include `contractId` to `name` mapping via Contract entities

---

### Core Data Endpoints

| Data Type                  | Endpoint                                       | Description                     |
| -------------------------- | --------------------------------------------- | ------------------------------- |
| Accounts                   | `GET /account/list`                           | Get all accounts                |
| Account by ID              | `GET /account/{id}`                           | Get account by ID               |
| Fills                      | `GET /fill/list`                              | Get all fills                   |
| Fills by Account           | `GET /fill/deps?masterid={id}`               | Get fills for specific account  |
| Orders                     | `GET /order/list`                             | Get all orders                  |
| Orders by Account          | `GET /order/deps?masterid={id}`              | Get orders for specific account |
| Order Versions             | `GET /orderVersion/list`                      | Get all order versions          |
| Order Versions by Order    | `GET /orderVersion/deps?masterid={orderId}`  | Get versions for order          |
| Positions                  | `GET /position/list`                          | Get all positions               |
| Positions by Account       | `GET /position/deps?masterid={id}`            | Get positions for account       |
| Contracts                  | `GET /contract/list`                          | Get all contracts               |
| Contract by Name           | `GET /contract/find?name={name}`              | Find contract by name           |
| Products                   | `GET /product/list`                           | Get all products                |
| Product Suggest            | `GET /product/suggest?t={text}&l={limit}`    | Search products                 |
| Execution Reports          | `GET /executionReport/list`                   | Get all execution reports       |
| Execution Reports by Order | `GET /executionReport/deps?masterid={orderId}` | Get reports for order           |

---

## Example JSON Structure

For a complete working example with metadata envelope, see [`examples/sample-data.json`](examples/sample-data.json).

The example file includes:
- Metadata envelope (version, platform, timestamp, purpose)
- Sample trader account with `accountId: 12345`
- Sample fills with execution details
- Sample orders with order status
- Sample positions with net position data
- Sample contracts and products for instrument mapping

---

## Contact

For questions regarding this data format, please reach out to the Stackorithm representative or info@stackorithm.co.

_Document Version: 5.0_  
_Platform: Tradovate_  
_Last Updated: February 2026_
_Copyright © 2026 Stackorithm LLC. All rights reserved._
