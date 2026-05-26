# Credit_Card_Financial_Dashboard
Dashboard using Power BI
Credit Card Financial Dashboard — Power BI

> An end-to-end business intelligence project that transforms raw credit card and customer data into a real-time interactive weekly dashboard, enabling stakeholders to monitor performance, track revenue trends, and make data-driven decisions.

---

Table of Contents
1. [Project Overview](#project-overview)
2. [Problem Statement](#problem-statement)
3. [Dataset](#dataset)
4. [Tools & Technologies](#tools--technologies)
5. [Methodology](#methodology)
6. [Dashboard & Output](#dashboard--output)
7. [How to Run](#how-to-run)
8. [Results & Conclusion](#results--conclusion)
9. [Future Work](#future-work)
10. [Author & Contact](#author--contact)

---

Project Overview

This project builds a **Credit Card Weekly Financial Dashboard** using Power BI, fed by a PostgreSQL database populated from structured CSV data. The dashboard consists of two interactive report pages — a **Transaction Report** and a **Customer Report** — that together provide a 360° view of credit card operations across an entire fiscal year (53 weeks).

The project covers the complete data pipeline: raw data preparation → SQL database design → data import → Power BI data modeling → DAX calculations → visual dashboard → insight delivery.

**What was done:**
- Designed and populated a PostgreSQL relational database from CSV files
- Connected Power BI to the live SQL database for real-time data refresh
- Built custom DAX measures for revenue KPIs, week-over-week comparisons, and customer segmentation
- Developed two fully interactive dashboard pages with filters for quarter, card category, gender, and income group
- Extracted actionable business insights from the final dashboard

---

Problem Statement

Credit card operations generate thousands of transactions every week across multiple customer segments, card categories, and geographies. Without a centralized reporting system, answering critical business questions requires manual data extraction and spreadsheet work — a slow, error-prone process that delays decision-making.

**Business questions this dashboard answers:**
- How is weekly revenue trending, and did it grow or shrink compared to last week?
- Which card categories, customer segments, and states are driving the most revenue?
- What is the delinquency rate, and how does it compare across customer groups?
- Which expenditure types dominate spending, and through what payment methods?
- How are customers distributed by age, income, education, and job — and how does that affect revenue?

**The goal** was to build a single, always-current dashboard that replaces manual reporting and gives stakeholders real-time answers to these questions every week.

---

Dataset

The project uses four CSV files that together form a complete picture of credit card activity for the year 2023 (Weeks 1–53).

### File 1: `credit_card.csv` — Transaction Data
- **Rows:** ~10,108 records (Weeks 1–52)
- **Key columns:**

| Column | Description |
|---|---|
| `Client_Num` | Unique customer identifier |
| `Card_Category` | Blue, Silver, Gold, Platinum |
| `Annual_Fees` | Annual card fee charged |
| `Credit_Limit` | Customer's credit limit |
| `Total_Trans_Amt` | Total transaction amount for the week |
| `Total_Trans_Vol` | Number of transactions |
| `Interest_Earned` | Interest charged on revolving balance |
| `Avg_Utilization_Ratio` | Credit utilization percentage |
| `Use_Chip` | Payment method — Swipe, Chip, or Online |
| `Exp_Type` | Expenditure category — Bills, Entertainment, Fuel, Grocery, Food, Travel |
| `Delinquent_Acc` | Whether the account is delinquent (0/1) |
| `Week_Start_Date`, `Week_Num`, `Qtr` | Time dimensions |

### File 2: `customer.csv` — Customer Profile Data
- **Rows:** ~10,108 records
- **Key columns:**

| Column | Description |
|---|---|
| `Client_Num` | Joins to transaction data |
| `Customer_Age` | Age of the customer |
| `Gender` | Male / Female |
| `Income` | Annual income |
| `Education_Level` | Graduate, High School, Post-Graduate, Doctorate, Uneducated, Unknown |
| `Marital_Status` | Married, Single, Unknown |
| `Customer_Job` | Blue-collar, Businessman, Govt, Retirees, Self-employed, White-collar |
| `Dependent_Count` | Number of dependents |
| `State_cd` | US state code |
| `Cust_Satisfaction_Score` | Satisfaction rating (1–5) |

### File 3 & 4: `cc_add.csv` & `cust_add.csv` — Week 53 Supplemental Data
- **Rows:** ~185 records each
- Additional data for Week 53 (31st Dec) appended after initial load to simulate a real incremental data refresh.

**Total records across all files: ~20,590**  
**Time period covered: Full Year 2023 (January – December, 53 weeks)**

---

Tools & Technologies

| Category | Tool / Technology | Purpose |
|---|---|---|
| **Database** | PostgreSQL | Database creation, table design, data import via COPY |
| **Query Language** | SQL | Table schema definition, data loading, date formatting |
| **BI & Visualization** | Power BI Desktop | Data modeling, dashboard design, visual creation |
| **DAX** | Data Analysis Expressions | KPI measures, time intelligence, calculated columns |
| **Data Format** | CSV (Excel-compatible) | Raw data staging and preparation |
| **Version Control** | Git & GitHub | Project hosting and sharing |

---

Methodology

### Step 1 — Data Preparation
- Reviewed all four CSV files to understand column types, date formats, and data quality
- Identified and resolved a PostgreSQL date parsing issue (`datestyle` mismatch for `Week_Start_Date`) before import

### Step 2 — Database Design & Import (SQL)
- Created a PostgreSQL database (`ccdb`) with two normalized tables: `cc_detail` and `cust_detail`
- Imported base data (Weeks 1–52) using `COPY` commands
- Appended Week 53 data using a second `COPY` pass to simulate a live weekly refresh

### Step 3 — Data Modeling in Power BI
- Connected Power BI Desktop directly to the PostgreSQL database
- Established a relationship between `cc_detail` and `cust_detail` on `Client_Num`
- Verified data types and cleaned column names for usability

### Step 4 — DAX Calculations
Created the following measures and calculated columns:

```dax
-- Total Revenue KPI
Revenue = annual_fees + total_trans_amt + interest_earned

-- Week-over-Week Revenue
Current_week_Revenue = CALCULATE(SUM(Revenue),
    FILTER(ALL(cc_detail), week_num2 = MAX(week_num2)))

Previous_week_Revenue = CALCULATE(SUM(Revenue),
    FILTER(ALL(cc_detail), week_num2 = MAX(week_num2) - 1))

-- Customer Age Segmentation
AgeGroup = SWITCH(TRUE(),
    customer_age < 30, "20-30",
    customer_age < 40, "30-40",
    customer_age < 50, "40-50",
    customer_age < 60, "50-60",
    customer_age >= 60, "60+")

-- Income Segmentation
IncomeGroup = SWITCH(TRUE(),
    income < 35000, "Low",
    income < 70000, "Med",
    income >= 70000, "High")
```

### Step 5 — Exploratory Data Analysis (EDA) & Insights
Key patterns explored:
- **Revenue composition:** Annual fees + transaction amounts + interest — understanding which component drives the most revenue
- **Card category performance:** Blue card volume vs. Platinum card value
- **Customer segmentation:** Revenue contribution by age group, income group, education, job type, and marital status
- **Geographic analysis:** State-wise revenue concentration
- **Payment behavior:** Swipe vs. Chip vs. Online transaction preferences
- **Risk indicators:** Delinquency rate and activation rate tracking

### Step 6 — Dashboard Development
- Built two report pages in Power BI (Transaction Report & Customer Report)
- Added interactive slicers for Quarter, Card Category, Gender, Income Group, and Week
- Exported final dashboards as PDF reports for stakeholder sharing

---

Dashboard & Output

### Page 1 — Credit Card Transaction Report

**Purpose:** Tracks how revenue and transactions are flowing across card types, payment methods, and customer profiles.

**Visuals used:**
- **KPI Cards** — Revenue (55M), Transaction Amount (45M), Transaction Count (656K), Interest Earned (7.84M)
- **Stacked Bar Chart** — Revenue by Card Category (Blue, Silver, Gold, Platinum)
- **Horizontal Bar Charts** — Revenue by Expenditure Type, Education Level, Customer Job
- **Clustered Bar Chart** — Revenue by Use Chip method (Swipe / Chip / Online)
- **Dual-axis Line + Bar Chart** — Total Transaction Count & Revenue by Quarter
- **Matrix Table** — Card Category × Revenue, Transaction Amount, Interest Earned breakdown
- **Slicers** — Quarter, Gender, Income Group, Week Start Date

### Page 2 — Credit Card Customer Report

**Purpose:** Profiles the customer base to show which segments are most valuable and how demographics relate to revenue.

**Visuals used:**
- **KPI Cards** — Revenue (55M), Income (576M), Interest Earned (7.84M), Customer Satisfaction Score (3.19)
- **Horizontal Bar Charts** — Revenue by Income Group, Age Group, Dependent Count, Education Level, State Code, Marital Status
- **Line Chart** — Revenue by Week, split by Gender (F vs. M), showing weekly trend across 53 weeks
- **Matrix Table** — Customer Job × Revenue, Income, Interest Earned
- **Slicers** — Quarter, Card Category, Income Group, Gender, Week Start Date

---

How to Run

### Prerequisites
- PostgreSQL installed and running
- Power BI Desktop installed (free from Microsoft)
- Git (to clone the repo)

Steps

**1. Clone the repository**
```bash
git clone https://github.com/YOUR_USERNAME/Credit_Card_Financial_Dashboard.git
cd Credit_Card_Financial_Dashboard
```

**2. Set up the PostgreSQL database**
```sql
-- In pgAdmin or psql:
CREATE DATABASE ccdb;
```

**3. Run the SQL setup script**
- Open `sql/SQL_Query_Financial_Dashboard_Data.sql`
- Update the file paths in the `COPY` commands to match your local `data/` folder
- If you get a date error, run this first:
```sql
SET datestyle TO 'ISO, DMY';
```
- Execute the full script — this creates both tables and imports all data including Week 53

**4. Connect Power BI to PostgreSQL**
- Open Power BI Desktop → Get Data → PostgreSQL Database
- Enter your server (`localhost`) and database (`ccdb`)
- Load both `cc_detail` and `cust_detail` tables

**5. Apply DAX measures**
- In the Power BI data model, add the DAX calculated columns and measures listed in the Methodology section

**6. Build or explore the dashboard**
- Use the provided PDF exports as a reference for the layout
- Apply slicers to filter by quarter, card category, gender, and income group

---

Results & Conclusion

| Metric | Value |
|---|---|
| Total YTD Revenue | **$57 Million** |
| Total Interest Earned | **$8 Million** |
| Total Transaction Amount | **$46 Million** |
| Total Transactions | **656,000+** |
| Week-over-Week Revenue Growth (Week 53) | **+28.8%** |
| Top Card Category by Revenue | **Blue ($46M — 83%)** |
| Top 2 Cards by Transaction Share | **Blue + Silver = 93%** |
| Top Revenue States | **TX, NY, CA = 68%** |
| Top Customer Segment | **Businessmen ($17.4M)** |
| Top Expenditure Type | **Bills ($14M)** |
| Overall Activation Rate | **57.5%** |
| Overall Delinquency Rate | **6.06%** |

**Conclusion:** The dashboard successfully consolidates 53 weeks of credit card data into two clear, interactive reports. The analysis reveals that Blue card holders and businessman customers are the primary revenue drivers, that swipe-based transactions dominate payment behavior, and that three states account for more than two-thirds of all revenue — insights that can directly inform product, marketing, and risk strategies.

---

Future Work

- **Predictive modeling:** Integrate a Python-based churn prediction or delinquency risk model and surface the results in the dashboard
- **Automated data refresh:** Set up a scheduled pipeline (using Python or PostgreSQL triggers) to automatically append new weekly data without manual CSV imports
- **Expanded geography:** Add a filled map visual to show state-level revenue distribution geographically
- **Customer lifetime value (CLV):** Build a CLV measure using transaction history to rank customers by long-term value, not just weekly revenue
- **Anomaly detection:** Add conditional formatting or alerts to flag weeks where revenue, delinquency, or utilization ratios deviate significantly from the trend
- **Mobile layout:** Optimize the Power BI report for the mobile view so stakeholders can check KPIs on the go

---

Author & Contact

**Shafaque Ara**

| Platform | Link |
|---|---|
| GitHub | `[ https://github.com/shafaqueara8-arch/Credit_Card_Financial_Dashboard.git]` |

| LinkedIn | `[ www.linkedin.com/in/shafaqueara]` |

| Email | `[shafaqueara8@gmail.com]` |

---


