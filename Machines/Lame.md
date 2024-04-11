
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/ca637176-cf2b-4889-a779-4923d10d4bc9" alt="Lame HTB">
</p>

<h1 align="center">Lame</h1>

<h2>Intro</h2>
<p>In this walkthrough we are going to be talking about the machine <strong>Lame</strong></p>

<h2>Walkthrough</h2>

<h3>Recon and service enumeration</h3>
<p>Let's start, first i'm gonna try to ennumerate the services:</p>
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/ab684db0-820c-45db-b169-0644019c15aa" alt="Service enum 1">
</p>

<p>We can see here that it has FTP, SSH, and if i don't have bad memory port 139 it's usually SMB. Let's run a more agressive scan</p>
<pre>nmap -p21,22,139 -sCV -Pn -vvv -f 10.10.11.8</pre>

<p>As we can see here, yep, port 139 is for SMB, and FTP has vsftpd, which has called my attention:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/de47ef64-d3b1-4ff7-87ac-4783dc7f287a" alt="Service enum 2">
</p>

<p>By some quick research with searchsploit, both versions of theservices have some vulns:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/cb5cbe7c-7e07-42be-9932-2658a9360c9f" alt="Service enum 3">
</p>

<p>So let's check with our good old friend Google if there are some scripts to vuln them (I know that with Metasploit should be easier to do, but i want to get used to search things and don't depend of Metasploit always, in the end i'm a rookie!)</p>


<h3>Getting inside</h3>
<p>First i've searched something for VSFTPD. Looks like the common response of my researchs land me into CVE-2011-2523, and by what i've just seen it seems that there should be a backdoor on port 6200. Here is some info about it:</p>

[CVE-2011-2523](https://nvd.nist.gov/vuln/detail/CVE-2011-2523)

<p>Now let's search a script for us to see if we can get into. Found a cool repo!</p>

https://github.com/padsalatushal/CVE-2011-2523

<p>I've thought that it was it but seems that it times out me becouse of port 6200:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/8dca005d-be41-4671-a49b-fde8f908722c" alt="CVE-2011-2523">
</p>

<p>Now, let's see if we can do something with that SMB 3.0.2. Iv'e seen on when iv'e used seachsploit that, it looks that it has a vuln that let attackers run commands becouse there is an option on smb.conf called
"username map script" that is enabled, so let's try it out. (Here is were iv'e got the info)</p>

[CVE-2007-2447](https://nvd.nist.gov/vuln/detail/CVE-2007-2447)

<p>And yep, i've found out another cool repo, it looks like by using netcat, it should give us a reverse shell. Here is the repo:</p>

https://github.com/amriunix/CVE-2007-2447

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/6d0f64cd-272c-4fd2-9c4f-2e7f64b1a35c" alt="CVE-2007-2447">
</p>

<p>Gotcha, and to my surprise, there is no need for privilvege escalation, we already got root privileges! So we can get both user and root flags:</p>
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/65ebfd6d-3522-48e4-b559-f61187302003" alt="Flags">
</p>

<p>Be nice!</p>
