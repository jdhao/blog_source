---
title: "Learning SQL from Scratch(2): Setup and Preparations"
date: 2022-06-04T19:03:20+08:00
markup: markdown
tags: [PostgreSQL, MySQL]
categories: [SQL]
---

In this post, I will talk about setting up SQL databases and also the tools needed to write SQL queries.

<!--more-->

# Set up SQL databases

## Mysql install and setup

Install using HomeBrew:

```
brew install mysql
```

Start and stop the mysql server:

```
brew services start mysql
brew services stop mysql
```

### Connect to mysql server in macOS

+ Host: localhost
+ Port: 3306
+ Username: root

## postgresql install

Install using HomeBrew:

```
brew install postgresql
```

Start and stop the postgres server:

```
brew services start postgresql
brew services stop postgresql
```

### Connect to postgres server in macOS

+ Host: localhost
+ Database: postgres
+ Username: jdhao

No password is needed.

# Try sql online

If you do not want to install and just want to try with different SQL databases,
you can use [sqlfiddle](http://sqlfiddle.com/), which supports both MySQL, PostgreSQL and SQLite.

# GUI client

Most people will probably choose a GUI client to work with the databases.

## Mysql workbench

[Mysql workbench](https://www.mysql.com/products/workbench/) is a GUI application to help us manage mysql server more easily.

## Pgadmin

If you use PostgreSQL, you can try [Pgadmin 4](https://www.pgadmin.org/download/).

## Datagrip

[Datagrip](https://www.jetbrains.com/datagrip/) is developed my JetBrains and it is not free.
It has support for all kinds of databases.

It has autocompletion for SQL keywords, functions, table names, and column names etc.
Highly recommended, intelligent and very good user experience.

## DBeaver

[DBeaver](https://dbeaver.io/download/) is an open source database client.

It also has autocompletion, but it is not as powerful as Datagrip.

Change keyword case in DBeaver: https://stackoverflow.com/a/60854946/6064933

## BeeKeeper Studio

[BeeKeeper](https://www.beekeeperstudio.io/) is another database client.

No autocompletion, very simple GUI, less features than DBeaver and Datagrip.
Not recommended.
