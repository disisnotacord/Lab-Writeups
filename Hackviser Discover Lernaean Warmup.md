# Hackviser Discover Lernaean Warmup

## Executive Summary
Default credentials and lack of brute force protection led to compromise of 1 user account

## Introduction
HTTP is used to access websites. SSH is used to remotely connect over an encrypted tunnel. Both are extremely common services, however, they can have major security flaws when configured incorrectly

## Objective
Find security weaknesses in the target machine

## Scope
The scope of this engagement is any service running on the target IP address given 

This is a white box engagement

Information given:

- Target IP address
- The ports running on the target machine are 2 digits
- Writeup Available in case we get stuck

## Methodology
nmap was used for reconnaissance, hydra was used to brute force a password

## Findings
We first ran an nmap scan on the target
![An nmap scan showing the target 172.20.7.188 with ports 22 (ssh) and 80 (http) open](images/Hackviser/Warmups/Discover_Lernaean/nmapscan.png)

Ports 22 and 80 were open. To confirm services running, we ran an nmap version scan
![An nmap scan showing port 22's service as OpenSSH and port 80's service as Apache](images/Hackviser/Warmups/Discover_Lernaean/nmapservicescan.png)

This confirms that the server is running ssh and http. To further enumerate, we used ffuf to scan directories
![A ffuf scan showing filemanager with response 301 (Permanently moved) and server-status with response 403 (forbidden)](images/Hackviser/Warmups/Discover_Lernaean/ffuf_dir_scan.png)

Going to filemanager, we see a login screen. We first try default credentials (user:12345)

Default Credentials were accepted. We can now access the directories.
![A page showing a linux filesystem](images/Hackviser/Warmups/Discover_Lernaean/user_12345_creds.png)

To enumerate further, we check /etc/passwd
![The file /etc/passwd. There is a user named rock](images/Hackviser/Warmups/Discover_Lernaean/etc_passwd.png)

This user seems to be a normal user account. We try to brute force the ssh login
![A hydra scan showing the user rock's ssh password as 7777777](images/Hackviser/Warmups/Discover_Lernaean/hydra_ssh.png)

With this password, we now login as the user rock. As per lab requirements, we check the bash history of the user.
![An image showing the history of the user](images/Hackviser/Warmups/Discover_Lernaean/bash_history_rock.png)

Severity assessment: 🟠 **High**

## Remediation
- Do not use default credentials
- Add rate limits and other forms of brute force protection
- Use proper file permissions and do not allow /etc/passwd to be read by other users
- Use different directory names

## Conclusion
A user account was compromised due to insufficient security protocols. Overall risk was 🟠 **High**
