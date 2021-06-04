---
tags: [Linux]
title: Useful_regex_searches
created: '2020-01-30T20:16:16.167Z'
modified: '2020-01-30T20:55:29.260Z'
---

# Useful regex searches
Created Friday 27 October 2017


* ===== Regex Basics =====



Control Characters (Anchor a search to a certain point in the string)
^ Start of a line
$ End of a line

Character Types
\s White Space
\S Not White Space
\d Numeric digit
\D not Numeric Digit
\w Word Character (a letter, number or underscore _)
\W Not a word character

Operators
 * Match zero or More
 + Match one or more
 ? Match Zero or One

Special Character
| Alternative (Or)

Protection character
\ Escape the next character

Include Characters
[...]  Example [a-z]+ matches one or more every letter a thru z

Exclude Characters
[^...] Example [^a-z]+ matches one or more of anything until it hits any letter a to z. 

Repetition
{#} Number of Repetitions, example match 3 letter a  a{3}
{#,#} Range of Repetitions, example, match 1 to 3 digits \d{1,3}

Logical Grouping
( <regex> )  Enclose regex search in parentheses. 
 
Named capture group
(?p<VarName>regex)  Whatever regex matches will be stored to variable named "VarName"

----

Using Regex with Grep
---------------------


grep -E -o "regex" filename

-E tells grep to interpet search pattern as regex
-o tells grep to only output what we match.  

Without the -o grep will highlight the match but show the whole line.  

---

Common examples
---------------



* Email Address

\b[a-zA-Z0-9.-]+@[a-zA-Z0-9.-]+\.[a-zA-Z0-9.-]+\b


* ip address 

\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b


* mac address

\b(?<mac_address>(\w{2}:){5}\w{2})\b


* Contents of a quoted string

\"(?p<contents>[^"]+)\"


* Match multiple lines

(?m)


* Greedy vs Lazy

>Greedy, Grab data until the last instance of a match
>Lazy, Grab until the first instance of a match.
<.+?> will match <12345> , while
<.+> will match both <12345> and <12345><67890>
To use Greedy place a ? after a * or +


