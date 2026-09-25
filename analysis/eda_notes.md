# Exploratory Data Analysis Notes

Working notes captured during exploratory analysis. These observations are preliminary and may not appear in the final analysis.

## Data Quality Observations

### Undocumented blank device types
- `device_type` contains blank values that are not documented in the data dictionary.
- The data dictionary lists expected values as iOS, Android, Web, and N/A for automated/ATM transactions.
- Blank values occur across multiple transaction types, including transaction types that are not ATM or automated.
- The blanks were retained as unknown rather than being converted to N/A because their meaning could not be reliably inferred.

**Follow-up:** Determine whether missing device information is associated with transaction outcomes, fraud flags, or particular transaction types if it becomes relevant to the analysis.

## Transaction Health Observations

### Fraud flags represent a substantial share of transactions
- Approximately 20% of transactions are flagged as potentially fraudulent.
- This represents roughly one in five transactions in the dataset.
- There is currently no benchmark in the supplied materials establishing what Zephyr Bank considers a normal or acceptable fraud-flag rate.
- A fraud flag represents potential fraud, not necessarily confirmed fraud.

**Follow-up:** Investigate whether fraud flags are concentrated by merchant category, transaction type, customer segment, age band, region, or other transaction characteristics.

### Fraud rates vary substantially by merchant category

- Fraud rates are not evenly distributed across merchant categories.
- Nine categories — Fuel, Online Retail, Electronics, Gambling, Loan & Credit, Dining & Restaurants, Entertainment, Education, and Subscription Services — have fraud rates clustered around 29–31%.
- The remaining categories have substantially lower fraud rates, clustered around 10–11%.
- The sharp separation between these groups is more notable than the relatively small differences among categories within each group.
- Some results were unexpected based on initial assumptions, particularly the high fraud rates for Fuel and Education and the comparatively low rate for Clothing & Fashion.
- The current analysis shows an association between merchant category and fraud-flag rate but does not explain why these differences exist.

**Follow-up:** Investigate whether other merchant-category characteristics, such as `risk_flag`, correspond with the apparent high- and low-fraud clusters.

### Merchant risk flags do not clearly align with observed fraud rates

- Merchant-category `risk_flag` does not show a clear relationship with observed fraud-flag rates.
- Aggregate fraud rates are similar across all three risk classifications:
  - High risk: 19.88%
  - Medium risk: 22.30%
  - Low risk: 18.97%
- Medium-risk categories have the highest aggregate fraud rate, while high-risk categories do not show a substantially higher fraud rate than low-risk categories.
- The previously observed merchant-category clusters around approximately 29–31% and 10–11% fraud rates contain categories from multiple risk classifications.
- Several categories classified as Low risk appear in the higher fraud-rate cluster.
- Based on this dataset, `risk_flag` does not appear to explain the large differences in fraud rates observed between merchant categories.

**Follow-up:** Investigate other transaction and customer characteristics to determine whether they better explain the high- and low-fraud merchant-category clusters. The definition and intended use of `risk_flag` would also need to be understood before concluding that the classifications themselves are inaccurate.

### Missing device type is associated with elevated fraud rates

- Transactions with a blank `device_type` have an observed fraud rate of approximately 40%, compared with approximately 20% for transactions identified as Android or Web.
- Missing device values are concentrated within the same merchant categories that previously formed the higher fraud-rate cluster of approximately 29–31%.
- When merchant-category fraud rates are broken down by device type, categories in the higher-fraud cluster contain blank-device transactions with particularly high fraud rates, while categories in the lower-fraud cluster do not show the same pattern.
- This strengthens the relevance of the previously identified data-quality issue: blank `device_type` values are undocumented in the supplied data dictionary and cannot safely be interpreted as N/A.
- The current analysis establishes an association between missing device information and elevated fraud flags but does not establish why the relationship exists or whether missing device information itself is predictive of fraud.
- iOS is present as a device type in the overall transaction population but did not appear among fraud-flagged transactions.
- The significance of this difference should be evaluated against the transaction volume for each device type.

