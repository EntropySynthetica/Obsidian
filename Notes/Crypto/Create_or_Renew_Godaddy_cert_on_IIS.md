---
tags: [Windows]
title: Create_or_Renew_Godaddy_cert_on_IIS
created: '2020-01-30T20:16:16.519Z'
modified: '2020-01-30T20:38:34.566Z'
---

# Create or Renew Godaddy cert on IIS
Created Thursday 15 February 2018

IIS requries a .pfx file which is a password protected file containing the private key and cert.  

Go Daddy will supply two files, xxxxxxxx.crt where xxx is random letters and numbers, and gd_bundle-g2-g1.crt which is the intermediate cert.  
We also need the private key which was created when the inital Certificate signing request was done.  

The following command will create the PFX file, 

openssl pkcs12 -export -out arvig.com.pfx -inkey private.key -in xxxxxxxxxxxx.crt

You will be asked to create a password when creating the PFX file. 

Copy the PFX file to the IIS server and open IIS Manager.  Go to the Server Certificates window and select import and add it to the personal cert store. 

Find the path to the PFX file and enter the password you created earlier.  

Go to your site and edit the bindings.  Select your SSL cert you just added.  


