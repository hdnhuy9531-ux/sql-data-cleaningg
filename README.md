# sql-data-cleaningg
<img width="512" height="338" alt="image" src="https://github.com/user-attachments/assets/1be4cce2-50cb-422d-9958-4bba5a67c989" />

This is an educational project on data cleaning and preparation using SQL. The original database in CSV format is located in the file club_member_info.csv. Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.

## 1. View data
View 10 first rows
```
SELECT *
FROM club_member_info cmi
LIMIT 10;
```
Result
|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|Assistant Professor|7/31/2013|
|      ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|Programmer III|5/27/2018|
|Sydel Sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|Budget/Accounting Analyst I|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|Desktop Support Technician|10/20/2015|
|  Gaylor Redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|Legal Assistant|5/29/2019|
|Wanda del mar       |44|single|wkunzel5@slideshare.net|937-467-6942|10864 Buhler Plaza,Hamilton,Ohio|Human Resources Assistant IV|3/24/2015|
|Joann Kenealy|41|married|jkenealy6@bloomberg.com|513-726-9885|733 Hagan Parkway,Cincinnati,Ohio|Accountant IV|4/17/2013|
|   Joete Cudiff|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 Dwight Plaza,Grand Rapids,Michigan|Research Nurse|11/16/2014|
|mendie alexandrescu|46|single|malexandrescu8@state.gov|504-918-4753|34 Delladonna Terrace,New Orleans,Louisiana|Systems Administrator III|3/12/1921|

| fey kloss|52|married|fkloss9@godaddy.com|808-177-0318|8976 Jackson Park,Honolulu,Hawaii|Chemical Engineer|11/5/2014|
## 2. Copy new table
### 2.1 Create new table for cleaning 
``` sql
CREATE TABLE club_member_info_cleaned (
	full_name VARCHAR(50),
	age INTEGER,
	martial_status VARCHAR(50),
	email VARCHAR(50),
	phone NVARCHAR(50),
	full_address NVARCHAR(50),
	job_title VARCHAR(50),
	membership_date NVARCHAR(50)
);
```
### 2.2 Copy all values from original table
```sql
INSERT INTO club_member_info_cleaned 
SELECT* FROM club_member_info;
```
## 3. Cleaning data
### 3.1 Full_name:
#### 3.1.1 Trim whitespace 
``` sql
UPDATE club_member_info_cleaned 
SET full_name = TRIM(full_name );
```
Result
|full_name|
|---------|
|addie lush|
|ROCK CRADICK|
|Sydel Sharvell|
|Constantin de la cruz|
|Gaylor Redhole|
|Wanda del mar|
|Joann Kenealy|
|Joete Cudiff|
|mendie alexandrescu|
|fey kloss|
#### 3.1.2 Upper all the names
```sql
UPDATE club_member_info_cleaned 
SET full_name = UPPER(full_name );
```
Result
|full_name|
|---------|
|ADDIE LUSH|
|ROCK CRADICK|
|SYDEL SHARVELL|
|CONSTANTIN DE LA CRUZ|
|GAYLOR REDHOLE|
|WANDA DEL MAR|
|JOANN KENEALY|
|JOETE CUDIFF|
|MENDIE ALEXANDRESCU|
|FEY KLOSS|
### 3.2 Age:
#### 3.2.1 Replace age greater than 100
```sql
UPDATE club_member_info_cleaned 
SET age = CAST(SUBSTR(age, 1, 2) AS INTEGER)
WHERE age > 100 AND age <> '';
```
#### 3.2.2 Update whitespace with AVG value
```sql
UPDATE club_member_info_cleaned 
SET age = 42
WHERE age = '';
```
### 3.3 Other columns
Similar to the full_name and age columns, the remaining columns also have a lot of data that needs to be cleaned.
```sql
UPDATE club_member_info_cleaned
SET 
    martial_status = TRIM(martial_status),
    email = TRIM(email),
    phone = TRIM(phone),
    full_address = TRIM(full_address),
    job_title = TRIM(job_title),
    membership_date = TRIM(membership_date);
```
To delete all the missing data:
```sql
DELETE FROM club_member_info_cleaned
WHERE martial_status = ""
OR email = ""
OR phone = ""
OR full_address = ""
OR job_title = ""
OR membership_date = "";
```
To replace all the missing data with 'null' 
``` sql
UPDATE club_member_info_cleaned
SET 
    martial_status = null WHERE martial_status = '';
UPDATE club_member_info_cleaned
SET 
    email = null WHERE email = '';
UPDATE club_member_info_cleaned
SET 
    phone = null WHERE phone = '';
UPDATE club_member_info_cleaned
SET 
    full_address = null WHERE full_address = '';
UPDATE club_member_info_cleaned
SET 
    job_title = null WHERE job_title = '';
UPDATE club_member_info_cleaned
SET 
    membership_date = null WHERE membership_date = '';
```
### 3.4 Membership_date
At membership_date column, I updated the new data to match the format of "MM/DD/YYYY" by:
``` sql
UPDATE club_member_info_cleaned
SET membership_date = 
    CASE
        WHEN membership_date LIKE "_/__/____" THEN "0" || SUBSTR(membership_date, 1, 1) || SUBSTR(membership_date, 2)
        WHEN membership_date LIKE "__/_/____" THEN SUBSTR(membership_date, 1, 3) || "0" || SUBSTR(membership_date, 4)
        WHEN membership_date LIKE "_/_/____" THEN "0" || SUBSTR(membership_date, 1, 2) || "0" || SUBSTR(membership_date, 3)
        WHEN membership_date LIKE "__/__/____" THEN membership_date
        ELSE NULL
    END;
```
Result
|membership_date|formatted_date|
|---------------|--------------|
|07/31/2013|07/31/2013|
|05/27/2018|05/27/2018|
|10/06/2017|10/06/2017|
|05/29/2019|05/29/2019|
|03/24/2015|03/24/2015|
|04/17/2013|04/17/2013|
|11/16/2014|11/16/2014|
|03/12/1921|03/12/1921|
|11/05/2014|11/05/2014|
|03/12/2017|03/12/2017|
