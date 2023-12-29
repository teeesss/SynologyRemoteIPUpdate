# SynologyRemoteIPUpdate
Remotely update your IP address to ensure VPN is active

## Prerequistes / Assumptions 
- lftp installed - Example here: https://ingmarverheij.com/how-to-install-ipkg-on-synology-nas-ds212/
- Directory created on remote host.com - For this example it would be /ip (Reference end of this document for more info)
- Script and credentials will be in the /volume1/homes/$localusername < The user running the script. Set in ip_credentials.txt

## Create a separate file for credentials:
Login to your Synology NAS and create the following file ip_credentials.txt

```
vi ip_credentials.txt
```
Copy/Paste the following and replace: your_username, your_password, host.com, localusername and with your details
Only update base_path if your path is differentthan /volume1/homes - Otherwise don't edit

```
username="your_username"                     ### Set your ftp username to host.com
password="your_password"                     ### Set your ftp password for host.com
host="host.com"                              ### Replace with your Web URL for hosting index.html
localusername=local_username"                ### NAS username and were the crentialss/script will be located
base_path="/volume1/homes/$localusername/"   ### Don't modify unless you know what you are doing
```

## Write and Exit File
Type :wq and then hit *Enter*

```
:wq
```

This write/quits the change to your file

## Modify your script:
Create the script and paste the following below. The script will reference your ip_credentials.txt so they are not exposed in plaintext

```
vi ip_script.sh
````

## Copy/Paste the following

```
#!/bin/bash

# Source the credentials and base path from a separate file
source_path="/var/services/homes/jonesy"  ### Example /var/services/homes/username
source $source_path/ip_credentials.txt    ### Source Path set above
filename="index.html"                     ### Change to another filename as needed

myip=$(curl ifconfig.me)
date
now=$(date)

# Append content to index.html
echo "<html> $myip <br>" > $filename
echo "$now" >> $filename

# Check if index.html exists on the remote server
lftp -c "open -u $username,$password $host"

if [ $? -ne 0 ]; then
    # If it doesn't exist, create a blank index.html remotely
    lftp -c "open -u $username,$password $host; put -O $host/ip/$filename /dev/null"
fi

# Upload the index.html file
lftp -c "open -u $username,$password $host; put -O $host/ip/ $filename"
```

## Write and Exit  File
Type :wq and then hit *Enter*

```
:wq
```

## Set appropriate permissions:
Ensure that only authorized users have read access to the ip_ip_credentials.txt file:
Set it to Read for root and the user you are logged in as

```
chmod 440 /path/to/ip_ip_credentials.txt
```

## Make script executable

```
chmod +x ip_script.sh
```

## Create directory on remote host.com
Make sure you create a subdirectory of /ip off the root directory on host.com 
This is needed so the script can be uploaded to host.com/ip - Otherwise the upload will fail

Add ip_credentials.txt to your .gitignore file:
If you're using version control (e.g., Git), make sure to add ip_credentials.txt to your .gitignore file to avoid accidentally sharing sensitive information.

This way, your sensitive information is stored separately, reducing the risk of exposure. Also, make sure to secure the file system permissions to restrict access to the credentials file.

## Browse to https://host.com/ip and see the IP address your NAS is showing

## Example output:
### 205.101.122.111
### Fri Dec 29 15:45:03 CST 2023
