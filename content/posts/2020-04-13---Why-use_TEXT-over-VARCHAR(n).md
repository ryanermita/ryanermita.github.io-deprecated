---
title: "Why use TEXT over VARCHAR(n)"
date: "2020-04-13"
template: "post"
draft: false
slug: "why-use-text-over-varchar-n"
category: "Software Engineering"
tags:
  - "databases"
description: "I encountered an issue with VARCHAR(n) regarding the character limit and this experience makes me think of the best way to solved it. It can be easily solved by increasing the N on the VARCHAR(N) field, but I went to another route by changing the field from VARCHAR(n) to TEXT. This article will explain the rationale behind that solution."
---

I got an error earlier regarding exceeding the character length limit in a `varchar(n)` column. This issue raised some questions; should we just increased the number of character limit for the `varchar(n)` or convert the column type to `text`? When should we use `varchar(n)` over `text` and vice-versa?

based on this [article](https://www.depesz.com/2010/03/02/charx-vs-varcharx-vs-varchar-vs-text/), `varchar(n)` and `text` are saved in the same structure underneath, which is `varlena`, a `C` data structure. The difference is that `varchar(n)` has a built-in character length limit which is `n` . Also, based on the article, changing the `n` in a `varchar(n)` column in a live environment is very painful because it will cause DB locks that will result to system downtime. Using `text` is preferred as it has unlimited character length and if we need to add character length limit restriction it can be done by DB functions and changing those DB functions wont cause DB locks and system downtime. It that sense `text` is more manageable and flexible.

In my case, changing my column type to `text` is the more applicable solution as I don’t know how much data my column will hold. I should’ve thought of this before I built my DB tables. Well, we’re not perfect and we'll learn/relearn better practices because of this kind of mistakes.

I’m grateful to one of our DevOps who give me this [article](https://www.depesz.com/2010/03/02/charx-vs-varcharx-vs-varchar-vs-text/): *thumbsup*