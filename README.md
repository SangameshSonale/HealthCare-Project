## Comprehensive SQL Healthcare Analytics: Insights from Patient Data
This project focuses on leveraging SQL to analyze and derive insights from hospital patient data. It includes tasks such as identifying operational efficiencies, evaluating healthcare equity, summarizing patient details, and uncovering trends in hospital procedures. By combining advanced SQL techniques like joins, subqueries, CTEs, and data manipulation functions, this project demonstrates how SQL can transform raw data into actionable insights for better decision-making in healthcare.

## Why This Project
The primary goal of this project was to analyze hospital data to uncover actionable insights for improving patient care, operational efficiency, and decision-making. By tackling real-world healthcare challenges, such as optimizing hospital stays, identifying high-procedure specialties, and ensuring equitable treatment, this project demonstrates the practical application of SQL in solving critical problems. It also highlights the importance of data-driven strategies in enhancing healthcare outcomes and efficiency.

## Here's Why You Should Keep Reading
This project dives into real-world healthcare challenges, showcasing how SQL can transform raw data into actionable insights. From optimizing hospital operations to uncovering patient care trends, it highlights the practical use of advanced SQL techniques to solve problems and support data-driven decisions. Keep reading to explore how data analytics can make a meaningful impact in healthcare.


## What & Where of the Dataset
The dataset used in this project was sourced from Kaggle.com, a trusted platform for open-source datasets. It contains comprehensive information on hospital operations, including patient demographics, procedures, medications, and outcomes. This dataset is ideal for educational purposes and demonstrates how SQL can be applied to analyze and extract insights from real-world healthcare data.### Hospital Stay Analysis

## Analysis:
### Hospital Stay Analysis
Analyzed patient stay durations to optimize hospital bed usage and improve efficiency. Used SQL to create histogram-like visualizations, revealing most patients stay less than 7 days. Insights support better resource management, patient turnover, and cost reduction.

