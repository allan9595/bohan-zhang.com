---
layout: post
title: LazySysAdmin WalkThrough
---
[LazySysAdmin](https://www.vulnhub.com/entry/lazysysadmin-1,205/)

Perface:

LazySysadmin - The story of a lonely and lazy sysadmin who cries himself to sleep

This is my second write up in Vulnhub. This box I rated as a easy -> intermediate box. It is a interested boot2root box educating new comes in basic linux security. So, let's jump right into the hacking!

First, as usual, doing a Nmap to enumerating basic info about this system!

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 2.43.48 PM.png" >

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 2.43.57 PM.png" >

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 2.44.48 PM.png" >

As we can see, ssh, http, smb, and mysql services are open for us to hack. At this point, I decided to use dirb to enumerating all the relevant relevant directories.

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 2.56.42 PM.png" >

I highlight something in the image, WORDPRESS! I just love to hack Wordpress! After I know the service site runs wordpress, I immediately thought I should try "admin" "admin" on the wp-login.php page, however, no lucks on default credentials! I decided to use wpscan to see if there is any possible vulnerable plugins in the wordpress allow me to do something. However, no luck in here too.
At this point, I thought maybe I should try to use "rouckyou.txt" wordlist to bruteforce the password since I have known the "Admin" is a user.

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 10.32.28 PM.png" >

At the sametime, I was thinking maybe there is another way to hack into the system. Let's not waste time on one way if that eventually not works.

As I mentioned early, We found ports 139 and 445 are open, both of them are smb servers and responsible for auth local users to access shared resources, refer as WORKGROUP. So, let's use enum4linux to enumerating all the users in the group.

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 10.14.00 PM.png" >

Interesting enough, "togie" shows as a local user. Let's try to use Hydra to brute-force the ssh password.

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 10.16.56 PM.png" >

Yikies! "12345" quickly found as the password for togie! okok, now I can ssh to that machine! After I ssh into that machine, I found I can't cd out the directory because the admin restricted my ability to use certain commands.

After some creative thinking, I tell myself, could I spawn a python shell very quick and bypass restriction in this current shell? Luckily, it worked! Sometimes you just got to try harder and think out of box! #tryharder

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 10.24.46 PM.png" >

Now I give up the wpscan brute-force and quickly target to wp-config.php file since the site runs wordpress and the mysql usually stored at there. In that file, I found mysql: "Admin", password is: "TogieMYSQL12345^^". Later on , I found this credentials is wordpress login credentials too.

<img src="../img/lazy-admin/Screen Shot 2018-09-20 at 10.27.22 PM.png" >

However, I still need to root this machine ! I quickly target to the /etc/passwd under sudo(root)! Oh boy, the admin didn't restrict sudoers and I can run sudo from togie! Then things become easy for me! I decided run sudo and change root password in /etc/passwd.

<img src="../img/lazy-admin/Screen Shot 2018-09-21 at 11.19.20 AM.png" >

Now this machine was rooted!


Things taken away:
1. If you are a linux admin, restrict sudoers! not everyone need sudo. Least privilege principle.
2. Use strong passowrds for all your services running in the network!
