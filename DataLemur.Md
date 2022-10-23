# 📝 SQL Practice DataLemur

## 📌 Question 1 : Subquery
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
