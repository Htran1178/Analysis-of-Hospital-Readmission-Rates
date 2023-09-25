SELECT 
   a.patient_id, 
   a.admission_date, 
   a.discharge_date, 
   CASE WHEN b.admission_date IS NOT NULL THEN 1 ELSE 0 END AS readmitted_within_30_days,
   p.age, 
   p.gender,
   t.treatment_type
FROM admissions a
LEFT JOIN admissions b ON a.patient_id = b.patient_id 
   AND DATEDIFF(DAY, a.discharge_date, b.admission_date) BETWEEN 1 AND 30
JOIN patients p ON a.patient_id = p.id
JOIN treatments t ON a.treatment_id = t.id
SELECT 
   t.treatment_type, 
   AVG(CASE WHEN readmitted_within_30_days = 1 THEN 1 ELSE 0 END) AS readmission_rate
FROM admissions a
JOIN treatments t ON a.treatment_id = t.id
GROUP BY t.treatment_type
HAVING COUNT(a.patient_id) > 50
ORDER BY readmission_rate DESC
