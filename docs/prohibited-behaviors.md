# Prohibited Trading Behaviors Detection

This document explains the trading behaviors that the Stackorithm prop firm platform models are designed to detect using the historical data provided.

---

## Overview

The Stackorithm Trader Risk Analysis Dashboard uses a two-layer analysis process to ensure reliable results. The first layer scans large volumes of trades to identify unusual or potentially risky behavior. The second layer validates these findings by reviewing specific trades and calculating clear, measurable indicators.

The system does not make approval or rejection decisions. Instead, it provides insights, evidence, and recommendations to support human judgment. Final decisions remain with the firm's risk, compliance, or operations teams.

---

## Trader Verdicts & Risk Scores

### Verdict Categories

Every trader receives one of two verdicts based on the analysis:

| Verdict    | Meaning                                              | Visual    |
| ---------- | ---------------------------------------------------- | --------- |
| **SAFE**   | No rule violations detected. Traders can proceed normally. | Green badge |
| **NEED REVIEW** | One or more violations detected. Requires review before approval. | Red badge |

### Risk Score System

The Risk Score is a numerical value from **0 to 10** that quantifies the severity of detected violations. Higher scores indicate more serious violations, stronger evidence, or multiple behavior patterns that require closer attention.

| Score Range | Color  | Interpretation                                    | Review Priority                     |
| ----------- | ------ | ------------------------------------------------- | ----------------------------------- |
| **0.0**     | Gray   | No violations detected. Clean trading record.     | No review needed                    |
| **0.1 - 2.9** | Lime | Minor violations or low confidence flags.         | Low priority: Quick review recommended |
| **3.0 - 4.9** | Amber | Moderate violations.                              | Medium priority: Careful review required |
| **5.0 - 6.9** | Orange | Serious violations. Strong evidence of rule-breaking. | High priority: Detailed investigation needed |
| **7.0+**    | Red   | Critical violations. Multiple severe infractions. | Urgent priority: Immediate attention required |

### How Risk Scores Are Calculated

Each detected behavior contributes points to the total risk score based on:
- **Severity**: How serious the violation is
- **Confidence**: How certain the system is about the detection
- **Evidence Strength**: Amount and quality of supporting evidence
- **Multiple Violations**: Additional points when multiple behaviors are detected

### Behavior Severity Hierarchy

The following behaviors contribute to risk scores with different weights, listed from highest to lowest impact:

**Hedging > Copy Trading > High Frequency > Gambling > Martingale > News Trading**

---

## Detected Behaviors

### 1. Hedging

**What It Is**

Hedging involves opening positions that offset each other in order to significantly reduce or eliminate market exposure. While hedging is a legitimate risk management technique in professional trading, it can be exploited in evaluation environments to create risk-free or near-risk-free trading conditions. This can occur either across multiple accounts (cross-account hedging) or within a single account using correlated instruments (internal hedging / statistical arbitrage).

**Detection Types**

- **Internal Hedging (Statistical Arbitrage)**: Holding positions on correlated instruments (e.g., EURUSD and GBPUSD) that create near net-neutral market exposure
- **Cross-Account Hedging**: Taking opposite positions on the same instrument across two or more accounts controlled by the same person

**Why It's Problematic**

- Eliminates or substantially reduces market risk, making trading risk-free or low-risk
- Defeats the purpose of evaluating trading skill under real market exposure
- Exploits evaluation terms by removing the risk component
- For cross-account: Guarantees one account profits while another loses, then submit only the winning account

**Evidence Provided**

- Timestamped position overlaps
- Correlation statistics and hedge ratios
- Side-by-side examples showing overlapping positions
- Clear overlap periods where exposure was neutralized
- Time Delay showing seconds between entries

---

### 2. Copy Trading

**What It Is**

Copy trading occurs when a trader systematically replicates trade executions from another account, external signal provider, or automated copy engine instead of making independent trading decisions. Replication may be manual or automated and is identified through consistent alignment in timing, direction, and instrument selection.

