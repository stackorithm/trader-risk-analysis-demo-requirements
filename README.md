# Trader Risk Analysis - Demo Data Requirements

This repository defines the data format specifications required for demonstrating the trader risk analysis models with real trader historical data. The data is used as input for models that detect prohibited trading behaviors.

---

## ⚠️ Risk Disclaimer

**This documentation are provided for demonstration purposes only.**

- The data format specifications provided herein are for demonstration and testing purposes.
- Past performance or historical trading data does not guarantee future results.
- Users are responsible for ensuring compliance with all applicable laws and regulations in their jurisdiction.
- Stackorithm LLC assumes no liability for any use or misuse of this information.

---

## Overview

The demo data should contain a list of traders, where each trader includes their complete historical trading activity. This data is analyzed by models designed to detect prohibited trading behaviors described in our documentation [here](docs/prohibited-behaviors.md).

## Current Supported Platforms

| Platform         | Documentation                                                    | Example Data                                                        |
| ---------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------- |
| **MetaTrader 5** | [View Specification](platforms/metatrader5/data-requirements.md) | [sample-data.json](platforms/metatrader5/examples/sample-data.json) |
| **cTrader**      | [View Specification](platforms/ctrader/data-requirements.md)     | [sample-data.json](platforms/ctrader/examples/sample-data.json)     |
| **Tradovate**    | [View Specification](platforms/tradovate/data-requirements.md)   | [sample-data.json](platforms/tradovate/examples/sample-data.json)   |

**📌 Don't see your platform?** If your trading platform isn't listed above or your data format differs from our specifications, don't worry, we're flexible and can adapt to your format. **Contact us with a raw sample of your data** and we'll work with you to integrate it. Our goal is to ensure low friction and easy data extraction from your side.

---

## Directory Structure

```
demo/
├── docs/
│ └── prohibited-behaviors.md # Behavior detection guide
├── platforms/
│ ├── ctrader/
│ │ ├── README.md
│ │ ├── data-requirements.md
│ │ └── examples/
│ │ └──── sample-data.json
│ ├── metatrader5/
│ │ ├── README.md
│ │ ├── data-requirements.md
│ │ └── examples/
│ │ └──── sample-data.json
│ └── tradovate/
│ ├──── README.md
│ ├──── data-requirements.md
│ └──── examples/
│ └────── sample-data.json
└── README.md # This file
```

---

## Quick Start

1. **Choose your platform** from the supported platforms list above
2. **Review the data requirements** for your platform
3. **Prepare your data** according to the specification
4. **Validate against the example** in the \`examples/\` directory
5. **Contact us** when your data is ready for demonstration

---

## Data Format

All platforms require a root JSON structure containing an array of traders. Each trader object includes:

- **Account identifier** - Unique ID for the trading account (no PII required)
- **Historical deals/trades** - All executed transactions
- **Order history** - All orders (pending, filled, cancelled)
- **Platform-specific data** - Symbols, positions, contract sizes, etc.

See individual platform documentation for detailed field specifications.

---

## Example Data Format

Each example JSON file contains a root object with a `traders` array. Each trader object includes:

- Account identifier (e.g., `login`, `accountId`, `ctidTraderAccountId`)
- Complete historical deals/fills/positions
- Complete order history
- Platform-specific metadata (symbols, contracts, products, etc.)

See the example files for reference:

- [MetaTrader 5 Example](platforms/metatrader5/examples/sample-data.json)
- [cTrader Example](platforms/ctrader/examples/sample-data.json)
- [Tradovate Example](platforms/tradovate/examples/sample-data.json)

---

## Additional Documentation

- [Prohibited Trading Behaviors](docs/prohibited-behaviors.md) - Learn about copy trading, hedging, martingale, and other prohibited behaviors our models detect

## Contact

For questions regarding this data format, to schedule a demonstration, or if your platform/format isn't listed above:

📧 **Email:** info@stackorithm.co
🌐 **Website:** [Stackorithm](https://stackorithm.co)

**We're flexible!** If your data format differs from our specifications, send us a raw sample and we'll adapt to your needs.

---

## Version & Platform Information

- **Document Version:** 1.0
- **Last Updated:** February 2026
- **Copyright:** Stackorithm LLC All rights reserved.
