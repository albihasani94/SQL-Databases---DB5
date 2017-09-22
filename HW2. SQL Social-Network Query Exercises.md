Q1.
Find the names of all students who are friends with someone named Gabriel.

```sql
select h1.name from highschooler h1, highschooler h2, friend f
where h1.id = f.id1
and h2.id = f.id2
and h2.name = 'Gabriel'
```

Q2.
For every student who likes someone 2 or more grades younger than themselves, return that student's name and grade, and the name and grade of the student they like.

```sql
select h1.name, h1.grade, h2.name, h2.grade
from highschooler h1, highschooler h2, likes
where h1.grade-h2.grade>=2
and h1.id = likes.id1
and h2.id = likes.id2
```

Q3.
For every pair of students who both like each other, return the name and grade of both students. Include each pair only once, with the two names in alphabetical order. 

```sql
select h1.name, h1.grade, h2.name, h2.grade
from (Highschooler as h1 join (select id1, id2 
                               from likes join (select id2 as id1 ,id1 as id2 from likes) 
                                    using (id1, id2))as a on h1.id=a.id1) join Highschooler as h2 on h2.id=a.id2
where h1.name < h2.name
```

Q4.
Find all students who do not appear in the Likes table (as a student who likes or is liked) and return their names and grades. Sort by grade, then by name within each grade.

```sql
select name, grade
from highschooler h1
where h1.id not in (select id1 from likes)
and h1.id not in (select id2 from likes)
order by grade, name
```

Q5.
For every situation where student A likes student B, but we have no information about whom B likes (that is, B does not appear as an ID1 in the Likes table), return A and B's names and grades.

```sql
select h1.name, h1.grade, h2.name, h2.grade from highschooler h1
join likes on (h1.id=likes.id1)
join highschooler h2 on (h2.id=likes.id2)
where h2.id not in (select id from highschooler h join likes on (h.id=likes.id1))
```

Q6.
Find names and grades of students who only have friends in the same grade. Return the result sorted by grade, then by name within each grade.

```sql
select distinct h1.name, h1.grade from highschooler h1
where not exists(select * from highschooler h2
join friend f on (h2.id = f.id2)
where 
h1.id=f.id1 and
h1.grade <> h2.grade)
order by h1.grade, h1.name
```

Q7.
For each student A who likes a student B where the two are not friends, find if they have a friend C in common (who can introduce them!). For all such trios, return the name and grade of A, B, and C.

```sql
select (select name from Highschooler where id=ab.id1), 
       (select grade from Highschooler where id=ab.id1), 
       (select name from Highschooler where id=ab.id2), 
       (select grade from Highschooler where id=ab.id2), 
       (select name from Highschooler where id=c.id2), 
       (select grade from Highschooler where id=c.id2)
from (select * from likes except select * from friend) ab join friend c on ab.id1=c.id1
where exists (select * from friend where ab.id2=id1 and c.id2=id2)
```

Q8.
Find the difference between the number of students in the school and the number of different first names.

```sql
select (select count(*) from highschooler) - (select count(*) from (select distinct(name) from highschooler))
```

Q9.
Find the name and grade of all students who are liked by more than one other student. 

```sql
select name, grade from highschooler
where (select count(*) from likes where id2=id)>1
```