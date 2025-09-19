# HR Absenteeism & Incentive Analysis | SQL + Power BI

## 📊 Project Overview
This project explores employee absenteeism and health data to understand workforce trends and design potential incentive programs.  
The work covers the full process: building a SQL database, writing queries to analyze absenteeism patterns, and creating an interactive Power BI dashboard for HR decision-making.  

---

## 🎯 Problem & Mission
Employee absenteeism can lead to lower productivity and higher costs. HR leaders often look for ways to reward reliable and healthy employees while managing overall workforce costs.  

The mission of this project was to:
- Load and manage HR data in SQL Server  
- Use SQL queries to explore absenteeism drivers and calculate potential incentive payouts  
- Build a dashboard in Power BI to visualize results and make the findings easy to interpret  

*Note: This dataset is simulated for practice. Some values don’t always align with real-world logic (for example, PhD salaries showing lower averages than high school salaries). In a real business setting, these anomalies would trigger a data quality review. The focus here is on demonstrating technical workflow and communication of results, not the accuracy of the sample data.*  

---

## 📝 HR Requests
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

2. **Calculate wage adjustments for non-smokers**  
   - Determine annual compensation increases  
   - Insurance budget: $983,221 allocated for non-smokers  

3. **Build a dashboard for HR leadership**  
   - Visualize absenteeism trends  
   - Enable exploration by health status, education, and workload  

---

## 🛠️ Tools & Skills
- **SQL (Microsoft SQL Server)**: joins, filters, aggregations, case logic, query optimization  
- **Power BI**: data modeling, KPIs, custom sorting, interactive visuals, dashboard design  
- **GitHub**: project documentation and version control  

---

## 📂 Dataset
The dataset includes information on employee demographics, education, workload, health indicators, and reasons for absence.  
It was lightly cleaned and structured before being used for SQL queries and Power BI reporting.  

---

## 📑 Project Workflow
1. **Database Setup** – Load HR dataset into SQL Server  
2. **SQL Queries** – Join and filter data to analyze absenteeism, workload, and compensation  
3. **Compensation Modeling** – Use SQL `CASE` statements to simulate different bonus rules  
4. **Power BI Integration** – Import SQL results and prepare the data model  
5. **Dashboard Build** – Create visuals, KPIs, and slicers to allow HR leaders to explore insights  

---

## 📈 Final Dashboard
👉 [Click here to view the interactive dashboard](https://app.powerbi.com/view?r=eyJrIjoiNjE4MzJmYTgtMTE0YS00MjA4LTliZmMtMzJmYmNhYzQ4MzAzIiwidCI6IjllZjlmNDg5LWUwYTAtNGVlYi04N2NjLTNhNTI2MTEyZmQwZCIsImMiOjF9)  

*(If you’d like to view screenshots, they are included in this repo.)*

---

## 📌 Key Insights
- Absenteeism reasons and certain health conditions are strongly correlated with higher absence days.  
- Education level and workload show trends in relation to compensation and absence rates.  
- Incentive programs can be structured to reward **low absenteeism and healthy work habits**.  

---

## Personal Contact
If you’d like to connect or collaborate, reach out via:  
- [LinkedIn](https://linkedin.com/in/coltondumm/)  
- GitHub: [Colton Dumm](https://github.com/ColtonDumm)  
