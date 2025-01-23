# HealthCare-Project

Analyzed patient stay durations to optimize hospital bed usage and improve efficiency. Used SQL to create histogram-like visualizations, revealing most patients stay less than 7 days. Insights support better resource management, patient turnover, and cost reduction.

```sql
SELECT ROUND(time_in_hospital, 1) AS bucket, 
COUNT(*) AS count,
RPAD('', COUNT(*)/100, '*') AS bar FROM patient.health 
GROUP BY bucket
ORDER BY bucket;
```
![Histogram](https://github.com/user-attachments/assets/53d78a20-1ea5-4a1a-aff0-08116d5ab09b)

```sql
SELECT DISTINCT medical_specialty, ROUND(AVG(num_procedures), 1) AS avg_procedures,
COUNT(medical_specialty) AS count FROM patient.health 
GROUP BY medical_specialty
HAVING count > 51 AND avg_procedures >2.5 
ORDER BY avg_procedures DESC
```
![Medical Speciality on avg procedures](https://github.com/user-attachments/assets/42a31d6f-bc23-4efc-ab22-e94972d3c5ce)

```sql
SELECT race, AVG(num_lab_procedures) AS num_avg_lab_procedures FROM patient.health AS ph
JOIN patient.demographics AS pd on 
ph.patient_nbr = pd.patient_nbr 
GROUP BY race 
ORDER BY num_avg_lab_procedures DESC;
```
![race_avg_lab_procedures](https://github.com/user-attachments/assets/6de92ebe-f57c-427a-8150-1be606cb0f90)

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

```sql
SELECT patient_nbr FROM patient.demographics WHERE race = 'AfricanAmerican' 
UNION
SELECT patient_nbr FROM patient.health WHERE metformin= 'up'
```
![patience_nbr](https://github.com/user-attachments/assets/b6fbbf5d-a55b-462b-a163-cd7a20dbea54)

```sql
WITH avg_time AS 
(SELECT  AVG(time_in_hospital) FROM patient.health)
SELECT * FROM patient.health 
WHERE admission_type_id = 1 
AND time_in_hospital < (SELECT * FROM avg_time);
```
![Capture](https://github.com/user-attachments/assets/8ee28a6c-313d-4f14-a7ca-66b6626ffefd)

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







