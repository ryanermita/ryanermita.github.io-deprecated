---
title: Running Raw SQL Queries in Ruby on Rails
date: "2015-11-01"
template: "post"
draft: false
slug: "running-raw-sql-queries-in-ruby-on-rails"
category: "Software Engineering"
tags:
  - "queries"
  - "sql"
  - "ruby on rails"
  - "programming"
description: "Sometimes you have to work with raw SQL in Ruby on Rails(RoR) for the better performance of your application. This article will show you a simple way to run raw SQL queries on RoR."
---

Sometimes you have to work with raw SQL in RoR for the better performance of your application. But how would you do it? Here’s a simple example on how to use raw SQL in RoR:

```
sql = "Select * from table"
records = ActiveRecord::Base.connection.execute(sql)
```

RoR devs, whats your take on this? any comments, suggestions, or ideas regarding raw SQL in RoR? I love to know. Let’s talk in the comment section.