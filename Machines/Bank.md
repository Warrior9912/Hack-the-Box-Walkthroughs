<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f01fb677-2f64-45ed-881a-44360e78fcb7">
</p>

<h1>Bank</h1>

<h2>Intro</h2>
<p>Here we are going to talk about the machine <strong>Bank</strong>. It's a Linux machine. We are going to be exploiting an RCE to gain access and using a backdoor that someone left us.</p>

<h2>Walkthrough</h2>

<h3>Recon and Ennumeration</h3>

<p>First of, let's do a TCP SYN scan with Nmap to check what we got here before doing a more agressive scan.</p>


<pre>nmap -p- -v -sS -Pn -f -n --min-rate 5000 --open 10.10.10.29 -oG allPorts</pre>

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
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/4b9b5720-26c5-43a2-95be-963162fbf5ee">
</p>

<p>Now let's do a more thorough scan to see what technologies these ports have. The i've added a combined two new flags, and added the specified ports that we are looking for</p>

<pre>nmap -p22,53,80 -v -Pn -n --min-rate 5000 --open -sCV 10.10.10.29 -oN targeted</pre>

<ul>
  <li><strong>-sC</strong>: It runs default scripts to get more info about our target. Uses common scripts for common vulns</li>
  <li><strong>-sV</strong>: this flag will return the version of the service that is on the open port</li>
  <li><strong>-oN targeted</strong>: this flag outputs the scan on a file named targeted. has a more comprehensible result</li>
</ul>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/4f67bce5-f540-4b53-a536-ac8a16e31275">
</p>

<p>We've seen that it has a web service, so it's a good idea that before starting doing something, ennumerate if there are any possible subdirs. I use <strong>Wfuzz</strong> to check this</p>

<pre>wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --hc 404 http://bank.htb/FUZZ</pre>

<ul>
  <li><strong>-c</strong>: this flag will give me the colourised output, easier to read</li>
  <li><strong>-z</strong>: used to specify the fuzzing payload source, then we specify the wordlist with <strong>file,</strong></li>
  <li><strong>--hc 404</strong>: hide status code. I did a quick fuzz before to check which error was the most repeated and remove it from the output. Error 404 stands for Not Found, so we are just ignoring not found subdirs</li>
</ul>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/9721a9cf-b4a8-49a2-bb21-a96ce2e6c3cd">
</p>

<p>We have obtained 4 useful subdirectories: <strong>/uploads, /assets, /inc</strong> and <strong>/balance-transfer</strong></p>

<h3>Getting inside</h3>

<p>Now, i've added the ip to <strong>/etc/hosts</strong>, then i've checked the web service, we got a port 80 open so let's just check it</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/a4928abb-924c-440b-b38c-460a8ff90c28">
</p>

<p>Got a simple loggin, iv'e checked the source code to see if there is a hint of something, but nope, got nothing</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/64e96e45-cda1-41b0-9261-4fe8037aced6">
</p>

<p>So here i've decided to check the subdirectories from the fuzz that we've done before. I've tryed first <strong>/uploads</strong>. We don't have permissions so, nothing</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/4f659bb6-e04c-4bbb-b41a-78ac4164c1b1">
</p>

<p>In <strong>/assets</strong> i've got nothing too, just the assets of the web, no info at all</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/91367032-6dd1-404e-8683-163974b9c79d">
</p>

<p>In <strong>/inc</strong>, got no luck at all, but trying <strong>/balance-transfer</strong> we get the following page</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/6f17bc28-200a-402f-b02c-a635e83cd42f">
</p>

<p>I downloaded the first file to see what it was, it turns out that they are files that store emails and passwords.</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/740128e4-e503-47b1-b9da-a039ed9214bc">
</p>

<p>I had thought about downloading the files one by one, but before doing that, I decided to check how many ".acc" there are on this page, and for whatever reason, downloading them one by one is a bad idea. Here i'm doing:</p>

<pre>curl -s http://bank.htb/balance-transfer/ | grep -F '.acc' | wc -l</pre>

<ul>
  <li><strong>-s</strong>: is a Curl option that won't show error messages</li>
  <li><strong>grep -F '.acc'</strong>: with this, we are searching the string '.acc'</li>
  <li><strong>wc -l</strong>: command to count lines and flag to display only the number of lines</li>
</ul>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/abb6fc06-8aec-4906-a3e9-573200af2058">
</p>

<p>I've noticed that this files have a size, so i'm gonna check if there is an outlier. Here is what iv'e done to check this:</p>

<pre>curl -s http://bank.htb/balance-transfer/ | grep -F '.acc' | grep -Eo '[a-f0-9]{32}\.acc.*"right">.+ ' | head</pre>