```sql
SELECT ROUND(time_in_hospital, 1) AS bucket, 
COUNT(*) AS count,
RPAD('', COUNT(*)/100, '*') AS bar FROM patient.health 
GROUP BY bucket
ORDER BY bucket;
```
![Histogram](https://github.com/user-attachments/assets/53d78a20-1ea5-4a1a-aff0-08116d5ab09b)

### Medical Specialty Procedure Analysis

Analyzed medical specialties to identify those performing the highest average number of procedures. Used SQL to calculate averages, count patients per specialty, and filter results based on meaningful thresholds. Key highlights include:

* Identified specialties with an average of more than 2.5 procedures.
* Filtered data to include specialties with over 50 patients for reliability.
* Delivered actionable insights to help the director focus on high-procedure specialties.

```sql
SELECT DISTINCT medical_specialty, ROUND(AVG(num_procedures), 1) AS avg_procedures,
COUNT(medical_specialty) AS count FROM patient.health 
GROUP BY medical_specialty
HAVING count > 51 AND avg_procedures >2.5 
ORDER BY avg_procedures DESC
```
![Medical Speciality on avg procedures](https://github.com/user-attachments/assets/42a31d6f-bc23-4efc-ab22-e94972d3c5ce)

### Race-Based Lab Procedure Analysis
This project evaluates potential disparities in the number of lab procedures performed across different racial groups in a hospital. By combining patient demographic data and health information through SQL joins, insights were derived to address the Chief of Nursing's concerns.

Key Highlights:
* Used an INNER JOIN to merge demographic and health datasets on patient_nbr.
* Calculated the average number of lab procedures (AVG(num_lab_procedures)) grouped by race.
* Observed no significant gaps in the number of lab tests performed across races.
  
```sql
SELECT race, AVG(num_lab_procedures) AS num_avg_lab_procedures FROM patient.health AS ph
JOIN patient.demographics AS pd on 
ph.patient_nbr = pd.patient_nbr 
GROUP BY race 
ORDER BY num_avg_lab_procedures DESC;
```
![race_avg_lab_procedures](https://github.com/user-attachments/assets/6de92ebe-f57c-427a-8150-1be606cb0f90)

### Lab Procedure Frequency and Hospital Stay Correlation Analysis

This project investigates the correlation between the frequency of lab procedures and the average length of hospital stay. By grouping patients into categories based on their number of lab procedures, insights were derived about how procedure frequency relates to hospital stay duration.

Key Highlights:

* Used a CASE WHEN statement to categorize lab procedure counts into "few," "average," or "many."
* Calculated average hospital stay duration (AVG(time_in_hospital)) for each group using GROUP BY.
* Found a positive correlation: patients undergoing more lab procedures tend to stay longer in the hospital.

```sql
SELECT AVG(time_in_hospital) AS avg_time,
CASE 
     WHEN num_lab_procedures >= 0 AND num_lab_procedures < 25 THEN 'few'
	 WHEN num_lab_procedures >= 25 AND num_lab_procedures < 50 THEN 'average'
     WHEN num_lab_procedures >= 50 THEN 'many'
END AS procedure_frequency
FROM patient.health 
GROUP BY procedure_frequency
ORDER BY avg_time DESC
```
![avg_time_procedure](https://github.com/user-attachments/assets/ef58fe15-033c-4b27-9d8a-e9fa2f32ecc9)

### Combining Patient Data with UNION

This project demonstrates the use of SQL's UNION to combine datasets by stacking rows from different tables. The task was to create a consolidated list of patient IDs based on specific criteria for research purposes.

Key Highlights:

* Used UNION to combine patient IDs of African American patients and those with "Up" for metformin.
* Ensured the queries returned the same number of columns with matching data types for seamless stacking.
* Delivered a clean, unified list of patient IDs efficiently.

```sql
SELECT patient_nbr FROM patient.demographics WHERE race = 'AfricanAmerican' 
UNION
SELECT patient_nbr FROM patient.health WHERE metformin= 'up'
```
![patience_nbr](https://github.com/user-attachments/assets/b6fbbf5d-a55b-462b-a163-cd7a20dbea54)

### Highlighting Hospital Success Stories with SQL Subqueries and CTEs

This project identifies cases where patients were admitted for emergencies (admission_type_id = 1) and stayed in the hospital for less than the average duration, showcasing hospital efficiency and success stories.

Key Highlights:

* Utilized a subquery to calculate the average hospital stay and filter patients below this threshold.
* Simplified complex queries using a Common Table Expression (CTE) for better readability and reusability.
* Delivered a list of emergency cases with shorter-than-average stays, emphasizing hospital effectiveness in handling emergencies.

```sql
WITH avg_time AS 
(SELECT  AVG(time_in_hospital) FROM patient.health)
SELECT * FROM patient.health 
WHERE admission_type_id = 1 
AND time_in_hospital < (SELECT * FROM avg_time);
```
![Capture](https://github.com/user-attachments/assets/8ee28a6c-313d-4f14-a7ca-66b6626ffefd)

### Patient Medication Summary Report 

This project generates a written summary for the top 50 patients based on the number of medications, with lab procedures as a tiebreaker. It highlights SQL's capabilities in concatenating text for creating readable and insightful reports.

Key Highlights:

* Combined data from the health and demographics tables using an INNER JOIN on patient_nbr.
* Used CONCAT and CASE WHEN to format patient details into a human-readable summary.
* Ordered results by num_medications (primary) and num_lab_procedures (tie-breaker).
* Limited results to the top 50 patients.

```sql
SELECT CONCAT('patient ', ph.patient_nbr , ' was ', pd.race , ' AND ', 
      (CASE WHEN readmitted='NO ' THEN 'was not admitted. They had ' ELSE 'was readmitted. They had ' END),
      num_medications, ' medications and ', num_lab_procedures, ' lab procedures. ') AS summary 
      FROM 
      patient.health AS ph JOIN patient.demographics AS pd 
      ON
      ph.patient_nbr=pd.patient_nbr     
	  ORDER BY num_medications DESC,
               num_lab_procedures DESC LIMIT 50
```
![Capture1](https://github.com/user-attachments/assets/1276a2e7-9c58-4ab8-891b-9697d1bfec9d)

Top 5 Insights from the Project
* ### Hospital Stay Optimization:
Patients admitted for emergencies (admission_type_id = 1) but discharged in less time than the average hospital stay highlight the hospital’s ability to handle critical cases efficiently.

* ### Procedure Frequency Correlation:
A positive correlation was identified between the number of lab procedures and the length of hospital stays. Patients undergoing more procedures tend to stay longer, offering opportunities to explore resource allocation strategies.

* ### High-Procedure Specialties:
Specialties with the highest average procedures (e.g., above 2.5) and sufficient patient volume (count > 50) were identified, allowing hospital administrators to focus on areas with the most significant operational impact.

* ### Equitable Treatment Analysis:
No significant disparities were found in the average number of lab procedures performed across different racial groups, suggesting equity in patient treatment.

* ### Patient Medication Summaries:
Created a detailed and human-readable summary for the top 50 medication-intensive patients, combining demographic and procedural data. This insight provides a clear narrative of patient care for reporting or research purposes.







