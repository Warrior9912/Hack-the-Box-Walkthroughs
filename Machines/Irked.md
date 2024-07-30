<p align="center">
  <img src="https://github.com/user-attachments/assets/61f230cd-0531-4f59-8615-f6642b947a1b">
</p>
<h1 align="center">Irked</h1>

<h2>Intro</h2>

<p>In this walkthrough we are going to talk about the machine <strong>Irked</strong>. It's a Linux machine which has IRC enabled and we are going to vulnerate it. IRC stands for Internet Relay Chat. It is a text-based chat system. You can check more on this Wikipedia article:</p>

<p align="center">
  <a href="https://github.com/wiki/IRC"><img src="![image](https://github.com/user-attachments/assets/6114b1b1-e064-47fb-a2ad-6c9ef2a59c73)"></a>
</p>


<h2>Walkthrough</h2>

<h3>Enumeration and reconaissance</h3>

<p>Let's start, as usual, by doing a fast TCP SYN scan with nmap to check which ports are we facing</p>

<pre>nmap -p- -v -sS -Pn -f -n --min-rate 5000 --open 10.10.10.117 -oG allPorts</pre>

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
  <img src="https://github.com/user-attachments/assets/791a9ec5-e470-425f-ade9-e97c1f8a3b73">
</p>

<p>As we can see on the image ports 22, 80, and 6697 are open. I emphasise port 6697 because it is a secondary port of the IRC service. I've done a more in deep scan to check if there is something more that we should check</p>

<pre>nmap -p22,80,111,6697,8067,48516,65534 -v -Pn -n --min-rate 5000 --open -sCV 10.10.10.117 -oN targeted</pre>

<ul>
  <li><strong>-sC</strong>: It runs default scripts to get more info about our target. Uses common scripts for common vulns</li>
  <li><strong>-sV</strong>: this flag will return the version of the service that is on the open port</li>
  <li><strong>-oN targeted</strong>: this flag outputs the scan on a file named targeted. has a more comprehensible result</li>
</ul>

<p align="center">
  <img src="https://github.com/user-attachments/assets/e1e0d316-2fe7-4eb3-a5fb-4ccc167ed628">
</p>

<p>I've also done some fuzzing, becouse, when we check the web service, we can only see an image, and checking the source code doesn't give us any hint. But as we can see in the image, there is nothing we can check</p>

<p align="center">
  <img src="![1 2](https://github.com/user-attachments/assets/818cac89-c995-47a8-9d9c-37d69e66ed0f)">
</p>

<p>So at this point i've just searched for IRC exploits and CVE's and i've found this Github Repo</p>

<p align="center">
  <a href="https://github.com/Ranger11Danger/UnrealIRCd-3.2.8.1-Backdoor"><img src="https://gh-card.dev/repos/Ranger11Danger/UnrealIRCd-3.2.8.1-Backdoor.svg"></a>
</p>


<p>let's try it to get into the machine</p>

<h3>Getting inside</h3>

<p>So as I said, let's try that Python script. First, checking the guts of the code, found that we have to writte the IP of the machine and the port that we are attacking, in this case IP 10.10.10.117 and port 6697</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/aff86932-4ab9-44d7-93ab-58c06c00d660">
</p>

<p>And from what the README says, that's it, that's the only thing we have to change, we have to take into account what service we are going to run to get the reverse shell, in my case I'm using netcat and that's it, that's how simple it should be to make a RCE. so we simply run the script in one terminal and listen to the response in another terminal. So we simply run the script in one terminal and listen to the response in another terminal.</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/87c7556d-844c-49ed-9231-4395101c8e76">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/89e0dbb4-dc9d-4a52-bfec-2a7cf990ece0">
</p>

<p>With the current user we can't read the user flag, so I simply investigated to see if I could find relevant information. I looked at the user ‘djmardov’, and inside the /Documents directory, I found an hidden file ‘.backups’ and a user.txt flag that has a symbolic link to the flag in the user's ‘ircd’ folder.</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/c3e317fa-0987-4078-b203-332ed702c656">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/510b6c47-6906-47d9-be92-3397b24a9fe0">
</p>

<p>When we see what's inside, it shows us what I think is a password, and a text that tells us it's a steg backup. At this point I don't know what ‘steg’ is, with a quick search I found that it refers to ‘steganogrphy’, from what I have found this is used to save secret information inside image files (mainly), I leave you the article where I found this information, in it gives us the tool ‘steghide’, so I will try to use it with the only image I have available, the image of the web page.</p>

<p align="center">
  <a href="https://github.com/articles/steganography-how-to-hide-data-in-images-and-extract-them"><img src="https://github.com/user-attachments/assets/cfa02053-1bb1-44ee-b62b-11d20ecb0121"></a>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/cc8b0950-57d8-40ea-9e8a-557d74d32442">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/6885de55-e4d3-4dc7-86ca-7d7a665a6316">
</p>

<p>I leave the explanation of the command below</p>

<pre>steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSss</pre>

<ul>
  <li><strong>extract</strong>: this option tells Steghide to extract hidden data from file</li>
  <li><strong>-sf irked.jpg</strong>: "sf" stands for "stegofile", the file which has hidden data, in this case irked.jpg</li>
  <li><strong>-p</strong>: stand for "password", i think that we dont have to explain much about it 😄</li>
</ul>

<p>Looks that we extracted something called pass.txt. Checking if I could access the user ‘djmardov’ with the password that was inside pass.txt we can see that yes, it allows us to use that user. So we already have the flag user.txt.</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/36c380ba-4b62-4eeb-8eaf-97a17aab8cd3">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/3edea30d-1156-4e20-a020-e2179b924e37">
</p>

<p>So now we just have to do the privilege escalation!</p>

<h3>Privilege escalation</h3>

<p>First i've tryed to do <strong>sudo -l</strong>, but looks like sudo doesn't exist. Then I checked for files with root privileges</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/b778e732-1f95-4fec-826e-6d736d73fd91">
</p>

<p>That file 'viewuser' looks suspicious so let's execute it. Maybe the first time of execution gives an error of execution perms, we can solve it by ust giving it with <strong>chmod +x</strong></p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/9b7eecbb-5e2d-4f43-834b-c0eef99efebb">
</p>

<p>It says that the directories /tmp/listusers doesn't exist, so just create it with <strong>mkdir</strong>. Now looks that by writting commands into that file they are executed as root</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/a5cc2521-4e9b-48e5-90df-1f8c5a02cc23">
</p>

<p>So, at this point i just had to call a root shell</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/513312f8-27cf-49b8-ac73-9ee9a3b08999">
</p>

<p>And get that root flag!</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/10736b65-bf0e-4691-819c-f4866464977e">
</p>

<p>That's all! With this machine I've learned what is "steganography". Be nice!</p>
