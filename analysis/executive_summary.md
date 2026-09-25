# Executive Summary

Analysis of 1,500 Zephyr Bank transactions from January through May 2026 identified concentrated fraud exposure, systematic fee-pricing deviations, and unusual transaction-outcome patterns that warrant further investigation. While several broader trends were observed across customer segments, transaction types, and regions, the strongest signals were concentrated within specific customers and transaction characteristics.

## Key Findings

### Fraud exposure is highly concentrated among a small number of customers

Approximately 20% of all transactions were fraud-flagged, but all 300 fraud flags originated from just four of the bank's 20 customers. Each of these customers had a 100% fraud-flag rate. Rajan Mehta represents a particularly significant value exposure: every transaction in the distinct £12K+ transaction group belongs to this customer, and all are fraud-flagged.

This customer-level concentration is substantially stronger than the fraud differences observed across merchant categories, devices, or customer segments.

### Fee application shows systematic deviations from typical pricing

Zephyr Bank generated £750.17 in fee revenue from January through May 2026. Actual fees frequently differed from the `typical_fee_gbp` associated with each transaction type.

International transactions were £487.11 below typical pricing on a net basis, while domestic transactions were £487.00 above typical pricing. Below-typical pricing was particularly concentrated in Charity & Donations, Utilities, and Clothing & Fashion.

Based on the January–May average monthly fee revenue of approximately £150.03, H2 fee revenue is projected at approximately £900.20 if the observed run rate continues. This is a simple run-rate projection and does not account for seasonality, changes in transaction volume or mix, or changes to fee application.

Because the supplied data defines transaction-type fees as *typical* rather than required, observed deviations cannot yet be classified as confirmed revenue leakage or overcharging.

### Transaction outcomes have an unusually deterministic relationship with device type

Each device group contains exactly 375 transactions, yet transaction outcomes differ dramatically. All Android transactions are Pending, all Web transactions are Reversed, and all transactions with a missing device type are Declined. iOS is the only device group containing Completed transactions.

The pattern is sufficiently pronounced to warrant validation of the underlying data and transaction-processing behavior before attributing the differences to customer device choice or using them to guide UX decisions.

### KYC status does not identify the fraud exposure observed in this dataset

All fraud-flagged transactions belong to KYC-verified customers, while no fraud flags were observed among non-KYC customers. KYC-verified transactions also had a higher decline rate of approximately 37.5%, compared with approximately 25% for non-KYC transactions.

These results do not indicate that KYC verification causes higher risk, but they show that non-KYC status alone would not have identified the customers driving observed fraud exposure.

### Transaction failures are high across transaction types, but no individual channel clearly explains the issue

Card Refund, Loan Repayment, and Standing Order have the highest failure rates at 75%, while even lower-failure transaction types remain around 50%.

At the channel level, failure rates are comparatively similar at approximately 58–63%, suggesting that the overall failure issue is not isolated to a single channel.

## Top 3 Recommendations

### 1. Prioritize investigation of concentrated customer-level fraud exposure

Investigate the four customers responsible for all fraud-flagged activity, with particular attention to Rajan Mehta's large-value transactions. Determine whether existing fraud controls appropriately escalate repeated flagged activity and whether additional customer- or transaction-level controls are warranted.

### 2. Validate fee application against actual pricing and waiver rules

Determine whether the systematic domestic and international fee deviations, as well as concentrations within specific merchant categories, reflect intentional pricing, approved waivers, or fee-application errors.

Quantify recoverable revenue only after the expected fee for each transaction can be established. Any validated changes to fee application should also be incorporated into future revenue projections rather than assuming the current £900.20 H2 run rate will remain unchanged.

### 3. Investigate the device/status relationship and underlying device data quality

Validate the `device_type` field and determine whether the deterministic relationship between device and transaction status reflects genuine transaction-processing behavior, data mapping or instrumentation issues, or another underlying cause.

The dataset contains undocumented blank `device_type` values across multiple transaction types. These records were retained as unknown rather than converted to N/A because their intended meaning could not be reliably inferred from the supplied documentation. Because all transactions with a missing device type are Declined, resolving this data-quality issue should be part of the investigation before device-level product decisions are made.