**Follow-up:** Profile transactions with blank `device_type` to determine whether they share transaction types, channels, statuses, domestic/international characteristics, or other attributes that could explain the elevated fraud rate.

### Fraud flags were observed only among KYC-verified customers

- The dataset contains both KYC-verified and non-KYC-verified customers.
- All fraud-flagged transactions were associated with KYC-verified customers; no fraud-flagged transactions were observed among non-KYC-verified customers.
- This does not support an association between non-KYC status and fraud flags in the observed dataset.
- The significance of this result depends on the relative transaction volume of KYC-verified and non-KYC-verified customers.

**Follow-up:** Compare transaction counts and fraud rates by KYC verification status to determine whether the absence of fraud flags among non-KYC customers is meaningful given their transaction volume.

### Fraud flags vary substantially by device type

- Transaction volume is evenly distributed across device types, with 375 transactions each for blank, Android, iOS, and Web.
- Despite equal transaction volumes, fraud-flag rates differ substantially by device type.
- Transactions with blank `device_type` have an observed fraud rate of approximately 40%.
- Android and Web transactions each have fraud rates of approximately 20%.
- No fraud-flagged transactions were observed among the 375 iOS transactions.
- Because transaction volume is identical across device groups, differences in transaction volume do not explain the observed differences in fraud rates.
- The blank `device_type` values remain a data-quality concern because they are not documented in the supplied data dictionary and cannot safely be interpreted as N/A.

**Follow-up:** Profile device groups, particularly blank and iOS transactions, across channel, transaction type, merchant category, transaction status, and domestic/international status to determine what other characteristics distinguish these groups.

### Channel does not explain device-level fraud differences

- Each device group contains the same transaction volume and the same distribution across channels.
- Differences in channel composition therefore do not explain the substantial differences in fraud rates observed across device types.

### Transaction type does not explain device-level fraud differences

- Each device group contains the same distribution across transaction types.
- Each of the 15 transaction types has 25 transactions within each device group.
- Differences in transaction-type composition therefore do not explain the substantial differences in fraud rates observed across device types.

### Customer segment varies across device types and is associated with fraud

- Unlike channel and transaction type, customer-segment composition differs substantially across device types.
- Business transactions are heavily concentrated among transactions with a blank `device_type`, with a smaller Business population associated with Web.
- Premium transactions are present across multiple device types, including iOS, despite no fraud-flagged transactions being observed for iOS.
- Fraud rates also vary by customer segment: Premium has the highest observed fraud rate at approximately 40%, followed by Business at approximately 25% and Standard at approximately 17%. No fraud flags were observed among Starter transactions.
- When fraud rate is examined by both merchant category and customer segment, Premium transactions show 100% fraud rates within several of the merchant categories previously identified as belonging to the higher-fraud cluster.
- Premium does not appear in the fraud-rate visual for the lower-fraud merchant categories. This may indicate that Premium transactions in those categories have a 0% fraud rate rather than that Premium transactions are absent from those categories.

**Follow-up:** Verify transaction and fraud counts for Premium customers across merchant categories to determine whether Premium transactions are present in the lower-fraud categories with no fraud flags. Continue investigating how customer segment interacts with device type and merchant category.

### Fraud flags are highly concentrated within specific merchant-category and customer-segment combinations

- Customer segment and merchant category show a highly structured interaction with fraud flags.
- Premium transactions are present across both the higher- and lower-fraud merchant-category groups.
- Within the nine merchant categories previously identified as the higher-fraud cluster, all Premium transactions are fraud-flagged.
- Premium transactions are also present in the lower-fraud merchant categories, but none are fraud-flagged.
- This explains how Premium can have an overall fraud rate of 40% despite showing 100% fraud rates within the higher-fraud merchant categories.
- Similar concentration patterns are visible for other customer segments, suggesting that the aggregate merchant-category fraud rates are driven by specific category/segment combinations rather than uniformly elevated fraud across all transactions in those categories.
- The highly discrete 0% or 100% patterns at this level suggest the dataset contains strongly structured fraud relationships. Further slicing may provide limited additional business insight.

