
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d343a130-20d1-43d7-b454-b14e526d37f9">
</p>

<h1 align="center">Grandpa</h1>

<h2>Intro</h2>
<p>In this walkthrough we are going to talk about the machine <strong>Grandpa</strong> which is a Windows machine.</p>

<h2>Walkthrough</h2>
<h3>Recon and Service enumeration</h3>

<p>To get started, as always, we start ennumerating with <strong>nmap</strong>:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/a24cd6e7-b939-480e-9b9c-1e598e0ca15e">
</p>

<p>As we can see, there is a WebDav, so, to check if it's possible a WebDav vuln, we can use <strong>davtest</strong> as it follows:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/44ec4f84-a0ea-4a03-95ce-ddfec6beca1f">
</p>

<p>We can see that there is nothing enabled, so let's check the nmap report again. We see that it is an ISS 6.0, so here we should check for posssible exploit with <strong>searchsploit</strong></p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/48980dff-70bb-419d-be5b-fd2929532118">
</p>

<h3>Getting Inside</h3>

<p>So, to save you some time, at least for me, none of the options are valid, so we have to do a little research. I found the following Github repository:</p>

<p align="center">
  <img src="https://gh-card.dev/repos/g0rx/iis6-exploit-2017-CVE-2017-7269.svg">
</p>

<p>To get the reverse shell that we need we should do the following:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/7f5f7fc6-23e2-43c1-b521-af5fffc8652a">
</p>

![4](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/7da8fb1f-897f-4866-aa4a-6c8354eff355)

<p>As we can see it has been easy to catch the reverse shell, but we are not authority/system, we are <strong>authority/network system</strong> and as we can see in the following image, we don't have any permissions</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/cfb0947b-a4ab-43fd-a661-9e78ff90e1a6">
</p>

<p>Going to the root directory I noticed that there is a strange directory that I have not seen on any Windows system:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d9de7993-9cc4-4c69-9f41-6d39c3ee538f">
</p>

<p>And while trying to create and write a file I realised that it is possible to create and write to this directory</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/2dff9512-ff68-4dcc-8247-5343df15bdfe">
</p>

<p>I took a look at the privileges my user has to see if I could do anything spial and found that I have 3 permissions enabled</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/20e97a88-e97a-43a0-8ff8-756a65e1aa95">
</p>

<p>The one that has caught my attention is SEImpersonatePrivilege, which from the research I have done, I have read that we can do a Token Theft, which would allow us to steal the security token of an administrator user and be able to gain privileges with it.</p>

<h3>Privilege Escalation</h3>

<p>So, we have come to the conclusion that comes the complex part, privilege escalation. I have found the following repositories, both have helped me to gain privileges</p>

<p align="center">
  <img src="https://gh-card.dev/repos/SecWiki/windows-kernel-exploits.svg">
</p>

<p align="center">
  <img src="https://gh-card.dev/repos/Re4son/Churrasco.svg">
</p>

<p>Before doing anything, I have realized that neither with <strong>certutil</strong> nor with <strong>powershell Invoke-WebRequest</strong> has let me upload neither the reverse shell nor the exploit, so I attach a repository with which we can upload files.</p>

<p align="center">
  <img src="https://gh-card.dev/repos/fortra/impacket.svg">
</p>

<p>Let's prepare our reverse shell:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/fb395fd7-442a-46a8-a3c8-7ade50d26311">
</p>

<p>We upload the files to the victim machine</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/faf302b5-926f-4901-bbac-4def9c585e2b">
</p>

<p>Open another terminal and run netcan on the port you want to, then use the exploit like this:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/cafe6c20-d7b3-4d74-9a25-8c18bcc15bb2">
</p>

<p>And as we can see, we have gained administrator permissions!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/cafe408a-0090-4916-9e68-5e9fdcc40ba8">
</p>

<p>So now we just have to get the flags!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/04e639b8-206b-4f21-a8bc-0ab9eb572ee9">
</p>

<p>With that we just finished another machine. Be nice!</p>
