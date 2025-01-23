# HealthCare-Project

```sql
SELECT ROUND(time_in_hospital, 1) AS bucket, 
COUNT(*) AS count,
RPAD('', COUNT(*)/100, '*') AS bar FROM patient.health 
GROUP BY bucket
ORDER BY bucket;
```
