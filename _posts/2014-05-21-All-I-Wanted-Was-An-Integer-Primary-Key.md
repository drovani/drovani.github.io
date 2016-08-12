---
layout: post
title: All I Wanted Was an Integer Primary Key
---

> The [ASP.NET Identity](http://www.asp.net/identity) system is designed to replace the previous ASP.NET Membership and Simple Membership systems. It includes profile support, OAuth integration, works with OWIN, and is included with the ASP.NET templates shipped with Visual Studio 2013.

In and of itself, it is a very nice and pleasant library that really does integrate nicely into a WebAPI or MVC project.  The template project that comes with VS2013 spins up very quickly and provides the illusion of something that the developer can just forget about.  Were that the case, I would not have this post.

All I wanted was to change the default primary key from a `GUID` to an int.  My initial thought was to change the default `ApplicationUser` from inheriting from the non-generic IdentityUser to the generic `IdentityUser<int, VigilUserLogin, VigilUserRole, VigilUserClaim>`.  Compiling worked just fine, running caused all kinds of type casting problems.  Why?  Because all of the other classes surrounding the `IdentityUser` were all coded to the `string` type (which internally is a `GUID`).

Thus began the big hunt to find out all of the places where I needed to override, inherit, or otherwise manipulate the library to get the primary key to be an integer. This meant replacing the five main classes that create the authentication and authorization schema:

- IdentityUser
- IdentityRole
- IdentityUserRole
- IdentityUserClaim
- IdentityUserLogin

Then I had to create a new `UserStore` and `UserManager` to utilize these new classes, and create a new `ClaimsPrincipal` to store and retrieve the new security identifier (Sid) for the user. Finally, a new `BaseController` class was created that all other controllers must inherit from, in order to utilize all of this new code.

{% gist https://gist.github.com/drovani/25285473d49aa5a3d3ba %}

At this point, I am wrestling with the fact that now my domain model has a hard requirement on the Microsoft.AspNet.Identity library. I had initially hoped to have my User and Role domain models be completely separated from the actual authentication/authorization system. This creates a hard link that is going to be the death of me unless I can find a way to isolate the framework and run unit tests without requiring the database. For now, though, I am going to continue in this direction, and turn a blind eye to the technical debt accruing from this decision. Maybe before I ship this, I will find a better way to do it all, but for now at least I have my integers.