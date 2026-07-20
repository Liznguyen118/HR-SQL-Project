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
SELECT *
FROM HRDataset_v14
LIMIT 10;
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
