---
tags: [Code]
title: Cisco_Python_Ping_Utility
created: '2020-01-30T20:16:16.091Z'
modified: '2020-01-30T20:37:41.061Z'
---

# Cisco Python Ping Utility
Created Tuesday 19 June 2018


Mass ping utility for CiscoLive NOC. Creates dashboards from fping utility. Leverages fping, InfluxDB and Grafana

Must have fping 4.0 installed. Must have InfluxDB installed. Must have Grafana installed.

Edit the devices.txt.sample file and save as devices.txt with your device IPs and hostnames. Edit the MassPing.py file variables section to reflect your environment configuration.

Code from <https://github.com/jasoncdavis/MassPing>

	#!/usr/local/bin/python3.6
	
	"""
	MassPing.py
	
	script loading a file with a list of devices to ping, then pings the devices, then puts
	the results into a webpage
	
	v1	2017-1217	jadavis Initial version
	v2  2018-0118	jadavis	Updated to tag influxdb data with hostname as supplied in devicelist;
							also removed HTML web page creation in lieu of using Grafana
	"""
	
	import shlex
	from subprocess import check_output, Popen, PIPE
	import re
	import requests
	import sys #needed for sys.exit
	import schedule
	import time
	import datetime
	
	
	## User defined vars
	devicelist = "devicelist.txt"
	influxserver = "localhost" #hostname or IP of InfluxDB server
	databasename = "CHANGEME"     #name of existing InfluxDB database
	##
	
	
	
	def get_fping_output(cmd):
		args = shlex.split(cmd)
		proc = Popen(args, stdout=PIPE, stderr=PIPE, encoding='utf8')
		out, err = proc.communicate()
		exitcode = proc.returncode
		return exitcode, out, err
	
	
	def load_devicefile():
		iplist = dict()
		with open(devicelist) as file:
			for line in file:
				line = line.strip() #preprocess line
				ipaddress, hostname, location, function = line.split()
				iplist[ipaddress] = [hostname,location,function]
		return iplist
	
	def getpingresults():
		iplist = dict(load_devicefile())
		cmd = "/usr/bin/fping -C 1 -A -q {}".format(" ".join(map(str, iplist.keys())))
		exitcode, out, results = get_fping_output(cmd)
		
		pingresults = []
		for aline in results.split("\n"):
			#print('Working on line: {0}'.format(aline))
			if aline:
				m = re.match(r"(\S+)\s+:\s(\S+)", aline)
				ipaddress = m.group(1)
				rtt = m.group(2)
				if rtt == '-':
					iplist[ipaddress] += (float(9999),)
				elif float(rtt) > warningratio:
					iplist[ipaddress] += (float(rtt),)
				else:
					iplist[ipaddress] += (float(rtt),)
		
		#print(iplist)
		return iplist
	
	def createtabledata():
		iplist = getpingresults()
		influxdata = []
		for key, values in iplist.items():
			#print(key, values)
			#print(values[0], values[1], values[2], values[3])
			# InfluxDB line protocol template...
			# ping,host=<host>,hostname=<hostname>,location=<location> rtt=<value>
			influxentry = "ping,host=" + key + ",hostname=" + values[0] + ",location=" + values[1] + ",function=" + values[2] + " rtt=" + str(values[3])
			influxdata.append(influxentry)
			
		influxdata = '\n'.join(influxdata)
		#print(influxdata)
		return influxdata
		
	
	def write2influx():
		influxdata = createtabledata()
		url = "http://localhost:8086/write"
		params = {"db":databasename}
		headers = {
	    	'Content-Type': "application/x-www-form-urlencoded",
	    	}
		
		response = requests.request("POST", url, data=influxdata, headers=headers, params=params)
		#print(response.text)
		
	def dowork():
		timenow = datetime.datetime.fromtimestamp(time.time()).strftime('%Y-%m-%d %H:%M:%S')
		start = datetime.datetime.now()
		print("Started...", timenow)
		write2influx()
		end = datetime.datetime.now()
		elapsed = end - start
		#print(elapsed.seconds,":",elapsed.microseconds) 
	
		#timenow = datetime.datetime.fromtimestamp(time.time()).strftime('%Y-%m-%d %H:%M:%S')
		print("   Done... {0}.{1}sec".format(elapsed.seconds, elapsed.microseconds))
		
		
	schedule.every(10).seconds.do(dowork)
	
	while 1:
		schedule.run_pending()
		time.sleep(1)	

