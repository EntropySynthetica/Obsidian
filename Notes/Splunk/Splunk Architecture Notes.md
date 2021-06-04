---
tags: [Splunk]
title: Splunk Architecture Notes
created: '2020-01-30T20:16:16.211Z'
modified: '2020-07-19T05:14:34.098Z'
---

# Splunk Architecture Notes
Created Tuesday 06 June 2017

Volume,
Velocity
Variety

Phase 1 Collect and Index Data
Phase 2 Search and Investigate
Phase 3 Add Knowldge

a. data interpitation, fields, field extractions, macros
b. data calssification, event types, transactions
c. data enrichment, lookups, reports, transforms views
d. data normalization, aliases, tags
e. data models. 

Phase 4 Monitor and Alert
Phase 5. Report and analize. 

Data Inputs,

Files and directories
Network Events
Windows Sources
Scripted Inputs
Modular Inputs

Indexer Files,
Raw Data, original data in compressed form
Index files, search optmized fiels that point to the raw data, plus metadata.

Splunk clusters do data replication
Benefits of replication
-data availiability: an indexer is always availbleto handle incoming data.
-data fidelity: never lose data
-data recovery: your system con tolerate a failed indexer without losing your data.

Splunk config information
- system settings
- authentication and authorization information
- index mappings and settings
- deployment and cluster configurations
- knowledge objects and saved searches


Apps -  contain dashboards

add-ons data feed, modular input, scripts or other mechanisim for data collection. add ons are smaller, reusable but don't have a UI

Dashboards are simple XML

Highlights of simple XML extensions
-access splunkjs stack
-add custom css stylesheets and javascript files at both app and page level
-take advantage of tokens and search strings to style text elements
-create custom visulaizations to modle data
-table cell renders support provide table sytle and behavior customization

RestAPI 
-Configure and manage a splunk isntance
-create and run searches
-create applications that interact with splunk

Get to request info
Post to Update
Delete to remove an entity.  

API sends back an http code and a response in XML, JSON, or CSV. XML default
Rest APi divided into endpoitns or URIs served by Splunk server. Each of splunks resources (apps, users, searches, jobs, indexes, inputs and others) has a corresponding endpoint.

Default Port 8089

Access API directly or with Splunk SDK in common programing languages.
SDK allows you to, 
-Intergate with 3rd party reporting
-log directly into Splunk
- Intergate search results into another service or 3rd party app
-extract data for archiving
-extend visulazation options

Splunk SDKs are wrappers around the rest API that do a lot of work such as
-Authenticating
-Managing Namespaces
-Simplifiying access to rest endpoints
-building the correct URL for an endpoint





