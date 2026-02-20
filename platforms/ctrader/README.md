# cTrader Data Requirements

This directory contains the data format specification for **cTrader** platform integration with the prop firm trading platform.

---

## Overview

The cTrader data format captures complete trading account history including:

| Component | Description |
|-----------|-------------|
| **Traders** | List of cTrader accounts (identified by `ctidTraderAccountId`) |
| **Deals** | All executed trades with commission, margin info |
| **Orders** | Order history with execution details |
| **Symbols** | Trading instrument metadata (symbols, digits, pip position) |

---

## Quick Reference

### Trader Account Structure
- `ctidTraderAccountId`: cTrader account ID (e.g., `12345678`)
- `balance`: Account balance in cents (scaled by `moneyDigits`)

### Deal Data Points
- Trade ID, symbol ID, volume, trade side
- Execution timestamps, deal status
- Commission, margin rate, money digits
- Position labels, comments

### Order Data Points
- Order ID, order type, order status
- Position ID, executed volume, execution price
- Trade data (symbol, volume, side, timestamps)
- Last update timestamp

### Symbol Data Points
- Symbol ID, symbol name (e.g., "EURUSD")
- Digits (decimal precision), pip position

---

## Documentation

- **[Full Specification](data-requirements.md)** - Complete field definitions, enum values, and API message references
- **[Example Data](examples/sample-data.json)** - Working JSON example with metadata envelope

---

## Platform-Specific Notes

### API Integration
- cTrader data format aligns with **ProtoOA** API messages
- Field names match ProtoOA schema conventions
- See [data-requirements.md](data-requirements.md) for API endpoint references

### Timestamps
- All timestamps in **milliseconds** (Unix epoch)
- Example: `1722556800000` = August 2, 2024

### Volume & Balance
- Volume in base currency units (not lots)
- Balance in cents (scaled by `moneyDigits` field)
- Example: `balance: 1000000` = $10,000.00 with `moneyDigits: 8`

### Required Enums
- Trade Side: 1=Buy, 2=Sell
- Order Type: 0=Market, 1=Limit, 2=Stop, etc.
- Order Status: 0=Submitted, 1=Accepted, 2=Filled, etc.
- Deal Status: Various execution states

See [data-requirements.md](data-requirements.md) for complete enum tables.

---

## Data Extraction Checklist

- [ ] Each trader has valid `ctidTraderAccountId`
- [ ] Include **all deals** from account history
- [ ] Include **all orders** from account history
- [ ] Include **symbol metadata** for traded instruments
- [ ] Timestamps in milliseconds
- [ ] Balance properly scaled (cents × moneyDigits)
- [ ] Enum fields use integer values

---

## Contact

For questions regarding cTrader data format:  
📧 **info@stackorithm.co**

---

_Document Version: 5.0_  
_Platform: cTrader_  
_Last Updated: February 2026_