**Follow-up:** Retain the merchant-category/customer-segment interaction as a potential driver of fraud exposure and revisit it if later analysis reveals related patterns.

### Gambling is overrepresented among fraud flags, but is not an isolated driver

- Gambling accounts for approximately 5.6% of transaction volume and approximately 8.3% of fraud flags, meaning its share of fraud flags exceeds its share of overall transactions.
- However, Gambling does not stand out substantially from several other merchant categories. Multiple categories in the previously identified higher-fraud cluster show a similar level of overrepresentation.
- Crypto Exchange shows the opposite pattern, accounting for approximately 5.6% of transaction volume but only approximately 2.7% of fraud flags.
- The results therefore do not indicate that Gambling or Crypto Exchange independently drives fraud exposure. Instead, Gambling is one member of a broader group of merchant categories with disproportionately high fraud representation, while Crypto Exchange belongs to the lower-fraud group.

### Fraud exposure is concentrated among four customers, preventing identification of a unique highest-exposure transaction

- The challenge asks which single transaction represents the highest risk-adjusted exposure based on high transaction amount, a fraud flag, and Declined status.
- Among transactions meeting the fraud-flagged and Declined criteria, seven transactions tie for the highest transaction amount at £518.18.
- The supplied criteria therefore do not identify a single highest-exposure transaction.
- Further investigation at the customer level revealed that fraud flags are completely concentrated among four customers: Charlotte Lewis, Daniel Okafor, Mohammed Al-Hassan, and Rajan Mehta.
- Each customer has 75 transactions, and all 75 transactions for each of these four customers are fraud-flagged, producing a 100% fraud rate.
- The remaining 16 customers also have 75 transactions each but have no fraud-flagged transactions.
- As a result, all 300 fraud-flagged transactions in the dataset are attributable to four of the 20 customers, representing 20% of customers but 100% of fraud flags.
- The concentration of fraud at the customer level is more pronounced than the differences previously observed across merchant categories, devices, and customer segments.

**Follow-up:** Reassess previously observed fraud patterns to determine how much of the apparent variation by merchant category, device type, and customer segment is explained by the four customers responsible for all fraud-flagged transactions.

## Revenue & Fee Health Observations

### Fee revenue is concentrated across specific transaction types and customers

- Zephyr Bank generated £750.17 in total fee revenue during the analysis period.
- Outbound Transfer generated the most fee revenue of any transaction type, followed by International Transfer.
- Business customers generated substantially more total fee revenue than the other customer segments.
- Fee revenue is also concentrated among a relatively small number of individual customers, with Samuel Whitmore and Sofia Patel generating the highest total fee revenue.
- Several customers generated little or no fee revenue during the period.

**Follow-up:** Investigate whether fees are being consistently applied, particularly for international transfers, and compare actual fees charged with the expected `typical_fee_gbp`.

### Fee deviations differ sharply between domestic and international transactions

- Actual transaction fees rarely align exactly with the `typical_fee_gbp` associated with each transaction type.
- International transactions show a strong pattern of below-typical pricing. Below-typical deviations total £512.11, while above-typical deviations total £25.00, resulting in a net difference of £487.11 below typical pricing.
- Domestic transactions show the opposite pattern. Below-typical deviations total £75.00, while above-typical deviations total £562.00, resulting in a net difference of £487.00 above typical pricing.
- In both groups, the differences are distributed across many relatively small per-transaction deviations rather than being driven by isolated large anomalies.
- The nearly equal but opposite net deviations for domestic and international transactions are notable and warrant further investigation.
- Because `typical_fee_gbp` represents typical rather than explicitly required pricing, these differences cannot be classified as confirmed revenue leakage or overcharging from the available data alone.

**Follow-up:** Confirm fee-pricing and waiver rules to determine whether the opposing domestic and international fee patterns are expected pricing behavior or indicate a systematic fee-application issue.

### Business customers generate the highest fee revenue per transaction

