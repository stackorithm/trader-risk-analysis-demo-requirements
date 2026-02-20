# MetaTrader 5 Data Requirements

This directory contains the data format specification for **MetaTrader 5** platform integration with the prop firm trading platform.

---

## Overview

The MetaTrader 5 data format captures complete trading account history including:

| Component | Description |
|-----------|-------------|
| **Traders** | List of MT5 accounts (identified by `login`) |
| **Deals** | All executed trades with profit/loss, commission, fees |
| **Orders** | Order history (market, limit, stop orders) |

---

## Quick Reference

### Trader Account Structure
- `login`: MT5 account number (e.g., `93940`)

### Deal Data Points
- Execution timestamp, deal ID, position ID
- Entry direction (IN, OUT, INOUT, OUT_BY)
- Deal action (Buy, Sell, Balance, etc.)
- Profit, commission, storage (swaps), fees
- Symbol, volume (lots), contract size, price
- Stop Loss, Take Profit, comments, reasons

### Order Data Points
- Order ID, position ID, symbol
- Order type (Market, Limit, Stop, etc.)
- Order state (Started, Placed, Filled, Canceled, etc.)
- Volume in units, timestamps in milliseconds
- Order price, trigger price, comments

---

## Documentation

- **[Full Specification](data-requirements.md)** - Complete field definitions, enum values, and validation checklist
- **[Example Data](examples/sample-data.json)** - Working JSON example with metadata envelope

---

## Platform-Specific Notes

### Volume Units
- **Deals**: Volume in **lots** (e.g., `1.02`)
- **Orders**: Volume in **units** (e.g., `102000`)
- Relationship: `volume_units = volume_lots × 100,000`

### Timestamps
- Deals: Unix timestamp in **seconds**
- Orders: Unix timestamp in **milliseconds**

### Required Enums
- Deal Actions (`action`): 0=Buy, 1=Sell, 2+=Balance operations
- Deal Entry (`entry`): 0=IN, 1=OUT, 2=INOUT, 3=OUT_BY
- Deal Reasons (`reason`): 0=Client, 1=Expert, 3=SL, 4=TP, 5=SO, etc.
- Order Types (`type`): 0=Buy, 1=Sell, 2=BuyLimit, 3=SellLimit, etc.
- Order States (`state`): 0=Started, 1=Placed, 4=Filled, 2=Canceled, etc.

See [data-requirements.md](data-requirements.md) for complete enum tables.

---

## Data Extraction Checklist

- [ ] Each trader has valid `login` number
- [ ] Include **all deals** from account history
- [ ] Include **all orders** from account history
- [ ] Timestamps in correct format (seconds vs milliseconds)
- [ ] Numeric fields use correct types (float vs integer)
- [ ] Use default values for optional fields (`storage`: 0.0, `fee`: 0.0, etc.)
- [ ] Enum fields use integer values (not string names)

---

## Contact

For questions regarding MetaTrader 5 data format:  
📧 **info@stackorithm.co**

---

_Document Version: 2.0_  
_Platform: MetaTrader 5_  
_Last Updated: February 2026_
