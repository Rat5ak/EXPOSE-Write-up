CTF Challenge Write-Up: Expose
Challenge Information

    Challenge Name: Expose
    Category: Red Teaming
    Difficulty Level: Easy

Introduction

The "Expose" challenge is designed to test red teaming skills, focusing on the risks associated with exposing unnecessary services on a machine. Participants were required to start a Virtual Machine (VM) and use various tools provided in the AttackBox to identify vulnerabilities and capture flags.
Tools Used

    Nmap: Network scanning tool used for initial reconnaissance.
    FTP Client: Used for connecting to the FTP server.
    Gobuster: Utilized for directory enumeration.
    Web Browser: Used for exploring discovered web directories.
    Burp Suite: Used for capturing and analyzing web requests.
    sqlmap: Used for automated SQL injection testing.

Detailed Solution
Initial Analysis

The first step taken was to run a comprehensive Nmap scan:

sudo nmap -sV -O -p- -A -sC 10.10.47.35

This scan was intended to identify open ports, running services, the operating system, and other valuable information about the target machine.
Nmap Scan Results:

    Start Time: 01:32 EST
    Total Scan Duration: Approximately 534.80 seconds
    Target IP: 10.10.47.35
    Open Ports and Services:
        21/tcp: FTP service, vsftpd 2.0.8 or later, with anonymous FTP login allowed.
        22/tcp: SSH service, OpenSSH 8.2p1 Ubuntu 4ubuntu0.7.
        53/tcp: Domain service, ISC BIND 9.16.1 (Ubuntu Linux).
        1337/tcp: HTTP service, Apache httpd 2.4.41 (Ubuntu), with a title "EXPOSED".
        1883/tcp: MQTT protocol, mosquitto version 1.6.9.
    OS Detection: Inconclusive. Linux OS suspected based on service information.
    Network Distance: 4 hops

Methodology
Step 1: Initial Reconnaissance with Nmap

    Initiated to gather as much information as possible about the target system.

Step 2: Exploring the FTP Server

    Connected to the FTP server at port 21 as an anonymous user.
    Findings: No files were found in the FTP server upon the anonymous login.

Step 3: Directory Enumeration with Gobuster

    Ran Gobuster for directory enumeration on the HTTP service at port 1337.
    Gobuster Results: Discovered directories such as /.htpasswd, /.htaccess, /admin, /admin_101, /javascript, /phpmyadmin, and /server-status.

Step 4: Exploring Web Directories

    /admin Directory: Found an admin portal with no functionality on login attempts.
    /admin_101 Directory: Encountered a similar admin portal with a pre-filled username hacker@root.thm. Login attempts with this username resulted in an 'error' message, indicating its validity.

Step 5: SQL Injection Analysis with Burp Suite and sqlmap

    Captured the POST request on the /admin_101 login page.
    Analyzed using sqlmap, revealing vulnerabilities in the email parameter.
    Identified the back-end DBMS as MySQL version 5.6 or higher.
    Extracted data from the expose database, including user credentials and config table information.
    Cracked password easytohack from the config table hash.

Step 6: Accessing Restricted URLs

    /file1010111/index.php: Logged in using easytohack. Found a message suggesting further investigation into hidden elements or parameter fuzzing.
    /upload-cv00101011/index.php: Prompted for a password. The provided hint was "the name of a machine user starting with letter 'z'".

Step 7: Investigating Hidden Elements, Parameter Fuzzing, System File Access, and Exploiting File Upload Vulnerability

    Explored file upload features, discovering restrictions to PNG or JPG files.
    Successfully uploaded a modified PHP reverse shell named revshell.phpD.jpg using Burp Suite.
    Gained basic shell access as www-data user through a Netcat listener on port 4444.
    Accessed SSH credentials for the user zeamkish and successfully logged in via SSH.
    Retrieved the user flag: THM{USER_FLAG_1231_EXPOSE}.

Step 8: Privilege Escalation and Capturing the Root Flag

    Discovered that nano had the SUID bit set.
    Edited the /etc/shadow file to change the root password.
    Logged in as root using the new password and accessed the root directory.
    Captured the root flag: THM{ROOT_EXPOSED_1001}.

Challenges and Learnings

    Reconnaissance: Key in establishing the landscape of the challenge.
    Tool Mastery: Demonstrated the value of various tools in different stages of the challenge.
    Adaptability and Persistence: Essential in navigating through obstacles.
    Privilege Escalation Techniques: Critical in understanding Unix permissions and SUID binaries.

Flag

    User Flag: THM{USER_FLAG_1231_EXPOSE}
    Root Flag: THM{ROOT_EXPOSED_1001}

Conclusion

The "Expose" CTF challenge provided a comprehensive and educational experience in red teaming. It offered insights into network scanning, web exploitation, SQL injection, privilege escalation, and more. Successfully capturing both the user and root flags underscored a well-rounded understanding of various penetration testing methodologies and techniques.
