# [SQLZoo Tutorial Solutions](http://sqlzoo.net/wiki/SQL_Tutorial)

## [SELECT FROM Nobel Tutorial:](http://sqlzoo.net/wiki/SELECT_from_Nobel_Tutorial)

### 1: Change the query shown so that it displays Nobel prizes for 1950.
```sh
SELECT yr, subject, winner  
FROM nobel
WHERE yr = 1950
```
### 2: Show who won the 1962 prize for Literature.
```sh
SELECT winner
FROM nobel
WHERE yr = 1962 AND subject = 'Literature'
```
### 3: Show the year and subject that won 'Albert Einstein' his prize.
```sh
SELECT yr, subject 
FROM nobel 
WHERE winner = 'Albert Einstein'
```
### 4: Give the name of the 'Peace' winners since the year 2000, including 2000.
```sh
SELECT winner 
FROM nobel 
WHERE subject='Peace' AND yr >= 2000
```
### 5: Show all details (yr, subject, winner) of the Literature prize winners for 1980 to 1989 inclusive.
```sh
SELECT yr, subject, winner 
FROM nobel 
WHERE subject = 'Literature' AND yr between 1980 AND 1989
```
### 6: Show all details of the presidential winners: Theodore Roosevelt, Woodrow Wilson, Jimmy Carter
```sh
SELECT * 
FROM nobel 
WHERE winner IN ('Theodore Roosevelt', 'Woodrow Wilson', 'Jimmy Carter')
```
### 7: Show the winners with first name John.
```sh
SELECT winner 
FROM nobel 
WHERE winner LIKE 'John%'
```
### 8: Show the Physics winners for 1980 together with the Chemistry winners for 1984.
```sh
SELECT * 
FROM nobel 
WHERE subject = 'Physics' AND yr = 1980
UNION
SELECT * 
FROM nobel 
WHERE subject = 'Chemistry' AND yr = 1984
```
### 9: Show the winners for 1980 excluding the Chemistry and Medicine.
```sh
SELECT *
FROM nobel 
WHERE yr = 1980 AND subject NOT IN ('Chemistry', 'Medicine')
```
### 10: Show who won a 'Medicine' prize in an early year (before 1910, not including 1910) together with winners of a 'Literature' prize in a later year (after 2004, including 2004).
```sh
SELECT * 
FROM nobel 
WHERE subject = 'Medicine' AND yr < 1910 
union 
SELECT * 
FROM nobel 
WHERE subject = 'Literature' AND yr >= 2004 
```
### 11: Find all details of the prize won by PETER GRÜNBERG
```sh
SELECT * 
FROM nobel 
WHERE winner = 'PETER GRüNBERG' 
```
### 12: Find all details of the prize won by EUGENE O'NEILL.
```sh
SELECT * 
FROM nobel 
WHERE winner = 'EUGENE O\'NEILL'
```
### 13: List the winners, year and subject where the winner starts with Sir. Show the the most recent first, then by name order.
```sh
SELECT winner, yr, subject 
FROM nobel 
WHERE winner LIKE 'sir%' 
ORDER BY 'yr'
```
### 14: Show the 1984 winners ordered by subject and winner name; but list Chemistry and Physics first.
```sh
SELECT winner, subject, subject IN ('Chemistry', 'Physics')
FROM nobel
WHERE yr = 1984
ORDER BY subject IN ('Chemistry', 'Physics')
```
##[SELECT within SELECT Tutorial:](http://sqlzoo.net/wiki/SELECT_within_SELECT_Tutorial)

