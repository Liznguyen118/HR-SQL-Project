# HR SQL Project

## 1. Project Overview

This project analyzes an HR dataset containing 311 employee records to explore workforce composition, employee retention, compensation, performance, engagement, attendance, and recruitment outcomes.

The analysis uses SQL to identify workforce patterns, compare employee groups, highlight potential HR concerns, and provide practical insights for workforce planning and employee retention.

The main objectives are to:

- Analyze workforce distribution by department and position.
- Examine termination patterns and common reasons for leaving.
- Compare salary, performance, engagement, and attendance indicators.
- Evaluate recruitment sources and manager-level workforce outcomes.
- Provide data-based HR insights and recommendations.

## 2. Data Source

This project uses `HRDataset_v14.csv`, a simulated HR dataset containing 311 employee records and 36 columns.

The dataset includes information about:

- Employee demographics and employment status
- Departments, positions, managers, and salaries
- Hiring and termination dates
- Termination reasons
- Performance, engagement, and satisfaction
- Attendance and lateness
- Recruitment sources

The dataset was imported into SQLite as the `hr_employee_data` table.

### 2.1 Data Preview

```SQL
SELECT * FROM HRDataset_v14 LIMIT 10;
```
The result: This query displays the first 10 records to review the table structure, column names, and sample values before conducting the analysis.
|Employee_Name|EmpID|MarriedID|MaritalStatusID|GenderID|EmpStatusID|DeptID|PerfScoreID|FromDiversityJobFairID|Salary|Termd|PositionID|Position|State|Zip|DOB|Sex|MaritalDesc|CitizenDesc|HispanicLatino|RaceDesc|DateofHire|DateofTermination|TermReason|EmploymentStatus|Department|ManagerName|ManagerID|RecruitmentSource|PerformanceScore|EngagementSurvey|EmpSatisfaction|SpecialProjectsCount|LastPerformanceReview_Date|DaysLateLast30|Absences|
|-------------|-----|---------|---------------|--------|-----------|------|-----------|----------------------|------|-----|----------|--------|-----|---|---|---|-----------|-----------|--------------|--------|----------|-----------------|----------|----------------|----------|-----------|---------|-----------------|----------------|----------------|---------------|--------------------|--------------------------|--------------|--------|
|Adinolfi, Wilson  K|10026|0|0|1|1|5|4|0|62506|0|19|Production Technician I|MA|1960|07/10/83|M |Single|US Citizen|No|White|7/5/2011||N/A-StillEmployed|Active|Production       |Michael Albert|22|LinkedIn|Exceeds|4.6|5|0|1/17/2019|0|1|
|Ait Sidi, Karthikeyan   |10084|1|1|1|5|3|3|0|104437|1|27|Sr. DBA|MA|2148|05/05/75|M |Married|US Citizen|No|White|3/30/2015|6/16/2016|career change|Voluntarily Terminated|IT/IS|Simon Roup|4|Indeed|Fully Meets|4.96|3|6|2/24/2016|0|17|
|Akinkuolie, Sarah|10196|1|1|0|5|5|3|0|64955|1|20|Production Technician II|MA|1810|09/19/88|F|Married|US Citizen|No|White|7/5/2011|9/24/2012|hours|Voluntarily Terminated|Production       |Kissy Sullivan|20|LinkedIn|Fully Meets|3.02|3|0|5/15/2012|0|3|
|Alagbe,Trina|10088|1|1|0|1|5|3|0|64991|0|19|Production Technician I|MA|1886|09/27/88|F|Married|US Citizen|No|White|1/7/2008||N/A-StillEmployed|Active|Production       |Elijiah Gray|16|Indeed|Fully Meets|4.84|5|0|1/3/2019|0|15|
|Anderson, Carol |10069|0|2|0|5|5|3|0|50825|1|19|Production Technician I|MA|2169|09/08/89|F|Divorced|US Citizen|No|White|7/11/2011|9/6/2016|return to school|Voluntarily Terminated|Production       |Webster Butler|39|Google Search|Fully Meets|5.0|4|0|2/1/2016|0|2|
|Anderson, Linda  |10002|0|0|0|1|5|4|0|57568|0|19|Production Technician I|MA|1844|05/22/77|F|Single|US Citizen|No|White|1/9/2012||N/A-StillEmployed|Active|Production       |Amy Dunn|11|LinkedIn|Exceeds|5.0|5|0|1/7/2019|0|15|
|Andreola, Colby|10194|0|0|0|1|4|3|0|95660|0|24|Software Engineer|MA|2110|05/24/79|F|Single|US Citizen|No|White|11/10/2014||N/A-StillEmployed|Active|Software Engineering|Alex Sweetwater|10|LinkedIn|Fully Meets|3.04|3|4|1/2/2019|0|19|
|Athwal, Sam|10062|0|4|1|1|5|3|0|59365|0|19|Production Technician I|MA|2199|02/18/83|M |Widowed|US Citizen|No|White|9/30/2013||N/A-StillEmployed|Active|Production       |Ketsia Liebig|19|Employee Referral|Fully Meets|5.0|4|0|2/25/2019|0|19|
|Bachiochi, Linda|10114|0|0|0|3|5|3|1|47837|0|19|Production Technician I|MA|1902|02/11/70|F|Single|US Citizen|No|Black or African American|7/6/2009||N/A-StillEmployed|Active|Production       |Brannon Miller|12|Diversity Job Fair|Fully Meets|4.46|3|0|1/25/2019|0|4|
|Bacong, Alejandro |10250|0|2|1|1|3|3|0|50178|0|14|IT Support|MA|1886|01/07/88|M |Divorced|US Citizen|No|White|1/5/2015||N/A-StillEmployed|Active|IT/IS|Peter Monroe|7|Indeed|Fully Meets|5.0|5|6|2/18/2019|0|16|

