# Network Analysis

### Time Thieves
At least two users on the network have been wasting time on YouTube. Usually, IT wouldn't pay much mind to this behavior, but it seems these people have created their own web server on the corporate network. So far, Security knows the following about these time thieves:

- They have set up an Active Directory network.
- They are constantly watching videos on YouTube.
- Their IP addresses are somewhere in the range 10.6.12.0/24.

You must inspect your traffic capture to answer the following questions:

1. **What is the domain name of the users' custom site?**

The domain name is **Frank-n-Ted-DC.frank-n-ted.com**.

Filter: `ip.addr==10.6.12.0/24`

Results Screenshot:

![Domain name](/Images/Day%203/Frank%20n%20Ted%20web%20domain%20name.JPG "Domain name")

2. **What is the IP address of the Domain Controller (DC) of the AD network?**

IP address is **10.6.12.12** (Frank-n-Ted-DC.frank-n-ted.com)

Filter: `ip.addr==10.6.12.0/24`

Results Screenshot of Protocol Info:

![IP address of domain controller](/Images/Day%203/frank%20n%20ted%20ip%20address.JPG "IP address of domain controller")

3. **What is the name of the malware downloaded to the 10.6.12.203 machine? Once you have found the file, export it to your Kali machine's desktop.**

Malware file is **june11.dll**.

Filter: `ip.addr==10.16.12.203 and http.request.method==GET`

Export: File > Export Objects > HTTP...

Results Screenshot:

![june11.dll malware](/Images/Day%203/malware%20packet.JPG "june11.dll malware")

4. **Upload the file to VirusTotal.com. What kind of malware is this classified as?**
This type of malware is classified as a **Trojan**.

VirusTotal Analysis Screenshot:

![Virus Total june11.dll scan results](/Images/Day%203/june11%20malware%20on%20virustotal.JPG "Virus Total june11.dll scan results")

### Vulnerable Windows Machines
The Security team received reports of an infected Windows host on the network. They know the following:

- Machines in the network live in the range 172.16.4.0/24.
- The domain mind-hammer.net is associated with the infected computer.
- The DC for this network lives at 172.16.4.4 and is named Mind-Hammer-DC.
- The network has standard gateway and broadcast addresses.

Inspect your traffic to answer the following questions:

1. **Find the following information about the infected Windows machine:**
    - Host name: **ROTTERDAM-PC**
    - IP address: **172.16.4.205**
    - MAC address: **00:59:07:b0:63:a4**

Filter: `ip.src==172.16.4.4 and kerberos.CNameString`

Results Screenshot:

![Pcap infected Windows machine](/Images/infected-windows-machine.png "Pcap infected Windows machine")

2. **What is the username of the Windows user whose computer is infected?**
The username is **matthijs.devries**.

Filter: `ip.src==172.16.4.205 and kerberos.CNameString`

Results Screenshot:

![Infected windows machine](/Images/Day%203/infected%20windows%20ip%20search.JPG "Infected windows machine")

![Username](/Images/Day%203/Windows-Username.JPG "Username")

3. **What are the IP addresses used in the actual infection traffic?**
The IP Addresses are: 172.16.4.205, 185.243.115.84 and 166.62.111.64

By using the original filter, I went to Statistics > Conversations and filtered by the packets from high to low. Doing this, I was able to determine the above IP addresses were the most affected.

Filter: `ip.addr==172.16.4.205 and ip.addr==185.243.115.84`

Results screenshot:

![Infected traffic](/Images/Day%203/infected-traffic.JPG "Infected traffic")


### Illegal Downloads
IT was informed that some users are torrenting on the network. The Security team does not forbid the use of torrents for legitimate purposes, such as downloading operating systems. However, they have a strict policy against copyright infringement.

IT shared the following about the torrent activity:
- The machines using torrents live in the range 10.0.0.0/24 and are clients of an AD domain.
- The DC of this domain lives at 10.0.0.2 and is named DogOfTheYear-DC.
- The DC is associated with the domain dogoftheyear.net.

Your task is to isolate torrent traffic and answer the following questions:

1. **Find the following information about the machine with IP address 10.0.0.201:**
- MAC address: **00:16:17:18:66:c8**
- Windows username: **elmer.blanco**
- Host Name (OS version): **BLANCO-DESKTOP**

Filter: `ip.src==10.0.0.201 and kerberos.CNameString`

Results Screenshot:

![IP 10.0.0.201 (elmer.blanco machine)](/Images/Day%203/illegal-download-domain.JPG "IP 10.0.0.201 (elmer.blanco machine)")

2. **Which torrent file did the user download?**
The torrent file is **Betty_Boop_Rythm_on_the_Reservation.avi.torrent**.

Filter: `ip.addr==10.0.0.201 and (http.request.uri contains “.torrent”)`

![Torrent file](/Images/Day%203/torrent-file.JPG "Torrent file")