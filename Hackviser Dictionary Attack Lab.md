# Hackviser Dictionary Attack Lab - Weak Credentials

## Executive Summary
The application permits unlimited password guessing without rate limits, allowing an attacker to discover a valid password through a dictionary attack.

## Introduction
A dictionary attack is a type of brute-force attack where an attacker attempts to authenticate using passwords from a wordlist.

Target: https://assuring-forearm.europe1.hackviser.space
Engagement: Hackviser "Dictionary Attack" Lab

## Objective
Achieve a brute-force login to the admin account

## Scope
The scope of the engagement is only the target website. This is a white-box engagement

Information given: 

This lab contains a login form with weak login credentials.

To complete the lab, find the password of "admin" user with a dictionary attack.

## Methodology
Burp Suite was used to find the parameter names, and ffuf was used to fuzz the password parameter

## Findings
We first opened the website, configured the Burp Suite proxy, and intercepted the login request

![An image showing Burp Suite open, There are 2 requests, with the top one highlighted. The Highlighted URL is "https://assuring-forearm.europe1.hackviser.space". The method is POST.](images/Hackviser/Labs/Dictionary%20Attack/Burpsuite%20login%20request.png)

After this, we sent the request to Burp Intruder, to let all other traffic flow. 

![An image with Burp Suite intruder open. The image shows that the parameter used for the username is "username", and the parameter used for the password is "password"](images/Hackviser/Labs/Dictionary%20Attack/Burp%20Intruder.png)

To avoid Burp Suite community edition's slow intruder speed, we used the command line tool "ffuf".

We also added the header "Content-Type: application/x-www-form-urlencoded" so the application treated the content as such

![An image with a terminal open. The command shown in the image is "ffuf -u https://assuring-forearm.europe1.hackviser.space/login.php -X POST -d "username=admin&password=FUZZ" -H "Content-Type: application/x-www-form-urlencoded" -w /usr/share/wordlists/kali-wordlists/rockyou.txt"](images/Hackviser/Labs/Dictionary%20Attack/ffuf.png)

In 129ms, ffuf came back with a redirect (code 302). This is common for login pages, as they will redirect to a homepage or profile page. The password has been changed post-assessment

![An image with the output of the ffuf command shown. It shows a redirect using the password "superman". The user quit out after seeing the redirect, as indicated by the warning "[WARN] Caught keyboard interrupt (Ctrl-C)
"](images/Hackviser/Labs/Dictionary%20Attack/ffuf_results.png)

Using the password, we get a successful login.

![An image showing the profile of "Hallows Effie". Personal details are also shown](images/Hackviser/Labs/Dictionary%20Attack/login.png)

Severity assessment: 🔴 **Critical.** An admin account was compromised, along with many of his personal details. 

## Remediation
- Implement MFA for administrator accounts
- Minimize PII exposed through user profiles
- Enforce rate limits on password attempts
- Enforce strong password policies

## Conclusion
An administrator account, along with their personal details, were compromised. Overall risk was 🔴 **Critical.**