## 3. Dataset Structure

### 3.1 Record and Employee ID Validation
Before conducting the workforce analysis, several data-quality checks were performed to verify record uniqueness, missing values, and logical consistency.

```SQL
SELECT
	COUNT (*) AS total_record,
	COUNT (DISTINCT EmpID) AS unique_employee_id,
	SUM (CASE WHEN Termd = 0 THEN 1 ELSE 0 END ) AS active_employee,
	SUM (CASE WHEN Termd = 1 THEN 1 ELSE 0 END ) AS terminated_employees
FROM HRDataset_v14;
```

The result:
|total_record|unique_employee_id|active_employee|terminated_employees|
|------------|------------------|---------------|--------------------|
|311|311|207|104|

The dataset contains 311 employee records and 311 unique employee IDs, indicating that no duplicate employee IDs are present. Of these records, 207 employees are active and 104 have been terminated.

### 3.2 Employment Status Consistency

```SQL
SELECT
    SUM(
        CASE
            WHEN Termd = 1
                 AND (
                     DateofTermination IS NULL
                     OR TRIM(DateofTermination) = ''
                 )
            THEN 1
            ELSE 0
        END
    ) AS terminated_missing_date,

    SUM(
        CASE
            WHEN Termd = 0
                 AND DateofTermination IS NOT NULL
                 AND TRIM(DateofTermination) <> ''
            THEN 1
            ELSE 0
        END
    ) AS active_with_termination_date
	FROM HRDataset_v14;
```
The result:
|terminated_missing_date|active_with_termination_date|
|-----------------------|----------------------------|
|0|0|

This check verifies that terminated employees have a termination date and that active employees do not. No inconsistencies were found between the termination indicator and termination date.

### 3.3 Missing and Invalid Values

```SQL
SELECT
    SUM(
        CASE
            WHEN EmpID IS NULL THEN 1
            ELSE 0
        END
    ) AS missing_emp_id,

    SUM(
        CASE
            WHEN Employee_Name IS NULL
                 OR TRIM(Employee_Name) = ''
            THEN 1
            ELSE 0
        END
    ) AS missing_employee_name,

    SUM(
        CASE
            WHEN Department IS NULL
                 OR TRIM(Department) = ''
            THEN 1
            ELSE 0
        END
    ) AS missing_department,

    SUM(
        CASE
            WHEN Position IS NULL
                 OR TRIM(Position) = ''
            THEN 1
            ELSE 0
        END
    ) AS missing_position,

    SUM(
        CASE
            WHEN DateofHire IS NULL
                 OR TRIM(DateofHire) = ''
            THEN 1
            ELSE 0
        END
    ) AS missing_hire_date,

    SUM(
        CASE
            WHEN Salary IS NULL THEN 1
            ELSE 0
        END
    ) AS missing_salary,

    SUM(
        CASE
            WHEN Salary <= 0 THEN 1
            ELSE 0
        END
    ) AS invalid_salary
    FROM HRDataset_v14;
```
The result:
|missing_emp_id|missing_employee_name|missing_department|missing_position|missing_hire_date|missing_salary|invalid_salary|
|--------------|---------------------|------------------|----------------|-----------------|--------------|--------------|
|0|0|0|0|0|0|0|

No missing or invalid values were found in the key fields required for the workforce analysis. The dataset is sufficiently complete for department, position, hiring, and compensation analysis.

## 4. Workforce Composition

### 4.1 Current Workforce by Department

```SQL
SELECT 
		Department,
		COUNT (*) AS employee_record,
		ROUND (
			 COUNT(*) *100.0 /
				(SELECT 
					COUNT (*) 
					FROM HRDataset_v14
					WHERE Termd = 0),2) AS workforce_percentage
	FROM HRDataset_v14
	WHERE Termd = 0
	GROUP BY Department
	ORDER BY employee_record DESC;
```

