+++
title = "Mybatis Builder"
date = 2024-01-20
summary = "An IDEA plugin integrated with MyBatis Generator."
featured = true
weight = 5
tags = ["idea", "mybatis"]
categories = ["Plugin"]
url = "mybatis-builder"
main_image = "/images/mybatis.svg"
+++



[Plugin homepage](https://plugins.jetbrains.com/plugin/12449-mybatis-builder) \| [Source code](https://github.com/chuntungho/mybatis-builder)

A free GUI tool integrated with MyBatis Generator, compatible with IntelliJ IDEA, Android Studio.

Supported databases: mysql/postgresql/oracle/sqlserver/sqlite/h2.

Provides with additional features as below.

* Merge existing MyBatis files automatically.
* Support select with lock statement.
* Support Lombok @Data annotation on java model.
* Customizable mapper annotation, default is Spring @Repository.
* Customizable patterns for mapper type, example type and SQL file name.
* "Copy as Executable SQL" from MyBatis log.
* "Run MyBatis Generator" with official configuration file.

Snapshots:

* Tool window appears on left bottom.  
  ![1](/upload/2021/07/1-bf371f71187a40a985a6e0a76bf1b7ed.png)

* Click the first button on toolbar to open Settings Dialog, manage connections, default settings and plugin settings.  
  ![2-1](/upload/2021/07/2-1-69fc5d7ba73545c1bcbc8dba37f3e7c6.png)  
  ![2-2](/upload/2021/07/2-2-bebc69c7f06c49bca13d80e7817cd89c.png)  
  ![2-3](/upload/2021/07/2-3-77c9d5e3821b457089c894694aa640fd.png)

* Select table(s) and then click the second button to open Building Dialog, configure MyBatis Generator parameters, click "Open" button to override or ignore column, click "Stash" button to save parameters without generation, click "OK" button to generate MyBatis files.  
  ![3-1](/upload/2021/07/3-1-66356c0d6b4d466aa6b176029a6fa25a.png)  
  ![3-1-1](/upload/2021/07/3-1-1-dcce30b896034872987b2bf3c393a23a.png)  
  ![3-2](/upload/2021/07/3-2-04b8eb915df94d3ba5019a5914b9a5ae.png)

* Genereated result as following.  
  ![4](/upload/2021/07/4-7323a7e64b4f48aa8c80f4ce4c1624e2.png)

* Alternatively, right click on configuration file to "Run MyBatis Generator".  
  ![5](/upload/2021/07/5-d094501f757a4c47bc1de1a9067d641b.gif)

* An additional utility, select printed SQL on console and right click to "Copy as Executable SQL".  
  ![6](/upload/2021/07/6-f3a2541eb77643e489c06c19ec99517c.gif)