- Business customers generate approximately £1.00 in fee revenue per transaction, the highest of any customer segment.
- Starter and Premium customers each generate approximately £0.50 per transaction, while Standard customers generate approximately £0.17.
- Business therefore leads in both total fee revenue and fee revenue per transaction, indicating that its higher total fee contribution is not simply the result of transaction volume.

### Transaction value reached a pronounced low in April

- Monthly transaction value was highest in January at approximately £465K and lowest in April at approximately £265K before rebounding in May.
- April also had the lowest transaction count, but transaction volume alone does not account for the decline in value.
- February and April had similar transaction counts, while April generated substantially less transaction value, indicating that April also had lower average transaction values.

### Fraud flags show a clear monthly pattern

- Fraud-flagged activity varies meaningfully across the five-month period in both absolute count and fraud rate.
- January and May show the highest fraud exposure, with approximately 74 and 72 flagged transactions respectively and fraud rates of roughly 24% and 23%.
- April shows the lowest fraud exposure, with approximately 37 flagged transactions and a fraud rate of roughly 13%.
- Because the fraud rate follows the same general pattern as fraud-flagged transaction count, the differences are not attributable solely to changes in overall transaction volume.

### Decline rate decreased through March before rising again in May

- The overall decline rate is 35%.
- Monthly decline rate decreased from approximately 37.5% in January to approximately 33% in March and remained near that level in April before increasing to approximately 35.5% in May.
- Declined transaction counts fell more sharply in April, reflecting April's lower overall transaction volume.
- The decline-rate pattern broadly resembles the temporal pattern observed for fraud flags, although the available analysis has not yet established whether the two are related.
- The decline-rate pattern broadly resembles the temporal pattern observed for fraud flags. Fraud-flagged transactions have a higher decline rate than non-fraud transactions (50% vs. 31.25%), but approximately 71% of all declined transactions are not fraud-flagged, indicating that fraud alone does not explain the decline pattern.

### Weekly fraud and decline activity varies without a clear sustained cluster

- Both fraud and decline rates fluctuate substantially from week to week across the analysis period.
- Fraud rates range from roughly 9% to 30%, with multiple peaks and troughs distributed throughout the five-month period rather than concentrated within a single sustained period.
- Decline rates similarly fluctuate throughout the period, with no individual week or sustained sequence of weeks showing a clear concentration.
- Fraud-flagged transaction counts also fluctuate considerably, with the highest weekly count occurring in May, but elevated counts are not sustained across consecutive weeks.

### High-risk merchant category activity varies by day of week

- Transactions associated with merchant categories classified as High risk are most prevalent Sunday through Tuesday, representing approximately 13–14% of transactions on those days.
- The rate declines through the middle of the week and reaches its lowest point on Thursday at approximately 7%, before increasing again Friday and Saturday.
- The transaction count follows a similar pattern, indicating that the day-of-week differences are not explained solely by variation in overall transaction volume.
- High-risk merchant-category activity, fraud, and declines each exhibit different day-of-week patterns.

### High-risk merchant category activity varies by day of week but shows no clear time-of-day concentration

- Transactions associated with merchant categories classified as High risk are most prevalent Sunday through Tuesday, representing approximately 13–14% of transactions on those days.
- The rate reaches its lowest point on Thursday at approximately 7%, before increasing again Friday and Saturday.
- High-risk transaction rates also vary by hour of day, ranging from approximately 4% to 17%, but elevated rates are scattered across multiple hours rather than concentrated within a consistent time window.
- Fraud and decline rates similarly fluctuate across the day without showing a common time-of-day pattern, suggesting the three measures capture distinct aspects of transaction risk and operational outcomes.

### Fee revenue remains relatively stable despite changes in transaction volume

- Monthly fee revenue ranges from approximately £134 to £163, reaching its lowest point in February and highest point in March.
- Fee revenue per transaction varies from approximately £0.47 to £0.54 and does not consistently follow overall transaction volume.
- April generated the highest fee revenue per transaction at approximately £0.54 despite having the lowest transaction volume of the five-month period.
- May processed substantially more transactions than April but generated similar total fee revenue, corresponding with a decline in fee revenue per transaction to approximately £0.48.

