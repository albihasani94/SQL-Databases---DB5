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