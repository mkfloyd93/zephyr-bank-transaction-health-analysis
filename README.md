# Zephyr Bank Transaction Health Analysis

## Project Overview

This project analyzes transaction activity for **Zephyr Bank**, a fictional UK fintech neobank, with the goal of identifying meaningful patterns in transaction performance, fraud exposure, fee revenue, customer behavior, and operational risk.

The project is based on the **June 2026 DataDNA UK Fintech Neobank Digital Transaction Health Monitor Analytics Challenge** from Onyx Data.

[View the original DataDNA challenge](https://datadna.onyxdata.co.uk/challenges/june-2026-datadna-uk-fintech-neobank-digital-transaction-health-monitor-analytics-challenge/)

The challenge provides transaction, customer, transaction-type, and merchant-category data and asks participants to uncover insights that could help **Risk, Finance, Product, and Compliance teams** improve transaction performance, reduce financial and operational risk, and make better data-informed decisions.

For this portfolio project, I approached the challenge as an end-to-end analysis rather than focusing only on dashboard creation. The work includes data validation, exploratory analysis, Power BI reporting, interpretation of key findings, and recommendations for further investigation.

---

## Project Deliverables

### Power BI Report

The completed four-page Power BI report is available in both PDF and `.pbix` formats.

- [View the PDF Report](report/zephyr_bank_transaction_analysis.pdf)
- [Download the Power BI Report](report/zephyr_bank_transaction_analysis.pbix)

### Analysis & Recommendations

- [Executive Summary](analysis/executive_summary.md)
- [Detailed Analysis](analysis/detailed_analysis.md)
- [Actionable Insights](analysis/actionable_insights.md)
- [Exploratory Analysis Notes](analysis/eda_notes.md)

### Source Materials

- [Challenge Brief](source/docs/CHALLENGE_BRIEF.md)
- [Data Dictionary](source/docs/DATA_DICTIONARY.md)
- [Source Data](source/data/)

---

## Business Questions

The analysis focused on four primary areas:

- **Risk & Compliance:** Where is fraud exposure concentrated, and which customer or transaction characteristics provide useful risk signals?
- **Finance:** How is fee revenue performing, and where do actual fees differ from typical pricing?
- **Product & Operations:** Where are unsuccessful transaction outcomes concentrated, and what should be investigated before making product or UX changes?
- **Customer Behavior:** How do transaction value, fraud exposure, and fee generation differ across customer segments?

The analysis also explored patterns across merchant categories, KYC status, devices, channels, transaction types, regions, account tenure, and time.

---

## Dataset

The supplied dataset covers **1,500 transactions from January through May 2026** and uses a dimensional structure consisting of a transaction fact table and supporting customer, transaction-type, and merchant-category dimensions.

The source data includes:

- `dim_customer.csv`
- `dim_merchant_category.csv`
- `dim_transaction_type.csv`
- `fact_transactions_updated.csv`

Key fields used in the analysis include:

- Transaction date and amount
- Transaction status
- Fraud flag
- Fee charged
- Customer segment and KYC status
- Transaction type and channel
- Merchant category and risk classification
- Domestic/international status
- Region
- Device type

The original challenge brief, data dictionary, and source data are retained in the [`source/`](source/) directory for reference.

---

## Tools Used

### Power BI Desktop

Power BI was used for:

- Data modeling and relationship validation
- DAX measures and calculated columns
- Exploratory analysis
- Interactive report development
- Data visualization

### Git & GitHub

Git and GitHub were used for:

- Version control
- Project organization
- Analysis documentation
- Portfolio presentation

### Markdown

Markdown was used to document:

- Exploratory analysis and investigation history
- Executive findings
- Detailed analysis
- Actionable business recommendations
- Project methodology and supporting documentation

---

## Analysis Process

### 1. Data Understanding & Validation

I began by reviewing the supplied challenge brief, data dictionary, table structure, and relationships.

Initial validation included checking field values, missing data, transaction distributions, and whether the supplied data matched its documentation.

One early data-quality issue was an undocumented set of blank `device_type` values. The data dictionary specifies `N/A` for transactions without a recorded device, but the transaction data contains blank values instead. Because their meaning could not be reliably inferred, these values were retained as unknown rather than automatically recoded.

### 2. Exploratory Data Analysis

Exploratory analysis was conducted iteratively in Power BI, with findings and follow-up questions documented in [`eda_notes.md`](analysis/eda_notes.md).

Rather than treating the challenge's guiding questions as a fixed checklist, I used them as starting points and followed patterns that emerged from the data.

Analysis included:

- Fraud concentration by customer, segment, merchant category, KYC status, device, and region
- Large-value transaction behavior
- Fee revenue and fee deviations
- Domestic versus international activity
- Customer-segment performance
- Transaction failure rates by type and channel
- Device and transaction-status relationships
- Monthly, weekly, and day/time patterns
- Geographic and account-tenure patterns

This iterative process was particularly important where an initial aggregate pattern changed after deeper investigation. For example, segment- and geographic-level fraud patterns became substantially more informative after analysis revealed that all fraud flags were concentrated among only four customers.

### 3. Power BI Report Development

After identifying the findings most relevant to business decision-making, I developed a four-page Power BI report.

**Executive Overview** provides a high-level view of transaction value, fee revenue, and monthly performance.

**Risk & Compliance** examines customer-level fraud concentration, merchant risk classifications, and KYC outcomes.

**Finance** focuses on fee deviations, merchant-category patterns, and customer-segment fee performance.

**Product & Operations** examines transaction reliability, device/status behavior, channel performance, and transaction-type failure rates.

The report separates executive-level information from the deeper functional views needed by Risk, Finance, Product, and Operations stakeholders.

The complete report is available in the [`report/`](report/) directory.

### 4. Detailed Analysis

The [`Detailed Analysis`](analysis/detailed_analysis.md) documents the evidence and reasoning behind the report's primary findings.

It includes:

- Statistical summaries by customer segment
- Supporting evidence for key findings
- Responses to the challenge's guiding questions
- Power BI visualizations of key trends and patterns
- Additional exploratory findings that did not require dedicated dashboard visuals
- Data limitations and interpretation considerations

### 5. Actionable Insights

The [`Actionable Insights`](analysis/actionable_insights.md) translate the strongest analytical findings into potential business actions.

Recommendations focus on:

- Prioritizing investigation of the four customers responsible for all observed fraud flags
- Validating actual fee charges against pricing and waiver rules before classifying fee deviations as revenue leakage
- Investigating the unusually deterministic relationship between device type and transaction status before making UX or product changes

Each recommendation also identifies the expected business impact and the additional data required to move from an analytical signal to a supported business decision.

---

## Key Findings

### Fraud exposure is highly concentrated

All **300 fraud-flagged transactions** belong to only four customers, each with a 100% observed fraud rate. This customer-level concentration is substantially stronger than differences observed across merchant risk classifications, customer segments, or regions.

### Large-value exposure is concentrated in one customer

Every transaction of **£12K or more** belongs to Rajan Mehta, and all of his transactions are fraud flagged. This also materially contributes to the unusually high transaction value observed within the Premium customer segment.

### Fee behavior differs systematically between domestic and international transactions

International transactions were charged a net **£487.11 below typical fees**, while domestic transactions were charged a net **£487.00 above typical fees**.

Because the supplied `typical_fee_gbp` field represents typical rather than explicitly mandatory pricing, these differences cannot be classified as confirmed revenue leakage or overcharging without additional pricing and waiver information.

### Transaction outcomes show an unusual relationship with device type

Only **225 of 1,500 transactions (15%)** reached Completed status, while **900 (60%)** were Declined or Reversed.

Device-level analysis revealed an unusually deterministic pattern:

- Android transactions are entirely Pending.
- Web transactions are entirely Reversed.
- Transactions with blank device values are entirely Declined.
- iOS is the only device group containing Completed transactions.

The pattern warrants validation of device capture, transaction-status mapping, and upstream processing before being interpreted as a product or UX issue.

### Channel alone does not explain transaction failures

Failure rates remain relatively similar across channels at approximately **58–63%**. Transaction type provides greater differentiation: **Card Refund, Loan Repayment, and Standing Order each have a 75% failure rate**.

---

## Repository Structure

```text
.
├── README.md
│
├── analysis/
│   ├── images/
│   ├── actionable_insights.md
│   ├── detailed_analysis.md
│   ├── eda_notes.md
│   └── executive_summary.md
│
├── report/
│   ├── zephyr_bank_transaction_analysis.pbix
│   └── zephyr_bank_transaction_analysis.pdf
│
└── source/
    ├── data/
    │   ├── dim_customer.csv
    │   ├── dim_merchant_category.csv
    │   ├── dim_transaction_type.csv
    │   └── fact_transactions_updated.csv
    │
    └── docs/
        ├── CHALLENGE_BRIEF.md
        └── DATA_DICTIONARY.md