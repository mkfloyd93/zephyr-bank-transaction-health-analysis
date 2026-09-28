# Actionable Insights

The analysis identified three areas where Zephyr Bank has a clear opportunity to reduce risk, validate revenue performance, and improve transaction reliability. Because several patterns in the dataset are unusually concentrated, the recommendations prioritize investigation and validation before broader policy or product changes are made.

---

## 1. Prioritize investigation of concentrated customer-level fraud exposure

### What was discovered

Fraud exposure is highly concentrated rather than broadly distributed across Zephyr Bank's customer base.

- **300 of 1,500 transactions (20%)** are fraud flagged.
- All 300 fraud-flagged transactions belong to only **four of the 20 customers**: Charlotte Lewis, Daniel Okafor, Mohammed Al-Hassan, and Rajan Mehta.
- Every transaction for each of these four customers is fraud flagged, resulting in a **100% observed fraud rate**.
- **Rajan Mehta accounts for every £12K+ transaction** in the dataset, and all of his transactions are fraud flagged.
- Merchant risk classifications provide comparatively little differentiation: observed fraud rates are 22.30% for Medium, 19.88% for High, and 18.97% for Low.

### Why it matters

The customer-level concentration provides a more useful basis for investigation than broad merchant-risk, geographic, or customer-segment classifications.

For example, Premium customers appear to have elevated fraud exposure at the segment level, but deeper analysis shows that the result is heavily influenced by specific customers. Acting on the segment-level pattern alone could therefore lead Zephyr to apply controls too broadly while overlooking the much stronger customer-level signal.

Rajan Mehta warrants particular attention because the same customer combines **100% fraud-flagged activity with the entire population of unusually large £12K+ transactions**.

### Recommended next steps

1. **Prioritize the four customers responsible for all fraud flags for case review**, beginning with Rajan Mehta because of the additional large-value exposure.
2. Review transaction histories, fraud-rule triggers, account activity, authentication events, and confirmed fraud outcomes for these customers.
3. Determine whether the 100% fraud rates reflect legitimate fraud detection, an overly broad rule, duplicated/systematic flags, or a data-generation or processing issue.
4. Compare confirmed fraud outcomes with the current `is_flagged_fraud` indicator before changing broader merchant, segment, or regional risk policies.

### Expected impact

A targeted review should allow Risk teams to focus investigative resources on the strongest observed concentration rather than applying additional controls across large customer populations.

If the fraud flags are valid, this could enable faster intervention on high-exposure accounts. If the concentration reflects a rule or data-quality problem, identifying it could reduce unnecessary investigations and improve the reliability of downstream fraud reporting.

### Deeper investigation / additional data needed

- Confirmed fraud outcomes and investigation dispositions
- Fraud-rule or model triggers associated with each flag
- Authentication and login activity
- Historical customer transaction behavior outside the January–May period
- Account restrictions, alerts, or previous fraud cases
- Additional information on the intended meaning and use of merchant `risk_flag`

---

## 2. Validate fee application before treating pricing deviations as revenue leakage

### What was discovered

Zephyr Bank generated **£750.17 in fee revenue** from January through May, with monthly fee revenue remaining relatively stable despite changes in transaction activity.

However, actual fees differ systematically from `typical_fee_gbp`:

- International transactions were charged a net **£487.11 below typical fees**.
- Domestic transactions were charged a net **£487.00 above typical fees**.
- The differences are distributed across many relatively small transaction-level deviations rather than a few large anomalies.
- Below-typical charges are most concentrated in **Charity & Donations, Utilities, and Clothing & Fashion**.
- Above-typical charges are most concentrated in **Entertainment, Fuel, and Subscription Services**.
- Business customers generate approximately **£1.00 in fee revenue per transaction**, compared with approximately £0.50 for Starter and Premium and £0.17 for Standard.

### Why it matters

The nearly equal but opposing domestic and international patterns suggest that fee differences may be systematic rather than random.

However, `typical_fee_gbp` does not establish the fee that Zephyr was contractually required to charge. The observed £487.11 international difference therefore **cannot currently be treated as confirmed lost revenue**, just as the domestic difference cannot automatically be classified as overcharging.

The business risk is two-sided: Zephyr could be missing legitimate fee revenue, or actual pricing could be operating correctly while reporting lacks the information needed to explain why charges differ from typical pricing.

### Recommended next steps

1. **Validate actual transaction fees against Zephyr's pricing and waiver rules**, beginning with international transactions and the merchant categories showing the largest below-typical deviations.
2. Identify whether each deviation is explained by an approved waiver, customer-specific pricing, promotion, product rule, or other legitimate adjustment.
3. Separate **expected deviations from unexplained deviations** once the applicable pricing rules are available.
4. Quantify recoverable revenue only for transactions where the expected fee can be established and the actual fee was incorrectly applied.
5. If unexplained patterns remain, review fee-calculation and application logic for domestic and international transactions separately.

### Expected impact