The result:
|Department|employee_record|workforce_percentage|
|----------|---------------|--------------------|
|Production       |126|60.87|
|IT/IS|40|19.32|
|Sales|26|12.56|
|Software Engineering|7|3.38|
|Admin Offices|7|3.38|
|Executive Office|1|0.48|

The query filters the dataset to include only current employees, groups them by department, and calculates each department's share of the active workforce.

Insight: 
- Production is the largest department, accounting for 60.87% of the current workforce. IT/IS is the second-largest department with 19.32%, followed by Sales with 12.56%.
- The workforce is therefore highly concentrated in Production. This suggests that workforce planning, attendance management, and retention initiatives within Production could have a substantial impact on the organization as a whole.

### 4.2 Most Common Positions Among Current Employees
```SQL
	SELECT 
		position,
		COUNT (*) AS Employee_record,
		ROUND(AVG(Salary),2) AS average_salary
	FROM HRDataset_v14 hv
	WHERE Termd = 0 
	GROUP BY position
	ORDER BY employee_record DESC, average_salary DESC;
```
The result:
|Position|Employee_record|average_salary|
|--------|---------------|--------------|
|Production Technician I|85|56061.02|
|Production Technician II|31|65108.06|
|Area Sales Manager|23|64243.0|
|Production Manager|9|73198.89|
|IT Support|8|63684.38|
|Software Engineer|6|95857.67|
|Data Analyst|6|89405.83|
|Sr. Network Engineer|5|93070.8|
|BI Developer|4|95465.0|
|Network Engineer|4|58186.0|
|Senior BI Developer|3|84802.67|
|Accountant I|3|63507.67|
|Sr. Accountant|2|102859.0|
|Database Administrator|2|101849.5|
|Sales Manager|2|69360.5|
|President & CEO|1|250000.0|
|CIO|1|220450.0|
|Director of Sales|1|180000.0|
|IT Director|1|178000.0|
|Director of Operations|1|170500.0|
|IT Manager - Infra|1|157000.0|
|Data Architect|1|150290.0|
|IT Manager - DB|1|140920.0|
|IT Manager - Support|1|138888.0|
|BI Director|1|110929.0|
|Sr. DBA|1|100031.0|
|Shared Services Manager|1|93046.0|
|Software Engineering Manager|1|77692.0|
|Administrative Assistant|1|51920.0|

Insight:
- Production Technician I is the most common position, with 85 current employees, followed by Production Technician II with 31 employees.
- The two production technician roles account for a substantial share of the active workforce, which is consistent with Production being the organization’s largest department. This suggests that workforce planning and retention initiatives for production technicians could affect a large portion of employees.

### 4.3 Hiring Trend

```SQL
	SELECT 
		SUBSTR(TRIM(DateofHire),-4) AS hiring_year,
		COUNT (*) AS number_of_hire
	FROM HRDataset_v14 hv 
	GROUP BY hiring_year 
	ORDER BY hiring_year ;
```
The result:
|hiring_year|number_of_hire|
|-----------|--------------|
|2006|1|
|2007|2|
|2008|3|
|2009|7|
|2010|9|
|2011|83|
|2012|45|
|2013|44|
|2014|60|
|2015|36|
|2016|14|
|2017|6|
|2018|1|

Insight:
- Hiring increased substantially in 2011, when the organization recorded 83 hires, the highest number in the dataset. Another notable hiring period occurred in 2014 with 60 hires.
- After 2015, hiring volume declined sharply, falling to 14 hires in 2016, 6 in 2017, and 1 in 2018. However, the later years may represent partial-year or incomplete data, so the decline should be interpreted cautiously.

### 4.4 Current Workforce by Gender
```SQL
SELECT 
		CASE 
			WHEN TRIM(Sex) = 'M' THEN 'Male'
			WHEN Sex = 'F' THEN 'Female'
			ELSE 'Unknown'
		END AS Gender,
		COUNT (*) AS current_employee,
		ROUND (
			COUNT (*) * 100.0 / 
				(SELECT COUNT (*) 
				 FROM HRDataset_v14
				 WHERE Termd = 0)
				,2) AS workforce_percentage
		FROM HRDataset_v14 hv 
		WHERE Termd = 0
		GROUP BY Gender
		ORDER BY current_employee DESC;
```

The result:
|Gender|current_employee|workforce_percentage|
|------|----------------|--------------------|
|Female|116|56.04|
|Male|91|43.96|

This query filters the dataset to include current employees and groups them by gender. It calculates the number and percentage of current employees in each group.

Insight:

Female employees represent 56.04% of the current workforce, while male employees account for 43.96%. The overall current workforce is relatively balanced, with a moderately higher representation of female employees.


## 5. Employee Retention Analysis
### 5.1 Terminated Employee Records by Department

