# dataVisualization
Using StackOverflow dataset from BigQuery to answer these questions:


### Q1: What percentage of questions were answered anually?(SQL) how it's trending?(Tableau)
```
SELECT
  EXTRACT(YEAR FROM creation_date) AS Year,
  COUNT(*) AS Number_of_Questions,
  ROUND(100 * SUM(IF(answer_count > 0, 1, 0)) / COUNT(*), 1) AS Percent_Questions_with_Answers
FROM
  `bigquery-public-data.stackoverflow.posts_questions`
GROUP BY
  Year
HAVING
  Year = 2010
  # Year from 2010 to 2019
  # Question: how can I get the answer by operating once instead of 10 times?
  
ORDER BY
  Year
result
```

![tableau](https://github.com/fogloria/dataVisualization/blob/master/imgs/tableau.png)
interactive:https://public.tableau.com/profile/chilin#!/vizhome/Q1_15935103209000/Dashboard2


### Q2:Which hour(PST) should you ask a question so you can get the answer fast?
```
with inst_hour as
(SELECT EXTRACT(hour from q.creation_date) as question_hour,
 				q.accepted_answer_id AS answer_id,
 				q.id AS question_id
 FROM `bigquery-public-data.stackoverflow.posts_questions` q
 	full join `bigquery-public-data.stackoverflow.posts_answers` a
 				on q.id=a.parent_id
 WHERE a.creation_date<timestamp_add(q.creation_date,interval 5 minute)
)

SELECT question_hour,
			COUNT(answer_id) AS count_answers,
			COUNT(question_id) AS count_questions,
			round(COUNT(answer_id)/count(question_id)*100,2) AS percent_questions
FROM inst_hour
GROUP BY question_hour
ORDER BY question_hour
```


### Q3:Global user location(top 1,000 per reputation)
Here:https://www.kaggle.com/joesserandom/top-1-000-reputation-geographical-distribution

![distribution](https://github.com/fogloria/dataVisualization/blob/master/imgs/map.png)


### Q4:Which language is most popular?(has most related questions in different year) (heatmap)
```
SELECT EXTRACT(YEAR from creation_date) AS year,count(tags) as tags_num
FROM `bigquery-public-data.stackoverflow.posts_questions` 
WHERE tags LIKE 'swift'
GROUP BY year 
```
![heatmap](https://github.com/fogloria/dataVisualization/blob/master/imgs/language%20sorted%20by%20first%20appearance.png)