**Why It's Problematic**

- Trading decisions are derived from external sources rather than the trader's own analysis
- Reliance on external signals creates an inherently unsustainable trading approach
- Performance reflects the signal provider's strategy, not the evaluated trader's ability
- Automated copying tools can introduce execution patterns not representative of live discretionary trading
- Widespread signal replication creates systemic and operational risks

**Evidence Provided**

- **Match Rate**: Percentage of trades that match another account based on instrument, direction, and execution timing
- **Source Account Identification**: Most likely signal-providing account based on temporal alignment
- **Execution Time Delay**: Time difference between source and copy account executions
- **Symbol and Direction Matching**: Confirmation of same instrument and trade direction (BUY/SELL)
- **Price Proximity**: Entry price differences between source and copy trades
- **Execution Pattern Characteristics**: Differences between discretionary vs automated execution

---

### 3. High Frequency Trading

**What It Is**

High frequency and latency-based trading refers to strategies that rely on extremely fast execution, very short holding times, and rapid trade repetition to capture small price movements. Profitability in these strategies is primarily driven by execution speed and technical exploitations rather than market analysis, risk management, or directional skill.

**Why It's Problematic**

- Relies on execution speed advantages rather than decision quality
- Creates unequal trading conditions based on infrastructure, proximity, or automation
- Produces profits that may disappear under normal latency or execution conditions
- Distorts risk and performance metrics due to extremely short exposure times
- Undermines fair comparison between discretionary and automated traders
- Exploits temporary market microstructure effects not intended for skill evaluation
- Not realistically replicable in live markets without specialized infrastructure

**Evidence Provided**

- **Execution Latency**: Time between order submission and execution
- **Latency–Profit Dependency**: Whether profits are disproportionately generated from fastest executions
- **Tick Scalping**: Positions closed below minimum holding time with profit concentration in ultra-short trades
- **Burst Trading Patterns**: Episodes of unusually high trading frequency within short time intervals
- **Trading Session Dependency**: Profit distribution across market sessions
- **Instrument (Symbol) Dependency**: Whether violations are concentrated in specific instruments
- **Time-of-Day Clustering**: Whether trading activity is heavily concentrated within narrow time windows

---

### 4. Gambling Behavior

**What It Is**

Gambling behavior refers to emotional, impulsive trading decisions without a consistent, disciplined approach or legitimate trading methodology. This includes erratic position sizing, poor risk management practices, and trading patterns that resemble casino-style gambling rather than professional trading with a repeatable edge.

**Why It's Problematic**

- Violates the requirement for a legitimate, repeatable trading approach
- Creates unsustainable risk exposure that leads to account destruction
- Results driven by luck and emotion rather than skill or analysis
- Likely to lead to significant losses in live trading

**Evidence Provided**

- **Position Size Variability**: Large and inconsistent changes in position size across trades
- **Risk Management**: Repeated absence of stop losses, acceptance of outsized losses
- **Holding Time Distribution**: Extremely short or highly inconsistent holding periods
- **Loss Chasing Patterns**: Systematic increases in position size following losing trades
- **Overconfidence Scaling**: Disproportionate increases in risk following winning streaks
- **Deadline Driven Trading**: Sudden spikes in trade frequency and risk near evaluation deadlines
- **Symbol Selection**: Frequent switching across many instruments without observable focus

---

### 5. Martingale Pattern

**What It Is**

A Martingale pattern occurs when a trader repeatedly opens additional positions on the same instrument and in the same direction while existing positions are in floating loss. The intent is to improve the average entry price or recover losses by increasing exposure instead of accepting a loss. This behavior results in rapidly escalating and concentrated risk.

**Why It's Problematic**

- Risk exposure increases non-linearly as positions are added during adverse price movement
- A single extended losing sequence can lead to unrecoverable drawdowns or account failure
- Losses are deferred rather than managed, obscuring poor entry decisions during evaluation
- The strategy implicitly assumes access to unlimited or near-unlimited capital
- Such strategies cannot be sustained under realistic margin, drawdown, and risk constraints