```SQL
SELECT 
		TRIM (Department) AS Department,
		COUNT (*) 		  AS total_employee,
		SUM(
		CASE 	
			WHEN Termd = 1 THEN 1 
			ELSE 0
		END) AS Terminated_employee,
		ROUND (
			SUM(
				CASE
					WHEN Termd = 1 THEN 1 
					ELSE 0 
				END) * 100.0 / COUNT (*)
				,2) AS terminated_percentage
		FROM HRDataset_v14
		GROUP BY Department
		ORDER BY  terminated_percentage DESC;
```
The result:
|Department|total_employee|Terminated_employee|terminated_percentage|
|----------|--------------|-------------------|---------------------|
|Production|209|83|39.71|
|Software Engineering|11|4|36.36|
|Admin Offices|9|2|22.22|
|IT/IS|50|10|20.0|
|Sales|31|5|16.13|
|Executive Office|1|0|0.0|

This query groups employee records by department and calculates the number and percentage of terminated employees within each department.

Insight:
- Production has the highest number of terminated employees and the highest termination percentage at 39.71%. Software Engineering has the second-highest percentage at 36.36%, although the department contains only 11 employee records.
- The results should be interpreted with consideration of department size, as percentages based on small groups may be less representative.

### 5.2 Common Termination Reasons
```SQL
SELECT
			TRIM (TermReason) AS Termination_reason,
			COUNT (*) AS Number_of_termination,
			ROUND (
				COUNT (*) *100.0 /
					(SELECT
						COUNT (*) 
					FROM HRDataset_v14
					WHERE Termd = 1)
					,2) AS Termination_percentage
		FROM HRDataset_v14 hv 
		WHERE Termd = 1
		GROUP BY hv.Department 
		ORDER BY  Number_of_termination DESC;
```

The result:
|Termination_reason|Number_of_termination|Termination_percentage|
|------------------|---------------------|----------------------|
|Another position|20|19.23|
|unhappy|14|13.46|
|more money|11|10.58|
|career change|9|8.65|
|hours|8|7.69|
|attendance|7|6.73|
|return to school|5|4.81|
|relocation out of area|5|4.81|
|retiring|4|3.85|
|performance|4|3.85|
|no-call, no-show|4|3.85|
|military|4|3.85|
|medical issues|3|2.88|
|maternity leave - did not return|3|2.88|
|gross misconduct|1|0.96|
|Learned that he is a gangster|1|0.96|
|Fatal attraction|1|0.96|


Insight:
- The most common termination reason is moving to another position, accounting for 19.23% of all terminated employee records. This is followed by unhappiness at 13.46% and seeking more money at 10.58%.
- Several of the leading reasons, including another position, unhappiness, higher pay, career change, and working hours, appear to be voluntary and potentially related to employee retention. However, the dataset does not provide enough context to determine the underlying causes of these decisions.

### 5.3 Termination by Position
```SQL
SELECT 
			TRIM (Position) AS Position,
			COUNT (*) AS total_employee,
			SUM(
			CASE 
				WHEN Termd = 1 THEN 1
				ELSE 0
			END) AS Terminated_employee,
			ROUND (
				SUM(
					CASE 
					 WHEN Termd = 1 THEN 1
			         ELSE 0
					END
					) * 100.0 / COUNT (*)
					,2) AS Termination_percentage
			FROM HRDataset_v14 hv 
			GROUP BY Position
			HAVING COUNT (*) >= 5
			ORDER BY  Termination_percentage DESC;
```

The result:
|Position|total_employee|Terminated_employee|Termination_percentage|
|--------|--------------|-------------------|----------------------|
|Database Administrator|5|3|60.0|
|Production Technician II|57|26|45.61|
|Software Engineer|10|4|40.0|
|Production Technician I|137|52|37.96|
|Production Manager|14|5|35.71|
|Network Engineer|5|1|20.0|
|Area Sales Manager|27|4|14.81|
|Data Analyst|7|1|14.29|
|Sr. Network Engineer|5|0|0.0|
|IT Support|8|0|0.0|

Insight:
- Database Administrator has the highest termination percentage at 60.00%, but the result is based on only five employee records and should therefore be interpreted cautiously.
- Production Technician II has a termination percentage of 45.61% across 57 records, while Production Technician I has 52 terminated employees and a termination percentage of 37.96%. Because these roles also represent a large portion of the workforce, retention issues among production technicians may have a substantial operational impact.

## 6. Salary Analysis
### 6.1 Salary overview
```SQL
SELECT 	
	TRIM (Department) AS Department,
	Count (*) AS Employee,
	MIN (Salary) AS Minimum_Salary,
	MAX (Salary) AS Maximum_Salary,
	ROUND (AVG (Salary),2) AS Average_Salary
FROM HRDataset_v14 hv 
GROUP BY Department 
ORDER BY Average_Salary DESC;
```
The result:
|Department|Employee|Minimum_Salary|Maximum_Salary|Average_Salary|
|----------|--------|--------------|--------------|--------------|
|Executive Office|1|250000|250000|250000.0|
|IT/IS|50|50178|220450|97064.64|
|Software Engineering|11|77692|108987|94989.45|
|Admin Offices|9|49920|106367|71791.89|
|Sales|31|55875|180000|69061.26|
|Production|209|45046|170500|59953.55|

