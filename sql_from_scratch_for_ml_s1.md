---
title: "Learning SQL from Scratch(1): Do We Even Need to Start?"
date: 2022-06-04T01:19:17+08:00
markup: markdown
tags: [PostgreSQL, MySQL]
categories: [SQL]
---

As a machine learning engineer who mainly deals with vision-based projects,
I have little opportunity in my work to actually use SQL (Structured Query Language.).
Even when I need it, I often just ask the data engineers for a favor.

However, I thought it would be an essential skill to learn even if I do not need it for now.

<!--more-->

# Why does machine learning needs SQL?

It really depends on what kind of data you are dealing with.
If you are doing traditional machine learning, for example, recommendation, ranking,
the raw data will probably be store in database, and you will need SQL for data wrangling.

If you are working on computer vision, audio signal recognition etc.,
you may not need to know SQL, but it depends.

Ref:

+ https://www.quora.com/Is-learning-SQL-necessary-for-machine-learning
+ https://www.quora.com/Why-did-Google-name-SQL-the-number-one-skill-for-machine-learning-engineers
+ https://www.quora.com/How-much-SQL-do-you-need-as-an-AI-engineer

# Which of the following SQL databases to use?

When it comes to the actual SQL databases, there are a lot of SQL databases out there.

+ SQLite: It is an embeddable DBMS. It is small and use local files. Can not handle large-scale requests and tera-bytes of data.
+ [MySQL](https://github.com/mysql/mysql-server): It is a client-server RDBMS. It has more features than sqlite. Now development led by people from Oracle.
+ [postgresSQL](https://www.postgresql.org/): Another RDMBS. It has more advanced feature and it is open source.
+ [Microsoft SQL server](https://en.wikipedia.org/wiki/Microsoft_SQL_Server): It is a commercial software.
+ [Oracle SQL](https://docs.oracle.com/cd/B19306_01/server.102/b14200/intro.htm): It is also a commercial software.

All these different databases support some of the common SQL syntax,
but they may have their own extended syntax.

Postgresql seems to support more standard SQL syntax than mysql,
but much of the knowledge is transferable between the two.

Practically, we can learn MySQL or PostgreSQL,
but it seems that Postgres have more detailed documentations.

In the below, I also give some links discussing the differences between different database software.

ref:

+ https://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems
+ MySql vs sqlite: https://stackoverflow.com/q/4813890/6064933
+ sqlite faster than Mysql? https://stackoverflow.com/q/29452110/6064933
+ Mysql vs postgresql:
    + https://stackoverflow.com/a/4059569/6064933
    + https://www.reddit.com/r/learnprogramming/comments/pbzrjp/which_db_should_i_learn_mysql_or_postgresql/
    + https://www.ibm.com/cloud/blog/postgresql-vs-mysql-whats-the-difference
