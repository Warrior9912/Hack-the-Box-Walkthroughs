<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/7f585141-34fa-4feb-87b2-21cdf3b46cd2" alt="Devel HTB">
</p>


<h1 align="center">Devel</h1>

<h2>Intro</h2>
<p>Here we are gonna resolve the machine <strong>Devel</strong></p>

<h2>Walkthrough</h2>

<h3>Recon and service enumeration</h3>
<p>First things first, let's see what does this machine have:</p>
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/cf4710e8-a389-4994-a0f3-982bb1831514" alt="Service enum 1">
</p>

<p>As we can see we the ports that are open are port 21 (FTP) and port 80 (http).</p>

<p>Let's do a deeper examination to see what do we have here:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/0286f1fd-b310-41cc-85d4-dbef54a57757" alt="Service enum 2">
</p>

<p>Observing this, we notice that first of all, we can connect with user "annonymous" and without password via ftp to the server, and that port 80, is an IIS, which is a web server software created by Microsoft.</p>

<h3>Getting inside</h3>
<p>First was checking the endpoints that showed the FTP service, and as we can see, it works, so let's assume that they are on the same folder than is the web service</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/88b8ca97-f2d7-4c1b-816f-c5bb701ae90e" alt="Endpoints 1">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/458d3208-39d4-490f-8529-17eae0e46b63" alt="Endpoints 2">
</p>

<p>And yes! As we connect via FTP we notice that in the same folder we are in we got the files that Nmap showed us:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/64b1b231-0723-48d3-ac93-fae79b6bd82b" alt="FTP 1">
</p>

<p>So that means one cool thing! We can upload a reverse shell to exececute it when we refresh the web. So let's use msfvenom to do this, notice that we are using .aspx extension because usually IIS runs under
  .NET framework, msfvenom supports this extension, so all nice (Remember that the FTP session has to be on the same directory that your reverse shell is, so it will find it when we try to upload it):</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/33e73241-32d1-4dce-a36e-ff62c1f97521" alt="FTP 2">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/94523995-03c0-499c-a01b-51f80ebf18d4" alt="FTP 3">
</p>


<p>Thinking that we are going to use Metasploit (at this point I don't know other way to get a reverse shell in Windows machines) we set up our session</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/ce3d6e1e-e86f-4fc8-aa2e-f4ab1b937388" alt="Reverse Shell">
</p>

<p>When we write "run" Meterpreter is gonna be listtening, and when we write the endpoint of in web, in my case 10.10.10.5/reverse.aspx, it will execute the reverse shell. As we are already in we could get the user flag by just calling a shell, but as we are using Metasploit, it sure has a script that can give us a root shell directly. Given this, we are going to use "local_exploit_suggester" to see what does it recommend, i've got this: </p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/9f6c1b00-69be-450f-9952-037a56dd173e" alt="local_exploit_suggester">
</p>

<p>I'm gonna use MS10 (when Microsoft reports their security bulletins, they call their vulns MSXX, being XX the year that was published, for example, MS08 means that that vuln is from 2008), becouse iv'e seen by using "systeminfo" that the os has never been updated and thinking that it's running on a Windows 7, it may work.</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/cda71e11-a8b4-4ec7-86ba-c1fbc50f0745" alt="Payload">
</p>

<p>As we run it, and refresh the web, we get access to it, and by calling a shell we can see that we have root permissions!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/3bd7ebaa-4eb1-4f42-8f2b-111d6b34bbbf" alt="Permissions">
</p>

<p>And so, by that, we can have both flags!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/5ea085d8-ff89-4f44-91ba-5d9b957018fb" alt="Root flag">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/6ff52af7-e7ba-41da-8b9f-d9df5143fe0e" alt="User Flag">
</p>

<p>Be nice!</p>
