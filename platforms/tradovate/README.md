# Tradovate Data Requirements

This directory contains the data format specification for **Tradovate** platform integration with the prop firm trading platform.

---

## Overview

The Tradovate data format captures complete trading account history including:

| Component | Description |
|-----------|-------------|
| **Traders** | List of Tradovate accounts (identified by `accountId`) |
| **Fills** | All executed trade fills with price, quantity, commission |
| **Orders** | Order history with order status and details |
| **Positions** | Historical position snapshots with net position data |
| **Contracts** | Contract/instrument symbol mapping |
| **Products** | Product metadata for contract size and tick size |

---

## Quick Reference

### Trader Account Structure
- `accountId`: Tradovate account ID (e.g., `12345`)

### Fill Data Points
- Fill ID, account ID, contract ID
- Timestamp, trade date (year/month/day)
- Quantity, price, buy/sell direction
- Execution ID, order ID, cumulative quantity
- Commission

### Order Data Points
- Order ID, account ID, contract ID
- Timestamp, action timestamp
- Contract symbol, order type (Market, Limit, Stop)
- Order quantity, status, order status

### Position Data Points
- Position ID, account ID, contract ID
- Timestamp, trade date
- Net position, net price
- Bought/sold quantities and values
- Previous position and price

### Contract & Product Data Points
- Contract: ID, name (e.g., "MESM4")
- Product: ID, name (e.g., "MES"), value per point, tick size

---

## Documentation

- **[Full Specification](data-requirements.md)** - Complete field definitions, enum values, and validation checklist
- **[Example Data](examples/sample-data.json)** - Working JSON example with metadata envelope

---

## Platform-Specific Notes

### Timestamp Formats
- **Timestamps**: ISO 8601 format (e.g., `"2024-08-24T14:15:22Z"`)
- **Trade Date**: Object with `year`, `month`, `day` fields

### Contract/Product Hierarchy
1. **Products** define instrument properties (e.g., "MES" = Micro E-mini S&P 500)
   - `valuePerPoint`: Dollar value per tick (e.g., 5.0)
   - `tickSize`: Minimum price movement (e.g., 0.25)

2. **Contracts** are product instances (e.g., "MESM4" = MES expiring June 2024)
   - Mapped to products via `contractId` → `productId`

3. **Fills/Orders/Positions** reference contracts
   - Use `contractId` to look up contract symbol
   - Use contract to look up product for contract size

### Futures Trading
- Tradovate specializes in futures contracts
- Quantity represents contract count (not lots or units)
- Products define contract multiplier (`valuePerPoint`)

---

## Data Extraction Checklist

- [ ] Each trader has valid `accountId`
- [ ] Include **all fills** from account history
- [ ] Include **all orders** from account history
- [ ] Include **historical positions** snapshots
- [ ] Include **contract metadata** for traded instruments
- [ ] Include **product metadata** for contract size calculations
- [ ] Timestamps in ISO 8601 format
- [ ] Trade dates as year/month/day objects
- [ ] Contract-to-product relationships maintained

---

## Contact

For questions regarding Tradovate data format:  
📧 **info@stackorithm.co**

---

_Document Version: 5.0_  
_Platform: Tradovate_  
_Last Updated: February 2026_
