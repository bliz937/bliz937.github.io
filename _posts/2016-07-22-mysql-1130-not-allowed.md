---
layout: post
title:  "MySql Not Allowed to Connect"
date:   2016-07-22 13:00:00 +0200
description: "MySql server Error 1130 Not allowed to connect."
tags: mysql sql server network permission phpmyadmin wamp apache security
---
Running a MySql server on another machine, I wanted to connect to it remotely.

## My problem
Trying to manually connect with the ```mysql``` application, I get the following response:

```powershell
PS C:\wamp64\bin\mysql\mysql5.7.9\bin> .\mysql.exe -u root -h 192.168.x.y
ERROR 1130 (HY000): Host 'hostname' is not allowed to connect to this MySQL server
PS C:\wamp64\bin\mysql\mysql5.7.9\bin>
```

## My fix
On the MySql server machine, open up a web browser and go to ```http://localhost/phpmyadmin``` - I am running [wamp](www.wampserver.com) on windows, so apache, mysql, php and phpmyadmin is all bundled together in one application.

![phpmyadmin login](/assets/2016/07/phpmyadmin-login.PNG)

After logging in, with an admin account, click on ```User accounts``` in the bar at the top.
Select the account you want to connect remotely with.

![phpmyadmin user edit](/assets/2016/07/phpmyadmin-user-edit.png)

Select ```Login Information``` and then the drop down ```Host name```, of which you can specify a host or select 'Any host'. I am selecting 'Any host' - this is not a good idea for _production servers_.

![phpmyadmin user edit](/assets/2016/07/phpmyadmin-user-host.png)

Finally, click ```Go``` in the bottom right hand corner and you will, should, end up with a successful SQL query shown in phpmyadmin. The resulting query for me was:

```mysql
CREATE USER 'root'@'%' IDENTIFIED WITH mysql_native_password AS '***';
GRANT USAGE ON *.* TO 'root'@'%' REQUIRE NONE;
```

Connecting from the remote machine results in a successful connection.

TL;DR: Change user host login setting.
