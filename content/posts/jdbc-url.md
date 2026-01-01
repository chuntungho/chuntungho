---
title: JDBC Connection URL
date: 2020-05-18 00:00:00.0
updated: 2022-04-07 11:19:15.403
summary: JDBC Connection URL
categories: ["Tech"]
tags: ["jdbc"]
---

## MySQL
**Driver**: com.mysql.jdbc.Driver
**URL**: jdbc:mysql://\<host>:\<port>/\<database>

## MariaDB
**Driver**: org.mariadb.jdbc.Driver
**URL**: jdbc:mariadb://\<host>:\<port>/\<database>

## PostgreSQL
**Driver**: org.postgresql.Driver
**URL**: jdbc:postgresql://\<host>:\<port>/\<database>

## SQL Server
**Driver**: com.microsoft.sqlserver.jdbc.SQLServerDriver
**URL**: jdbc:microsoft:sqlserver://$host:$port;DatabaseName=$database

## Oracle
**Driver**: oracle.jdbc.driver.OracleDriver
**URL**: jdbc:oracle:thin:@\<host>:\<port>/\<ServiceName>

## DB2
**Driver**: com.ibm.db2.jcc.DB2Driver
**URL**: jdbc:db2://\<host>:\<port>/\<database>

## H2
**Driver**: org.h2.Driver
**URL**: jdbc:h2:\<file-path>

## Sqlite
**Driver**: org.sqlite.JDBC
**URL**: jdbc:sqlite:\<file-path>