### 1: List each country name where the population is larger than 'Russia'.
```sh
SELECT name 
FROM world
WHERE population > (SELECT population FROM world
                    WHERE name='Russia'
                    )
```
### 2: Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.
```sh
SELECT name 
FROM world 
WHERE continent = 'Europe' AND 
                    gdp/population > (SELECT gdp/population 
                                      FROM world 
                                      WHERE name = 'United Kingdom'
                                      )
```
### 3: List the name and continent of countries in the continents containing either Argentina or Australia. Order by name of the country.
```sh
SELECT name, continent 
FROM world 
WHERE continent IN (SELECT continent 
                    FROM world 
                    WHERE name = 'Argentina' OR name ='Australia'
                    ) 
ORDER BY name
```
### 4: Which country has a population that is more than Canada but less than Poland? Show the name and the population.
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
### 5: Show the name and the population of each country in Europe. Show the population as a percentage of the population of Germany.
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
### 6: Which countries have a GDP greater than every country in Europe?
```sh
SELECT name 
FROM world 
WHERE gdp > ALL(SELECT MAX(gdp)  
                FROM world 
                WHERE continent = 'Europe' 
                  AND gdp IS NOT NULL
                )
```
### 7: Find the largest country (by area) in each continent, show the continent, the name and the area.
```sh
SELECT continent, name, area 
FROM world x
WHERE area >= ALL(SELECT area 
                  FROM world y
                  WHERE y.continent = x.continent
                    AND area > 0
                  )
```
### 8: List each continent and the name of the country that comes first alphabetically.
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
### 9: Find the continents where all countries have a population <= 25000000. Then find the names of the countries associated with these continents. Show name, continent and population.
```sh
SELECT name, continent, population
FROM world x 
WHERE 25000000 > ALL (SELECT population 
                      FROM world y 
                      WHERE y.continent = x.continent) 
ORDER BY continent
```
### 10: Some countries have populations more than three times that of any of their neighbours (in the same continent). Give the countries and continents. 
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

### 1: Show the total population of the world.
```sh
SELECT SUM(population)
FROM world
```
### 2: List all the continents - just once each.
```sh
SELECT DISTINCT continent 
FROM world
```
### 3: Give the total GDP of Africa.
```sh
SELECT SUM(gdp) 
FROM world 
WHERE continent = 'Africa'
```
### 4: How many countries have an area of at least 1000000.
```sh
SELECT COUNT(name) 
FROM world 
WHERE area >= 1000000
```
### 5: What is the total population of ('France', 'Germany', 'Spain')?
```sh
SELECT SUM(population) 
FROM world 
WHERE name IN ('France','Germany','Spain')
```
### 6: For each continent show the continent and number of countries.
```sh
SELECT continent, COUNT(name) 
FROM world 
GROUP BY continent
```
### 7: For each continent show the continent and number of countries with populations of at least 10 million.
```sh
SELECT continent, COUNT(name)
FROM world
WHERE population >= 10000000
GROUP BY continent
```
### 8: List the continents that have a total population of at least 100 million.
```sh
SELECT continent 
FROM world 
GROUP BY continent 
having SUM(population) >= 100000000
```
## [The JOIN operation:](http://sqlzoo.net/wiki/The_JOIN_operation)


### 1: Show matchid and player name for all goals scored by Germany.
```sh
SELECT matchid, player 
FROM goal 
WHERE teamid = 'GER'
```
### 2: Show id, stadium, team1, team2 for game 1012.
```sh
SELECT id,stadium,team1,team2
FROM game 
WHERE id = '1012'
```
### 3: Show the player, teamid and mdate and for every German goal.
```sh
SELECT player, teamid, mdate
FROM game JOIN goal ON id = matchid
WHERE teamid = 'GER'
```
### 4: Show the team1, team2 and player for every goal scored by a player called Mario.
```sh
SELECT team1, team2, player 
FROM game JOIN goal ON id = matchid 
WHERE player LIKE 'Mario%'
```
### 5: Show player, teamid, coach, gtime for all goals scored in the first 10 minutes.
```sh
SELECT player, teamid, coach, gtime 
FROM goal JOIN eteam ON teamid = id 
WHERE gtime <= 10
```
### 6: List the the dates of the matches and the name of the team in which 'Fernando Santos' was the team1 coach.
```sh
SELECT mdate, teamname 
FROM game JOIN eteam ON team1 = eteam.id
WHERE coach = 'Fernando Santos'
```
### 7: List the player for every goal scored in a game where the stadium was 'National Stadium, Warsaw'.
```sh
SELECT player 
FROM game JOIN goal ON id = matchid 
WHERE stadium = 'National Stadium, Warsaw'
```
### 8: Instead show the name of all players who scored a goal against Germany.
```sh
SELECT DISTINCT player
FROM game JOIN goal ON matchid = id 
WHERE (team1='GER' OR  team2='GER') AND teamid != 'GER' 
```
### 9: Show teamname and the total number of goals scored.
```sh
SELECT teamname, COUNT(teamid)
FROM eteam JOIN goal ON id=teamid
GROUP BY teamname
```
### 10: Show the stadium and the number of goals scored in each stadium.
```sh
SELECT stadium, COUNT(goal.matchid) 
FROM game JOIN goal ON id = matchid 
GROUP BY stadium
```
### 11: For every match involving 'POL', show the matchid, date and the number of goals scored.
```sh
SELECT matchid,mdate, COUNT(teamid)
FROM game JOIN goal ON matchid = id 
WHERE (team1 = 'POL' OR team2 = 'POL') 
GROUP BY matchid
```
### 12: For every match where 'GER' scored, show matchid, match date and the number of goals scored by 'GER'
```sh
SELECT matchid, mdate, COUNT(matchid) 
FROM game JOIN goal ON id = matchid 
WHERE teamid='GER' GROUP BY matchid
```
### 13: List every match with the goals scored by each team as shown. 
```sh
SELECT  mdate, team1,
        SUM(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1, 
        team2,
        SUM(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
FROM game LEFT OUTER JOIN goal ON id = matchid
GROUP BY mdate, matchid, team1, team2
ORDER BY mdate, matchid, team1, team2
```
## [More JOIN operations:](http://sqlzoo.net/wiki/More_JOIN_operations)

