---
tags: [Splunk]
title: Run_Splunk_in_Docker
created: '2020-01-30T20:16:16.331Z'
modified: '2020-01-30T20:51:56.055Z'
---

# Run Splunk in Docker
Created Friday 21 December 2018

<https://hub.docker.com/r/splunk/splunk>
<https://github.com/splunk/splunk-ansible/tree/develop/roles/splunk_common/tasks>


docker pull splunk/splunk:latest

docker run -d -p 8000:8000 -e 'SPLUNK_START_ARGS=--accept-license' -e 'SPLUNK_PASSWORD=<password>' --hostname SplunkSH --name SplunkSH splunk/splunk:latest

To connect into the Container:
docker exec -t -i SplunkSH /bin/bash



Options to create the container with from the image. 

Environment Variables:
  * SPLUNK_USER - user under which to run Splunk (default: splunk)
  * SPLUNK_GROUP - group under which to run Splunk (default: splunk)
  * SPLUNK_HOME - home directory where Splunk gets installed (default: /opt/splunk)
  * SPLUNK_START_ARGS - arguments to pass into the Splunk start command; you must include '--accept-license' to start Splunk (default: none)
  * SPLUNK_ROLE - the role of this Splunk instance (default: splunk_standalone)
  Acceptable values:
- splunk_standalone
- splunk_search_head
- splunk_indexer
- splunk_deployer
- splunk_license_master
- splunk_cluster_master
- splunk_heavy_forwarder
  * SPLUNK_LICENSE_URI - URI or local file path (absolute path in the container) to a Splunk license
  * SPLUNK_STANDALONE_URL, SPLUNK_INDEXER_URL, ... - comma-separated list of resolvable aliases to properly bring-up a distributed environment.
 This is optional for standalones, but required for multi-node Splunk deployments.
  * SPLUNK_BUILD_URL - URL to a Splunk build which will be installed (instead of the image's default build)
  * SPLUNK_APPS_URL - comma-separated list of URLs to Splunk apps which will be downloaded and installed

Examples:
  * docker run -it -p 8000:8000 splunk/splunk start
  * docker run -it -e SPLUNK_START_ARGS=--accept-license -p 8000:8000 -p 8089:8089 splunk/splunk start
  * docker run -it -e SPLUNK_START_ARGS=--accept-license -e SPLUNK_LICENSE_URI=<http://example.com/splunk.lic> -p 8000:8000 splunk/splunk start
  * docker run -it -e SPLUNK_START_ARGS=--accept-license -e SPLUNK_INDEXER_URL=idx1,idx2 -e SPLUNK_SEARCH_HEAD_URL=sh1,sh2 -e SPLUNK_ROLE=splunk_search_head --hostname sh1 --network splunknet --network-alias sh1 -e SPLUNK_PASSWORD=helloworld -e SPLUNK_LICENSE_URI=<http://example.com/splunk.lic> splunk/splunk start

