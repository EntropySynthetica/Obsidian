---
tags: [Database, Postgresql]
title: Postgresql Notes
created: '2020-02-27T22:02:32.049Z'
modified: '2020-02-27T22:08:37.994Z'
---

# Postgresql Notes

## Connect to DB
If DB is Local
`sudo -u postgres psql`

If DB is remote
`psql -h <server ip> -U <username> -d <database name>`

## Create a Database
`CREATE DATABASE <database name>;`

## Create User
`CREATE USER <username> WITH ENCRYPTED PASSWORD '<password>';`
`GRANT ALL PRIVILEGES ON DATABASE <database name> TO <username>;`

## Change User Password
`alter user <username> with encrypted password '<password>';`
