# HR SQL Project Brief

## Workforce Retention, Performance and Employee Experience Analysis

## 1. Business scenario

You are working as a **People Analytics Associate** for a mid-sized organization. Management wants a clearer view of workforce composition, employee retention, compensation, performance, engagement, attendance, recruitment sources, and manager-level workforce indicators.

Your task is to analyze the HR dataset using SQL and prepare a structured report on GitHub. The report must show the queries, query results, explanations, findings, and practical HR recommendations.

## 2. Dataset

Use the file `HRDataset_v14.csv` and import it into DBeaver as:

```text
hr_employee_data
```

The dataset contains **311 employee records and 36 columns**. Important fields include:

- `EmpID`: employee identifier
- `Employee_Name`: employee name
- `Salary`: annual salary
- `Termd`: termination indicator, where 0 means not terminated and 1 means terminated
- `Position`: job title
- `DateofHire`: hire date
- `DateofTermination`: termination date
- `TermReason`: termination reason
- `EmploymentStatus`: active or termination category
- `Department`: employee department
- `ManagerName`: employee manager
- `RecruitmentSource`: hiring source
- `PerformanceScore`: performance category
- `EngagementSurvey`: engagement score
- `EmpSatisfaction`: employee satisfaction score
- `SpecialProjectsCount`: number of special projects
- `LastPerformanceReview_Date`: most recent performance review date
- `DaysLateLast30`: days late during the last 30 days
- `Absences`: number of absences
- `Sex`, `RaceDesc`, `MaritalDesc`: demographic fields

## 3. Main project objectives

The analysis should help management understand:

1. The current workforce structure.
2. Where employee termination is concentrated.
3. Whether compensation differs across departments, positions, and employee groups.
4. How performance, engagement, satisfaction, and attendance vary across the workforce.
5. Which recruitment sources and managers show stronger or weaker workforce outcomes.
6. Which areas may require further HR investigation.

## 4. Mandatory report requirements

Your GitHub report must contain:

- A clear hierarchy of Markdown headers.
- A project overview and business context.
- A dataset and table overview.
- A query showing the first 5 to 10 rows of the table.
- Every SQL query used in the project.
- A Markdown result table for every analytical query.
- A plain-language explanation of what each query does.
- Findings and insights based on the results.
- A final conclusion and practical HR recommendations.
- A limitations section.
- All fractional values rounded to **2 decimal places**.

Do not claim causation when the analysis only shows association. For example, a lower engagement score among terminated employees does not prove that low engagement caused termination.

---

# 5. Research questions

## Section A: Dataset overview and data quality

### Question 1: Dataset overview

Provide the following metrics:

- Total employee records
- Number of active employees
- Number of terminated employees
- Number of departments
- Number of positions
- Earliest hire date
- Latest hire date

Suggested result columns:

| Metric | Value |
|---|---:|

### Question 2: Data-quality assessment

Check and report:

- Duplicate `EmpID` values
- Duplicate employee names
- Blank or null departments
- Blank or null positions
- Blank or null manager names
- Employees marked as terminated but missing `DateofTermination`
- Employees marked as active but containing a termination date
- Termination dates earlier than hire dates
- Salary values equal to or below zero
- Text fields containing leading or trailing spaces

Do not delete records immediately. First identify the issue, count the affected records, and explain whether it could affect the analysis.

---

## Section B: Workforce composition

### Question 3: Current headcount by department

Which departments have the largest and smallest current workforces?

Required output:

| Department | Current employees | Workforce percentage |
|---|---:|---:|

Use only active employees.

### Question 4: Most common current positions

What are the 10 most common positions among active employees?

Required output:

| Position | Current employees | Average salary |
|---|---:|---:|

### Question 5: Hiring trend by year

How has hiring volume changed over time?

Required output:

| Hiring year | Number of hires |
|---:|---:|

Identify the highest-hiring year and any noticeable increases or decreases.

### Question 6: Workforce distribution by gender

How is the current workforce distributed by gender?

Required output:

| Gender | Current employees | Workforce percentage | Average salary |
|---|---:|---:|---:|

Clean leading or trailing spaces in the gender field before grouping.

---

## Section C: Retention and termination

### Question 7: Termination concentration by department

Which departments have the highest share of terminated employee records?

Required output:

| Department | Total employee records | Terminated employees | Termination percentage |
|---|---:|---:|---:|

This is not an annual turnover rate. Describe it as the percentage of records associated with termination in this dataset.

### Question 8: Most common termination reasons

What are the most common reasons employees left the organization?

Required output:

| Termination reason | Number of employees | Percentage of terminations |
|---|---:|---:|

Exclude records representing employees who are still employed.

### Question 9: Tenure before termination