Insight:
- Executive Office has the highest average salary at $250,000.00, but this department contains only one employee record, so the result is not representative of a broader salary pattern.
- Among departments with larger employee groups, IT/IS has the highest average salary at $97,064.64, followed by Software Engineering at $94,989.45. Production has the lowest average salary at $59,953.55 and also represents the largest department with 209 employee records.
- The wide differences between minimum and maximum salaries within several departments suggest that they contain employees from different positions or levels of seniority. Further analysis by position would provide a more meaningful compensation comparison.

### 6.2 Performance and employee outcome:
```SQL
SELECT 
	TRIM (PerformanceScore) AS Performance_score,
	COUNT (*) AS Employee,
	ROUND (AVG(Salary),2) AS Average_salary,
	ROUND (AVG (EngagementSurvey), 2) AS Average_engagement,
	ROUND (AVG (EmpSatisfaction),2) AS Average_satisfaction,
	ROUND (AVG (DaysLateLast30),2) AS Average_lateday,
	ROUND (AVG (Absences),2) AS Average_absences
FROM HRDataset_v14 hv 
GROUP BY Performance_score
ORDER BY Employee DESC;
```

The result:
|Performance_score|Employee|Average_salary|Average_engagement|Average_satisfaction|Average_lateday|Average_absences|
|-----------------|--------|--------------|------------------|--------------------|---------------|----------------|
|Fully Meets|243|68366.72|4.24|3.95|0.02|10.22|
|Exceeds|37|77144.86|4.48|4.11|0.0|10.49|
|Needs Improvement|18|68407.56|2.99|3.61|3.78|11.33|
|PIP|13|58971.08|2.22|2.54|4.31|8.31|

This query groups employee records by performance category and calculates the number of employees in each group. It then compares the average salary, engagement score, satisfaction score, absences, and late days across the performance categories.

The results are ordered from the highest performance category (`Exceeds`) to the lowest (`PIP`) to make differences between employee groups easier to compare. All average values are rounded to two decimal places.

Insights:
- Most employees are rated `Fully Meets`, with 243 employees, while only 13 employees are in the `PIP` category.
- Employees rated `Exceeds` have the highest average salary at $77,144.86, as well as the highest average engagement score of 4.48 and satisfaction score of 4.11. This group also recorded no late days during the last 30 days.
- In contrast, employees in the `PIP` category have the lowest average engagement score at 2.22, the lowest satisfaction score at 2.54, and the highest average number of late days at 4.31. Employees rated `Needs Improvement` also show relatively low engagement and satisfaction, with an average of 3.78 late days.
- Lateness shows a clearer relationship with performance categories than absences. The `Needs Improvement` group has the highest average absences at 11.33, while the `PIP` group has a lower average of 8.31 absences. Therefore, the results do not indicate a consistent relationship between absence levels and performance.
- Although higher-performing groups generally show stronger engagement and satisfaction, these results represent associations within the dataset. They do not prove that engagement, satisfaction, salary, or attendance directly caused the performance outcomes.

### 6.3 Active and terminated employees:
```SQL
SELECT 
			CASE 
				WHEN Termd = 0 THEN 'Active'
				WHEN Termd = 1 THEN  'Terminated'
				ELSE 'Unknown'
			END AS Employee_group,	
		COUNT (*) AS Employee,
		ROUND (AVG (Salary),2) AS Average_salary,
		ROUND (AVG (EngagementSurvey),2) AS Average_engagement_score,
		ROUND (AVG (EmpSatisfaction),2) AS Average_satisfaction,
		ROUND (AVG (Absences),2) AS Average_absences,
		ROUND (AVG (DaysLateLast30),2) AS Average_Lateday
FROM HRDataset_v14
GROUP BY Employee_group 
ORDER BY Employee DESC;
```

The result:
|Employee_group|Employee|Average_salary|Average_engagement_score|Average_satisfaction|Average_absences|Average_Lateday|
|--------------|--------|--------------|------------------------|--------------------|----------------|---------------|
|Active|207|70694.03|4.12|3.89|9.83|0.29|
|Terminated|104|65690.08|4.09|3.88|11.05|0.66|

This query uses a `CASE` statement to classify employee records into two groups: `Active` and `Terminated`, based on the `Termd` field. It then groups the records by employee status and calculates the number of employees, average salary, engagement, satisfaction, absences, and late days for each group. All average values are rounded to two decimal places to make the results easier to compare.

