# SynologyRemoteIPUpdate
Remotely update your IP address to ensure VPN is active

## Prerequistes 
lftp installed - Example here: https://ingmarverheij.com/how-to-install-ipkg-on-synology-nas-ds212/


## Create a separate file for credentials:
Login to your Synology NAS and create the following file credentials.txt

```
vi credentials.txt
```
Copy/Paste the following and replace: your_username, your_password, and host.com with your details
```
username="your_username"
password="your_password"
host="host.com"
```
To write and exit enter:

```
:wq
```

This write/quits the change to your file

## Modify your script:
Create the script and paste the following below. The script will reference your credentials.txt so they are not exposed in plaintext

```
#!/bin/bash

# Source the credentials from a separate file
source /path/to/credentials.txt

myip=$(curl ifconfig.me)
date
now=$(date)

lftp -c "open -u $username,$password $host; put -O $host/ip/ /volume1/homes/$username/index.html"
echo "<html> "$myip" <br>" > index.html
echo "$now" >> index.html
```

## Set appropriate permissions:
Ensure that only authorized users have read access to the credentials.txt file:

```
chmod 400 /path/to/credentials.txt
```

## Create directory on remote host.com
Make sure you create a subdirectory of /ip off the root directory on host.com 
This is needed so the script can be uploaded to host.com/ip - Otherwise the upload will fail

Add credentials.txt to your .gitignore file:
If you're using version control (e.g., Git), make sure to add credentials.txt to your .gitignore file to avoid accidentally sharing sensitive information.

This way, your sensitive information is stored separately, reducing the risk of exposure. Also, make sure to secure the file system permissions to restrict access to the credentials file.

## Browse to https://host.com/ip and see the IP address your NAS is showing
