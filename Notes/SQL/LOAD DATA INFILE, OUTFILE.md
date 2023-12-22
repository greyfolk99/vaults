#sql #mariadb #mysql


아래와 같은 테이블이 있고,
```sql
CREATE TABLE hourly_time_series (
  id INT AUTO_INCREMENT PRIMARY KEY,
  model_type VARCHAR(255),
  variable VARCHAR(255),
  region VARCHAR(255),
  model_date DATETIME(6)
  forecast_date DATETIME(6),
  avg DECIMAL(10, 2),
  min DECIMAL(10, 2),
  max DECIMAL(10, 2),
  created_at DATETIME(6),
);
```

CSV 헤더가 아래와같을 때

|modelType|variable|region|modelDate|forecastDate|avg|min|max|
|---|---|---|---|---|---|---|---|
|str|str|str|int|int|double|double|double|


### INFILE

csv -> db (int를 date로 변환하고 create_at을 modelDate와 같은 시간으로 입력)
```sql
LOAD DATA INFILE 'import/hourly.csv'  
INTO TABLE hourly_time_series  
FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n'  
IGNORE 1 LINES  
(model_type, variable, region, @model_date, @forecast_date, avg, min, max)  
SET model_date = STR_TO_DATE(CAST(@model_date AS CHAR), '%Y%m%d%H%i%s'),  
forecast_date = STR_TO_DATE(CAST(@forecast_date AS CHAR), '%Y%m%d%H%i%s'),  
created_at = STR_TO_DATE(CAST(@model_date AS CHAR), '%Y%m%d%H%i%s');
```

### OUTFILE

hourly_time_series 를 csv로 저장
```sql
SELECT * INTO OUTFILE 'export/hourly.csv'
FROM (SELECT * FROM hourly_time_series)
```