Insight:
- The dataset contains 207 active employees and 104 terminated employees.
- Active employees have a higher average salary of $70,694.03, compared with $65,690.08 among terminated employees, a difference of approximately $5,003.95.
- Average engagement and satisfaction scores are almost identical between the two groups. Active employees have an average engagement score of 4.12 and satisfaction score of 3.89, while terminated employees record averages of 4.09 and 3.88, respectively. These small differences do not indicate a meaningful separation between the groups.
- Attendance indicators show a clearer difference. Terminated employees have an average of 11.05 absences, compared with 9.83 among active employees. They also record more late days on average, at 0.66 compared with 0.29 for active employees.
- Within this dataset, terminated employees are therefore associated with slightly lower salaries and less favourable attendance patterns. However, these results show associations only and do not prove that salary, absences, or lateness caused employee termination.

## 7. Recruitment and management analysis
### 7.1 Recruitment-source outcomes
```SQL
		SELECT 
			RecruitmentSource AS Recruitment_source,
			COUNT (*) AS Hires,
			SUM (
				CASE 
					WHEN Termd = 1 THEN 1 
					ELSE 0
				END) AS Active_employee,
			ROUND (
				SUM (
					CASE 
						WHEN Termd = 0 THEN 1
						ELSE 0
					END) *100.0 / COUNT (*)
				,2) AS Active_percentage,
			ROUND (AVG (Salary),2) AS Average_salary,
			ROUND (
				AVG(
					CASE 
						WHEN PerformanceScore = 'Exceeds' THEN 4.0
						WHEN PerformanceScore = 'Fully Meets' THEN 3.0
						WHEN PerformanceScore  = 'Needs Improvement' THEN 2.0
						WHEN PerformanceScore = 'PIP' THEN 1.0
						ELSE NULL
					END)
					,2) AS Average_performance_related_score,
			ROUND (AVG (EngagementSurvey),2) AS Average_engagement_score
		FROM HRDataset_v14
		GROUP BY Recruitment_source
		ORDER BY Hires DESC;
```

The result:
|Recruitment_source|Hires|Active_employee|Active_percentage|Average_salary|Average_performance_related_score|Average_engagement_score|
|------------------|-----|---------------|-----------------|--------------|---------------------------------|------------------------|
|Indeed|87|21|75.86|75495.75|2.98|3.99|
|LinkedIn|76|18|76.32|64903.32|3.0|4.14|
|Google Search|49|30|38.78|60744.84|2.94|4.19|
|Employee Referral|31|5|83.87|77862.84|3.1|4.19|
|Diversity Job Fair|29|16|44.83|72251.24|3.0|4.08|
|CareerBuilder|23|11|52.17|63482.52|2.91|4.09|
|Website|13|1|92.31|61537.69|2.77|4.22|
|Other|2|1|50.0|83263.5|3.0|4.55|
|On-line Web application|1|1|0.0|52505.0|3.0|5.0|

Explanation:
- This query groups employee records by recruitment source and calculates the total number of hires from each source.
- It uses conditional aggregation to count employees who are still active and calculates the active-employee percentage by dividing active employees by total hires for each source.
- The query also calculates the average salary, average engagement score, and average performance score for each recruitment source. Performance categories are converted into numeric values using the defined four-point scale before the average is calculated. All fractional values are rounded to two decimal places.
  
**Performance Scoring Method**

Because `PerformanceScore` is a categorical field, the performance ratings were converted into the following numeric scale:
- Exceeds = 4
- Fully Meets = 3
- Needs Improvement = 2
- PIP = 1

The numeric values were then averaged for each recruitment source. A higher average score indicates that employees recruited through that source were generally associated with stronger performance ratings.

This scale was created for this analysis and does not represent an official company performance-scoring system.

Insight:
- Indeed generated the highest hiring volume with 87 employees, followed by LinkedIn with 76. Their active-employee percentages are similar at 75.86% and 76.32%, respectively, suggesting that both sources combine substantial hiring volume with relatively strong employee retention within the dataset.
- Employee Referral appears to produce the strongest overall outcomes among sources with a meaningful number of hires. It generated 31 hires, recorded an active-employee percentage of 83.87%, and had the highest average performance score among the larger recruitment sources at 3.10. Its average engagement score was also relatively high at 4.19.
- Website recorded the highest active-employee percentage at 92.31%, but this result is based on only 13 hires. It also had the lowest average performance score at 2.77, indicating that a high active percentage alone does not necessarily represent the strongest overall recruitment outcome.
- Google Search generated 49 hires but had an active-employee percentage of only 38.78%, substantially lower than Indeed, LinkedIn, and Employee Referral. Although its average engagement score was relatively strong at 4.19, its lower active percentage may justify further investigation into the roles recruited, employee tenure, or termination patterns associated with this source.
- Sources such as Other and On-line Web Application contain only two and one employee records, respectively. Their results should not be used for broad conclusions because the sample sizes are too small.
- Overall, Employee Referral appears to offer the strongest combination of hiring volume, active-employee percentage, performance, and engagement. Indeed and LinkedIn remain important because they generate the largest number of hires while maintaining active percentages above 75%. These patterns show associations within the dataset and do not prove that the recruitment source itself caused stronger employee outcomes.

