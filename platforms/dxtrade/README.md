# DXtrade Data Requirements

This directory contains the data format specification for **DXtrade** platform integration with the prop firm trading platform.

---

## Overview

The DXtrade data format captures complete trading account history including:

| Component       | Description                                                                       |
| --------------- | --------------------------------------------------------------------------------- |
| **Instruments** | Global instrument/symbol catalogue                                                |
| **Orders**      | Historical orders (working and final state) with executions and cash transactions |
| **Transfers**   | Deposit, withdrawal, and other cash transfer history per account                  |

---

## Quick Reference

### Account Structure

- `account`: Composite account identifier in `domain:login` format (e.g., `default:12345`)

### Instrument Data Points

- Symbol, type, description, lot size
- Price increment, pip size, multiplier
- Currency, asset class, trading hours

### Order Data Points

- Order ID, order code, client order ID
- Instrument, type (Market, Limit, Stop), side (Buy/Sell)
- Status, final status flag, TIF
- Issue time, transaction time
- Legs, executions, cash transactions, audit info

### Transfer Data Points

- Transfer code, account, comment
- Transaction time, created time
- Associated cash transactions (type, value, currency)

---

## Documentation

- **[Full Specification](data-requirements.md)** - Complete field definitions, enum values, and validation checklist
- **[Example Data](examples/sample-data.json)** - Working JSON example with metadata envelope

---

## Platform-Specific Notes

### Authentication

- Login via the Accounts API to obtain a session token
- All subsequent requests require `Authorization: DXAPI {token}` header

### API Endpoints

| Type         | Base URL                               |
| ------------ | -------------------------------------- |
| Trading API  | `https://demo.dx.trade/dxsca-web`      |
| Accounts API | `https://demo.dx.trade/dxweb/rest/api` |

### Nested Structures

Orders contain nested sub-structures that should be flattened for columnar storage:

| Sub-structure      | Prefix               | Description                                 |
| ------------------ | -------------------- | ------------------------------------------- |
| `legs`             | `legs_*`             | Order leg (price, quantity, fill info)      |
| `executions`       | `executions_*`       | Execution records per order                 |
| `cashTransactions` | `cashTransactions_*` | Cash transactions per order                 |
| `audit`            | `audit_*`            | IP address and user agent at order issuance |

---

## Data Extraction Checklist

- [ ] `ORDERS_FROM` and `TRANSFERS_FROM` set to the platform launch date (e.g., `2025-01-01`)
- [ ] Each order deduplicated by `orderId`
- [ ] Each transfer deduplicated by `transferCode`
- [ ] Timestamps in ISO 8601 format (e.g., `"2025-03-14T09:30:00.000Z"`)
- [ ] Numeric fields use appropriate types (float for prices/amounts, integer for counts)
- [ ] Nested sub-structures flattened into columnar form
- [ ] Account identifiers use `domain:login` composite format throughout

---

## Contact

For questions regarding DXtrade data format:  
**info@stackorithm.co**

---

_Document Version: 1.0_  
_Platform: DXtrade_  
_Last Updated: April 2026_
