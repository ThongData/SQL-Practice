# 📝 SQL Practice DataLemur

## 📌 Question 1 : 
![Data Lemur 1](https://user-images.githubusercontent.com/116416338/197381798-2908be1a-687e-40e7-bff1-fe8560aafa75.png)
````sql
select
count(DISTINCT a.user_id)
from
(SELECT 
user_id,
sum(spend) as spending,
transaction_date,
rank() over ( PARTITION BY User_id order by transaction_date) as ranking
FROM user_transactions
group by user_id, transaction_date
order by user_id, transaction_date) as a
where ranking =1
and spending>50

````
## 📌 Question 2 : 
![Data Lemur 2 1](https://user-images.githubusercontent.com/116416338/197386632-c14b9abc-1b5c-4d4d-ab27-2358d6f4bc76.png)
![Data Lemur 2 2](https://user-images.githubusercontent.com/116416338/197386657-cad38c80-9416-45ae-aee5-4fcdfd35fb61.png)

**Solution 1**

````sql
WITH popular_companies 
AS (
  SELECT
    employees.personal_profile_id,
	MAX(pages.followers) AS highest_followers
  FROM employee_company AS employees 
  LEFT JOIN company_pages AS pages
    ON employees.company_id = pages.company_id  
  GROUP BY employees.personal_profile_id)

SELECT profiles.profile_id
FROM popular_companies AS companies
LEFT JOIN personal_profiles AS profiles
	ON companies.personal_profile_id = profiles.profile_id
WHERE profiles.followers > companies.highest_followers
ORDER BY profiles.profile_id;
````

**Solution 2**
````sql
select
temp.profile_id
from
(SELECT
p.profile_id,
p.name,
p.followers as indiv_followers,
c.followers as company_followers,
rank() over(PARTITION BY p.profile_id order by c.followers desc) as ranking
FROM personal_profiles p
  left join employee_company e
    on p.profile_id = e.personal_profile_id
left join company_pages c
    on c.company_id = e.company_id) as temp
where ranking = 1 and indiv_followers>company_followers
````
