# DXtrade Demo Data Requirements

This document defines the data format required for demonstrating the prop firm trading platform with real trader historical data from DXtrade.

---

## Overview

The demo data should contain a list of traders (accounts), where each trader includes their complete historical trading activity:

| Component            | Description                                                                      |
| -------------------- | -------------------------------------------------------------------------------- |
| **Instruments List** | General information about the instruments currently traded in the system         |
| **Orders**           | Historical orders (both working orders and orders in final state) on an account. |
| **Transfers**        | Deposit,withdrawal, etc, cash transfer history per account                       |

---

## Personally Identifiable Information

This document describes fields that may contain personally identifiable information (PII). When preparing demo datasets or sharing data outside of a secure environment, certain fields **are recommended to be masked or anonymized** if there are concerns about data privacy.

- Fields such as `login`, `email`, and `ip` addresses should be masked to reduce exposure of sensitive information.
- If `login` is masked, all composite account identifiers using the `domain:login` format (appearing in orders, transfers) should reflect the same anonymized value to maintain referential consistency.
- Likewise, if `email` and `ip` addresses are masked, related logs or audit trails should consistently use the anonymized value to preserve dataset integrity.

---

## Data Format Specification

### Root Structure

```json
{
  "trades": [
    {
      "account": "default:12345",
      "orders": [...],
      "transfers": [...]
    }
  ],
  "instruments":[
    {
      "instrument_list": [...]
    }
  ]
}
```

---

## API Base URLs

| Type         | Base URL                               |
| ------------ | -------------------------------------- |
| Trading API  | `https://demo.dx.trade/dxsca-web    `  |
| Accounts API | `https://demo.dx.trade/dxweb/rest/api` |

Authentication is performed via a session token obtained at login and sent as `Authorization: DXAPI {token}` on all subsequent requests.

---

## Instruments

Global instrument/symbol catalogue. Paginated via `limit` + `start-from`.

### Endpoint

| Data Type   | Endpoint                 | Description               |
| ----------- | ------------------------ | ------------------------- |
| Instruments | `GET /instruments/query` | Full instrument catalogue |

### Instrument Fields

