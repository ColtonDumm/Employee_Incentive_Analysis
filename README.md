# HR Absenteeism & Health Incentive Analysis | SQL Server + Power BI

## Executive Summary
- **Goal:** Reduce absenteeism costs and reward healthy, reliable employees using an end-to-end SQL → Power BI pipeline.
- **What I built:** (1) SQL logic to identify bonus-eligible employees, (2) a budget-driven wage-uplift model for non-smokers, and (3) an interactive HR dashboard.
- **Eligibility (health bonus):** Non-smoker, BMI 18.5–24.9, no disciplinary failures, and absenteeism **≤ company average**.
- **Non-smoker wage adjustment:** Insurance budget **$983,221** distributed evenly across all non-smokers → per-employee raise = `Budget / NonSmokerCount`  
  ↳ Example with current data: ≈ **$0.69/hour** (~**$1.43k/year**).
- **Patterns observed (within this sample):** Higher absenteeism clusters in early/late summer; healthier behavior aligns with fewer absence hours; education patterns vary due to simulated data.
- **Note:** Dataset is simulated for practice; anomalies are flagged in the report. Findings illustrate workflow and decision support, not definitive company policy.

--- 

## Project Overview
This project explores employee absenteeism and health data to understand workforce trends and design potential incentive programs.  
The work covers the full process: building a SQL database, writing queries to analyze absenteeism patterns, and creating an interactive Power BI dashboard for HR decision-making.  

---

## Problem & Mission
Employee absenteeism can lead to lower productivity and higher costs. HR leaders often look for ways to reward reliable and healthy employees while managing overall workforce costs.  

The mission of this project was to:
- Load and manage HR data in SQL Server  
- Use SQL queries to explore absenteeism drivers and calculate potential incentive payouts  
- Build a dashboard in Power BI to visualize results and make the findings easy to interpret  

*Note: This dataset is simulated. Some values don’t always align with real-world logic (for example, PhD salaries showing lower averages than high school salaries). In a real business setting, these anomalies would warrant a data quality review. The focus here is on demonstrating technical workflow and communication of results, not the accuracy of the sample data.*  

---

## HR Requests
The HR department requested analysis and reporting to support a new incentive program. Their key requirements were:

### 1. **Identify healthy employees with low absenteeism**  
The HR team requested a list of employees eligible for a **$1,000 health bonus program**.  
For the Program we are defining eligibility as:
- Non-smoker  
- Healthy BMI (18.5–24.9)  
- No disciplinary failures  
- Absenteeism below the company average  

#### SQL Solution

- [SQL Query Code](https://github.com/ColtonDumm/Employee_Incentive_Analysis/blob/master/SQL_Overview)

#### Eligible Employees (preview)

| ID  | Absenteeism (hrs) |
| ---:| -----------------: |
| 66  | 1 |
| 82  | 1 |
| 89  | 1 |
| 114 | 1 |
| 121 | 1 | 


### 2. **Calculate wage adjustments for non-smokers**  
   - Determine annual compensation increases  
   - Insurance budget: $983,221 allocated for non-smokers  

- [SQL Query Code](https://github.com/ColtonDumm/Employee_Incentive_Analysis/blob/master/SQL_Overview)

#### Non-smoker Wage Adjustments (preview)

| ID | CurrentHourlyRate | HourlyRaise | NewHourlyRate | CurrentAnnualComp | AnnualRaise | NewAnnualComp |
|---:|------------------:|------------:|--------------:|------------------:|------------:|--------------:|
| 1  | 35               | 0.6891      | 35.6891       | 72,800            | 1,433.27    | 74,233.27     |
| 2  | 49               | 0.6891      | 49.6891       | 101,920           | 1,433.27    | 103,353.27    |
| 3  | 47               | 0.6891      | 47.6891       | 97,760            | 1,433.27    | 99,193.27     |
| 5  | 25               | 0.6891      | 25.6891       | 52,000            | 1,433.27    | 53,433.27     |
| 6  | 48               | 0.6891      | 48.6891       | 99,840            | 1,433.27    | 101,273.27    |



### 3. **Build a dashboard for HR leadership**  
   - Visualize absenteeism trends  
   - Enable exploration by health status, education, and hourly compensation 

#### Final Dashboard
[Click here to view the interactive dashboard](https://app.powerbi.com/view?r=eyJrIjoiNjE4MzJmYTgtMTE0YS00MjA4LTliZmMtMzJmYmNhYzQ4MzAzIiwidCI6IjllZjlmNDg5LWUwYTAtNGVlYi04N2NjLTNhNTI2MTEyZmQwZCIsImMiOjF9)  

[Screenshots of the dashboard](https://github.com/ColtonDumm/Employee_Incentive_Analysis/blob/master/HR_Dashboard.png)

---

## Tools & Skills
- **SQL (Microsoft SQL Server)**: joins, filters, aggregations, case logic, query optimization  
- **Power BI**: data modeling, KPIs, custom sorting, interactive visuals, dashboard design  
- **GitHub**: project documentation and version control  

---

## Data Model

#### Overview
The project uses three SQL Server tables:

- **Absenteeism_at_work** – transactional records of employee absences (multiple rows per employee).
- **Reasons** – lookup table mapping `Number` → `Reason` (text).
- **compensation** – one row per employee with current hourly pay (`comp_hr`).

This is a **transaction + lookup + attribute** setup.

---

#### Tables (concise)
**Absenteeism_at_work**
- Keys/links:  
  - `ID` (employee identifier)  
  - `Reason_for_absence` (links to `Reasons.Number`)
- Measures/metrics:  
  - `Absenteeism_time_in_hours`, `Work_load_Average_day`  
- Attributes (selected):  
  - `Month_of_absence`, `Day_of_the_week`, `Seasons`  
  - `Age`, `Education`, `Service_time`  
  - `Social_smoker`, `Social_drinker` 
  - `Body_mass_index`, `Height`, `Weight`  
  - `Transportation_expense`, `Distance_from_Residence_to_Work`, `Hit_target`, `Disciplinary_failure`

**Reasons**
- `Number` (PK) → short code
- `Reason` → description of the absence reason

**compensation**
- `ID` (PK) → employee identifier
- `comp_hr` → hourly compensation

---

#### Notes / Limitations
- There is **no true date column**—only month/day-of-week/season codes. If a real date becomes available, a proper `Date` dimension can be added.
- Employee attributes (age, education, BMI, etc.) live on the transactional table and there is **no separate employee dimension** in the source.
- `comp_hr` is stored in `compensation` and joined by `ID`.

---

## Project Workflow
1. **Database Setup** – Load HR dataset into SQL Server  
2. **SQL Queries** – Join and filter data to analyze absenteeism, workload, and compensation  
3. **Compensation Modeling** – Use SQL `CASE` statements to simulate different bonus rules  
4. **Power BI Integration** – Import SQL results and prepare the data model  
5. **Dashboard Build** – Create visuals, KPIs, and slicers to allow HR leaders to explore insights  

---

## Key Insights
- Employees who are abstain from drinking and smoking take on average two less days absent. This leads to the company getting more labor and likely less payout to insure them.
- Employees who have attained higher levels of education take less days off than their lesser educated counterparts.
- The beginning and end months of summer have the highest total time of absenteeism. This is likely caused by a mix of vacations and kids getting off and going to school. Work productivity may dip during these periods and should be planned for accordingly,
- Incentive programs can be structured to reward **low absenteeism and healthy work habits**.  

---

## Personal Contact 
- [LinkedIn](https://linkedin.com/in/coltondumm/)  
- [GitHub](https://github.com/ColtonDumm)  