This investigation would allow Finance to distinguish normal pricing behavior from genuine fee-application errors.

If errors are identified, correcting them could recover future fee revenue and improve pricing consistency. If the differences are intentional, documenting the applicable pricing logic would improve the accuracy of financial reporting and prevent legitimate fee adjustments from being incorrectly reported as leakage.

### Deeper investigation / additional data needed

- Formal fee schedules and pricing rules
- Customer- and product-specific pricing agreements
- Fee-waiver rules and waiver indicators
- Promotional or discounted pricing
- Expected fee at the individual transaction level
- Reason codes for fee adjustments or waivers
- Historical fee performance to determine whether the pattern predates the analysis period

---

## 3. Validate transaction processing and device data before making UX changes

### What was discovered

Transaction reliability is poor across the supplied dataset:

- Only **225 of 1,500 transactions (15%)** reached Completed status.
- **900 transactions (60%)** were Declined or Reversed under the analysis's failure definition.
- Card Refund, Loan Repayment, and Standing Order have the highest failure rates at **75%**.
- Failure rates remain high across all channels at approximately **58–63%**, with no single channel clearly explaining the issue.

The strongest operational pattern appears at the device level:

- All 375 Android transactions are Pending.
- All 375 Web transactions are Reversed.
- All 375 transactions with blank `device_type` are Declined.
- iOS is the only device group containing Completed transactions, with 225 Completed and 150 Declined.

The dataset also contains **375 blank device values**, although the supplied data dictionary documents N/A rather than blank as the expected value when a device is unavailable.

### Why it matters

At face value, the failure rates could suggest substantial UX or transaction-processing problems. However, the deterministic relationship between device and transaction status is unusual enough that immediately redesigning individual customer journeys would be premature.

The pattern could reflect actual processing behavior, but it could also result from status mapping, device instrumentation, missing data, upstream processing, or the structure of the supplied dataset.

Until that relationship is understood, Zephyr cannot confidently determine whether high failure rates represent a product problem or a data/processing problem.

### Recommended next steps

1. **Validate the source and mapping of `device_type` and `transaction_status` before making UX changes.**
2. Determine why blank device values are being recorded instead of the documented N/A value and whether those records share a common source system or processing path.
3. Investigate why Android transactions remain entirely Pending and Web transactions entirely Reversed.
4. Confirm whether transaction statuses represent final outcomes or point-in-time snapshots.
5. Once the data is validated, investigate **Card Refund, Loan Repayment, and Standing Order** first because they have the highest observed failure rates.
6. Review failure reasons within those transaction types to identify specific processing or customer-experience issues.

### Expected impact

Validating the underlying data and processing flow first reduces the risk of investing in UX changes that do not address the actual source of transaction failures.

If the device/status relationship reflects a technical or mapping issue, correcting it should improve the reliability of operational reporting and downstream analysis. If the pattern is genuine, Zephyr can then target the specific processing paths and transaction types associated with unsuccessful outcomes.

### Deeper investigation / additional data needed

- Source-system definitions for `device_type` and `transaction_status`
- Status history showing how transactions move from Pending to final outcomes
- Device and channel instrumentation documentation
- Reason for missing device values
- Detailed decline and reversal reason codes
- Processing-system or API error logs
- Customer journey and abandonment data
- Transaction retry behavior
- Historical failure-rate benchmarks

---

## Additional Areas to Monitor

The analysis identified several secondary patterns that do not currently justify standalone interventions but should be monitored or revisited as additional data becomes available.

**International transaction risk:** International transactions have a higher average value and observed fraud rate than domestic transactions, while also showing the strongest below-typical fee pattern. These overlapping characteristics make international activity a useful area for continued monitoring, although the current data does not establish a common cause.

**KYC outcomes:** No fraud flags were observed among non-KYC customers, while all observed fraud occurred among KYC-verified customers. This does not indicate that KYC verification increases risk; instead, it shows that KYC status does not explain fraud concentration in the current sample. Additional customer populations and confirmed fraud outcomes would be needed before drawing compliance conclusions.

**Regional patterns:** Fraud activity is concentrated in London and the Midlands, but the concentration is largely explained by the four fraud-flagged customers. Regional controls should therefore not be changed based on the current sample alone.

**Time-based patterns:** Fraud and decline rates fluctuate throughout the analysis period without a sustained weekly or time-of-day concentration. There is currently no strong evidence supporting time-based intervention.

---

## Recommended Priority

Based on the strength of the observed patterns and the information currently available, the immediate priorities are:

1. **Risk:** Investigate the four customers responsible for all fraud flags, with particular attention to Rajan Mehta's large-value activity.
2. **Finance:** Reconcile actual fees with applicable pricing and waiver rules to determine whether the domestic/international deviations are intentional or erroneous.
3. **Product & Operations:** Validate device and transaction-status data before using the observed failure patterns to drive UX or processing changes.

These actions focus first on the areas with the strongest evidence while identifying the additional data required before Zephyr makes broader customer, pricing, risk, or product-policy changes.