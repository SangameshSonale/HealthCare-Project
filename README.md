# HealthCare-Project

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