How long did terminated employees remain with the organization before leaving?

Create tenure groups:

- Under 1 year
- 1 to under 3 years
- 3 to under 5 years
- 5 years or more

Required output:

| Tenure group | Terminated employees | Percentage of terminations |
|---|---:|---:|

Also calculate the average tenure at termination.

---

## Section D: Compensation and performance

### Question 10: Salary by department

How does salary differ across departments?

Required output:

| Department | Employees | Minimum salary | Average salary | Maximum salary |
|---|---:|---:|---:|---:|

### Question 11: Performance and employee outcomes

How do salary, engagement, satisfaction, absences, and lateness differ across performance categories?

Required output:

| Performance score | Employees | Average salary | Average engagement | Average satisfaction | Average absences | Average late days |
|---|---:|---:|---:|---:|---:|---:|

### Question 12: Active versus terminated employees

Compare current and terminated employees across key workforce indicators.

Required output:

| Employee group | Employees | Average salary | Average engagement | Average satisfaction | Average absences | Average late days |
|---|---:|---:|---:|---:|---:|---:|

Use two groups:

- Active
- Terminated

Interpret differences carefully. The results show associations within the dataset, not causes of termination.

---

## Section E: Recruitment and manager analysis

### Question 13: Recruitment-source outcomes

Which recruitment sources appear to produce stronger employee outcomes?

Required output:

| Recruitment source | Hires | Active employees | Active percentage | Average salary | Average performance-related score | Average engagement |
|---|---:|---:|---:|---:|---:|---:|

Because `PerformanceScore` is categorical, define and explain any numeric scoring system before calculating an average. Alternatively, report the percentage of employees rated `Exceeds` or `Fully Meets`.

### Question 14: Workforce indicators by manager

Which managers oversee teams with stronger or weaker workforce indicators?

Required output:

| Manager | Employee records | Active employees | Terminated employees | Termination percentage | Average engagement | Average satisfaction | Average absences |
|---|---:|---:|---:|---:|---:|---:|---:|

Only include managers with at least 5 employee records to avoid ranking tiny teams as if one person represents an empire.

---

# 6. Optional advanced questions

## Optional 1: Salary comparison within comparable positions

Compare average salary by gender within the same position. Only include positions represented by both gender groups and clearly report employee counts.

## Optional 2: Employees who may require proactive HR attention

Identify active employees who meet at least two of the following conditions:

- Engagement below the company average
- Satisfaction below the company average
- Absences above the company average
- Days late above the company average
- Performance score is `Needs Improvement` or `PIP`

Call this group **employees who may require attention**, not employees who will resign.

## Optional 3: Recruitment source quality matrix

Classify recruitment sources using both hiring volume and active-employee percentage:

- High volume / high active percentage
- High volume / low active percentage
- Low volume / high active percentage
- Low volume / low active percentage

Explain how HR could use the matrix when allocating recruitment effort.

---

# 7. Expected final insights

Your conclusions should address questions such as:

- Which department represents the largest share of the workforce?
- Where are terminated records most concentrated?
- What termination reasons appear most frequently?
- Are terminated employees associated with different engagement, satisfaction, or attendance patterns?
- Which departments or positions have the highest average salary?
- Which recruitment sources combine hiring volume with stronger employee outcomes?
- Which managers or teams may require further investigation?

Do not merely restate the table. Explain why the finding could matter to HR or workforce planning.

# 8. Recommended GitHub report structure

```text
# Workforce Retention, Performance and Employee Experience Analysis

## 1. Project Overview
## 2. Data Source
## 3. Dataset Structure
## 4. Data Quality Assessment
## 5. Workforce Composition
## 6. Retention and Termination Analysis
## 7. Compensation and Performance Analysis
## 8. Employee Experience and Attendance
## 9. Recruitment and Manager Analysis
## 10. Key Insights
## 11. Recommendations
## 12. Limitations
## 13. Conclusion
```

For each research question, include:

```text
### Research Question
#### SQL Query
#### Results
#### Query Explanation
#### Findings and Insights
```

# 9. Limitations to include

- The dataset represents a fictitious organization created for learning purposes.
- It is a workforce snapshot and does not contain complete historical headcount for calculating a formal annual turnover rate.
- Associations do not prove causation.
- Some groups contain few employees and may not support broad conclusions.
- Salary comparisons do not fully control for experience, seniority, education, or location.
- Employee names are included in the source dataset, so avoid presenting unnecessary personal-level details in the public report.

# 10. Suggested scope

Complete Questions **1 to 12** as the core project.

Complete Questions **13 and 14** to make the project stronger for a People Analytics or Workforce Operations portfolio.

Treat the three advanced questions as optional extensions after learning subqueries or CTEs.
