---
tags: [Linux]
title: Delete_Files_Older_than
created: '2020-01-30T20:16:15.483Z'
modified: '2020-02-02T17:49:53.251Z'
---

# Delete Files Older than

```bash
find /path/to/files* -mtime +5 -exec rm {} \;
```

Note that there are spaces between rm, {}, and \;

Explanation

The first argument is the path to the files. This can be a path, a directory, or a wildcard as in the example above. I would recommend using the full path, and make sure that you run the command without the exec rm to make sure you are getting the right results.

The second argument, -mtime, is used to specify the number of days old that the file is. If you enter +5, it will find files older than 5 days.

The third argument, -exec, allows you to pass in a command such as rm. The {} \; at the end is required to end the command.