## 7.2 Workforce indicators by manager
```SQL
		SELECT 
			ManagerName AS Manager,
			COUNT (*) AS Employee_records,
			SUM(
				CASE 
					WHEN Termd = 0 THEN 1
					ELSE 0
				END) AS Active_employee,
			SUM (
				CASE 
					WHEN Termd = 1 THEN 1
					ELSE 0 
				END ) AS Terminated_employee,
			ROUND (
			SUM (
			CASE 
					WHEN Termd = 1 THEN 1
					ELSE 0 
				END ) * 100.0 / COUNT (*)
				,2) AS Termination_percentage,
			ROUND (AVG (EngagementSurvey), 2) AS Average_engagement,
			ROUND (AVG (EmpSatisfaction),2) AS Average_satisfaction,
			ROUND (AVG (Absences),2) AS Average_absences	
		FROM HRDataset_v14
		GROUP BY Manager
		HAVING Employee_records >= 5
		ORDER BY Employee_records DESC;
```

The result:
|Manager|Employee_records|Active_employee|Terminated_employee|Termination_percentage|Average_engagement|Average_satisfaction|Average_absences|
|-------|----------------|---------------|-------------------|----------------------|------------------|--------------------|----------------|
|Michael Albert|22|13|9|40.91|4.07|4.05|10.91|
|Kissy Sullivan|22|10|12|54.55|4.04|3.91|10.59|
|Kelley Spirea|22|16|6|27.27|4.48|3.82|11.36|
|Elijiah Gray|22|14|8|36.36|4.07|3.95|9.14|
|Brannon Miller|22|16|6|27.27|4.04|3.41|9.18|
|Webster Butler|21|8|13|61.9|4.33|4.05|11.86|
|Ketsia Liebig|21|16|5|23.81|4.05|4.1|9.14|
|David Stanley|21|15|6|28.57|4.15|3.95|10.38|
|Amy Dunn|21|8|13|61.9|3.92|3.81|9.52|
|Janet King|19|13|6|31.58|4.18|3.63|10.21|
|Simon Roup|17|9|8|47.06|4.23|3.82|9.47|
|Peter Monroe|14|13|1|7.14|4.03|3.93|10.57|
|John Smith|14|11|3|21.43|3.79|3.93|13.36|
|Lynn Daneault|13|12|1|7.69|3.8|4.08|9.69|
|Alex Sweetwater|9|6|3|33.33|4.08|4.22|9.78|
|Brian Champaigne|8|8|0|0.0|4.06|4.0|12.5|
|Jennifer Zamora|7|6|1|14.29|3.99|4.29|9.29|
|Brandon R. LeBlanc|7|5|2|28.57|4.35|3.57|7.86|

This query groups employee records by manager and calculates the size and workforce indicators of each manager's team.

Conditional aggregation is used to count active and terminated employees. The termination percentage is calculated by dividing the number of terminated employees by the total employee records associated with each manager.

The query also calculates the average engagement score, satisfaction score, and number of absences for each team. Only managers with at least five employee records are included to reduce the influence of very small groups. All average values and percentages are rounded to two decimal places.

Insight:
- The largest teams in the dataset contain 22 employee records. Elijiah Gray, Brannon Miller, Michael Albert, Kelley Spirea, and Kissy Sullivan each manage or have managed teams of this size.
- Amy Dunn and Webster Butler have the highest termination percentages at 61.90%, with 13 terminated employees out of 21 employee records each. Kissy Sullivan follows with a termination percentage of 54.55%, while Simon Roup records 47.06%.
- In contrast, Brian Champaigne has no terminated employees among eight employee records. Peter Monroe and Lynn Daneault also have relatively low termination percentages at 7.14% and 7.69%, respectively.
- The results do not show a simple relationship between engagement and termination. For example, Webster Butler's team has a relatively high average engagement score of 4.33 despite having one of the highest termination percentages. Kelley Spirea's team has the highest average engagement score of 4.48, while its termination percentage is considerably lower at 27.27%.
- John Smith's team records the highest average absences at 13.36, followed by Brian Champaigne at 12.50 and Webster Butler at 11.86. These teams may warrant further attendance analysis, but the figures alone do not establish that the manager caused the attendance pattern.
- Overall, the teams associated with Amy Dunn, Webster Butler, Kissy Sullivan, and Simon Roup may require further investigation because of their relatively high termination percentages. However, the dataset does not control for department, job type, employee tenure, team history, or organizational changes. Therefore, the results should be treated as indicators for further review rather than direct measures of manager performance.

