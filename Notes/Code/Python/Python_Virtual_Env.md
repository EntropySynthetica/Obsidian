---
tags: [Code]
title: Python_Virtual_Env
created: '2020-01-30T20:16:16.143Z'
modified: '2020-07-17T21:08:30.155Z'
---

# Python Virtual Env
Created Tuesday 18 December 2018


### Create a folder to store the virtual enviroment with this.  Creates the virtual environment inside the env directory

`python3 -m venv env`


### To active the virtual enviroment go into the folder and run

`source bin/activate`


### The command prompt should show the name of the virtual enviroment.  The enviroment can be verified with 

`which python`
and
`pip3 list`


### Install any required pip modules.  

`pip3 install <whatever>`


### When done put away the virtual enviroment with

`deactivate`

repeat step 2 to return to the enviroment.  

### To save your installed dependencies
`pip freeze > requirements.txt`

To install dependencies you've previously frozen
`pip install -r requirements.txt`

To see what you have installed
`pip list`

### Update PIP Version
`pip3 install --ignore-installed pip`

### Show out of date packages
`pip list --outdated`



