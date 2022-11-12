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

## 📌 Question 3 : 
![Data Lemur 3 1](https://user-images.githubusercontent.com/116416338/197391506-242f3b48-1db2-4f02-8c4e-2ab72a92704b.png)
![Data Lemur 3 2](https://user-images.githubusercontent.com/116416338/197391517-2b949f35-030a-4830-b33d-4977d8d392e6.png)

````sql
WITH top_artists
AS (
  SELECT 
    artist_id,
    DENSE_RANK() OVER (
      ORDER BY song_count DESC) AS artist_rank
  FROM (    
    SELECT songs.artist_id, COUNT(songs.song_id) AS song_count
    FROM songs
    INNER JOIN global_song_rank AS ranking
      ON songs.song_id = ranking.song_id
    WHERE ranking.rank <= 10
    GROUP BY songs.artist_id) 
AS top_songs)

SELECT 
  artists.artist_name, top_artists.artist_rank
FROM top_artists
INNER JOIN artists
  ON top_artists.artist_id = artists.artist_id
WHERE top_artists.artist_rank <= 5
ORDER BY top_artists.artist_rank, artists.artist_name;
````

````sql
select 
*
from(
SELECT
a.artist_name,
dense_rank() over(order by count(g.song_id) desc) as artist_rank
from artists a 
  left join songs s
   on s.artist_id = a.artist_id
    left join global_song_rank g
      on g.song_id = s.song_id
where g.rank<=10
group by a.artist_name) temp
where temp.artist_rank <=5
````

## 📌 Question 4 : 
![Data Lemur 4 1](https://user-images.githubusercontent.com/116416338/197393677-8a8effb8-15a1-4207-a857-141448cdb0ac.png)
![Data Lemur 4 2](https://user-images.githubusercontent.com/116416338/197393701-f4c5228c-9ea0-4be4-83c2-04a031152975.png)

````sql
with temp as (
SELECT  
user_id,
case when texts.text_id is null then 1 else 0 end as signup
FROM emails 
  left join texts 
    on emails.email_id = texts.email_id
    and signup_action = 'Confirmed')

SELECT
ROUND(SUM(signup)::decimal/ COUNT(user_id), 2) AS confirmation_rate
from temp
````
## 📌 Question 5 : 
![Data Lemur 5](https://user-images.githubusercontent.com/116416338/201484815-5139c0be-f78e-40c9-9a48-314f161a050e.png)
````sql
SELECT product_id,
       CASE
           WHEN category IS NULL THEN
                  (SELECT inner_table.category
                   FROM products AS inner_table
                   WHERE inner_table.product_id < products.product_id
                     AND inner_table.category IS NOT NULL
                   ORDER BY inner_table.product_id DESC
                   LIMIT 1)
           ELSE category
       END AS category,
       name
FROM products
ORDER BY product_id;
````
