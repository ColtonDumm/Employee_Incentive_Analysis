# HR Absenteeism & Health Incentive Analysis | SQL + Power BI

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

*Note: This dataset is simulated. Some values don’t always align with real-world logic (for example, PhD salaries showing lower averages than high school salaries). In a real business setting, these anomalies would warant a data quality review. The focus here is on demonstrating technical workflow and communication of results, not the accuracy of the sample data.*  

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
```sql
WITH base AS (
    SELECT
        ID,
        Body_mass_index,
        Social_smoker,
        Disciplinary_failure,
        Absenteeism_time_in_hours,
        AVG(Absenteeism_time_in_hours) OVER () AS avg_abs_hrs
    FROM Absenteeism_at_work
)
SELECT DISTINCT ID, Absenteeism_time_in_hours
FROM base
WHERE Social_smoker = 0
  AND Disciplinary_failure = 0
  AND Body_mass_index BETWEEN 18.5 AND 24.9
  AND Absenteeism_time_in_hours <= avg_abs_hrs;
```

#### Eligible Employees (preview)

| ID  | Absenteeism (hrs) |
| ---:| -----------------: |
| 66  | 1 |
| 82  | 1 |
| 89  | 1 |
| 114 | 1 |
| 121 | 1 | 


2. **Calculate wage adjustments for non-smokers**  
   - Determine annual compensation increases  
   - Insurance budget: $983,221 allocated for non-smokers  

```sql
--Create a table to select nonsmokers for the raise from the $983,221 budget
--Set the constants
DECLARE @Budget MONEY = 983221; -- $983,221 budget to distribute
DECLARE @HoursPerYear INT = 2080; -- 40 hrs * 52 wks

--Count of eligible people (non-smokers)
DECLARE @NonSmokerCount INT =
(
    SELECT COUNT(DISTINCT a.ID)
    FROM Absenteeism_at_work a
    WHERE a.Social_smoker = 0
);

--Compute raise per person and per hour
DECLARE @AnnualRaisePerEmployee DECIMAL(18,2) = @Budget / @NonSmokerCount;
DECLARE @HourlyRaisePerEmployee DECIMAL(18,4) = @AnnualRaisePerEmployee / @HoursPerYear;

--Per employee results (non-smokers)
SELECT
    a.ID,
    c.comp_hr as CurrentHourlyRate,
    @HourlyRaisePerEmployee as HourlyRaise,
    c.comp_hr + @HourlyRaisePerEmployee as NewHourlyRate,
    c.comp_hr * @HoursPerYear as CurrentAnnualComp,
    @AnnualRaisePerEmployee as AnnualRaise,
    c.comp_hr * @HoursPerYear + @AnnualRaisePerEmployee as NewAnnualComp
FROM Absenteeism_at_work a
JOIN compensation c on c.ID = a.ID
WHERE a.Social_smoker = 0
ORDER BY a.ID;
```

#### Non-smoker Wage Adjustments (prieview)

| ID | CurrentHourlyRate | HourlyRaise | NewHourlyRate | CurrentAnnualComp | AnnualRaise | NewAnnualComp |
|---:|------------------:|------------:|--------------:|------------------:|------------:|--------------:|
| 1  | 35               | 0.6891      | 35.6891       | 72,800            | 1,433.27    | 74,233.27     |
| 2  | 49               | 0.6891      | 49.6891       | 101,920           | 1,433.27    | 103,353.27    |
| 3  | 47               | 0.6891      | 47.6891       | 97,760            | 1,433.27    | 99,193.27     |
| 5  | 25               | 0.6891      | 25.6891       | 52,000            | 1,433.27    | 53,433.27     |
| 6  | 48               | 0.6891      | 48.6891       | 99,840            | 1,433.27    | 101,273.27    |



3. **Build a dashboard for HR leadership**  
   - Visualize absenteeism trends  
   - Enable exploration by health status, education, and hourly compensation 

#### Final Dashboard
[Click here to view the interactive dashboard](https://app.powerbi.com/view?r=eyJrIjoiNjE4MzJmYTgtMTE0YS00MjA4LTliZmMtMzJmYmNhYzQ4MzAzIiwidCI6IjllZjlmNDg5LWUwYTAtNGVlYi04N2NjLTNhNTI2MTEyZmQwZCIsImMiOjF9)  

*(If you’d like to view screenshots, they are included in this repo.)*

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
- People who are abstain from drinking and smoking take on average two less days absent. This leads to the company getting more labor and likely less payout to insure them.
- Employsse who have attained higher levels of eduaction take less days off than their lesser educated counterparts.
- The beginning and end months of summer have the highest total time of absenteeism. This is likely caused by a mix of vacations and kids getting off and going to school. Work productivity may dip during these periods and should be planned for accordingly,
- Incentive programs can be structured to reward **low absenteeism and healthy work habits**.  

---

## Personal Contact 
- [LinkedIn](https://linkedin.com/in/coltondumm/)  
- [GitHub](https://github.com/ColtonDumm)  
