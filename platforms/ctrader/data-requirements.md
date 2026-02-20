# cTrader Demo Data Requirements

This document defines the data format required for demonstrating the prop firm trading platform with real trader historical data from cTrader.

---

## Overview

The demo data should contain a list of traders (accounts), where each trader includes their complete historical trading activity:

| Component   | Description                                                    |
| ----------- | -------------------------------------------------------------- |
| **Traders** | List of trading accounts to include in the demo                |
| **Deals**   | All executed trades/deals for each trader                      |
| **Orders**  | All order history (pending, filled, cancelled) for each trader |
| **Symbols** | Trading symbol/instrument details (light format)               |

---

## Data Format Specification

### Root Structure

```json
{
  "traders": [
    {
      "ctidTraderAccountId": 12345678,
      "deals": [...],
      "orders": [...],
      "symbols": [...]
    }
  ]
}
```

---

## Trader (Account)

Represents a trading account in cTrader.

### Account Fields

| Field                 | Type    | Required | Description                                        |
| --------------------- | ------- | -------- | -------------------------------------------------- |
| `ctidTraderAccountId` | integer | Yes      | Unique cTrader account ID                          |
| `balance`             | integer | Yes      | Account balance (in cents, scaled by moneyDigits)  |

---

## Deals

Deals represent executed trades — the actual filled transactions in the market.

### Deal Fields (ProtoOADeal)