<ul>
  <li><strong>-Eo</strong>: -E enables extended regular expressions. -o only output the parts that match the pattern</li>
  <li><strong>'[a-f0-9]{32}\.acc.*"right">.+ '</strong>: this is not a command, it's the regular expression that <strong>grep</strong> uses. In order, it's telling grep that it has exactly 32 characters, that are lowercases from a to f,
  or digits from 0 to 9, <strong>\.acc</strong> it's telling how the string finish, <strong>.*"right"></strong>matches any characters (.+), followed by the string "right"> <strong>.+</strong></li>
  <li><strong>head</strong>: this is going to show me only the first 10 lines of output</li>
</ul>

<pre>curl -s http://bank.htb/balance-transfer/ | grep -F '.acc' | grep -Eo '[a-f0-9]{32}\.acc.*"right">.+ ' | cut -d'>' -f1,7 | tr '">' ' ' | head</pre>

<ul>
  <li><strong>cut -d'>' -f1,7</strong>: i'm extracting a section of each line of input. <strong>-d</strong> specifies the delimiter used to separate, in this case ">" it's the delimeter and <strong>-f1,7</strong>
  specifies the fields to select. 1 selects the first field, and 7 selects the seventh field based on the delimiter ">"</li>
  <li><strong>tr '">' ' '</strong>: i'm replacing ">" with spaces</li>
</ul>

<pre>curl -s http://bank.htb/balance-transfer/ | grep -F '.acc' | grep -Eo '[a-f0-9]{32}\.acc.*"right">.+ ' | cut -d'>' -f1,7 | tr '">' ' ' | sort -k2 -n | head</pre>

<ul>
  <li><strong>sort -k2 -n</strong>: <strong>-k2</strong> is sorting by the second field, in this case <strong>-n</strong> indicates that it's going to be sorted numerically, in this case, the lowest number first</li>
</ul>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/0edfbf0e-b93a-4b7c-b4a5-004315415768">
</p>

<p>Then iv'e done this, checking that the results where as expected</p>

<pre>curl -s http://bank.htb/balance-transfer/ | grep -F '.acc' | grep -Eo '[a-f0-9]{32}\.acc.*"right">.+ ' | cut -d'>' -f1,7 | tr '">' ' ' | cut -d' ' -f3 | sort | uniq -c</pre>

<ul>
  <li><strong>uniq -q<strong>: we are removing duplicated lines, and with <strong>-q</strong> prefixes each line of output with the count of how many times the line occurred in the input</li>
</ul>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/99d8b377-6ef9-4be5-b212-59511f12c2ad">
</p>

<p>THERE IS AN OUTLIER! Found it and download the file (we can also check the inside with curl)</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/2ff1b34a-df41-4c31-b170-0744badc1cb7">
</p>

<p>Here is what it contains, a clear user and password.</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/4163bb17-2d25-472e-8434-4a6d3107268b">
</p>

<p>And we are inside the webpage!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f1726b3b-9b2b-4606-a281-160bfa597fce">
</p>

<p>Here i found this page, looks like we have a RCE 😃</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/02dc250d-49f2-4b2a-86c2-5dac8f69a7a8">
</p>

<p>I've prepared a simple php payload to see if we can get a cmd</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/62f023c6-f19d-496b-8b10-ac61af978c52">
</p>

<p>Upload it but, seems that we can only upload images</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/4cac94e5-4125-4bcc-861d-56af8452e8dd">
</p>

<p>Changed the extension to <strong>.png</strong>, could upload it, but as it's not a real image i couldn't get any result</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/646b259c-20cc-49a0-82db-23bbf0465ad7">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/9f2b599a-7d04-4a18-b8e1-5e0aada7f6d8">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/52e3eb92-b2ab-4cf1-bbc3-1fb8111625e8">
</p>

<p>At this point i was a bit lost, so i decided to check the page code to see if there is a hint or something, and yep got it</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/882dc600-02e1-4fe2-9cd0-6e951466711d">
</p>

<p>So here i just had to upload the .htb file, see if i could call a cmd, and in that case, do a reverse shell and connect to a netcat session. I've urlencoded the reverse shell</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/7ded8bfa-0fad-4c47-b79e-2701afcfae19">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/88f9cfcb-1c2e-4391-b46d-16c62e0959f8">
</p>

<p>And we are in! I could get the user flag with www-data</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/e5e84200-889b-4157-9f83-f04f68442d28">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f2b2edfa-8a73-4a54-b6ab-d591b5726cd7">
</p>

<h3>Privilege Escalation</h3>

<p>Now we just have to get the root flag, first of iv'e tryed to <strong>sudo -l</strong> to check if i could execute any sudo command as this user, but nope, so iv'e checked for files that i could execute but with root permissions
I don't recognize any "emergency" file in linux, so i've checked what was inside, looks like it was a python binary</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d5af4987-672b-4870-9d79-8d287c99791a">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/ef8ba1f9-5713-4deb-a5fb-1863c31e8e31">
</p>

<p>I executed the code and got a root cmd session 😆. And got the root flag!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f80d4ad6-7872-4189-9045-53ba49500d97">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/4b9e7d1f-1715-4bdc-b4fb-e0af5b38fe43">
</p>

<p>That's all! It was quite fun to do this machine. Be nice!</p>
