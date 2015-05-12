# [SQLZoo Tutorial Solutions](http://sqlzoo.net/wiki/SQL_Tutorial)

## [SELECT FROM Nobel Tutorial:](http://sqlzoo.net/wiki/SELECT_from_Nobel_Tutorial)

### 1
```sh
SELECT yr, subject, winner  
FROM nobel
WHERE yr = 1950
```
### 2
```sh
SELECT winner
FROM nobel
WHERE yr = 1962 AND subject = 'Literature'
```
### 3
```sh
SELECT yr, subject 
FROM nobel 
WHERE winner = 'Albert Einstein'
```
### 4
```sh
SELECT winner 
FROM nobel 
WHERE subject='Peace' AND yr >= 2000
```
### 5
```sh
SELECT yr, subject, winner 
FROM nobel 
WHERE subject = 'Literature' AND yr between 1980 AND 1989
```
### 6
```sh
SELECT * 
FROM nobel 
WHERE winner IN ('Theodore Roosevelt', 'Woodrow Wilson', 'Jimmy Carter')
```
### 7
```sh
SELECT winner 
FROM nobel 
WHERE winner LIKE 'John%'
```
### 8
```sh
SELECT * 
FROM nobel 
WHERE subject = 'Physics' AND yr = 1980
UNION
SELECT * 
FROM nobel 
WHERE subject = 'Chemistry' AND yr = 1984
```
### 9 
```sh
SELECT *
FROM nobel 
WHERE yr = 1980 AND subject NOT IN ('Chemistry', 'Medicine')
```
### 10
```sh
SELECT * 
FROM nobel 
WHERE subject = 'Medicine' AND yr < 1910 
union 
SELECT * 
FROM nobel 
WHERE subject = 'Literature' AND yr >= 2004 
```
### 11
```sh
SELECT * 
FROM nobel 
WHERE winner = 'PETER GRüNBERG' 
```
### 12
```sh
SELECT * 
FROM nobel 
WHERE winner = 'EUGENE O\'NEILL'
```
### 13
```sh
SELECT winner, yr, subject 
FROM nobel 
WHERE winner LIKE 'sir%' 
ORDER BY 'yr'
```
### 14 
```sh
SELECT winner, subject, subject IN ('Chemistry', 'Physics')
FROM nobel
WHERE yr = 1984
ORDER BY subject IN ('Chemistry', 'Physics')
```
##[SELECT within SELECT Tutorial:](http://sqlzoo.net/wiki/SELECT_within_SELECT_Tutorial)

### 1
```sh
SELECT name 
FROM world
WHERE population > (SELECT population FROM world
                    WHERE name='Russia'
                    )
```
### 2
```sh
SELECT name 
FROM world 
WHERE continent = 'Europe' AND 
                    gdp/population > (SELECT gdp/population 
                                      FROM world 
                                      WHERE name = 'United Kingdom'
                                      )
```
### 3
```sh
SELECT name, continent 
FROM world 
WHERE continent IN (SELECT continent 
                    FROM world 
                    WHERE name = 'Argentina' OR name ='Australia'
                    ) 
ORDER BY name
```
### 4
```sh
SELECT name, population 
FROM world 
WHERE population > (SELECT population 
                    FROM world 
                    WHERE name = 'Canada'
                    ) 
AND population < (SELECT population 
                  FROM world 
                  WHERE name = 'Poland'
                  )
```
### 5
```sh
SELECT name, concat(round( (population*100) / 
                            (SELECT population 
                              FROM world 
                              WHERE name = 'Germany'
                      ), 0)
              ,'%') 
FROM world 
WHERE continent = 'Europe'
```
### 6 
```sh
SELECT name 
FROM world 
WHERE gdp > ALL(SELECT MAX(gdp)  
                FROM world 
                WHERE continent = 'Europe' 
                  AND gdp IS NOT NULL
                )
```
### 7
```sh
SELECT continent, name, area 
FROM world x
WHERE area >= ALL(SELECT area 
                  FROM world y
                  WHERE y.continent = x.continent
                    AND area > 0
                  )
```
### 8
```sh
SELECT continent, name 
FROM world x 
WHERE name < ALL(SELECT name 
                  FROM world y 
                  WHERE y.continent = x.continent 
                  AND name > 0
                ) 
GROUP BY continent
```
### 9
```sh
SELECT name, continent, population
FROM world x 
WHERE 25000000 > ALL (SELECT population 
                      FROM world y 
                      WHERE y.continent = x.continent) 
ORDER BY continent
```
### 10 
```sh
SELECT name, continent 
FROM world x
WHERE population > ALL(SELECT population*3 
                        FROM world y 
                        WHERE y.continent = x.continent 
                        AND y.name != x.name
                      )
```
## [SUM AND COUNT:](http://sqlzoo.net/wiki/SUM_and_COUNT)

### 1
```sh
SELECT SUM(population)
FROM world
```
### 2
```sh
SELECT DISTINCT continent 
FROM world
```
### 3
```sh
SELECT SUM(gdp) 
FROM world 
WHERE continent = 'Africa'
```
### 4
```sh
SELECT COUNT(name) 
FROM world 
WHERE area >= 1000000
```
### 5
```sh
SELECT SUM(population) 
FROM world 
WHERE name IN ('France','Germany','Spain')
```
### 6
```sh
SELECT continent, COUNT(name) 
FROM world 
GROUP BY continent
```
### 7
```sh
SELECT continent, COUNT(name)
FROM world
WHERE population >= 10000000
GROUP BY continent
```
### 8
```sh
SELECT continent 
FROM world 
GROUP BY continent 
having SUM(population) >= 100000000
```
## [The JOIN operation:](http://sqlzoo.net/wiki/The_JOIN_operation)


