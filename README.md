# Customer segmentation with SQL

![Oracle SQL](https://img.shields.io/badge/Oracle%20SQL-F80000?style=flat&logo=oracle&logoColor=white)
![Analytical SQL](https://img.shields.io/badge/Analytical%20SQL-2C3E50?style=flat)
![License](https://img.shields.io/badge/License-CC%20BY--NC%204.0-0a2c46?style=flat)

Which customers matter most, which ones are slipping away, and do a few of them bring in most of the money? I tried to answer that with nothing but SQL window functions: `NTILE`, `PERCENT_RANK`, `CUME_DIST`, `LAG`, `LAST_VALUE` and running sums.

## The data

| Dataset | Size | Used in |
|---|---|---|
| An online retail shop (UK), as Oracle `INSERT` statements in `data/OnlineRetaill.txt` | 12,858 order lines, 717 invoices, 110 customers, Dec 2010 to Dec 2011 | `Q1.sql`, `Q2.sql` |
| Daily customer spend in Egyptian pounds, in `data/Question 3.txt` | 574,396 rows, 20,000 customers, Nov to Dec 2019 | `Q3.sql` |

`data/Q3_Sample.txt` is a smaller slice of the second dataset for quick testing. `Online Retail Case Study.pdf` is the case study write-up.

## What I did

**Exploring the shop (`Q1.sql`)**

- The top 10% of products by revenue
- Customers split into five revenue bands
- Month-over-month revenue change
- **The Pareto check:** cumulative share of revenue against cumulative share of customers, to see whether roughly 20% of customers bring in 80% of the money
- Days since each customer's last purchase
- Average revenue by day of the week
- How many purchases a customer needs to reach £50 in total spend

**RFM segmentation (`Q2.sql`)**

Each customer gets three numbers:

- **Recency:** days since their last order
- **Frequency:** how many orders they've placed
- **Monetary:** how much they've spent

Recency gets a score from 1 to 5, and frequency and monetary value share a second score from 1 to 5, both using `NTILE(5)`. The pair of scores then maps to one of ten segments, from **Champions** (5, 5) through **Loyal Customers**, **At Risk** and **Cannot Lose Them**, down to **Hibernating** and **Lost** (1, 1).

**Customer habits (`Q3.sql`)**

- The longest run of consecutive days each customer made a purchase
- How many purchases it takes a customer to reach 250 EGP in total spend. The answer was about 7.

## Caveats

- **"Frequency and monetary" share one score.** `NTILE` orders by frequency first and uses monetary value only to break ties, so it's really a frequency score.
- **Only 110 customers in the shop data.** Five bands of about 22 customers each is a small base for segments.
- **No saved output.** The repo has the queries but not their results.

## How to run

The queries are written for Oracle. They use `TO_DATE`, `TRUNC` on dates, `ROWNUM` and row-value `IN` lists.

1. Run `data/OnlineRetaill.txt` to create and fill `tableRetail`.
2. Run the commented block at the top of `Q2.sql` once. It converts the text dates to real dates.
3. Run `Q1.sql` and `Q2.sql`.
4. For `Q3.sql`, create `CUST_TRANSACTIONS` with the commented `CREATE TABLE` at the top, then load `data/Question 3.txt` into it, for example with SQL Developer's import wizard.

## What I'd like to do next

Compare the RFM segments with a clustering method like k-means on the same three numbers.