### Large-value transactions declined sharply in April

- Transaction amounts show a distinct upper-value group beginning at approximately £12K, following a gap from transactions of approximately £3K and below.
- Large-value transactions (£12K+) occurred throughout the five-month period, with no anomalous upward spike in a single month.
- April was the exception in the opposite direction, with approximately 7 large-value transactions compared with roughly 15–20 in each of the other months.
- The reduction in large-value transactions likely contributed to April's previously observed decline in total transaction value.

### Large-value transactions declined sharply in April

- Transaction amounts show a distinct upper-value group beginning at approximately £12K, following a gap from transactions of approximately £3K and below.
- Large-value transactions (£12K+) occurred throughout the five-month period, with no anomalous upward spike in a single month.
- April was the exception in the opposite direction, with approximately 7 large-value transactions compared with roughly 15–20 in each of the other months.
- The reduction in large-value transactions likely contributed to April's previously observed decline in total transaction value.

### Premium customers disproportionately drive transaction value

- Standard customers generate the highest transaction volume with 450 transactions, while Premium customers account for 375 transactions.
- Despite having fewer transactions than Standard customers, Premium customers generate substantially more total transaction value than any other segment.
- Premium customers also have a substantially higher average transaction value at approximately £3.2K, compared with less than £1K for each of the other customer segments.
- All transactions in the distinct £12K+ large-value group are associated with Premium customers, indicating that the segment's higher total transaction value is driven by unusually large individual transactions rather than higher transaction volume.
- Premium's outsized transaction value is largely a customer-level concentration effect, driven by Rajan Mehta, whose transactions include every £12K+ transaction and are all fraud-flagged.

### Non-KYC customers do not show disproportionate fraud or decline rates

- Fraud flags are entirely concentrated among KYC-verified customers; no fraud-flagged transactions were observed among non-KYC customers.
- KYC-verified customers also have a higher decline rate at approximately 37.5%, compared with approximately 25% for non-KYC customers.
- In this dataset, non-KYC status is therefore not associated with elevated fraud or transaction declines.

### Transaction activity and fraud exposure are heavily concentrated in London

- London generates the highest transaction volume and substantially more transaction value than any other region.
- The Midlands has the second-highest transaction volume but substantially lower transaction value, indicating that London's high value is not explained by transaction volume alone.
- All £12K+ transactions belong to Rajan Mehta, a London-based Premium customer, contributing substantially to London's outsized transaction value.
- Fraud flags occur only in London and the Midlands, with approximately 225 fraud-flagged transactions in London and 75 in the Midlands.
- London also has the higher fraud rate at approximately 60%, compared with approximately 33% in the Midlands.
- Because all fraud flags in the dataset are concentrated among only four individual customers, the regional fraud pattern should be interpreted primarily as customer-level concentration rather than evidence of broader geographic risk.

### Account tenure shows limited independent relationship with transaction behavior

- Transaction totals vary across tenure cohorts, but each customer has exactly 75 transactions during the analysis period, indicating that differences in cohort transaction volume are driven by the number of customers in each cohort rather than differences in individual customer activity.
- The 6+ year cohort initially generated substantially higher fee revenue per transaction at approximately £0.78.
- All Business customers belong to the 6+ year cohort, and excluding Business customers reduces fee revenue per transaction for the 6+ year cohort to approximately £0.50.
- After excluding Business customers, the 2–3 year and 6+ year cohorts generate similar fee revenue per transaction, while the 4–5 year cohort remains lower at approximately £0.25.
- This suggests that customer segment composition, particularly the concentration of Business customers among older accounts, contributes more to the observed fee differences than account tenure alone.

### Failure rates vary substantially by transaction type

- Card Refund, Loan Repayment, and Standing Order have the highest failure rates at 75%.
- Each of these transaction types has 100 transactions: 50 Declined, 25 Reversed, and 25 Pending, with no Completed transactions.
- ATM Withdrawal, Purchase, and International Transfer have the next-highest failure rates at approximately 62.5% and generate the highest absolute numbers of failed transactions.
- Transaction types with lower failure rates are approximately 50%, indicating that unsuccessful transaction outcomes remain common across all transaction types.

