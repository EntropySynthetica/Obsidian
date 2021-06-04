---
tags: [Splunk]
title: Configuration_file_precedence
created: '2020-01-30T20:16:16.259Z'
modified: '2020-01-30T20:38:23.686Z'
---

# Configuration file precedence
Created Wednesday 01 November 2017

Splunk software uses configuration files to determine nearly every aspect of its behavior. A Splunk platform deployment can have many copies of the same configuration file. These file copies are usually layered in directories that affect either the users, an app, or the system as a whole.

When editing configuration files, it is important to understand how Splunk software evaluates these files and which ones take precedence.

When incorporating changes, Splunk software does the following to your configuration files:


* It merges the settings from all copies of the file, using a location-based prioritization scheme.



* When different copies have conflicting attribute values (that is, when they set the same attribute to different values), it uses the value from the file with the highest priority.



* It determines the priority of configuration files by their location in its directory structure, according to whether the file is located in a system, app, or user directory, in that order. To determine priority among the collection of apps directories, Splunk uses lexicographical order. Files in an apps directory named "A" have a higher priority than files in an apps directory named "B", and so on.


#### Precedence order within global context:
When the context is global (that is, where there's no app/user context), directory priority descends in this order:


1. System local directory -- highest priority
2. App local directories 
3. App default directories 
4. System default directory -- lowest priority


When consuming a global configuration, such as inputs.conf, Splunk first uses the attributes from any copy of the file in system/local. Then it looks for any copies of the file located in the app directories, adding any attributes found in them, but ignoring attributes already discovered in system/local. As a last resort, for any attributes not explicitly assigned at either the system or app level, it assigns default values from the file in the system/default directory.

#### Precedence order within app or user context
When there's an app/user context, directory priority descends from user to app to system:


1. User directories for current user -- highest priority 
2. App directories for currently running app (local, followed by default) 
3. App directories for all other apps (local, followed by default) -- for exported settings only 
4. System directories (local, followed by default) -- lowest priority


An attribute in savedsearches.conf, for example, might be set at all three levels: the user, the app, and the system. Splunk will always use the value of the user-level attribute, if any, in preference to a value for that same attribute set at the app or system level.

#### How app directory names affect precedence
Note: For most practical purposes, the information in this subsection probably won't matter, but it might prove useful if you need to force a certain order of evaluation or for troubleshooting.

To determine priority among the collection of apps directories, Splunk uses lexicographical order. Files in an apps directory named "A" have a higher priority than files in an apps directory named "B", and so on. Also, all apps starting with an uppercase letter have precedence over any apps starting with a lowercase letter, due to lexicographical order. ("A" has precedence over "Z", but "Z" has precedence over "a", for example.)

In addition, numbered directories have a higher priority than alphabetical directories and are evaluated in lexicographic, not numerical, order. For example, in descending order of precedence:

$SPLUNK_HOME/etc/apps/myapp1
$SPLUNK_HOME/etc/apps/myapp10
$SPLUNK_HOME/etc/apps/myapp2
$SPLUNK_HOME/etc/apps/myapp20
...
$SPLUNK_HOME/etc/apps/myappApple
$SPLUNK_HOME/etc/apps/myappBanana
$SPLUNK_HOME/etc/apps/myappZabaglione
...
$SPLUNK_HOME/etc/apps/myappapple
$SPLUNK_HOME/etc/apps/myappbanana
$SPLUNK_HOME/etc/apps/myappzabaglione
...

Lexicographical order sorts items based on the values used to encode the items in computer memory. In Splunk software, this is almost always UTF-8 encoding, which is a superset of ASCII.

Numbers are sorted before letters. Numbers are sorted based on the first digit. For example, the numbers 10, 9, 70, 100 are sorted lexicographically as 10, 100, 70, 9.
Uppercase letters are sorted before lowercase letters.
Symbols are not standard. Some symbols are sorted before numeric values. Other symbols are sorted before or after letters.
Note: When determining precedence in the app/user context, directories for the currently running app take priority over those for all other apps, independent of how they're named. Furthermore, other apps are only examined for exported settings.

#### Summary of directory precedence
Putting this all together, the order of directory priority, from highest to lowest, goes like this:

Global context:
$SPLUNK_HOME/etc/system/local/*

$SPLUNK_HOME/etc/apps/A/local/* ... $SPLUNK_HOME/etc/apps/z/local/*

$SPLUNK_HOME/etc/apps/A/default/* ... $SPLUNK_HOME/etc/apps/z/default/*

$SPLUNK_HOME/etc/system/default/*

App/user context:
$SPLUNK_HOME/etc/users/*

$SPLUNK_HOME/etc/apps/Current_running_app/local/*

$SPLUNK_HOME/etc/apps/Current_running_app/default/*

$SPLUNK_HOME/etc/apps/A/local/*, $SPLUNK_HOME/etc/apps/A/default/*, ... $SPLUNK_HOME/etc/apps/z/local/*, $SPLUNK_HOME/etc/apps/z/default/* (but see note below)

$SPLUNK_HOME/etc/system/local/*

$SPLUNK_HOME/etc/system/default/*

#### List of configuration files and their context
As mentioned, Splunk decides how to evaluate a configuration file based on the context that the file operates within, global or app/user. Generally speaking, files that affect data input, indexing, or deployment activities are global; files that affect search activities usually have a app/user context.

The props.conf and transforms.conf files can be evaluated in either a app/user or a global context, depending on whether Splunk is using them at index or search time.

Global configuration files
admon.conf
authentication.conf
authorize.conf
crawl.conf
deploymentclient.conf
distsearch.conf
indexes.conf
inputs.conf
outputs.conf
pdf_server.conf
procmonfilters.conf
props.conf -- global and app/user context
pubsub.conf
regmonfilters.conf
report_server.conf
restmap.conf
searchbnf.conf
segmenters.conf
server.conf
serverclass.conf
serverclass.seed.xml.conf
source-classifier.conf
sourcetypes.conf
sysmon.conf
tenants.conf
transforms.conf  -- global and app/user context
user-seed.conf -- special case: Must be located in /system/default
web.conf
wmi.conf

App/user configuration files
alert_actions.conf
app.conf
audit.conf
commands.conf
eventdiscoverer.conf
event_renderers.conf
eventtypes.conf
fields.conf
limits.conf
literals.conf
macros.conf
multikv.conf
props.conf -- global and app/user context
savedsearches.conf
tags.conf
times.conf
transactiontypes.conf
transforms.conf  -- global and app/user context
user-prefs.conf
workflow_actions.conf

#### Troubleshooting configuration precedence and other issues
Splunk's configuration file system supports many overlapping configuration files in many different locations. The price of this level of flexibility is that figuring out which value for which configuration option is being used in your Splunk installation can sometimes be quite complex. If you're looking for some tips on figuring out what configuration setting is being used in a given situation, read "Use btool to troubleshoot configurations" in the Troubleshooting Manual.

