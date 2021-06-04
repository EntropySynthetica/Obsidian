---
tags: [Linux]
title: Add_user_to_Group
created: '2020-01-30T20:16:15.431Z'
modified: '2020-02-12T02:27:05.692Z'
---

# Add user to Group
Created Tuesday 20 December 2016

Add a User to a Group (or Second Group) on Linux
Changing the group a user is associated to is a fairly easy task, but not everybody knows the commands, especially to add a user to a secondary group. We’ll walk through all the scenarios for you.

Add a New Group

To add a new group, all you need to do is use the groupadd command like so:

`groupadd groupname`

Add an Existing User to a Group

Next we’ll add a user to the group, using this syntax:

`usermod -a -G groupname username`

For example, to add user geek to the group admins, use the following command:

`usermod -a -G admins geek`

Change a User’s Primary Group

Sometimes you might want to switch out the primary group that a user is assigned to, which you can do with this command:

`usermod -g groupname username`

View a User’s Group Assignments

If you’re trying to figure out a permissions issue, you’ll want to use the id command to see what groups the user is assigned to:

`id username`

This will display output something like this:

uid=500(howtogeek) gid=500(howtogeek) groups=500(howtogeek), 1093(admins)

You can also use the groups command if you prefer, though it is the same as using `id -Gn username`.

`groups username`

View a List of All Groups

To view all the groups on the system, you can just use the groups command:

`groups`

Add a New User and Assign a Group in One Command

Sometimes you might need to add a new user that has access to a particular resource or directory, like adding a new FTP user. You can do so with the useradd command:

`useradd -g groupname username`

For instance, lets say you wanted to add a new user named jsmith to the ftp group:

`useradd -G ftp jsmith`

And then you’ll want to assign a password for that user, of course:

`passwd jsmith`

Add a User to Multiple Groups

You can easily add a user to more than one group by simply specifying them in a comma-delimited list, as long as you are assigning the secondary groups:

`usermod -a -G ftp,admins,othergroup username`