### International transactions are higher-value and more fraud-prone despite lower overall activity

- Domestic transactions account for substantially more total transaction value, fraud-flagged transactions, and failed transactions because they represent a larger share of overall transaction activity.
- International transactions have a higher average transaction value at approximately £1.45K, compared with approximately £1.2K for domestic transactions.
- International transactions also have a higher fraud rate at approximately 25%, compared with approximately 18–19% for domestic transactions.
- Failure rates are similar across the two groups, at approximately 60% for domestic transactions and 58% for international transactions.
- Earlier fee analysis also identified substantial deviations from typical fees among international transactions, with actual fees falling below typical fees overall.

### Transaction outcomes are unusually concentrated by device type

- Each device group contains exactly 375 transactions, but transaction outcomes differ sharply by device.
- All 375 Android transactions are Pending.
- All 375 transactions with missing device type are Declined.
- All 375 Web transactions are Reversed.
- iOS is the only device type associated with Completed transactions, with 225 Completed and 150 Declined transactions.
- As a result, Web and missing-device transactions have 100% failure rates under the Declined + Reversed definition, while iOS has a 40% failure rate and Android has no transactions classified as failed because all remain Pending.
- Combined with the highly uniform device distribution observed elsewhere in the dataset, this deterministic relationship should be treated cautiously and may indicate a constructed data pattern rather than an underlying causal device effect.

### Fee deviations are concentrated in specific merchant categories

- Actual fees charged differ from `typical_fee_gbp` across merchant categories rather than showing a uniform pattern.
- Charity & Donations, Utilities, and Clothing & Fashion show the largest positive net fee deviations, indicating that actual fees were below typical fees overall in these categories.
- Entertainment, Fuel, and Subscription Services show the largest negative net deviations, indicating that actual fees were above typical fees overall.
- The category-level concentration suggests that fee waivers, pricing rules, or fee application differences may vary by merchant category; the available data does not establish whether these deviations are intentional or erroneous.

### Failure rates are broadly consistent across channels despite differences in failed transaction volume

- The overall Declined rate is 35% (525 of 1,500 transactions).
- Failure rates, defined as Declined + Reversed, are relatively similar across channels at approximately 58–63%.
- ATM Network and Automated have the highest failure rates at approximately 62–63%, but the difference from Mobile App and Web Browser is small.
- Mobile App generates the highest absolute number of failed transactions, reflecting its higher transaction volume rather than a substantially elevated failure rate.

### ATM reversals and international declines show geographic concentration

- ATM Network reversals occur only in South East and Wales, with approximately 25 transactions in each region.
- International declines occur only in London, Scotland, and South East, with London accounting for approximately 50 and Scotland and South East approximately 25 each.
- South East is the only region represented in both ATM reversals and international declines.
- This overlap may warrant further operational investigation, but the available data does not establish a common underlying cause.

### Device usage varies substantially across customer segments

- Device usage differs by customer segment despite each device group containing exactly 375 transactions overall.
- Business transactions are concentrated entirely in Android (225 transactions) and Web (75), with no iOS or missing-device transactions.
- Premium customers use all four device groups, with Web accounting for the largest share.
- Standard and Starter customers are more broadly distributed across device types, with iOS representing a larger share of their activity.
- Because transaction status is unusually deterministic by device type in this dataset, these segment/device patterns should be interpreted cautiously when evaluating operational performance.

### H2 fee revenue run-rate projection

- Zephyr Bank generated £750.17 in fee revenue from January through May 2026, averaging approximately £150.03 per month.
- If the observed average monthly fee revenue continues, projected fee revenue for H2 2026 (July–December) is approximately £900.20.
- This is a simple run-rate projection rather than a statistical forecast.
- The projection assumes current fee-revenue patterns continue and does not account for seasonality, changes in transaction volume or mix, or changes to fee application.
- The projection is based on actual fees currently being charged and does not assume that previously identified below-typical fee deviations represent recoverable revenue.