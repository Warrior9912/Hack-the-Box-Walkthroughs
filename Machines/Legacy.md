

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/ca11f3a6-673f-4446-acb8-85ec327968c4" alt="Legacy HTB">
</p>


<h1 align="center">Legacy</h1>

<h2>Intro</h2>
<p>In this walkthrough we are going to be talking about the retired machine <strong>Legacy</strong>!</p>

<h2>Walkthrough</h2>

<h3>Recon and service enumeration</h3>
<p>First things first, let's do some recon and ennumeration:</p>
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/42df505a-11c9-453f-b868-10c29ada3561" alt="Service enum 1">
</p>


<p>"msrpc" called my attention, but let's do, as always, a more deep scan!</p>
<pre>nmap -p135,139,445 -sCV -Pn -vvv -f 10.10.10.4 -oN targeted</pre>
<p>I always run -oN to, once the scan is completed, in case that i need to check the services again, check them on the file targeted</p>

<p>As we can see on the following scan, it is a Windows XP, it has msrpc, and also, (i forgot to say it before), it has SMB:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/07163aa6-118f-4399-b38c-04d8ada7aff3" alt="Service enum 2">
</p>

<p>In this case, I didn't check searchsploit, and to my surprise I've got something quick:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/30986b89-565b-486e-b8f2-de5b1232f015" alt="Service enum 3">
</p>

<p>These are the resources!</p>

[Hack Tricks](https://book.hacktricks.xyz/network-services-pentesting/135-pentesting-msrpc)

[Rapid 7](https://www.rapid7.com/blog/post/2014/02/03/new-ms08-067/)


<h3>Getting inside</h3>
<p>I'm going to use Metasploit, as the guide from Rapid 7 uses it, so here we go. First we search the vuln just in case:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/54ebd3d7-6a7a-4bf6-af22-b8cf816e3428" alt="Metasploit 1">
</p>

<p>Now we set the LHOST, LPORT, set up the payload, as I don't know how to use it, I've checked how to:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/9e245340-89bf-4ebf-a1b0-51a681b28ada" alt="Metasploit 2">
</p>

<p>Finally, we set the RHOST, and launch the exploit!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/71f91229-c5ad-402e-956b-bb37e246772c" alt="Metasploit 3">
</p>

<p>And we are in by just calling a shell!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/685f7c05-d87a-4529-8676-a6c3011debc7" alt="Metasploit 3">
</p>

<p>Finally, we have to look up for the flags, as we are already root, there is no need to do privilege escalation! They both are in the Desktop folder of their respective user, as we can se here:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/2378f609-c3df-4f5a-b237-099282b57a12" alt="Flags 1">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/66d1d61a-0727-4fef-bc80-bda412a79c6d" alt="Flags 2">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/71afece8-9a01-4ee5-b1cc-a3fc3c9e5b53" alt="Flags 3">
</p>

<p>So thats all! Be nice!</p>
