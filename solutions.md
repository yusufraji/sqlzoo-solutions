# SQLZoo Solutions
Here are my solutions to SQL tutorials from
[SQLZoo](https://sqlzoo.net/wiki/SQL_Tutorial)

## Sections
1. [SELECT name](#selct-name) - Some pattern matching queries
2. [SELECT from World](#select-from-world) - In which we query the World country profile table.
3. [SELECT from Nobel](#select-from-nobel) - Additional practice of the basic features using a table of Nobel Prize winners.
4. [SELECT within SELECT](#select-within-select) - In which we form queries using other queries. 
5. [SUM and COUNT](#sum-and-count) - In which we apply aggregate functions.
6. [JOIN](#join) - In which we join two tables; game and goals.
7. [More JOIN operations](#more-join) - In which we join two tables; game and goals.
8. [using NULL](#using-null) - In which we look at teachers in departments.
9. [Self join](#self-join) - In which we join Edinburgh bus routes to Edinburgh bus routes.
    
## SELECT name
1. You can use `WHERE name LIKE 'B%'` to find the countries that start with "B".

    * The % is a _wild-card_ it can match any characters

   **Find the country that start with Y**

```sql
SELECT NAME
FROM   world
WHERE  NAME LIKE 'Y%'
```

## SELECT from World

1. Observe the result of running this SQL command to show the name, continent and population of all countries.

```sql
SELECT NAME,
       continent,
       population
FROM   world
```

2. Show the name for the countries that have a population of at least 200 million. 200 million is 200000000, there are eight zeros.

```sql
SELECT NAME
FROM   world
WHERE  population >= 200000000
```

3. Give the `name` and the **per capita GDP** for those countries with a `population` of at least 200 million. 

```sql
SELECT NAME,
       gdp / population
FROM   world
WHERE  population >= 200000000
```

4. Show the `name` and `population` in millions for the countries of the
   `continent` 'South America'. Divide the population by 1000000 to get
   population in millions.

```sql
SELECT NAME,
       population / 1000000
FROM   world
WHERE  continent = 'South America'
```

5. Show the `name` and `population` for France, Germany, Italy

```sql
SELECT NAME,
       population
FROM   world
WHERE  NAME IN ( 'France', 'Germany', 'Italy' )
```

6. Show the countries which have a name that includes the word 'United'

```sql
SELECT NAME
FROM   world
WHERE  NAME LIKE 'United%'
```

7. Two ways to be big: A country is **big** if it has an area of more than 3 million sq km or it has a population of more than 250 million.

    **Show the countries that are big by area or big by population. Show name,
    population and area.**

```sql
SELECT NAME,
       population,
       area
FROM   world
WHERE  area > 3000000
        OR population > 250000000
```

8. **Exclusive OR (XOR). Show the countries that are big by area (more than 3
   million) or big by population (more than 250 million) but not both. Show
   name, population and area.**

    * Australia has a big area but a small population, it should be **included**.
    * Indonesia has a big population but a small area, it should be **included**.
    * China has a big population **and** big area, it should be **excluded**.
    * United Kingdom has a small population and a small area, it should be
      **excluded**.

```sql
SELECT name,
       population,
       area
FROM   world
WHERE  area > 3000000 XOR population > 250000000
```

9. Show the `name` and `population` in millions and the GDP in billions for the
   countries of the `continent` 'South America'. Use the ROUND function to show
   the values to two decimal places.

   **For South America show population in millions and GDP in billions both to 2
   decimal places.**
   
```sql
SELECT NAME,
       Round(population / 1000000, 2),
       Round(gdp / 1000000000, 2)
FROM   world
WHERE  continent = 'South America'
```

10. Show the `name` and per-capita GDP for those countries with a GDP of at least
    one trillion (1000000000000; that is 12 zeros). Round this value to the
    nearest 1000.

    **Show per-capita GDP for the trillion dollar countries to the nearest $1000.**

```sql
SELECT NAME,
       Round(gdp / population, -3)
FROM   world
WHERE  gdp >= 1000000000000
```

11. Greece has capital Athens.

    Each of the strings 'Greece', and 'Athens' has 6 characters.

    **Show the name and capital where the name and the capital have the same number of characters.**

    * You can use the LENGTH function to find the number of characters in a string

```sql
SELECT NAME,
       capital
FROM   world
WHERE  Length(NAME) = Length(capital)
```

12. The capital of Sweden is Stockholm. Both words start with the letter 'S'.
    
    **Show the name and the capital where the first letters of each match. Don't
    include countries where the name and the capital are the same word.**

    * You can use the function LEFT to isolate the first character.
    * You can use <> as the NOT EQUALS operator.

```sql
SELECT NAME,
       capital
FROM   world
WHERE  LEFT(NAME, 1) = LEFT(capital, 1)
       AND ( capital <> NAME )
```

13. **Equatorial Guinea** and **Dominican Republic** have all of the vowels (a e i o u)
    in the name. They don't count because they have more than one word in the
    name. 

    **Find the country that has all the vowels and no spaces in its name.**

    * You can use the phrase `name NOT LIKE '%a%'` to exclude characters from your
      results.
    * The query shown misses countries like Bahamas and Belarus because they
      contain at least one 'a'

```sql
SELECT NAME
FROM   world
WHERE  NAME LIKE '%a%'
       AND NAME LIKE '%e%'
       AND NAME LIKE '%i%'
       AND NAME LIKE '%o%'
       AND NAME LIKE '%u%'
       AND NAME NOT LIKE '% %'
```

## SELECT from Nobel

1. Change the query shown so that it displays Nobel prizes for 1950.

```sql
SELECT yr,
       subject,
       winner
FROM   nobel
WHERE  yr = 1950
```

2. Show who won the 1962 prize for Literature.

```sql
SELECT winner
FROM   nobel
WHERE  yr = 1962
       AND subject = 'Literature'
```

3. Show the year and subject that won 'Albert Einstein' his prize.

```sql
SELECT yr,
       subject
FROM   nobel
WHERE  winner = 'Albert Einstein'
```

4. Give the name of the 'Peace' winners since the year 2000, including 2000.

```sql
SELECT winner
FROM   nobel
WHERE  subject = 'Peace'
       AND yr >= 2000
```

5. Show all details (**yr, subject, winner**) of the Literature prize winners for 1980 to 1989 inclusive. 

```sql
SELECT *
FROM   nobel
WHERE  subject = 'Literature'
       AND yr >= 1980
       AND yr <= 1989
```

6. Show all details of the presidential winners:

    * Theodore Roosevelt
    * Woodrow Wilson
    * Jimmy Carter
    * Barack Obama

```sql
SELECT *
FROM   nobel
WHERE  winner IN ( 'Theodore Roosevelt', 'Woodrow Wilson', 'Jimmy Carter',
                   'Barack Obama' )
```

7. Show the winners with first name John

```sql
SELECT winner
FROM   nobel
WHERE  winner LIKE 'John%'
```

8. **Show the year, subject, and name of Physics winners for 1980 together with the Chemistry winners for 1984.**

```sql
SELECT *
FROM   nobel
WHERE  ( subject = 'Chemistry'
         AND yr = 1984 )
        OR ( subject = 'Physics'
             AND yr = 1980 )
```

9. **Show the year, subject, and name of winners for 1980 excluding Chemistry and Medicine**

```sql
SELECT *
FROM   nobel
WHERE  yr = 1980
       AND subject NOT IN ( 'Chemistry', 'Medicine' )
```

10. Show year, subject, and name of people who won a 'Medicine' prize in an early year (before 1910, not including 1910) together with winners of a 'Literature' prize in a later year (after 2004, including 2004).

```sql
SELECT *
FROM   nobel
WHERE  ( subject = 'Medicine'
         AND yr < 1910 )
        OR ( subject = 'Literature'
             AND yr >= 2004 )
```

11. Find all details of the prize won by PETER GRÜNBERG

```sql
SELECT *
FROM   nobel
WHERE  winner = 'Peter Grünberg'
```

12. Find all details of the prize won by EUGENE O'NEILL

```sql
SELECT *
FROM   nobel
WHERE  winner = 'Eugene O''Neill'
```

13. Knights in order

    **List the winners, year and subject where the winner starts with Sir. Show
    the the most recent first, then by name order.**

```sql
SELECT winner,
       yr,
       subject
FROM   nobel
WHERE  winner LIKE 'Sir%'
ORDER  BY yr DESC,
          winner
```

14. The expression **subject IN ('Chemistry','Physics')** can be used as a value - it will be **0** or **1**.

    **Show the 1984 winners and subject ordered by subject and winner name; but list Chemistry and Physics last.**

```sql
SELECT winner,
       subject
FROM   nobel
WHERE  yr = 1984
ORDER  BY subject IN ( 'Physics', 'Chemistry' ),
          subject,
          winner
```

## SELECT within SELECT

1. **List each country name where the population is larger than that of 'Russia'.**

```sql
SELECT NAME
FROM   world
WHERE  population > (SELECT population
                     FROM   world
                     WHERE  NAME = 'Russia')
```

2. **Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.**

```sql
SELECT NAME
FROM   world
WHERE  gdp / population > (SELECT gdp / population
                           FROM   world
                           WHERE  NAME = 'United Kingdom')
       AND continent = 'Europe'
```

3. **List the name and continent of countries in the continents containing either Argentina or Australia. Order by name of the country.**

```sql
SELECT NAME,
       continent
FROM   world
WHERE  continent IN ( (SELECT continent
                       FROM   world
                       WHERE  NAME = 'Argentina'), (SELECT continent
                                                    FROM   world
                                                    WHERE  NAME = 'Australia') )
ORDER  BY NAME
```

4. **Which country has a population that is more than Canada but less than Poland? Show the name and the population.**

```sql
SELECT NAME,
       population
FROM   world
WHERE  population > (SELECT population
                     FROM   world
                     WHERE  NAME = 'Canada')
       AND population < (SELECT population
                         FROM   world
                         WHERE  NAME = 'Poland')
```

5. Germany (population 80 million) has the largest population of the countries in Europe. Austria (population 8.5 million) has 11% of the population of Germany.
   **Show the name and the population of each country in Europe. Show the
   population as a percentage of the population of Germany.**

```sql
SELECT NAME,
       Concat(Round(population / (SELECT population
                                  FROM   world
                                  WHERE  NAME = 'Germany') * 100), '%')
FROM   world
WHERE  continent = 'Europe'
```

6. **Which countries have a GDP greater than every country in Europe? [Give the name only.] (Some countries may have NULL gdp values)**

```sql
SELECT NAME
FROM   world
WHERE  gdp > ALL (SELECT gdp
                  FROM   world
                  WHERE  continent = 'Europe'
                         AND gdp > 0)
```

7. **Find the largest country (by area) in each continent, show the continent, the name and the area:**

```sql
SELECT continent,
       NAME,
       area
FROM   world x
WHERE  area >= ALL (SELECT area
                    FROM   world y
                    WHERE  y.continent = x.continent
                           AND area > 0)
```

8. **List each continent and the name of the country that comes first alphabetically.**

```sql
SELECT continent,
       NAME
FROM   world x
WHERE  x.NAME <= ALL (SELECT NAME
                      FROM   world y
                      WHERE  x.continent = y.continent)
```

9. **Find the continents where all countries have a population <= 25000000. Then find the names of the countries associated with these continents. Show name, continent and population.**

```sql
SELECT NAME,
       continent,
       population
FROM   world x
WHERE  25000000 >= ALL (SELECT population
                        FROM   world y
                        WHERE  x.continent = y.continent
                               AND y.population > 0)
```

10. **Some countries have populations more than three times that of all of their neighbours (in the same continent). Give the countries and continents.**

```sql
SELECT NAME,
       continent
FROM   world x
WHERE  population > ALL (SELECT population * 3
                         FROM   world y
                         WHERE  x.continent = y.continent
                                AND x.NAME != y.NAME)
```

## SUM and COUNT

1. Show the total **population** of the world. 

```sql
SELECT Sum(population)
FROM   world
```

2. List all the continents - just once each. 

```sql
SELECT DISTINCT continent
FROM   world
```

3. Give the total GDP of Africa 

```sql
SELECT Sum (gdp)
FROM   world
WHERE  continent = 'Africa'
```

4. How many countries have an **area** of at least 1000000 

```sql
SELECT Count(NAME)
FROM   world
WHERE  area > '1000000'
```

5. What is the total **population** of ('Estonia', 'Latvia', 'Lithuania') 

```sql
SELECT Sum (population)
FROM   world
WHERE  NAME IN ( 'Estonia', 'Latvia', 'Lithuania' )
```

6. For each **continent** show the **continent** and number of countries.

```sql
SELECT continent,
       Count(NAME)
FROM   world
GROUP  BY continent
```

7. For each **continent** show the **continent** and number of countries with populations of at least 10 million.

```sql
SELECT continent,
       Count(NAME)
FROM   world
WHERE  population >= 10000000
GROUP  BY continent
```

8. List the continents that **have** a total population of at least 100 million.

```sql
SELECT continent
FROM   world
GROUP  BY continent
HAVING Sum(population) >= 100000000
```

## JOIN

1. The first example shows the goal scored by a player with the last name 'Bender'. The `*` says to list all the columns in the table - a shorter way of saying `matchid, teamid, player, gtime`
**Modify it to show the matchid and player name for all goals scored by Germany.
To identify German players, check for: `teamid = 'GER'`**

```sql
SELECT matchid,
       player
FROM   goal
WHERE  teamid = 'GER'
```

2. From the previous query you can see that Lars Bender's scored a goal in game 1012. Now we want to know what teams were playing in that match.
Notice in the that the column `matchid` in the `goal` table corresponds to the `id` column in the `game` table. We can look up information about **game** 1012 by finding that row in the game table.
**Show id, stadium, team1, team2 for just game 1012**

```sql
SELECT id,
       stadium,
       team1,
       team2
FROM   game
WHERE  id = 1012
```

3. You can combine the two steps into a single query with a JOIN.
```sql
SELECT *
  FROM game JOIN goal ON (id=matchid)
```
The **FROM** clause says to merge data from the goal table with that from the game table. The **ON** says how to figure out which rows in **game** go with which rows in **goal** - the **matchid** from **goal** must match **id** from **game**. (If we wanted to be more clear/specific we could say
`ON (game.id=goal.matchid)`

The code below shows the player (from the goal) and stadium name (from the game table) for every goal scored.

**Modify it to show the player, teamid, stadium and mdate for every German
goal.**

```sql
SELECT player,
       teamid,
       stadium,
       mdate
FROM   game
       JOIN goal
         ON ( id = matchid )
WHERE  teamid = 'GER'
```

4. Use the same `JOIN` as in the previous question.

**Show the team1, team2 and player for every goal scored by a player called
Mario `player LIKE 'Mario%'`**

```sql
SELECT team1,
       team2,
       player
FROM   game
       JOIN goal
         ON ( id = matchid )
WHERE  player LIKE 'Mario%'
```

5. The table `eteam` gives details of every national team including the coach. You can `JOIN` `goal` to `eteam` using the phrase `goal JOIN eteam on teamid=id`

**Show `player`, `teamid`, `coach`, `gtime` for all goals scored in the first 10
minutes `gtime<=10`**

```sql
SELECT player,
       teamid,
       coach,
       gtime
FROM   goal
       JOIN eteam
         ON teamid = id
WHERE  gtime <= 10
```

6. To `JOIN` `game` with `eteam` you could use either
`game JOIN eteam ON (team1=eteam.id)` or `game JOIN eteam ON (team2=eteam.id)`

Notice that because `id` is a column name in both `game` and `eteam` you must specify `eteam.id` instead of just `id`

**List the dates of the matches and the name of the team in which 'Fernando
Santos' was the team1 coach.**

```sql
SELECT mdate,
       teamname
FROM   game
       JOIN eteam
         ON ( team1 = eteam.id )
WHERE  coach = 'Fernando Santos'
```

7. **List the player for every goal scored in a game where the stadium was 'National Stadium, Warsaw'**

```sql
SELECT player
FROM   game
       JOIN goal
         ON ( id = matchid )
WHERE  stadium = 'National Stadium, Warsaw'
```

8. The example query shows all goals scored in the Germany-Greece quarterfinal.

**Instead show the name of all players who scored a goal against Germany.**

```sql
SELECT DISTINCT player
FROM   game
       JOIN goal
         ON matchid = id
WHERE  ( team1 = 'GER'
          OR team2 = 'GER' )
       AND teamid != 'GER'
```

9. **Show teamname and the total number of goals scored.**

```sql
SELECT teamname,
       Count(teamname)
FROM   eteam
       JOIN goal
         ON id = teamid
GROUP  BY teamname
```

10. **Show the stadium and the number of goals scored in each stadium.**

```sql
SELECT stadium,
       Count(stadium)
FROM   game
       JOIN goal
         ON id = matchid
GROUP  BY stadium
```

11. **For every match involving 'POL', show the matchid, date and the number of goals scored.**

```sql
SELECT matchid,
       mdate,
       Count(teamid)
FROM   game
       JOIN goal
         ON matchid = id
WHERE  ( team1 = 'POL'
          OR team2 = 'POL' )
GROUP  BY matchid,
          mdate
```

12. **For every match where 'GER' scored, show matchid, match date and the number of goals scored by 'GER'**

```sql
SELECT matchid,
       mdate,
       Count(teamid)
FROM   game
       JOIN goal
         ON id = matchid
WHERE  ( teamid = 'GER' )
GROUP  BY matchid,
          mdate
```

13. **List every match with the goals scored by each team as shown. This will use "CASE WHEN" which has not been explained in any previous exercises.**

```sql
SELECT mdate,
       team1,
       Sum(CASE
             WHEN teamid = team1 THEN 1
             ELSE 0
           END) score1,
       team2,
       Sum(CASE
             WHEN teamid = team2 THEN 1
             ELSE 0
           END) score2
FROM   game
       LEFT JOIN goal
              ON matchid = id
GROUP  BY mdate,
          matchid,
          team1,
          team2
```

## More JOIN

12. Lead actor in Julie Andrews movies
```sql
SELECT title,
       NAME
FROM   movie
       JOIN casting
         ON ( id = casting.movieid )
       JOIN actor
         ON ( actor.id = actorid )
WHERE  movieid IN (SELECT movieid
                   FROM   actor
                          JOIN casting
                            ON ( actor.id = actorid )
                   WHERE  NAME = 'Julie Andrews')
       AND ord = '1'  
```

13. Actors with 15 leading roles
```sql
SELECT NAME
FROM   actor
       JOIN casting
         ON ( id = actorid
              AND ord = '1' )
GROUP  BY NAME
HAVING ( Count(movieid) >= 15 )
ORDER  BY NAME 
```

14. List the films released in the year 1978 ordered by the number of actors in the cast, then by title.
```sql
SELECT title,
       Count(actorid)
FROM   movie
       JOIN casting
         ON ( movie.id = movieid )
WHERE  yr = 1978
GROUP  BY title
ORDER  BY Count(actorid) DESC  
```

15. List all the people who have worked with 'Art Garfunkel'.
```sql
SELECT DISTINCT( NAME )
FROM   casting
       JOIN actor
         ON ( actorid = actor.id )
WHERE  NAME != 'Art Garfunkel'
       AND movieid IN (SELECT movieid
                       FROM   movie
                              JOIN casting
                                ON ( movie.id = movieid )
                              JOIN actor
                                ON ( actorid = actor.id )
                       WHERE  NAME = 'Art Garfunkel')  
```

## Using NULL

1. List the teachers who have NULL for their department.

```sql
SELECT NAME
FROM   teacher
WHERE  dept IS NULL
```

2. Note the INNER JOIN misses the teachers with no department and the departments with no teacher. 

```sql
SELECT teacher.NAME,
       dept.NAME
FROM   teacher
       INNER JOIN dept
               ON ( teacher.dept = dept.id )
```

3. Use a different JOIN so that all teachers are listed.

```sql
SELECT teacher.NAME,
       dept.NAME
FROM   teacher
       LEFT JOIN dept
              ON ( teacher.dept = dept.id )
```

4. Use a different JOIN so that all departments are listed.

```sql
SELECT teacher.NAME,
       dept.NAME
FROM   teacher
       RIGHT JOIN dept
               ON ( teacher.dept = dept.id )
```

5. Use COALESCE to print the mobile number. Use the number '07986 444 2266' if
   there is no number given. **Show teacher name and mobile number or '07986
   444 2266'**

```sql
SELECT NAME,
       COALESCE(mobile, '07986 444 2266')
FROM   teacher
```

6. Use the COALESCE function and a LEFT JOIN to print the teacher **name** and department name. Use the string 'None' where there is no department.

```sql
SELECT teacher.NAME,
       COALESCE(dept.NAME, 'None')
FROM   teacher
       LEFT JOIN dept
              ON ( teacher.dept = dept.id )
```

7. Use COUNT to show the number of teachers and the number of mobile phones.

```sql
SELECT Count(teacher.NAME),
       Count(mobile)
FROM   teacher
       LEFT JOIN dept
              ON ( teacher.dept = dept.id )
```

8. Use COUNT and GROUP BY **dept.name** to show each department and the number of staff. Use a RIGHT JOIN to ensure that the Engineering department is listed.

```sql
SELECT dept.NAME,
       Count(teacher.NAME)
FROM   teacher
       RIGHT JOIN dept
               ON ( teacher.dept = dept.id )
GROUP  BY dept.NAME
```

9. Use CASE to show the **name** of each teacher followed by 'Sci' if the teacher is in **dept** 1 or 2 and 'Art' otherwise.

```sql
SELECT teacher.NAME,
       ( CASE
           WHEN teacher.dept IN ( 1, 2 ) THEN 'Sci'
           ELSE 'Art'
         END )
FROM   teacher
```

10. Use CASE to show the name of each teacher followed by 'Sci' if the teacher is in dept 1 or 2, show 'Art' if the teacher's dept is 3 and 'None' otherwise.

```sql
SELECT teacher.NAME,
       ( CASE
           WHEN teacher.dept IN ( 1, 2 ) THEN 'Sci'
           WHEN teacher.dept = 3 THEN 'Art'
           ELSE 'None'
         END )
FROM   teacher
```

## Self join

1. How many **stops** are in the database.

```sql
SELECT Count (DISTINCT id)
FROM   stops
```

2. Find the **id** value for the stop 'Craiglockhart'

```sql
SELECT id
FROM   stops
WHERE  NAME = 'Craiglockhart'
```

3. Give the **id** and the **name** for the **stops** on the '4' 'LRT' service.

```sql
SELECT id,
       NAME
FROM   stops
       JOIN route
         ON ( stops.id = route.stop )
WHERE  company = 'LRT'
       AND num = '4'
```

4. The query shown gives the number of routes that visit either London Road (149) or Craiglockhart (53). Run the query and notice the two services that link these **stops** have a count of 2. Add a HAVING clause to restrict the output to these two routes.

```sql
SELECT company,
       num,
       Count(*)
FROM   route
WHERE  stop = 149
        OR stop = 53
GROUP  BY company,
          num
HAVING Count(*) = 2
```

5. Execute the self join shown and observe that b.stop gives all the places you can get to from Craiglockhart, without changing routes. Change the query so that it shows the services from Craiglockhart to London Road.

```sql
SELECT a.company,
       a.num,
       a.stop,
       b.stop
FROM   route a
       JOIN route b
         ON ( a.company = b.company
              AND a.num = b.num )
WHERE  a.stop = 53
       AND b.stop = 149
```

6. The query shown is similar to the previous one, however by joining two copies of the **stops** table we can refer to **stops** by **name** rather than by number. Change the query so that the services between 'Craiglockhart' and 'London Road' are shown. If you are tired of these places try 'Fairmilehead' against 'Tollcross'

```sql
SELECT a.company,
       a.num,
       stopa.NAME,
       stopb.NAME
FROM   route a
       JOIN route b
         ON ( a.company = b.company
              AND a.num = b.num )
       JOIN stops stopa
         ON ( a.stop = stopa.id )
       JOIN stops stopb
         ON ( b.stop = stopb.id )
WHERE  stopa.NAME = 'Craiglockhart'
       AND stopb.NAME = 'London Road'
```

7. Give a list of all the services which connect stops 115 and 137 ('Haymarket' and 'Leith')

```sql
SELECT DISTINCT r1.company,
                r1.num
FROM   route r1,
       route r2
WHERE  r1.num = r2.num
       AND r1.company = r2.company
       AND r1.stop = 115
       AND r2.stop = 137
```

8. Give a list of the services which connect the **stops** 'Craiglockhart' and 'Tollcross'

```sql
SELECT DISTINCT r1.company,
                r1.num
FROM   route r1,
       route r2,
       stops s1,
       stops s2
WHERE  r1.num = r2.num
       AND r1.company = r2.company
       AND r1.stop = s1.id
       AND r2.stop = s2.id
       AND s1.NAME = 'Craiglockhart'
       AND s2.NAME = 'Tollcross'
```

9. Give a distinct list of the **stops** which may be reached from 'Craiglockhart' by taking one bus, including 'Craiglockhart' itself, offered by the LRT company. Include the company and bus no. of the relevant services.

```sql
SELECT DISTINCT s2.NAME,
                r2.company,
                r2.num
FROM   route r1,
       route r2,
       stops s1,
       stops s2
WHERE  r1.num = r2.num
       AND r1.company = r2.company
       AND r1.stop = s1.id
       AND r2.stop = s2.id
       AND s1.NAME = 'Craiglockhart'
```
