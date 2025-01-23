# HealthCare-Project

```sql
SELECT ROUND(time_in_hospital, 1) AS bucket, 
COUNT(*) AS count,
RPAD('', COUNT(*)/100, '*') AS bar FROM patient.health 
GROUP BY bucket
ORDER BY bucket;
```
![Alt text](![Histogram](https://github.com/user-attachments/assets/4e3e81ba-b749-4975-aefb-481b6f6a0f88)
)
