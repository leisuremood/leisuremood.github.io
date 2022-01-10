---
layout: post
title:  "基础sqlite查询sea-orm"
date:   2015-09-01
tags:
      - it
---


# Table of Contents

1.  [SQLite 3 一些基本的使用](#orgddca924)
2.  [An async & dynamic ORM for Rust](#orgcfbef05)


<a id="orgddca924"></a>

# SQLite 3 一些基本的使用

<https://www.yiibai.com/sqlite/sqlite_sub_queries.html>

<https://www.cnblogs.com/Mr_JinRui/archive/2010/05/12/1733662.html>

    SELECT * FROM COMPANY WHERE ID IN (SELECT ID FROM COMPANY WHERE SALARY > 45000) ;
    insert into revtbl select * from revtbl;
    DROP VIEW IF EXISTS v_test;
    CREATE VIEW IF NOT EXISTS v_test AS SELECT * FROM test;


<a id="orgcfbef05"></a>

# An async & dynamic ORM for Rust

An Object Relational Mapper (ORM) is a programming library to help you interact with a relational database from an Object-Oriented Programming (OOP) language.

<https://www.sea-ql.org/SeaORM/docs/introduction/orm>

