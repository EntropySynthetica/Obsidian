|   |   |   |   |
|---|---|---|---|
|**Regular Expressions (Regexes)**|   |   |   |
|Regular Expressions are useful in multiple areas: search commands regex and rex; eval functions match() and replace(); and in field extraction.|   |   |   |
|**Regex**|**Note**|**Example**|**Explanation**|
|**\s**|white space|\d\s\d|digit space digit|
|**\S**|not white space|\d\S\d|digit nonwhitespace digit|
|**\d**|digit|\d\d\d-\d\d-<br><br>\d\d\d\d|SSN|
|**\D**|not digit|\D\D\D|three non-digits|
|**\w**|word character (letter, number, or _)|\w\w\w|three word chars|
|**\W**|not a word character|\W\W\W|three non-word chars|
|**\[...\]**|any included character|\[a-z0-9#]|any char that is a thru z, 0 thru 9, or #|
|**\[^...\]**|no included character|\[^xyz]|any char but x, y, or z|
|*****|zero or more|\w*|zero or more words chars|
|**+**|one or more|\d+|integer|
|**?**|zero or one|\d\d\d-?\d\d-<br><br>?\d\d\d\d|SSN with dashes being optional|
|**\|**|or|\w\|\d|word or digit character|
|**(?P\<var>**<br><br>**...)**|named extraction|(?P\<ssn>\d\d\d-<br><br>\d\d-\d\d\d\d)|pull out a SSN and assign to 'ssn' field|
|**(?: ...**<br><br>**)**|logical or atomic grouping|(?:[a-zA-Z]\|\d)|alphabetic character OR a digit|
|**^**|start of line|^\d+|line begins with at least one digit|
|**$**|end of line|\d+$|line ends with at least one digit|
|**{...}**|number of repetitions|\d{3,5}|between 3-5 digits|
|**\\**|escape|\[|escape the \[ character|