**Evidence Provided**

- **Sequence Count**: Number of distinct Martingale sequences identified across instruments and directions
- **Total Adds in Loss**: Total number of positions opened while existing positions were in floating loss
- **Maximum Sequence Depth**: Largest number of consecutive position additions within a single losing sequence
- **Floating Loss Rate**: Percentage of added positions that occurred while the account was in floating loss
- **Volume Escalation Pattern**: How position sizes changed across the sequence (fixed, doubling, exponential, Fibonacci)

---

### 6. News Trading

**What It Is**

News trading involves taking positions immediately before, during, or after major economic news releases (e.g., Non-Farm Payrolls, Federal Reserve announcements, GDP reports). These events typically cause sudden spikes in volatility, rapid price movements, and temporary liquidity imbalances.

**Why It's Problematic**

- Creates extreme slippage and execution instability
- Price movements are highly unpredictable and random
- Trading outcomes are more luck-based than skill-based
- Difficult to provide fair execution during high volatility
- Risk metrics (drawdown, expectancy, consistency) become unreliable

**Evidence Provided**

- **News Execution Flags**: Whether trading activity occurred within restricted news windows
- **Violation Instances**: Number of detected news-related trading occurrences with representative examples
- **News-Linked Trade Context**: Selected trades or positions aligned with specific news events
- **Profit Concentration**: Share of total profit generated during news periods vs overall performance
- **News vs Non-News Performance**: Profitability during news events vs normal market conditions

---

## Data Quality Requirements

To ensure accurate behavior detection, the provided data must:

### Completeness
- ✅ Include **all** deals from account history (no filtering)
- ✅ Include **all** orders (placed, filled, cancelled, rejected)
- ✅ Include **all** historical positions and snapshots
- ✅ Include complete symbol/contract/product metadata

### Accuracy
- ✅ Timestamps must be accurate and properly formatted
- ✅ Position sizes and volumes must be precise
- ✅ Enum values must use correct integer codes
- ✅ No missing or null required fields

### Consistency
- ✅ Deal IDs unique and sequential
- ✅ Position IDs correctly link deals
- ✅ Order-to-deal relationships maintained
- ✅ Symbol names match across deals and metadata

---

## Detection Process

1. **Data Ingestion**: Parse historical data from provided JSON files
2. **Feature Extraction**: Calculate behavioral indicators (frequency, size, correlation)
3. **Pattern Recognition**: ML models identify prohibited behavior patterns
4. **Risk Assessment**: Score accounts based on violation severity and frequency
5. **Reporting**: Generate detailed reports with evidence and timestamps

---

## Frequently Asked Questions

**Q: Can a trader dispute a violation finding?**
Yes. While the statistical evidence is objective, traders should have the opportunity to provide context or explain unusual circumstances. The burden of proof is on the trader to explain why their behavior differs from professional trading norms.

**Q: What if a trader has multiple low-severity violations?**
The risk score accounts for this. Multiple minor violations will accumulate into a higher overall risk score.

**Q: How often should the analysis be run?**
This depends on operational needs. Common schedules include: before each payout cycle, daily for active traders, or on-demand when traders request withdrawals.

**Q: What if a trader is flagged but has been profitable?**
Profitability does not excuse rule violations. Many prohibited strategies can produce short-term profits but are unsustainable and violate evaluation terms.

**Q: Can the system be wrong?**
The system uses rigorous statistical methods and provides confidence levels for each finding. False positives are minimized through conservative thresholds and multiple evidence requirements.

**Q: What about traders with very few trades?**
The system requires minimum sample sizes for each behavior (typically 5-30 trades). If a trader has insufficient data, they will show as SAFE with appropriate sample size indicators.

---

## Contact

For questions about behavior detection or data requirements:
📧 **info@stackorithm.co**

---

_Document Version: 2.0_  
_Last Updated: February 2026_  
_Copyright © 2026 Stackorithm LLC. All rights reserved._
