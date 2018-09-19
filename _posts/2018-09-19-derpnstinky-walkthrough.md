---
layout: post
title: DerpNStinky WalkThrough
---

[DerpNStinky](https://www.vulnhub.com/entry/derpnstink-1,221/)

Perface: 
Mr. Derp and Uncle Stinky are two system administrators who are starting their own company, DerpNStink. Instead of hiring qualified professionals to build up their IT landscape, they decided to hack together their own system which is almost ready to go live...

This machine is a pretty interesting boot2root machine, I had a lot of fun on this machine and learned some new things.

First, do a nmap scan and gathering inforamtion about this machine.
Target machine: 10.0.2.13

<img src="../img/darp-pen-testing/Screen Shot 2018-09-13 at 12.36.17 AM.png" >

As you can see, the ftp, ssh, http services are open for us to hack! Let's hit to the browser and go to 10.0.2.13. After runn dirb and enumerated all the directories, we see this website run wordpress. However, when I tried to go to wp-login.php, it says can't reach the page. Quick fix is nano /etc/hosts, add 10.0.2.13 derpnstink.local. Now, everything should work as expected. Also, we got our first flag by try harder! 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-13 at 12.38.29 AM.png" >

<img src="../img/darp-pen-testing/Screen Shot 2018-09-14 at 12.13.06 PM.png" >

admin login page! it't time to try some default crend. when I first tried user: "admin" , pass: "admin". it works perfect. Login in as admin. And I automatically think I can use wp_admin_shell_upload to establish a reverse__tcp shell. But, no luck in here. 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-14 at 12.15.43 PM.png" >

<img src="../img/darp-pen-testing/Screen Shot 2018-09-14 at 2.00.34 PM.png" >

Then run wp_scan to explore some vulnerabilities, find the slider plugin has a exploit! 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-14 at 2.00.08 PM.png" >

<img src="../img/darp-pen-testing/Screen Shot 2018-09-14 at 2.00.42 PM.png" >

Quickly establish a shell through that exploit! Since the target runs wordpress, first target for me would be look into wp_config file. Luckily, I got the phpmyadmin through that file! 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-15 at 4.37.23 PM.png" >

Login in the mysql as user: "root" pass: "mysql". At this point, the database has compromised. After browsing the database, find the second flag at wp_post. 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-15 at 4.57.13 PM.png" >

From www-data and home directories, I found mrderp and stinky are two users for this machine. Luckily, in wp_user, I found uncleStinky's passowrd. Using hashcat quicly cracked the hash. The passowrd is wedgie57! However, trying to ssh to stinky, no luck bc it's using key-pair auth.


<img src="../img/darp-pen-testing/Screen Shot 2018-09-15 at 5.53.24 PM.png" >

I was looking around and found maybe I should try ftp with this cred? It actually worked! Sometimes, You just got to try a little bit harder! #tryharder!

<img src="../img/darp-pen-testing/Screen Shot 2018-09-15 at 6.05.25 PM.png" >

In the ftp, I got unclestinky's private ssh! Oh yea, which means I can login to stinky account ! 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-15 at 6.09.24 PM.png" >

<img src="../img/darp-pen-testing/Screen Shot 2018-09-15 at 6.12.20 PM.png" >

Found flag3 in Desktop ! Also, found a wireshark PCAP file in Documents folder! interesting file enough worth me to look at! I knew at this point next step would be gain mrderp cred and hack into that account! After I looked the packets in wireshark, I found mrderp actually was a account in the wordpress, however, bad unclestinky deleted it for some reasons! 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-19 at 5.58.29 PM.png" >

Got mrderp's passwrod is derpderp......... really long ! Gain mrderp account access! I am stucked at here for a while because I am not sure what's next steps! After googling around and eventually found a helpdesk file un Desktop ! I knew I need to check what I can do with sudo with mrderp! 

<img src="../img/darp-pen-testing/Screen Shot 2018-09-19 at 3.14.40 PM.png" >

Thanks to the support team did me a favor and add this to mrderp's sudoer! bad practice! only thing at this point I need to do is make a bash ends with derpy.sh and run it as sudo in binaries, then I will gain ROOT!  


<img src="../img/darp-pen-testing/Screen Shot 2018-09-19 at 3.14.57 PM.png" >

NOW, THIS WHOLE MACHINE IS ROOTED !!!!!

<img src="../img/darp-pen-testing/Screen Shot 2018-09-19 at 3.15.15 PM.png" >



Things taken away if you are in info-sec or not: 

1. If you use WORDPRESS or manage WORDPRESS for someone, always make sure use strong PASSOWRD like "s876ehdfw6!!" and different username! "admin" "admin" are worst choice You can make when you use wordpress! 
2. keep you wordpress to lasest version which includes all the plugins!!!
3. use 2fa at login will be even more safer! 
4. Don't grant sudoer to anyone they ask for! Using least privilege principle! 
5. Avoid using wordpress if you can bc just too easy to be hacked! 
6. Always use https instead of http! 




