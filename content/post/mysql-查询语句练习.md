+++
title = "mysql查询语句练习"
date = 2019-06-13
lastmod = 2019-06-13T14:51:14+08:00
tags = ["Mysql"]
categories = ["learn"]
draft = false
toc = true

+++

> 这是mysql 数据库课程的作业题，有些题写法太多了，所以只写一个，可能有错误，如果有，请指出。。。

<!--more-->

## 表结构
student(sno,sname,ssex,sage,sdept)

sc(sno,cno,grade)

course(cno,cname,cpno,ccredit)


## 具体查询

1.查询有3门以上（包含3门）课程是90分以上（包含90分）的学生的学号及（90分以上的）课程数
```
select sno,count(*) from sc where grade >=90 group by sno having count(*)>=3;
```
2.查询张三所选修的课程号和成绩。
```
select cno,grade from sc where sno in (select sno from student where sname ="张三");
```
3.查询苏三选修的课程名称及学分。
```
select cname,ccredit from course where cno in (select cno from sc where sno in (select sno from student where sname="苏三"));
```
4.查询每个学生的学号、姓名、选修的课程名及成绩

```
select student.sno,sname,cname,grade from (student left join sc on student.sno=sc.sno) left join course on sc.cno=course.cno;
```
5.查询所有学生的选课情况，列出学号、姓名、课程号、课程名、成绩、学分。
```
select student.sno,sname,sc.cno,cname,grade,ccredit from (student left join sc on student.sno=sc.sno) left join course on sc.cno=course.cno;
```
6.查询所有低于90分的学生成绩记录，并按学号降序排列，列出学号、姓名、课程号、课程名、成绩。
```
select student.sno,sname,sc.cno,cname,grade from student,sc,course where student.sno=sc.sno and sc.cno=course.cno and grade<90 order by sno desc;
```
7.查询选修“2”号课程且成绩在90分以上的所有学生的学号、姓名、成绩。
```
select student.sno,sname,grade from student,sc where student.sno= sc.sno and cno='2' and grade >90;
```
8.查询“DB_DESIGN”的先行课名称。
```
select cname from course where cno=(select cpno from course where cname="DB_DESIGN");
```
9.查询同时选修了课程号为“1”、“2”的学生学号。
```
select a.sno from sc a,sc b where a.cno=1 and b.cno=2 and a.sno=b.sno;
```
10.查询计算机系全体学生的“DB_DESIGN”成绩，列出学号、姓名、课程名、成绩，并按成绩降序排列。
```
select tablea.sno,sname,cname,grade from (select sno,sname from student where sdept="CS")as tablea left join (select sno,cname,grade from sc,course where sc.cno=course.cno and cname="DB_DESIGN")as tableb on tablea.sno=tableb.sno order by grade desc;
```
11.查询其他系中比信息系所有学生年龄都不小的的学生姓名及年龄。
```
select sname,sage from student where sdept<>"IS" and sage >=all(select sage from student where sdept="IS");
```
12.查询没有选修“1”号课程的学生姓名。
```
select sname from student where not exists (select * from sc where cno='1' and sc.sno=student.sno);
```
13.查询有3名以上学生选修的课程号。
```
select cno from sc group by cno having count(*)>=3;
```
14.查询“DB_DESIGN”课程没有成绩的学生姓名。
```
select sname from student where exists (select grade from sc where grade is NULL and sc.sno=student.sno and sc.cno=(select cno from course where cname="DB_DESIGN"));
```
15.查询各门课程的成绩均不低于90分的学生姓名
```
select sname from student,sc where student.sno=sc.sno group by sname having min(grade)>=90;
```
16.查询“DB_DESIGN”成绩不低于该门课程平均分的学生姓名
```
select sname from student,sc,course where student.sno=sc.sno and course.cno=sc.cno and course.cname="DB_DESIGN" and grade>=(select avg(grade) from sc,course where course.cname="DB_DESIGN");
```
17.查询各个系男女学生的平均年龄和人数。
```
select sdept,ssex,avg(sage),count(sdept) from student group by sdept,ssex;
```
18.查询计算机系(CS)总平均成绩最高的学生学号和姓名。
```
select student.sno,sname from student,sc where student.sno=sc.sno and sdept="CS" group by student.sno,sname having avg(grade)>=all(select avg(grade) from student,sc where student.sno=sc.sno and sdept="CS");
```
19.查询总平均成绩大于85的学生人数。
```
select count(*) as "人数" from (select avg(grade) from sc group by sno having avg(grade)>85)as new_table;
```
20.查询“3”号课成绩比“95002”号同学该门课成绩高的所有学生的学号。
```
select a.sno from (select sno,grade from sc where cno='3')a,(select sno,grade from sc where cno='3' and sno="95002")b where a.grade>b.grade;
```
21.查询“2”号课成绩比“3”号课成绩高的所有学生的学号及其“2”号课和“3”号课的成绩
```
select a.sno,a.grade as '2\'grade',b.grade as '3\'grade' from (select sno,grade from sc where cno='2')a,(select sno,grade from sc where cno='3')b where a.grade>b.grade and a.sno=b.sno;
```
22.查询有二门以上（含两门）及格课程的学生姓名及其平均成绩。
```
select sname,avg(grade) from sc,student where student.sno=sc.sno group by sname having count(grade>=60)>=2;
```
23.查询各科成绩最高分和最低分，以如下字段形式显示：课程号，最高分，最低分
```
select course.cno,max(grade),min(grade) from course left join sc on course.cno=sc.cno group by cno;
```
24.查询最受欢迎的课程的课程号和选课人数
```
select cno,count(*) from sc group by cno order by count(*) desc limit 1;
```
25.查询和“95001”号同学学习的课程完全相同的其他同学的学号和姓名
```
select student.sno,sname from student,sc where student.sno=sc.sno and cno in (select cno from sc where sno="95001") group by student.sno having count(*)=(select count(*) from sc where sno="95001") and sno <>"95001";
```
