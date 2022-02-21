---
layout: post
title: "EF Core Inheritance"
subtitle: "How different approaches work"
date: 2022-02-20 01:23:55 -4000
background: '/img/posts/riccardo-annandale-7e2pe9wjL9M-unsplash.jpg'
---

<h3>EF Core Inheritance</h3>
<p>There are three approaches for implementing inheritance in Entity Framework.
<ul>
    <li>
        Table per Hierarchy (TPH)
    </li>
    <li>
        Table per Concrete (TPC)
    </li>
    <li>
        Table per Type (TPT)
    </li>
</ul>
<p>TPT and TPC both create a concrete tables when you implement inheritance whereas TPH records for multiple subclasses live in the same table. Right now EF core only supports TPH.</p>

<b>Table per Concrete (TPC)</b>
<p>In TPC, for each concrete class we get one table.</p> <p>If there are student and teacher that inherits from abstract person class, in the DB, we get two tables: <code>student</code> and <code>teacher</code>.</p><p>These two tables will both contain the fields from the abstract class. Sql server does not know or care about inheritance so we express in code that they are related.</p>

<b>Table per Type (TPT)</b>
<p>Similar to TPC but unlike TPC, in TPT, abstract class gets its own table. </p><p>Unlike TPC, TPT tables doesn't contain the inherited fields from the abstract class. If person abstract class has age and name, student and teacher won't have those fields because person class would have those fields already. The subclass and abstract class knows about the relation due to their shared primary key value.</p>

<b>Table per Hierarchy (TPH)</b>
<p>All subclasses in the inheritance are stored in the single database table. A column is used as <code>discriminator</code> and the discriminator will determine which DB record is for which type.</p><p>Since all the subclasses are stored in a single table, it has better performance compared to other approaches. However, since different subclasses are sharing one table, this means that all the columns have to be nullable because we don't know which field is not available in other subclasses.</p><p>Also, it's not intuitive to know that which field belongs to which.</p>
<br />
<small>Photo by <a href="https://unsplash.com/@pavement_special?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Samuel Sianipar</a> on <a href="https://unsplash.com/s/photos/typing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a></small>