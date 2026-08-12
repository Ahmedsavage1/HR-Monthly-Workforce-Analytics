# HR Monthly Workforce Analytics - Enterprise Solution

## 1. Project Overview
This project provides an end-to-end Power BI analytics solution for enterprise HR decision-making. It transforms flat workforce transactional data into a Star Schema and delivers dynamic insights into headcount, payroll distribution, performance ratings, and year-over-year metrics across 2024 and 2025.

---

## 2. Data Architecture & Modeling

### Data Normalization
The monolithic dataset (4,030 monthly records for 200 unique employees) was deconstructed into a Star Schema:
* **Fact Table:** `FactMonthlyWorkforce` (Metrics: Salary, Active Days, Absence, Performance Score, Overtime)
* **Dimension Tables:**
  * `DimEmployee` (Demographics: EmployeeID, Name, Gender, Hire Date)
  * `DimDepartment` (Department Identifiers and Names)
  * `DimJobRole` (Job Titles and Seniority Levels)
  * `DimDate` (Canonical calendar spanning 2024-01-01 to 2025-12-31)

### Relationship Configuration
* All relationships strictly adhere to One-to-Many (`1:*`) cardinality.
* Cross-filtering is enforced in a Single Direction from dimension tables to the central fact table.

---

## 3. Core DAX Implementation

```dax
// Measure 1: Total Salary Paid
Total Salary Paid = 
SUM(FactMonthlyWorkforce[SalaryPaid])

// Measure 2: Employee Headcount
Employee Headcount = 
DISTINCTCOUNT(FactMonthlyWorkforce[EmployeeID])

// Measure 3: Average Performance Score
Average Performance Score = 
AVERAGE(FactMonthlyWorkforce[PerformanceScore])

// Measure 4: Salary Paid — Previous Year
Salary Paid - Previous Year = 
CALCULATE(
    [Total Salary Paid],
    SAMEPERIODLASTYEAR(DimDate[Date])
)

// Measure 5: Department Payroll % of Total (Primary & Bonus Solutions)
Department Payroll % of Total = 
VAR CurrentDeptSalary = [Total Salary Paid]
VAR TotalCompanySalary = 
    CALCULATE(
        [Total Salary Paid],
        REMOVEFILTERS(DimDepartment)
    )
RETURN
    DIVIDE(CurrentDeptSalary, TotalCompanySalary, 0)

```

4. Report Structure & Visuals
The Power BI report contains two interactive pages with seamless navigation buttons and slicers (`Year`, `JobLevel`):
Page 1: Workforce Overview

* KPI Cards: Employee Headcount, Total Salary Paid, Average Performance Score.
* Visual 1: Headcount by Department (Column Chart).
* Visual 2: Gender Distribution (Donut Chart).
* Visual 3: Top Departments by Average Performance Score (Bar Chart).
* Visual 4: Employee Distribution across Job Roles (Treemap).

Page 2: Payroll Analytics

* KPI Cards: Total Salary Paid, Salary Paid - Previous Year, Average Performance Score.
* Visual 1: Monthly Salary Trend (Line Chart).
* Visual 2: Department Payroll Comparison (Column Chart).
* Visual 3: Department Contribution to Total Payroll % (Donut Chart using `REMOVEFILTERS`).
* Visual 4: Correlation between Total Salary Paid and Performance Score (Scatter Plot).

5. Repository Deliverables

* `HR_Monthly_Workforce_Analytics.pbix` - Complete Power BI Report file.
* `DAX_Measures_Script.txt` - Documented DAX script for all core measures.
* `data/` - Raw monthly transactional dataset (CSV format).
* `assets/` - Visual documentation including Data Model architecture and Dashboard pages.
