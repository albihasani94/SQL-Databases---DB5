Q1
Find the titles of all movies directed by Steven Spielberg.

```sql
SELECT title FROM MOVIE WHERE DIRECTOR = 'Steven Spielberg';
```

Q2
Find all years that have a movie that received a rating of 4 or 5, and sort them in increasing order.

```sql
SELECT distinct year FROM movie JOIN RATING USING (MID) where stars in (4, 5) order by year asc;
````

Q3
Find the titles of all movies that have no ratings. 

```sql
SELECT title from movie left outer join rating using (mid) where stars is null;
```

Q4
Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date.

```sql
select distinct name from reviewer join rating using (rid) where RATINGDATE is null;
```

Q5
Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars. 

```sql
SELECT re.name, m.title, ra.stars, ra.ratingDate
from reviewer re join rating ra using (rid)
join movie m using (mid)
order by re.name, m.title, ra.stars
```

Q6
For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie. 

```sql
select distinct name, title
from reviewer
join rating using (rid)
join movie using (mid)
where rid in 
(select r2.rid from rating r1, rating r2 
where r1.rid=r2.rid and r1.mid=r2.mid and r2.ratingDate>r1.ratingDate and r2.stars>r1.stars 
group by(r2.rid));
```

Q7
For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title. 

```sql
select title, max(stars)
from movie join rating using (mid)
group by title
order by title;
```

Q8
For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title. 

```sql
select title, (max(stars)-min(stars)) as spread
from movie join rating using (mid)
group by title
order by spread desc, title;
```

Q9
Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.) 

```sql
select (select avg(av)
from (select mid, year, avg(stars) as av 
from rating join movie using(mid) 
group by mid) 
where year<1980) - (select avg(av)
from (select mid, year, avg(stars) as av 
from rating join movie using(mid) 
group by mid) 
where year>1980)
```