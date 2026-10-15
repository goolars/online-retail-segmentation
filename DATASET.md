# Dataset Source Documentation

## Source
- **Name:** Online Retail II
- **Creator:** Daqing Chen (Chen, D., 2012)
- **Repository:** UCI Machine Learning Repository
- **URL / DOI:** https://doi.org/10.24432/C5CG6D  (dataset page: https://archive.ics.uci.edu/dataset/502/online+retail+ii)
- **Licence:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Date accessed:** 15 October 2026
- **File used:** `online_retail_II.xlsx` (two worksheets: "Year 2009-2010" and "Year 2010-2011")

## Description
All transactions for a UK based, mainly online retailer of unique all-occasion giftware, between
**1 December 2009 and 9 December 2011**. Many customers are wholesalers. The raw file contains **1,067,371 rows**
across 8 fields.

| Field | Type | Description |
|---|---|---|
| Invoice | nominal | 6-digit invoice number. A leading `C` marks a cancellation. |
| StockCode | nominal | Product code (mostly 5 digits, optionally with a letter suffix). |
| Description | nominal | Product name. |
| Quantity | numeric | Units per transaction line (negative on returns). |
| InvoiceDate | datetime | Date and time the transaction was generated. |
| Price | numeric | Unit price in pounds sterling (£). |
| Customer ID | nominal | 5-digit customer number (missing on ~22.8% of rows). |
| Country | nominal | Country where the customer resides (43 countries). |

## Preprocessing applied before analysis
Performed in `notebooks/online_retail_segmentation.ipynb`, in this order, with row counts recorded:

| Step | Rule | Rows removed |
|---|---|---|
| 0 | Raw (both sheets) | start = 1,067,371 |
| 1 | Drop duplicate rows on transaction content (incl. the Dec-2010 overlap, where 1,088 invoices appear in both sheets) | 34,335 |
| 2 | Drop cancellations (Invoice starts with `C`) | 19,104 |
| 3 | Keep product StockCodes only (drop POST, DOT, M, BANK CHARGES, AMAZONFEE, TEST001, gift_*, DCGS*, ...) | 4,791 |
| 4 | Drop Quantity ≤ 0 (residual, after cancellations removed) | 3,362 |
| 5 | Drop Price ≤ 0 (residual) | 2,566 |
| 6 | Drop rows with missing Customer ID | 226,636 |
| | **Final cleaned transactions** | **776,577** |

De-duplication is performed on transaction content (Invoice, StockCode, Description, Quantity, InvoiceDate, Price, Customer ID, Country), not a naive whole-row match, so the two yearly worksheets that overlap at the December 2010 boundary are not double-counted.

Derived field: `Revenue = Quantity × Price`. Customer-level features (Recency, Frequency, Monetary, plus tested
Average Order Value and Tenure) are built from the cleaned table on a snapshot date of 10 December 2011.

## Notes and caveats
- Unidentified-customer rows hold ~13.3% of valid revenue despite being ~22.8% of rows; they are excluded from the
  customer-level segmentation but discussed as a limitation.
- The data has no cost, margin, marketing, browsing, or return-reason fields; "value" is measured as revenue.
- Revenue is heavily UK-weighted (~84%).

## Citation (APA 7th)
Chen, D. (2012). *Online Retail II* [Data set]. UCI Machine Learning Repository. https://doi.org/10.24432/C5CG6D
