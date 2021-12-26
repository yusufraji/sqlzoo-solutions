# SQLZoo Solutions
Here are my solutions to SQL tutorials from
[SQLZoo](https://sqlzoo.net/wiki/SQL_Tutorial)

## Sections
7. [More JOIN operations](#more-join) - In which we join two tables; game and goals.
    
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
