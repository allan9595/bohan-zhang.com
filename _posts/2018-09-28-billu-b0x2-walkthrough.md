---
layout: post
title: Billu B0x2 walkthrough
---
[Billu:B0x2](https://www.vulnhub.com/entry/billu-b0x-2,238/)

Perface:

This Virtual machine is using ubuntu (32 bit)

Other packages used: -

PHP Apache MySQL Apache tomcat

This virtual machine is having intermediate to medium difficulty level. One need to break into VM using web application and from there escalate privileges to gain root access. Gaining low or root privilege shell can be done in two ways (for both)

Special Thanks to @ndishell1046 created this fun box!

Let's diving into the hacking!

First, as usual, doing a Nmap to enumerate all the services running on different ports.

<img src="../img/bull/Screen Shot 2018-09-21 at 10.23.32 PM.png" >

<img src="../img/bull/Screen Shot 2018-09-20 at 2.39.28 PM.png" >

As the screenshots shown above, the target machine runs ssh, http, Tomcat, and Rpcbind potentially allow us to hack. Also, I highlighted something, as we see, this machine is running Drupal 8. At least we know it uses a CMS content manager!

My first thought is going to the port 80, after some quick browsing around, I found out the login page. Immediately, brute-forcing crossed to my mind! So, quickly I used Burp getting the http-post-form parameters and the form value and quickly formed a hydra http-post-form attack! However, looks like the Drupal well prevents brute forcing attacking, after 5 attempts, many false positive comes back.....no luck in here...

<img src="../img/bull/Screen Shot 2018-09-28 at 4.36.03 PM.png" >

<img src="../img/bull/Screen Shot 2018-09-28 at 4.42.06 PM.png" >

<img src="../img/bull/Screen Shot 2018-09-28 at 4.42.30 PM.png" >

<img src="../img/bull/Screen Shot 2018-09-28 at 4.46.03 PM.png" >

After some frustration, I decided go back to the nmap and look some other open ports. Tomcat? Maybe I can try to hack into tomcat manager gui for the sake of hacking? Why not give a try! So, quickly, I fired up Metasploit. Search Tomcat. I think I would have big chance for tomcat_mgr_login module.

 <img src="../img/bull/Screen Shot 2018-09-28 at 12.41.34 PM.png" >

 Starting my brute-forcing process. At the same time, I wasn't just sit back and wait on this no-ending brute forcing going cause possibly wasting my time if not finding anything in the end.

 So, I came back to the port 80 and thought maybe the Drupal 8 can be exploited? Is there any tools for me to enumerate Drupal and exploit it? After some researching through googling. I found Droopescan this tool for scanning the Drupal. Seems pretty promising. Downloaded it and gave it a shot!

 <img src="../img/bull/Screen Shot 2018-09-28 at 12.37.21 PM.png" >

 As the screenshot shows, we got some interesting info back, the version of Drupal is 8.3.6 and default user found as "admin". But since the brute-forcing attacking not working for me, I decided to search for exploit! After some researching, I found "Drupalgeddon2" discovered by Vitalli in April 2018. Special Thanks to Vitalli's contribution! And it shows working on version less than 8.3.9.. Oh yea, let's hacking our Drupal 8.3.6 use this exploit on Metasploit.

 <img src="../img/bull/Screen Shot 2018-09-28 at 12.39.44 PM.png" >

 Finger crossed it works! Now I got a meterpreter shell! At same time, I gave up my Tomcat brute forcing since I have gained my low-level user privilege.

 <img src="../img/bull/Screen Shot 2018-09-28 at 12.44.35 PM.png" >

 <img src="../img/bull/Screen Shot 2018-09-28 at 11.20.23 AM.png" >

 Now it is time to privilege escalating to ROOT! Quickly I target to /etc/passwd ...... so sad..... one hundred times crying in here..... The permission is loosing like a ...... so I can edit it as www-data.

 <img src="../img/bull/Screen Shot 2018-09-28 at 12.52.35 PM.png" >

 <img src="../img/bull/Screen Shot 2018-09-28 at 1.01.06 PM.png" >

 I downloaded it from meterpreter and used openssl generating a hash, replacing the "x" with my hash and uploaded back, now, I had the root!

 <img src="../img/bull/Screen Shot 2018-09-28 at 12.52.28 PM.png" >

 <img src="../img/bull/Screen Shot 2018-09-28 at 12.52.35 PM.png" >

 <img src="../img/bull/Screen Shot 2018-09-28 at 1.00.02 PM.png" >

 Things we got away from this walkthrough:
 1. Updated your Drupal to latest version immediately as the versions affected shows above.
 2. Change default user and password for your Drupal site! No "admin" and weak password should appear!

 Reference:
 For the security flaw shows in the exploit, you can go to
 1. <https://www.exploit-db.com/exploits/44448/>
 2. <https://github.com/a2u/CVE-2018-7600>

 for more Reference!

 This box might have other ways to hack into as the author said! I will update this blog as I find another way to exploit this box.