## 8. Employees require HR attention:
Calculating the benchmark of active employees:
```SQL
	SELECT 
			ROUND (AVG (EngagementSurvey), 2) AS Average_engagement,
			ROUND (AVG (EmpSatisfaction),2) AS Average_satisfaction,
			ROUND (AVG (Absences),2) AS Average_absences,
			ROUND (AVG (DaysLateLast30),2) AS Average_lateday
		FROM HRDataset_v14 hv 
		WHERE Termd = 0;
```

The result:
|Average_engagement|Average_satisfaction|Average_absences|Average_lateday|
|------------------|--------------------|----------------|---------------|
|4.12|3.89|9.83|0.29|

Then, finding the employees requiring HR attention:
```SQL
			
	SELECT *
	FROM 
		(SELECT
			EmpID,
			TRIM (Department) AS Department,
			TRIM (Position) AS Postion,
			TRIM (ManagerName) AS Manager,
			EngagementSurvey,
        	EmpSatisfaction,
        	Absences,
        	DaysLateLast30,
        TRIM(PerformanceScore) AS Performance_score,
				CASE
					WHEN EngagementSurvey < 
						(SELECT 
							AVG (EngagementSurvey)
						 FROM HRDataset_v14 hv
						 WHERE Termd = 0)
					THEN 1
					ELSE 0
				END
				+
				CASE 
					WHEN EmpSatisfaction <
					 	 (SELECT 
					 	 	AVG (EmpSatisfaction)
					 	 	FROM HRDataset_v14
					 	 	WHERE  Termd = 0)
					 THEN 1
					 ELSE 0
				END
				+ 
				CASE 
					WHEN Absences <
						(SELECT 
							AVG (Absences)
						FROM HRDataset_v14
					 	WHERE  Termd = 0 )
					 THEN 1
					 ELSE 0 
				END
				+
				CASE
					WHEN DaysLateLast30 <
						(SELECT 
							AVG (DaysLateLast30)
						FROM HRDataset_v14
					 	WHERE  Termd = 0)
					 THEN 1
					 ELSE 0
				END 
				+
				CASE
					WHEN TRIM (PerformanceScore) 
								IN ('PIP','Needs Improvement')
					THEN 1
					ELSE 0
				END AS Attention_indicators
				FROM HRDataset_v14 hv2 
				WHERE Termd = 0) AS Employee_attention
				
				WHERE Attention_indicators >= 2
				ORDER BY 
					    Attention_indicators DESC,
   					    EmpID 
   				LIMIT 10;
```

The result:
|EmpID|Department|Postion|Manager|EngagementSurvey|EmpSatisfaction|Absences|DaysLateLast30|Performance_score|Attention_indicators|
|-----|----------|-------|-------|----------------|---------------|--------|--------------|-----------------|--------------------|
|10013|Sales|Area Sales Manager|Lynn Daneault|4.1|3|6|0|Exceeds|4|
|10137|Production|Production Technician II|Kelley Spirea|4.0|3|7|0|Fully Meets|4|
|10140|Sales|Area Sales Manager|John Smith|3.98|3|4|0|Fully Meets|4|
|10150|Software Engineering|Software Engineering Manager|Jennifer Zamora|3.84|3|4|0|Fully Meets|4|
|10151|IT/IS|Network Engineer|Peter Monroe|3.81|3|6|0|Fully Meets|4|
|10156|IT/IS|Database Administrator|Simon Roup|3.75|3|2|0|Fully Meets|4|
|10169|Production|Production Technician I|Elijiah Gray|3.51|3|2|0|Fully Meets|4|
|10179|IT/IS|Network Engineer|Peter Monroe|3.31|3|7|0|Fully Meets|4|
|10183|Production|Production Technician I|Michael Albert|3.21|3|7|0|Fully Meets|4|
|10184|Production|Production Technician II|Webster Butler|3.19|3|9|0|Fully Meets|4|


This query evaluates active employees against five workforce-attention indicators: below-average engagement, below-average satisfaction, above-average absences, above-average late days, and a performance rating of `Needs Improvement` or `PIP`.

Each condition met is assigned a value of 1. The values are added together to create an `Attention_indicators` score for each active employee. Employees who meet at least two conditions are included in the final results.

Company averages are calculated using active employees only, ensuring that each employee is compared with the current workforce.

Insight:
- A total of 89 active employees meet at least two workforce-attention indicators. Most of these employees meet two indicators, while a smaller group meets four or five conditions.
- Employees with higher numbers of indicators may warrant prioritised review, particularly where low engagement or satisfaction occurs alongside attendance or performance concerns.
- These results should be used as a starting point for supportive HR actions, such as manager check-ins, workload reviews, attendance discussions, or performance support. The indicators do not predict resignation and should not be used as evidence for disciplinary action without reviewing individual circumstances.
