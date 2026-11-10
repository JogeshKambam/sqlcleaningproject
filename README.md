# sqlcleaningproject
hr data is cleaned using sql 
create database project;

use project;

show tables;

rename table messy_hr_data to hr_data;

select * from hr_data;

select distinct * from hr_data;

desc  hr_data;

update hr_data
set email = "na"
where email is null or email ="" or email = "nan";

select * from hr_data;

##Use backticks(` `) ##
alter table hr_data
rename column `Phone Number`to phone_number;

update hr_data
set phone_number = "na"
where phone_number is null or phone_number =" " or phone_number = "nan";

select * from hr_data;

select distinct(phone_number)
from hr_data;

update hr_data
set phone_number = null
where phone_number ="na";

alter table hr_data
modify column phone_number varchar(30);

##if we want to convert into digits use set phone_number = replace(phone_number,'-',' '); ##

select distinct(age)
from hr_data;

update hr_data
set age=30
where age ="thirty";

update hr_data
set age=null
where age ="nan";

alter table hr_data
modify column age int;
desc hr_data;

select distinct(Salary)
from hr_data;

update hr_data
set Salary = 60000
where Salary ="sixty thousand";

update hr_data
set Salary=null
where Salary =' NAN ';

select length('NAN') from hr_data;

alter table hr_data
modify column Salary int;

select * from hr_data;

alter table hr_data
rename column `Joining Date` to  joining_date;

select distinct(joining_date)
from hr_data;

update hr_data
set joining_date = ( 
     case
     when joining_date = "" then "2018/04/05" 
     when joining_date = "01/15/2020" then "2020/15/01"
     when joining_date = "03-25-2019" then "2019/03/25"
     when joining_date = "2019.12.01" then "2019/12/01" else "Unknown" end);

SET SQL_SAFE_UPDATES = 0;


alter table hr_data
drop column joining_date_clean;

select * from hr_data;

SELECT
    SUM(CASE WHEN salary IS NULL THEN 1 ELSE 0 END) AS salary_nulls,
    SUM(CASE WHEN age IS NULL THEN 1 ELSE 0 END) AS department_nulls,
    sum(case when phone_number is null then 1 else 0 end) as phone_number_nulls
FROM hr_data;

SET @avg_age = (
    SELECT ROUND(AVG(age))
    FROM hr_data
);

UPDATE hr_data
SET age = @avg_age
WHERE age IS NULL;

SET @avg_sal = (
    SELECT AVG(Salary)
    FROM hr_data
);

UPDATE hr_data
SET Salary = @avg_sal
WHERE Salary IS NULL;

update hr_data
set phone_number = "Unknown"
where phone_number is null;

select * from resultshrdata ;

select distinct(joining_date) 
from resultshrdata;

alter table resultshrdata
add column joining_date_backup text;

ALTER TABLE resultshrdata ADD joining_date_backup TEXT;
UPDATE resultshrdata SET joining_date_backup = joining_date;

UPDATE resultshrdata
SET joining_date =
CASE
    WHEN joining_date REGEXP '^[A-Za-z]+ [0-9]{1,2}, [0-9]{4}$'
        THEN STR_TO_DATE(joining_date, '%M %e, %Y')

    WHEN joining_date REGEXP '^[0-9]{4}/[0-9]{2}/[0-9]{2}$'
        THEN STR_TO_DATE(joining_date, '%Y/%m/%d')

    WHEN joining_date REGEXP '^[0-9]{2}/[0-9]{2}/[0-9]{4}$'
        THEN STR_TO_DATE(joining_date, '%m/%d/%Y')

    WHEN joining_date REGEXP '^[0-9]{2}-[0-9]{2}-[0-9]{4}$'
        THEN STR_TO_DATE(joining_date, '%m-%d-%Y')

    WHEN joining_date REGEXP '^[0-9]{4}\\.[0-9]{2}\\.[0-9]{2}$'
        THEN STR_TO_DATE(joining_date, '%Y.%m.%d')
END;

select * from resultshrdata;
