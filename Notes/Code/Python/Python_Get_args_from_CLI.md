---
tags: [Code]
title: Python_Get_args_from_CLI
created: '2020-01-30T20:16:16.135Z'
modified: '2020-01-30T20:47:45.615Z'
---

# Python Get args from CLI
Created Friday 03 November 2017

	#!/usr/bin/env python3
	
	import argparse
	
	parser = argparse.ArgumentParser(allow_abbrev=False, description='Send a Give Badge request to RecognizeAPI.')
	
	parser.add_argument('--email', required=True, help='Email address of rep')
	parser.add_argument('--badgeid', required=True, help='Numeric BadgeID of the badge you want to give the rep')
	parser.add_argument('--message', nargs='?', required=False, default='', help='Optional message to include with the badge')
	args = parser.parse_args()
	
	
	print(args)
	print(args.email)





