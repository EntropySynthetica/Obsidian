---
tags: [Splunk]
title: Handy_Splunk_Commands
created: '2020-01-30T20:16:16.287Z'
modified: '2020-01-30T20:42:15.053Z'
---

# Handy Splunk Commands
Created Tuesday 06 September 2016

Handy Splunk Commands

REMEMBER - on Windows Powershell .\splunk <command> so a status would be .\splunk status 

If you are having issues with Splunk Forwarder permissions issues, you might need to start Splunk as su 
as ROOT 
su -l splunk -c “/opt/splunk/bin/splunk start" 

To set a user at reboot time  
splunk enable boot-start –user <user>
or
splunk enable boot-start –user splunker

Forwarder Commands
splunk add forward-server ###.##.###.###:9997 -method autobalance
splunk list forward-server 

Indexer Commands 
splunk display listen 
IF it says “Receiving is disabled" 
Enable the port in “Forwarding and Receiving” on the UI  
OR 
vi /opt/splunk/etc/apps/search/local/inputs.conf  
[<splunktcp://9997>]
connection_host = ip
OR
do the following on the Indexer CLI
splunk enable listen <port> -auth <username>:<password>


Deployment Server Commands 
(on the deployment clients) - splunk show deploy-poll 
(on the deployment clients) - splunk set deploy-poll <deployment server ip>:8089 
on deployment server - splunk reload deploy-server 

To enable deployment server 
splunk set deploy-poll deploymentserver.splunk.mycompany.com:8089 

To verify 
splunk show deploy-poll 

To disable 
splunk disable deploy-client 

One Shot Command 
splunk add oneshot -source edifecs-sample.log -sourcetype edifecs -index onboarding

To clean data after the One-shot command
stop splunk
/opt/splunk/bin/splunk clean eventdata -index <index name>

props.conf basic 
[name] 
TIME_PREFIX 
TIME_FORMAT 
MAX_TIMESTAMP_LOOKAHEAD 
SHOULD_LINEMERGE 
LINE_BREAKER 
TRUNCATE 

DATETIME_CONFIG - use for datetime.xml  and if you have no timestamp in the data DATETIME_CONFIG = CURRENT 

To add a user named joshua to your Splunk Instance 
splunk add user joshua -role admin -password chess

Cluster Specific commands
splunk apply cluster-bundle –skip-validation

splunk show cluster-bundle-status

splunk list cluster-peers

splunk show cluster-status


To clean up the dispatched searches (10 hours old and/or more)
./splunk clean-dispatch /opt/splunk/old-dispatch-jobs/ -10h


To see what files the Search Head is monitoring... 
./splunk list monitor 

To set a license from a License Server (to a slave indexer) 
In etc/system/local 
[license]
master_uri = <https://splunkdeploy01.kinneygroup.local:8089>
active_group = Forwarder
supportSSLV3Only = true

or the CLI approach
./splunk edit licenser-localslave -master_uri '<https://master:port>'


For our setup
./splunk edit licenser-localslave -master_uri <https://splunkdeploy01.kinneygroup.local:8089>



To reset the log level (to get rid of ScriptedAuth,ENABLED' failed: reason='feature='ScriptedAuth' is invalid)  message 

splunk set log-level LMDirective -level ERROR

To check the certificates on Splunk 6.2 or lower
/opt/splunk/bin/splunk cmd openssl x509 -in /opt/splunk/etc/auth/cacert.pem -text -noout |more


add to your .bashrc or .bash_profile(MAC)
spec() {
   less $SPLUNK_HOME/etc/system/README/${1}.conf.spec
}
alias spec=spec

export SPLUNK_HOME=/opt/my-demo01/splunk
export PATH=$PATH:$SPLUNK_HOME/bin

alias apps="cd $SPLUNK_HOME/etc/apps"
alias dapps="cd $SPLUNK_HOME/etc/deployment-apps"
alias mapps="cd $SPLUNK_HOME/etc/master-apps"
alias slapps="cd $SPLUNK_HOME/etc/slave-apps"
alias esl="cd $SPLUNK_HOME/etc/system/local"
alias logs="cd $SPLUNK_HOME/var/log/splunk"
alias slog="less $SPLUNK_HOME/var/log/splunk/splunkd.log"
alias sc="vi $SPLUNK_HOME/etc/system/local/serverclass.conf"
alias rd="$SPLUNK_HOME/bin/splunk reload deploy-server"


then run  source ~/.bashrc 

