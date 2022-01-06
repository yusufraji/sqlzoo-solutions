# SQLZoo Solutions
Here are my solutions to SQL tutorials from
[SQLZoo](https://sqlzoo.net/wiki/SQL_Tutorial)

## Sections
6. [JOIN](#join) - In which we join two tables; game and goals.
7. [More JOIN operations](#more-join) - In which we join two tables; game and goals.
8. [using NULL](#using-null) - In which we look at teachers in departments.
9. [Self join](#self-join) - In which we join Edinburgh bus routes to Edinburgh bus routes.
    
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
