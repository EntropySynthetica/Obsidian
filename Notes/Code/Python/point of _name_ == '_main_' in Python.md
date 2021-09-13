---
tags: [Code]
title: point_of___name___==_'__main__'_in_Python
created: '2020-01-30T20:16:16.115Z'
modified: '2020-01-30T20:47:04.142Z'
---

# point of   name   == '  main  ' in Python
Created Monday 16 December 2019

Suppose you create the following python file called greetings.py:

# contents of greetings.py

	def greet(name):
		print(f"Hello {name}!")
	
	print("This is an example:")
	greet("Alice")

Now, another user wants to use your function greet by including this import in his file new_file.py:

# contents of new_file.py

	import greetings

He runs new_file.py and sees the output:

This is an example:
Hello Alice!
The user is confused where this output comes from. What he did not know is that all lines in greetings.py are executed when he imported greetings including the last two lines that serve as an example.

You don't want the example to be printed when somebody imports greetings. This can be accomplished by adding if __name__ == '__main__':

# contents of greetings.py

	def greet(name):
		print(f"Hello {name}!")
	
	if name == 'main':
		print("This is an example: ")
		greet("Alice")


The last two lines are now only executed when you run greetings.py itself, but they are not executed anymore when you import greetings.

The reason why this works is that __name__ has the value greetings when somebody imports your file. However, if you execute your file directly, then __name__ has the value __main__, and the last two lines will be executed.

