<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/42586404-d6e4-41e2-85ae-c13c6d169235">
</p>

<h1 align="center">Blocky</h1>

<h2>Intro</h2>

<p>In this walkthrought we are gonna talk about the machine <strong>Blocky</strong>, it's an easy CTF</p>

<h2>Walkthrough</h2>

<h3>Recon and Ennumeration</h3>

<p>First of, as always, let's do a quick TCP SYN scan</p>

<pre>nmap -p- -v -sS -Pn -f -n --min-rate 5000 --open 10.10.10.37 -oG allPorts</pre>

<ul>
  <li><strong>-p-</strong>: we are specifying that we want as many ports as possible to be scanned<li>
  <li><strong>-v</strong>: gives me a more detailed output during the scan</li>
  <li><strong>-sS</strong>: here we specify that we are doing a TCP SYN scan, it's faster than a full TCP scan, at the moment we just want to check the ports</li>
  <li><strong>-Pn</strong>: this flag is set to disable host discovery. It is used when the target does not respond to pings</li>
  <li><strong>-n</strong>: disables DNS resolution, this flag gives us more speed in the scan by avoiding resolving hostname, we will check that later</li>
  <li><strong>--min-rate 5000</strong>: with this flag, we are setting the minimum packet sending rate to 5000 packets per second, it makes the scan faster</li>
  <li><strong>--open</strong>: here we are specifying that we only want to show in the scan result the ports that are open</li>
  <li><strong>-oG allPorts</strong>: I am saving the scan result in a grepeable file called allPorts</li>
</ul>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/285c9834-0c5f-44cc-ba4f-360a54c0f725">
</p>

<p>So, at the moment we have the following:</p>

<table align="center">
    <tr>
        <th>Ports</th>
        <th>Services</th>
    </tr>
    <tr>
        <td>21</td>
        <td>FTP</td>
    </tr>
    <tr>
        <td>22</td>
        <td>SSH</td>
    </tr>
    <tr>
        <td>80</td>
        <td>HTTP</td>
    </tr>
    <tr>
        <td>25565</td>
        <td>Minecraft</td>
    </tr>
</table>

<p>If we do a more exahustive scan </p>

<pre>nmap -p21,22,80,25565 -v -Pn -n --min-rate 5000 --open -sCV 10.10.10.29 -oN targeted</pre>

<ul>
  <li><strong>-sC</strong>: It runs default scripts to get more info about our target. Uses common scripts for common vulns</li>
  <li><strong>-sV</strong>: this flag will return the version of the service that is on the open port</li>
  <li><strong>-oN targeted</strong>: this flag outputs the scan on a file named targeted. has a more comprehensible result</li>
</ul>

<p>We get the following:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d9c36508-cd78-4264-82f3-9b5c119ea927">
</p>

<h3>Getting inside</h3>

<p>First, as there is a webservice, i'm gonna add it to <strong>/etc/hosts</strong>, after that, when I accessed it, I realised it was a wordpress, so I went directly to wp-login.php to see if I could access it directly with basic credentials (admin:admin, admin:12345...), without any success</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/838255dc-7c5a-4f0b-9de3-e450b9b34671">
</p>

<p>So here i've just thought of fuzzing subdirectroies to take a look if there was somthing interesting, looks like <strong>/plugins</strong> was free to access</p>

<p>
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/a89574f2-2c95-40f6-aed7-ad27ee8ffe24">
</p>

<p>Also checked for any usefull information with <strong>WPScan</strong>, as it is a Wordpress</p>

<pre>wpscan --url http://blocky.htb -e ap,t,tt,u</pre>

<ul>
  <li><strong>--url</strong>: this flag specifies the URL that we are targeting</li>
  <li><strong>-e</strong>: specifies the enumerations that WPScan has to perform, which can include:
    <ul>
      <li><strong>ap</strong>: enumerate all plugins</li>
      <li><strong>t</strong>: enumerate all themes</li>
      <li><strong>tt</strong>: enumerate all Timthumb files</li>
      <li><strong>u</strong>: enumerate all users</li>
    </ul>
  </li>
</ul>

<p>Found a user!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/af3ad183-7e45-48e4-9cca-ab28e764c02c">
</p>

<p>Now checking <strong>/plugins</strong>, we can see that there are 2 files that we can download:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/7105d879-6036-4c3d-b99f-700d694ebec2">
</p>

<p>I've downloaded both of them, as both are .jar files, we can check the inside of them with <strong>jd-gui</strong>, on the first one, i found what it looks like a password</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/a352c880-3abe-4169-88f6-b83f810284dc">
</p>

<p>Tryed to use it connecting via ssh to the machine and it worked! We are inside! And we got the user flag!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f864f2d4-6e02-4e7e-aefc-af51591eff18">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/3ae61b7a-aa83-4a10-ad14-b1207086d4b5">
</p>

<h3>Privilege escalation</h3>

<p>Now that we are inside as a user, we need to get root. First, i always check which persmissions do i have as a simple user. To my surprise I have full permissions, so I simply became root with the password we already had and we got the privilege escalation and thus the root flag!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f6b81837-9eab-4d08-a1b5-978ae5092821">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f5afcaf3-4f58-4158-ae1f-d8e13d910535">
</p>

<p>So this is it, we finished the machine Blocky, quite easy to be hones. Be nice!</p>
