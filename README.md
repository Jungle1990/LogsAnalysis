How to run:
a. Use following 3 sql command to create views
b. run python LogAnalysis.py to get output

Please use following 3 sql command to create view

1. create popular_articles view
CREATE OR REPLACE VIEW popular_articles AS
SELECT title, COUNT(title) AS views
FROM articles, log
WHERE log.path = CONCAT('/article/', articles.slug)
GROUP BY title ORDER BY views DESC;

2. create popular_authors view
CREATE OR REPLACE VIEW popular_authors AS
SELECT authors.name, count(authors.name) AS views
FROM articles, log, authors
WHERE log.path = CONCAT('/article/', articles.slug)
AND articles.author = authors.id
GROUP BY authors.name ORDER BY views DESC;

3. create requests_error_log view
CREATE OR REPLACE VIEW requests_error_log AS
SELECT total_requests.date, ROUND(100.0 * error_requests.count / total_requests.count, 2) AS error_percentage
FROM
(
SELECT COUNT(*) AS count, DATE(time) AS date
FROM log
GROUP BY date
) as total_requests
JOIN
(
SELECT COUNT(*) AS count, DATE(time) AS date
FROM log
WHERE status = '404 NOT FOUND'
GROUP BY date
) AS error_requests
ON error_requests.date = total_requests.date;