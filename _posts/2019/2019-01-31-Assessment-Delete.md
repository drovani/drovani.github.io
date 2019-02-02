---
layout: post
title: Adding Delete Functionality
series: Technical Assessment
category: "Rovani in C&sharp;"
treeid: techassessment-basic/tree/528a447383b5ce2037b042138b7f5b2a3ac76476
tags:
- career
- angular
- entityframework
- ngrxstore
- sqlserver
---

Now that I have a static list, the next step is to make it slightly more dynamic. This means that I need to learn how to send something back to the server, instead of just retrieving data from the server. My guess is that a delete method should be easier than an insert of a new record, though I expect them both to be about equal. The first piece I recognized that I needed to add is an identifier for indicating _which_ record to delete. I am a big fan of using a `Guid` as the datatype for a "primary key", so I quickly added a new field to the `BlogPost` and added that to my seed data. But how to I make a button in `Angular` and then make a `DELETE` call to the controller with the id of the selected post?

{% include series.html %}

