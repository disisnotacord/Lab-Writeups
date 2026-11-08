# Hackviser Bee Warmup

## Executive Summary
An SQL injection and file upload vulnerability revealed root credentials

## Introduction
SQL is a database query language which helps users create, update, maintain, and search databases. When misconfigured, it can allow bypassing logins

File uploads are common for things such as profile pictures. If they go unverified, it can lead to Remote code execution (RCE)

## Objective
Find a way to exploit the SQLi vulnerability and File upload vulnerability on the target machine 

## Scope
The scope of this engagement is the target machine. This is a white box engagement

Information given:
- IP address: 172.20.6.163
- Vulnerability types
- Writeup available

## Methodology
nmap was used for reconnaissance, a php file with the extension phtml was used to achieve RCE 

## Findings
We first used nmap to gain information on the target

![An nmap scan showing ports 80 and 3306 open](images/Hackviser/Warmups/Bee/nmapscan.png)

We found that ports 80 (http) and 3306 (mysql) are running

We further enumerate the http service by going to the website

![A website showing InnnovifyAI. There is a login button near the top right](images/Hackviser/Warmups/Bee/InnovifyAI.png)

We see that there is a login button. We attempt to go to the login page

![A DNS error not being able to find the requested page](images/Hackviser/Warmups/Bee/failed_dashboard.png)

The request failed because it redirected to a new domain. To get around this, we put the same IP address we scanned with nmap in our /etc/hosts file

![The /etc/hosts file shown through nano](images/Hackviser/Warmups/Bee/nanoecthosts.png)

After that we retry the request

The request was successful. We are met with a login screen. We try to enumerate the password field, but it never returns an SQL Error.  

We try to do the same with the email field, but it only accepts an email address.

To get around this, we change the html attribute requiring an email address. We remove the following line of code:

type="email"

![An image showing inspect element on a login screen](images/Hackviser/Warmups/Bee/changehtmlattribute.png)

After that, we try to enumerate with SQL.

![An image showing an SQL error](images/Hackviser/Warmups/Bee/sqlerror.png)

We get a proper SQL error. Trying the SQL injection, we get a successful login

![The website showing a dashboard](images/Hackviser/Warmups/Bee/dashboard.png)

Severity assessment: 🟠 **High**

To enumerate further, we go into the user settings page

![The settings panel for the user admin](images/Hackviser/Warmups/Bee/customers.png)

There's no password shown, but there is a profile picture upload. We try uploading a preset php file with GIF89a; (The magic header for GIF files)

The upload is successful, and we achieve RCE

![An image showing successful RCE POC with the command ls](images/Hackviser/Warmups/Bee/fileuploadvuln.png)

Severity assessment: 🔴 **Critical** 

Enumerating further shows a file named db_connect.php

We attempt to cat the file

![An attempted cat of a file showing an error](images/Hackviser/Warmups/Bee/fileneeded.png)

It only shows a few lines. To get around this, we view the source of the page

![A view-source: page showing hard coded root credentials](images/Hackviser/Warmups/Bee/source.png)

Severity assessment: 🔴 **Critical**


## Remediation
- Fix the SQL injection vulnerability using input santiziation
- Fix the file upload vulnerability by validating what files are allowed to be uploaded
- Change all leaked credentials

## Conclusion
The misconfiguration of SQL led to account compromise, the missing input validation for uploading files allowed RCE, which led to compromise of root credentials. Overall risk was 🔴 **Critical** 
