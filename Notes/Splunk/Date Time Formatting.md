
## Time
|   |   |
|---|---|
|%H|24 hour (leading zeros) (00 to 23)|
|%I|12 hour (leading zeros) (01 to 12)|
|%M|Minute (00 to 59)|
|%S|Second (00 to 61)|
|%N|subseconds with width (%3N = millisecs, %6N = microsecs, %9N = nanosecs)|
|%p|AM or PM|
|%Z|Time zone (EST)|
|%z|Time zone offset from UTC, in hour and minute: +hhmm or -hhmm. (-0500 for EST)|
|%s|Seconds since 1/1/1970 (1308677092)|

## Days
|   |   |
|---|---|
|%d|Day of month (leading zeros) (01 to 31)|
|%j|Day of year (001 to 366)|
|%w|Weekday (0 to 6)|
|%a|Abbreviated weekday (Sun)|
|%A|Weekday (Sunday)|

## Months 
|   |   |
|---|---|
|%b|Abbreviated month name (Jan)|
|%B|Month name (January)|
|%m|Month number (01 to 12)|

## Years
|   |   |
|---|---|
|%y|Year without century (00 to 99)|
|%Y|Year (2021)|

## Examples
|   |   |
|---|---|
|%Y-%m-%d|2021-12-31|
|%y-%m-%d|21-12-31|
|%b %d, %Y|Jan 24, 2021|
|%B %d, %Y|January 24, 2021|
|q\|%d %b '%y= %Y-%m-%d|q\|25 Feb '21 = 2021-02-25\||