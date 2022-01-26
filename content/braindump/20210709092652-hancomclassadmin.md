+++
title = "hancomclassadmin"
author = [7696122]
lastmod = 2022-01-19T10:26:44+09:00
draft = false
+++

## 평일 계산 query {#평일-계산-query}

```sql
set @date :=  '2021-07-04';
SELECT date_sub(@date, INTERVAL
CASE weekday(@date) -- weekday will give you the index of the day ( 0 = Monday, 1 = Tuesday, 2 = Wednesday, ... 6 = Sunday)
WHEN 0 THEN 3 -- If Monday then sub 3 days
WHEN 6 THEN 2 -- If Sunday then sub 2 days
ELSE 1 -- Else sub 1 day
END DAY);
```

```sql
set @date :=  '2021-07-04';
SELECT date_sub(@date, INTERVAL
CASE weekday(@date) -- weekday will give you the index of the day ( 0 = Monday, 1 = Tuesday, 2 = Wednesday, ... 6 = Sunday)
WHEN 0 THEN 3 -- If Monday then sub 3 days
WHEN 6 THEN 2 -- If Sunday then sub 2 days
ELSE 1 -- Else sub 1 day
END DAY);

set @date :=  '2021-07-03';
SELECT @date - INTERVAL (weekday( @date)+ 2)% 7+ 1 DAY 'Previous Friday';

DELIMITER //
CREATE FUNCTION GetPreviousWeekday(date Date)
RETURNS Date
BEGIN
    DECLARE previousWeekday Date;
    SET previousWeekday = date - INTERVAL (weekday(date)+ 2)% 7+ 1 DAY 'Previous Friday';
    RETURN previousWeekday;
END; //
DELIMITER ;
```