### 1
```sh
SELECT matchid, player 
FROM goal 
WHERE teamid = 'GER'
```
### 2
```sh
SELECT id,stadium,team1,team2
FROM game 
WHERE id = '1012'
```
### 3
```sh
SELECT player, teamid, mdate
FROM game JOIN goal ON id = matchid
WHERE teamid = 'GER'
```
### 4
```sh
SELECT team1, team2, player 
FROM game JOIN goal ON id = matchid 
WHERE player LIKE 'Mario%'
```
### 5
```sh
SELECT player, teamid, coach, gtime 
FROM goal JOIN eteam ON teamid = id 
WHERE gtime <= 10
```
### 6
```sh
SELECT mdate, teamname 
FROM game JOIN eteam ON team1 = eteam.id
WHERE coach = 'Fernando Santos'
```
### 7
```sh
SELECT player 
FROM game JOIN goal ON id = matchid 
WHERE stadium = 'National Stadium, Warsaw'
```
### 8
```sh
SELECT DISTINCT player
FROM game JOIN goal ON matchid = id 
WHERE (team1='GER' OR  team2='GER') AND teamid != 'GER' 
```
### 9
```sh
SELECT teamname, COUNT(teamid)
FROM eteam JOIN goal ON id=teamid
GROUP BY teamname
```
### 10
```sh
SELECT stadium, COUNT(goal.matchid) 
FROM game JOIN goal ON id = matchid 
GROUP BY stadium
```
### 11
```sh
SELECT matchid,mdate, COUNT(teamid)
FROM game JOIN goal ON matchid = id 
WHERE (team1 = 'POL' OR team2 = 'POL') 
GROUP BY matchid
```
### 12
```sh
SELECT matchid, mdate, COUNT(matchid) 
FROM game JOIN goal ON id = matchid 
WHERE teamid='GER' GROUP BY matchid
```
### 13  <-- FIXME
```sh
SELECT mdate, 
      team1, 
      SUM(CASE WHEN teamid = team1 THEN 1 ELSE 0 END)
      team 2, 
      SUM(CASE WHEN teamid = team2 THEN 1 ELSE 0 END)
FROM game JOIN goal ON matchid = id
GROUP BY mdate
```
## [More JOIN operations:](http://sqlzoo.net/wiki/More_JOIN_operations)

### 1
```sh
SELECT id, title
FROM movie
WHERE yr = 1962
```
### 2
```sh
SELECT yr 
FROM movie 
WHERE title = 'Citizen Kane'
```
### 3
```sh
SELECT id, title, yr 
FROM movie 
WHERE title LIKE '%star trek%' 
ORDER BY yr
```
### 4
```sh
SELECT title 
FROM movie 
WHERE id IN ('11768', '11955', '21191')
```
### 5
```sh
SELECT id 
FROM actor 
WHERE name = 'Glenn Close'
```
### 6
```sh
SELECT id 
FROM movie 
WHERE title = 'Casablanca'
```
### 7
```sh
SELECT name 
FROM actor JOIN casting ON id = actorid 
WHERE movieid = '11768'
```
### 8
```sh
SELECT name 
FROM (actor JOIN casting ON actor.id = actorid) 
      JOIN movie ON movieid = movie.id 
WHERE title = 'Alien'
```
### 9
```sh
SELECT title 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id 
WHERE name = 'Harrison Ford'
```
### 10
```sh
SELECT title 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id 
WHERE name = 'Harrison Ford' AND ord != 1
```
### 11
```sh
SELECT title, name 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id 
WHERE yr = '1962' AND ord = '1'
```
### 12
```sh
SELECT yr,COUNT(title) 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id
WHERE name = 'John Travolta'
GROUP BY yr
HAVING COUNT(title) = (SELECT MAX(c) 
                        FROM(SELECT yr,COUNT(title) AS c 
                            FROM (movie JOIN casting ON movie.id =     movieid
                          ) 
                        JOIN actor ON actorid = actor.id
                        WHERE name = 'John Travolta'
                        GROUP BY yr
                        ) AS t
                      )
```
### 13 
```sh
SELECT title, name 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id
WHERE ord = '1' AND movieid IN 
                      (SELECT movieid 
                        FROM (casting JOIN actor ON actorid = actor.id) 
                        WHERE name = 'Julie Andrews'
                      )
```
### 14
```sh
SELECT name 
FROM (actor JOIN casting ON actor.id = actorid) 
WHERE ord = '1'
GROUP BY name
having COUNT(name) >= 30
```
### 15 
```sh
SELECT title, COUNT(actorid) 
FROM (movie JOIN casting ON movie.id = movieid) 
WHERE yr = '1978' 
GROUP BY title 
ORDER BY COUNT(actorid) desc
```
### 16
```sh
SELECT name 
FROM (actor JOIN casting ON actor.id = actorid) 
WHERE name != 'Art Garfunkel' 
  AND movieid IN (SELECT movieid 
                  FROM casting JOIN actor ON actorid = actor.id 
                  WHERE name = 'Art Garfunkel'
                  )
```
## [Using NULL:](http://sqlzoo.net/wiki/Using_Null)


### 1
```sh
SELECT name 
FROM teacher 
WHERE dept IS NULL
```
### 2

```sh
SELECT teacher.name, dept.name
FROM teacher INNER JOIN dept ON teacher.dept = dept.id
```
### 3
```sh
SELECT teacher.name, dept.name
FROM teacher LEFT JOIN dept ON teacher.dept = dept.id
```
### 4
```sh
SELECT teacher.name, dept.name
FROM teacher RIGHT JOIN dept ON teacher.dept = dept.id
```