Source: [DXtrade API Documentation](https://demo.dx.trade/developers/#/DXtrade-REST-API)

| Field                    | Type   | Required | Description                                                                               |
| ------------------------ | ------ | -------- | ----------------------------------------------------------------------------------------- |
| `type`                   | string | Optional | Instrument type as configured on the platform (e.g. `INDEX`)                              |
| `symbol`                 | string | Yes      | Unique instrument symbol                                                                  |
| `version`                | int    | Optional | Version of the instrument. Numeric value that is increasing with each modification        |
| `description`            | string | Optional | Human-readable description of an instrument                                               |
| `priceIncrement`         | float  | Optional | Minimum price increment of the instrument (e.g. `0.0001`)                                 |
| `pipSize`                | float  | Optional | Size of 1 pip in the system (e.g. `0.001`)                                                |
| `lotSize`                | float  | Yes      | Lot size of the instrument                                                                |
| `multiplier`             | float  | Optional | Multiplier of the instrument                                                              |
| `currencyType`           | string | Optional | Currency type                                                                             |
| `quantityIncrement`      | float  | Optional | Contains quantity increment                                                               |
| `currency`               | string | Optional | Trading currency of the instrument                                                        |
| `firstCurrency`          | string | Optional | For Forex instruments, contains the first currency in the pair (e.g. `EUR` for `EUR/USD`) |
| `instrumentTreePath`     | string | Optional | Instrument tree path                                                                      |
| `assetClass`             | string | Optional | Asset class for an instrument                                                             |
| `tradingHours_weekDay`   | string | Optional | UTC week date the event occurs (e.g. Monday, 23:00:00Z)                                   |
| `tradingHours_eventType` | string | Optional | Either `SESSION_OPEN` or `SESSION_CLOSE`                                                  |

---

## Orders

Historical orders (both working orders and orders in final state) on an account.

### Endpoint

| Data Type | Endpoint                       | Description                       |
| --------- | ------------------------------ | --------------------------------- |
| Orders    | `GET /accounts/orders/history` | Order history filtered by account |

### Query Parameters

| Parameter     | Description                         |
| ------------- | ----------------------------------- |
| `accounts`    | Account identifier (`domain:login`) |
| `limit`       | Max records per request             |
| `issued-from` | Window start timestamp (ISO 8601)   |
| `issued-to`   | Window end timestamp (ISO 8601)     |

### Order Fields

Source: [DXtrade API Documentation](https://demo.dx.trade/developers/#/DXtrade-REST-API)

| Field              | Type   | Required | Description                                                                                           |
| ------------------ | ------ | -------- | ----------------------------------------------------------------------------------------------------- |
| `account`          | string | Yes      | Unique code of the account                                                                            |
| `orderId`          | int    | Yes      | Internal numeric id of an order chain in the system                                                   |
| `orderCode`        | string | Optional | Internal unique string code of an order chain in the system                                           |
| `version`          | int    | Optional | Numeric version of the account                                                                        |
| `clientOrderId`    | string | Optional | Unique order id assigned by the client during order placement                                         |
| `actionCode`       | string | Optional | Internal unique code of the last order modification                                                   |
| `legCount`         | int    | Optional | Always 1                                                                                              |
| `type`             | string | Yes      | Order type                                                                                            |
| `instrument`       | string | Yes      | Symbol of the order’s instrument                                                                      |
| `status`           | string | Yes      | Status of the order                                                                                   |
| `finalStatus`      | bool   | Optional | Boolean value indicating if order is in final state                                                   |
| `legs`             |        | Yes      | List of exactly 1 order leg                                                                           |
| `side`             | string | Yes      | Side of the order. Either BUY (long) or SELL (short)                                                  |
| `tif`              | string | Yes      | Time in force (expiration time for the order)                                                         |
| `priceOffset`      | float  | Optional | Price offset for protection orders                                                                    |
| `priceLink`        | string | Optional | Price link for protection orders                                                                      |
| `expireDate`       | string | Optional | Order expiration date in UTC                                                                          |
| `marginRate`       | float  | Optional | Order margin rate                                                                                     |
| `issueTime`        | string | Yes      | Timestamp in UTC when this order was accepted by the system                                           |
| `transactionTime`  | string | Yes      | Timestamp in UTC of the last order modification or fill                                               |
| `links`            |        | Optional | List of the links between this order and other orders in its group                                    |
| `executions`       |        | Yes      | List of all executions for this order                                                                 |
| `cashTransactions` |        | Yes      | List of all cash transactions related to this order. Reports are ordered chronologically              |
| `hedgedOrderId`    |        | Optional | For broker orders, a numeric id of the client order this order hedges. Included only for Dealer users |
| `externalOrderId`  |        | Optional | For broker orders, order id at the liquidity provider. Included only for Dealer users                 |
| `audit`            |        | Yes      | IP address and user agent that the trader had when the order was issued                               |

### Nested Sub-Structures (flattened)

| Nested Field       | Prefix               | Description                                 |
| ------------------ | -------------------- | ------------------------------------------- |
| `legs`             | `legs_*`             | First leg of multi-leg orders               |
| `executions`       | `executions_*`       | First execution record                      |
| `cashTransactions` | `cashTransactions_*` | First associated cash transaction           |
| `audit`            | `audit_*`            | IP address and user agent at order issuance |

### Order Leg

| Field               | Type   | Required | Description                                                                                         |
| ------------------- | ------ | -------- | --------------------------------------------------------------------------------------------------- |
| `instrument`        | string | Yes      | Symbol of the order’s instrument                                                                    |
| `positionEffect`    | string | Yes      | The effect of this order on a position                                                              |
| `positionCode`      | string | Yes      | ID of a position that should be modified by this order                                              |
| `price`             | float  | Yes      | Price of the order. It can be null when order is created using priceOffset and priceLink fields     |
| `legRatio`          | float  | Optional | Always 1.0                                                                                          |
| `quantity`          | float  | Yes      | Initial quantity of the order in units (not in lots)                                                |
| `filledQuantity`    | float  | Yes      | Quantity filled so far in units (not in lots). If no fills took place, this field equals to 0.0     |
| `remainingQuantity` | float  | Yes      | Quantity available for further execution (in units)                                                 |
| `averagePrice`      | float  | Yes      | Volume-weighted average price of all fills for this leg. The value is 0.0 if there are no fills yet |

### Order Execution

| Field                    | Type    | Required | Description                                                                                                                    |
| ------------------------ | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `account`                | string  | Yes      | Unique code of the account                                                                                                     |
| `executionCode`          | string  | Optional | Unique string code of the execution                                                                                            |
| `orderCode`              | string  | Optional | Internal unique string code of an order chain in the system                                                                    |
| `updateOrderId`          | int     | Optional | Internal numeric id of an updated order                                                                                        |
| `version`                | int     | Optional | Numeric version of the account                                                                                                 |
| `clientOrderId`          | string  | Optional | Unique order id assigned by the client during order placement                                                                  |
| `actionCode`             | string  | Optional | Internal unique code of the last order modification                                                                            |
| `instrument`             | string  | Optional | Symbol of the order’s instrument                                                                                               |
| `status`                 | string  | Optional | Status of the order after this execution                                                                                       |
| `finalStatus`            | boolean | Optional | Boolean value indicating if order is in final state after this execution                                                       |
| `filledQuantity`         | float   | Optional | Quantity filled so far in units (not in lots). If no fills took place, this field equals to 0.0                                |
| `lastQuantity`           | float   | Optional | The quantity of the fill, reported by the execution in units (not in lots), signed value. If no fill, equals 0.0               |
| `remainingQuantity`      | float   | Optional | Quantity available for further execution (in units) after this execution                                                       |
| `filledQuantityNotional` | float   | Optional | Quantity filled so far in notional units (not in lots). If no fills took place, this field equals to 0.0                       |
| `lastQuantityNotional`   | float   | Optional | The quantity notional of the fill, reported by the execution in notional units (not in lots); signed value                     |
| `lastPrice`              | float   | Optional | For executions of TRADE execution type, price of the trade. Omitted for all other execution types                              |
| `averagePrice`           | float   | Optional | Volume-weighted average price of all fills for this order up to (and including) this execution                                 |
| `transactionTime`        | string  | Yes      | Timestamp in UTC of the last order modification or fill                                                                        |
| `rejectReason`           | string  | Optional | If an order is rejected, this field contains a human-readable reject reason. In this case the rejectCode field is also present |
| `rejectCode`             | float   | Optional | Numeric code of an error in case of order rejections. Present (along with rejectReason) only if an order has been rejected     |
| `executionContext`       | object  | Optional | Execution details like client Bid and Ask, market Bid and Ask, execution strategy, spread type and spread markup               |

### Order Cash Transaction

| Field             | Type   | Required | Description                                                                                                                                                                   |
| ----------------- | ------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `account`         | string | Yes      | Unique code of the account this transaction belongs to                                                                                                                        |
| `transactionCode` | string | Yes      | Unique string code of the transaction                                                                                                                                         |
| `orderCode`       | string | Optional | Internal unique string code of an order chain in the system. Absent if this transaction is not related to an order.                                                           |
| `tradeCode`       | string | Optional | Unique string code of the trade. Corresponds to the executionCode of a trade (see Execution)                                                                                  |
| `positionCode`    | string | Yes      | Unique string code of the position. Corresponds to the positionCode for Positions. For net-based trading the field will not be present.                                       |
| `version`         | float  | Optional | Numeric version of the account to which this transaction relates                                                                                                              |
| `clientOrderId`   | string | Optional | Unique order id assigned by the client during order placement                                                                                                                 |
| `type`            | string | Yes      | Cash transaction type                                                                                                                                                         |
| `value`           | float  | Yes      | Amount of the transaction in account’s base currency for margin accounts and in transaction's currency for cash accounts, signed (positive means profit, negative means loss) |
| `currency`        | string | Optional | Currency of this transaction                                                                                                                                                  |
| `transactionTime` | string | Yes      | Timestamp in UTC of this transaction                                                                                                                                          |
| `createdTime`     | string | Optional | Timestamp in UTC of this transaction request creation. It could take some time till actual transaction happens, exact transaction time is shown in transactionTime field      |

### Audit

| Field       | Type   | Required | Description                                                                       |
| ----------- | ------ | -------- | --------------------------------------------------------------------------------- |
| `IP`        | String | Yes      | IP address that the client had when the order was issued.                         |
| `userAgent` | String | Yes      | User agent (client application id) that the client had when the order was issued. |

### Order Type

Possible values:

- `MARKET`
- `LIMIT`
- `STOP`

### TIF

One of:

- `GTC` (Markets, Limits, Stops)
- `DAY` (Limits, Stops)
- `GTD` (Limits, Stops, in this case the expireDate field will be present)

### Position Effect

Indicates whether the resulting position after a trade should be a new position or the trade should close an existing position. Required for position-based trading. If the value is `Close`, the positionCode field is also present.

Possible values:

- `OPEN`
- `CLOSE`

### Cash Transaction Type Values

One of:

- `COMMISSION`
- `FINANCING`
- `DEPOSIT`
- `WITHDRAWAL`
- `SETTLEMENT`
- `COST`
- `EX_DIVIDEND`
- `REBATE`
- `NEGATIVE_BALANCE_CORRECTION`
- `ADJUSTMENT`

### Order Status Values

| Status      | Details                                                                                                                     |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- |
| `ACCEPTED`  | Order has been accepted by the system                                                                                       |
| `WORKING`   | An order is working in the system: either pending execution or (in case of limit or stop order) waiting for a trigger quote |
| `CANCELED`  | Order has been cancelled in its entirety. This is a final state.                                                            |
| `COMPLETED` | Order has been filled (either completely or partially). This is a final state.                                              |
| `EXPIRED`   | Order has been expired. This is a final state.                                                                              |
| `REJECTED`  | Order has been rejected. This is a final state.                                                                             |

---

## Transfers

Cash transfers (deposits, withdrawals etc.) for a period of time on an account or multiple accounts. This includes transfers made manually as well as transfers that are a result of a position update (e.g. PnL settlement) and commissions.

### Endpoint

| Data Type | Endpoint                  | Description                               |
| --------- | ------------------------- | ----------------------------------------- |
| Transfers | `GET /accounts/transfers` | Cash transfer history filtered by account |

### Query Parameters

| Parameter   | Description                         |
| ----------- | ----------------------------------- |
| `accounts`  | Account identifier (`domain:login`) |
| `limit`     | Max records per request             |
| `time-from` | Window start timestamp (ISO 8601)   |
| `time-to`   | Window end timestamp (ISO 8601)     |

### Transfer Fields

Source: [DXtrade API Documentation](https://demo.dx.trade/developers/#/DXtrade-REST-API)

| Field              | Type             | Required | Description                                                                                                                                                              |
| ------------------ | ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `account`          | Account code     | Yes      | Unique code of the account                                                                                                                                               |
| `version`          | Number           | Optional | Numeric version of the account                                                                                                                                           |
| `transferCode`     | String           | Optional | Unique code of a transfer. Can be absent if this transfer is originated from a trade                                                                                     |
| `comment`          | String           | Optional | Transfer comment, if present                                                                                                                                             |
| `transactionTime`  | Timestamp        | Optional | Timestamp in UTC of this transfer                                                                                                                                        |
| `createdTime`      | Timestamp        | Optional | Timestamp in UTC of this transaction request creation. It could take some time till actual transaction happens, exact transaction time is shown in transactionTime field |
| `cashTransactions` | Cash Transaction | Yes      | List of all [cash transactions](#order-cash-transaction) related to this transfer. Reports are ordered chronologically.                                                  |

---

## Data Extraction Checklist

To prepare demo data, ensure:

- [ ] PII fields (`fullName`, `email`, `login`, `IP`, `userAgent`, `loc_*`, `doc_*`) are masked or anonymized
- [ ] `ORDERS_FROM` and `TRANSFERS_FROM` are set to the platform launch date (ex: `2025-01-01`)
- [ ] Each order is deduplicated by `orderId`
- [ ] Each transfer is deduplicated by `transferCode`
- [ ] Timestamps are in ISO 8601 format (e.g., `"2025-03-14T09:30:00.000Z"`)
- [ ] Numeric fields use appropriate types (float for prices/amounts, integer for quantities)
- [ ] Nested sub-structures (`legs`, `executions`, `cashTransactions`, `marginRate_tiers`) are flattened or exploded into columnar form
- [ ] Account identifiers use the `domain:login` composite format throughout

---

## Core Data Endpoints Summary

| Dataset        | Endpoint                       | API Base     |
| -------------- | ------------------------------ | ------------ |
| Account groups | `GET /register/client/group`   | Accounts API |
| Orders history | `GET /accounts/orders/history` | Trading API  |
| Instruments    | `GET /instruments/query`       | Trading API  |
| Transfers      | `GET /accounts/transfers`      | Trading API  |

---

## Contact

For questions regarding this data format, please reach out to the Stackorithm representative or info@stackorithm.co.

_Document Version: 1.0_
_Platform: DXTrade_
_Last Updated: April 2026_
_Copyright © 2026 Stackorithm LLC. All rights reserved._