### 1: List the films where the yr is 1962.
```sh
SELECT id, title
FROM movie
WHERE yr = 1962
```
### 2: Give year of 'Citizen Kane'.
```sh
SELECT yr 
FROM movie 
WHERE title = 'Citizen Kane'
```
### 3: List all of the Star Trek movies, include the id, title and yr (all of these movies include the words Star Trek in the title). Order results by year.
```sh
SELECT id, title, yr 
FROM movie 
WHERE title LIKE '%star trek%' 
ORDER BY yr
```
### 4: What are the titles of the films with id 11768, 11955, 21191.
```sh
SELECT title 
FROM movie 
WHERE id IN ('11768', '11955', '21191')
```
### 5: What id number does the actor 'Glenn Close' have?
```sh
SELECT id 
FROM actor 
WHERE name = 'Glenn Close'
```
### 6: What is the id of the film 'Casablanca'?
```sh
SELECT id 
FROM movie 
WHERE title = 'Casablanca'
```
### 7: Obtain the cast list for 'Casablanca'.
```sh
SELECT name 
FROM actor JOIN casting ON id = actorid 
WHERE movieid = '11768'
```
### 8: Obtain the cast list for the film 'Alien'
```sh
SELECT name 
FROM (actor JOIN casting ON actor.id = actorid) 
      JOIN movie ON movieid = movie.id 
WHERE title = 'Alien'
```
### 9: List the films in which 'Harrison Ford' has appeared.
```sh
SELECT title 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id 
WHERE name = 'Harrison Ford'
```
### 10: List the films where 'Harrison Ford' has appeared - but not in the star role.
```sh
SELECT title 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id 
WHERE name = 'Harrison Ford' AND ord != 1
```
### 11: List the films together with the leading star for all 1962 films.
```sh
SELECT title, name 
FROM (movie JOIN casting ON movie.id = movieid) 
      JOIN actor ON actorid = actor.id 
WHERE yr = '1962' AND ord = '1'
```
### 12: Which were the busiest years for 'John Travolta', show the year and the number of movies he made each year for any year in which he made more than 2 movies.
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
### 13: List the film title and the leading actor for all of the films 'Julie Andrews' played in. 
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
### 14: Obtain a list in alphabetical order of actors who've had at least 30 starring roles.
```sh
SELECT name 
FROM (actor JOIN casting ON actor.id = actorid) 
WHERE ord = '1'
GROUP BY name
having COUNT(name) >= 30
```
### 15: List the films released in the year 1978 ordered by the number of actors in the cast. 
```sh
SELECT title, COUNT(actorid) 
FROM (movie JOIN casting ON movie.id = movieid) 
WHERE yr = '1978' 
GROUP BY title 
ORDER BY COUNT(actorid) desc
```
### 16: List all the people who have worked with 'Art Garfunkel'.
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


### 1: List the teachers who have NULL for their department.

```sh
SELECT name 
FROM teacher 
WHERE dept IS NULL
```
### 2: Note the INNER JOIN misses the teacher with no department and the department with no teacher.
```sh
SELECT teacher.name, dept.name
FROM teacher INNER JOIN dept ON teacher.dept = dept.id
```
### 3: Use a different JOIN so that all teachers are listed.
```sh
SELECT teacher.name, dept.name
FROM teacher LEFT JOIN dept ON teacher.dept = dept.id
```
### 4: Use a different JOIN so that all departments are listed.
```sh
SELECT teacher.name, dept.name
FROM teacher RIGHT JOIN dept ON teacher.dept = dept.id
```
