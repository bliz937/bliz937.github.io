---
layout: post
title:  "PHP mysqli syntax error - creating db"
date:   2016-08-29 20:00:00 +0200
description: "PHP's mysqli returns a 'error in your SQL syntax' when trying to create a db."
tags: mysql sql server phpmyadmin apache mariadb php mysqli schema database
---
Using [mysqli](https://secure.php.net/manual/en/book.mysqli.php), I want to create a db giving a ```db.sql``` file.
My code looked like

```php
...
$sqlcmd = file_get_contents("db.sql");

if (!$conn->query($sqlcmd)) {
...
```

## My problem
When running this, I would receive the following error:

```
You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0; SET @OLD' at line 2
```

## My fix
After browsing around, I came to [multi_query](http://www.php.net/manual/en/mysqli.multi-query.php), which allows for multiple queries where as ```query()```, above, does a single query. Naturally I found this on [Stackoverflow](https://stackoverflow.com/questions/20735771/mysql-works-in-console-but-not-in-phps-mysqli-query-function#answer-20735794).

My code should thus look like:

```
$sqlcmd = file_get_contents("db.sql");


if (!$conn->multi_query($sqlcmd)) {
...
```

TL;DR: ```query()``` does one query, use ```multi_query()``` for multiple queries.