Source: [cTrader Open API Documentation - ProtoOADeal](https://help.ctrader.com/open-api/model-messages/)

| Field                     | Type   | Required | Description                                                           |
| ------------------------- | ------ | -------- | --------------------------------------------------------------------- |
| `dealId`                  | int64  | Yes      | The unique ID of the execution deal                                   |
| `orderId`                 | int64  | Yes      | Source order of the deal                                              |
| `positionId`              | int64  | Yes      | Source position of the deal                                           |
| `volume`                  | int64  | Yes      | Volume sent for execution, in cents                                   |
| `filledVolume`            | int64  | Yes      | Filled volume, in cents                                               |
| `symbolId`                | int64  | Yes      | The unique identifier of the symbol                                   |
| `createTimestamp`         | int64  | Yes      | The Unix time in milliseconds when the deal was sent for execution    |
| `executionTimestamp`      | int64  | Yes      | The Unix time in milliseconds when the deal was executed             |
| `utcLastUpdateTimestamp`  | int64  | Optional | The Unix time in milliseconds when deal was created/executed/rejected |
| `executionPrice`          | double | Yes      | Execution price                                                       |
| `tradeSide`               | int32  | Yes      | Trade direction: 1=Buy, 2=Sell (ProtoOATradeSide)                     |
| `dealStatus`              | int32  | Yes      | Status of the deal (see Deal Status values below)                     |
| `commission`              | int64  | Yes      | Amount of trading commission associated with the deal                 |
| `marginRate`              | double | Optional | Rate for used margin computation                                      |
| `moneyDigits`             | uint32 | Optional | Monetary precision (divide by 10^moneyDigits)                         |
| `baseToUsdConversionRate` | double | Optional | Base to USD conversion rate at deal execution time                    |
| `label`                   | string | Optional | Label field from corresponding order (max 100 chars)                  |
| `comment`                 | string | Optional | Comment field from corresponding order                                |
| `closePositionDetail`     | object | Optional | Closing position detail (valid only for closing deals)                |

### Close Position Detail Fields (ProtoOAClosePositionDetail)

| Field                          | Type   | Required | Description                                                  |
| ------------------------------ | ------ | -------- | ------------------------------------------------------------ |
| `entryPrice`                   | double | Yes      | Position price at the moment of filling the closing order    |
| `grossProfit`                  | int64  | Yes      | Amount of realized gross profit after closing deal execution |
| `swap`                         | int64  | Yes      | Amount of realized swap related to closed volume             |
| `commission`                   | int64  | Yes      | Amount of realized commission related to closed volume       |
| `balance`                      | int64  | Yes      | Account balance after closing deal execution                 |
| `quoteToDepositConversionRate` | double | Optional | Quote/Deposit currency conversion rate at closing time       |
| `closedVolume`                 | int64  | Optional | Closed volume in cents                                       |
| `balanceVersion`               | int64  | Optional | Balance version of the account                               |
| `moneyDigits`                  | uint32 | Optional | Monetary precision                                           |
| `pnlConversionFee`             | int64  | Optional | Fee for conversion when quote asset <> deposit asset         |

### Deal Status Values (`dealStatus`)

| Value | Name                | Description                         |
| ----- | ------------------- | ----------------------------------- |
| 2     | FILLED              | Deal fully filled                   |
| 3     | PARTIALLY_FILLED    | Deal partially filled               |
| 4     | REJECTED            | Rejected by liquidity provider      |
| 5     | INTERNALLY_REJECTED | Rejected by server                  |
| 6     | ERROR               | Error                               |
| 7     | MISSED              | No response from liquidity provider |

### Trade Side Values (`tradeSide`)

| Value | Name | Description     |
| ----- | ---- | --------------- |
| 1     | BUY  | Buy order/deal  |
| 2     | SELL | Sell order/deal |

---

## Orders

Orders represent trade requests — pending, active, and historical orders.

### Order Fields (ProtoOAOrder)

Source: [cTrader Open API Documentation - ProtoOAOrder](https://help.ctrader.com/open-api/model-messages/)

**⚠️ IMPORTANT**: cTrader uses nested `tradeData` object for core order fields. Timestamps are tracked via `tradeData.openTimestamp` and `utcLastUpdateTimestamp`.

| Field                 | Type   | Required | Description                                                        |
| --------------------- | ------ | -------- | ------------------------------------------------------------------ |
| `orderId`             | int64  | Yes      | Unique order ID                                                    |
| `tradeData`           | object | Yes      | Nested object containing symbolId, volume, tradeSide, label, comment |
| `orderType`           | int32  | Yes      | Order type (see Order Type values below)                           |
| `orderStatus`         | int32  | Yes      | Order status (see Order Status values below)                       |
| `executedVolume`      | int64  | Optional | Executed volume in cents                                           |
| `executionPrice`      | double | Optional | Execution price (for filled orders)                                |
| `stopLoss`            | double | Optional | Stop Loss price                                                    |
| `takeProfit`          | double | Optional | Take Profit price                                                 |
| `limitPrice`          | double | Optional | Limit price (for LIMIT orders)                                    |
| `stopPrice`           | double | Optional | Stop price (for STOP orders)                                       |
| `label`               | string | Optional | Order label (max 100 chars)                                       |
| `comment`             | string | Optional | User-specified comment                                             |
| `positionId`          | int64  | Required | Linked position ID                                                |
| `expirationTimestamp` | int64  | Optional | Expiration timestamp (for GTD orders) in Unix milliseconds         |
| `timeInForce`         | int32  | Optional | Time in force (see Time In Force values below)                   |
| `closingOrder`        | bool   | Optional | Whether this is a closing order                                    |
| `isStopOut`           | bool   | Optional | Whether order was stopped out                                      |
| `clientOrderId`       | string | Optional | Client-specified order ID                                         |
| `triggerMethod`       | int32  | Optional | Order trigger method                                              |
| `utcLastUpdateTimestamp` | int64  | Optional | Last modification timestamp in Unix milliseconds                   |
| `relativeStopLoss`    | int64  | Optional | Relative stop loss (1/100000 of price unit)                      |
| `relativeTakeProfit`  | int64  | Optional | Relative take profit (1/100000 of price unit)                    |
| `trailingStopLoss`    | bool   | Optional | Whether this is a trailing stop loss order                         |

#### TradeData Nested Fields

| Field           | Type    | Required | Description                                    |
| --------------- | ------- | -------- | ---------------------------------------------- |
| `symbolId`      | int64   | Yes      | Symbol ID                                      |
| `volume`        | int64   | Yes      | Order volume in cents                          |
| `tradeSide`     | int32   | Yes      | Trade direction: 1=Buy, 2=Sell                |
| `label`         | string  | Optional | Order label                                    |
| `comment`       | string  | Optional | User comment                                   |
| `openTimestamp` | int64   | Optional | Order creation timestamp (Unix ms)             |

### Order Type Values (`orderType`)

| Value | Name                  | Description             |
| ----- | --------------------- | ----------------------- |
| 1     | MARKET                | Market order            |
| 2     | LIMIT                 | Limit order             |
| 3     | STOP                  | Stop order              |
| 4     | STOP_LOSS_TAKE_PROFIT | Stop Loss / Take Profit |
| 5     | MARKET_RANGE          | Market range order      |
| 6     | STOP_LIMIT            | Stop Limit order        |

### Order Status Values (`orderStatus`)

| Value | Name      | Description                  |
| ----- | --------- | ---------------------------- |
| 1     | ACCEPTED  | Order accepted for execution |
| 2     | FILLED    | Order fully filled           |
| 3     | REJECTED  | Order rejected               |
| 4     | EXPIRED   | Order expired                |
| 5     | CANCELLED | Order cancelled              |

### Time In Force Values (`timeInForce`)

| Value | Name                | Description                 |
| ----- | ------------------- | --------------------------- |
| 1     | GOOD_TILL_DATE      | Valid until expiration date |
| 2     | GOOD_TILL_CANCEL    | Good until cancelled        |
| 3     | IMMEDIATE_OR_CANCEL | Immediate or cancel         |
| 4     | FILL_OR_KILL        | Fill or kill                |
| 5     | MARKET_ON_OPEN      | Market on open              |

---

## Symbols

Symbols represent the trading instruments.

### Symbol Fields (ProtoOALightSymbol)

Source: [ProtoOALightSymbol](https://github.com/spotware/openapi-proto-messages/blob/main/OpenApiModelMessages.proto)

| Field           | Type   | Required | Description                    |
| --------------- | ------ | -------- | ------------------------------ |
| `symbolId`      | int64  | Yes      | Unique symbol ID (for mapping)  |
| `symbolName`    | string | Yes      | Symbol name (e.g., "EURUSD")    |
| `digits`        | int32  | Yes      | Number of price digits          |
| `pipPosition`   | int32  | Yes      | Pip position in digits          |

---

## Data Extraction Checklist

To prepare demo data, ensure:

- [ ] Each trader has a valid `ctidTraderAccountId`
- [ ] Include **all deals** from the account history (ProtoOADealListReq)
- [ ] Include **all orders** from the account history (ProtoOAOrderListReq)
- [ ] Include **all symbols** referenced in deals/orders (using ProtoOALightSymbol for name mapping)
- [ ] Timestamps are in Unix milliseconds
- [ ] Numeric fields use appropriate types (divide monetary values by 10^moneyDigits)
- [ ] Volume is in cents (divide by 100 to get lots)
- [ ] Include `commission`, `swap` and other monetary fields where available

---

## API Endpoints Reference

Use the following cTrader Open API messages to extract the required data:

Source: [cTrader Open API Messages Documentation](https://help.ctrader.com/open-api/messages/)

### Data Extraction Endpoints

| Data Type        | Request Message                  | Response Message              | Description                       |
| ---------------- | -------------------------------- | ----------------------------- | --------------------------------- |
| Trader/Account   | ProtoOATraderReq                 | ProtoOATraderRes              | Get account details               |
| Trader by ID     | ProtoOATraderByIdReq             | ProtoOATraderRes              | Get account by cTrader ID         |
| Deals            | ProtoOADealListReq               | ProtoOADealListRes            | Get all deals                     |
| Deals by Position| ProtoOADealListByPositionIdReq   | ProtoOADealListRes            | Get deals for specific position   |
| Deals by Order   | ProtoOADealListByOrderIdReq      | ProtoOADealListRes            | Get deals for specific order      |
| Orders           | ProtoOAOrderListReq              | ProtoOAOrderListRes           | Get all orders                    |
| Orders by Position| ProtoOAOrderListByPositionIdReq  | ProtoOAOrderListRes           | Get orders for specific position  |
| Orders by Symbol | ProtoOAOrderListBySymbolIdReq    | ProtoOAOrderListRes           | Get orders for specific symbol    |
| Symbols          | ProtoOASymbolsListReq            | ProtoOASymbolsListRes         | Get all symbols                   |
| Assets           | ProtoOAAssetListReq              | ProtoOAAssetListRes           | Get all assets                    |
| Asset Classes    | ProtoOAAssetClassListReq         | ProtoOAAssetClassListRes      | Get all asset classes             |
| Account History  | ProtoOAAccountHistoryReq         | ProtoOAAccountHistoryRes      | Get account balance history       |
| Cash Operations  | ProtoOADepositWithdrawListReq    | ProtoOADepositWithdrawListRes | Get deposits/withdrawals          |

### Data Extraction Endpoints

- **cTrader Protocol**: Volume is in **cents** (e.g., `1000` = 10.00 lots = 1,000,000 units)
- **Conversion**: `volume / 100 = lots` (e.g., 1000 cents = 10 lots)
- **Money Values**: Divide by `10^moneyDigits` (typically 10^8)

---

## Example JSON Structure

For a complete working example with metadata envelope, see [`examples/sample-data.json`](examples/sample-data.json).

The example file includes:
- Metadata envelope (version, platform, timestamp, purpose)
- Sample trader account with `ctidTraderAccountId: 12345678`
- Sample deals with execution details
- Sample orders with trade data
- Sample symbols for instrument mapping

---

## Contact

For questions regarding this data format, please reach out to the Stackorithm representative or info@stackorithm.co.

_Document Version: 5.0_  
_Platform: cTrader_  
_Last Updated: February 2026_
_Copyright © 2026 Stackorithm LLC. All rights reserved._
