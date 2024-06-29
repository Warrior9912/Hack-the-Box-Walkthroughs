<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/4709c2e2-c242-4c9e-920b-0e4678a2d87f">
</p>

<h1 align="center">Granny</h1>

<h2>Intro</h2>
<p>In this machine we are going to be manouvering in the machine <strong>Granny</strong>. It's a Windows machine quite simmilar to the machine <strong>Grandpa</strong></p>

<h2>Walkthrough</h2>

<h3>Recon and ennumeration</h3>

<p>First things first, let's ennumerate the machine. In a first quick nmap scan we can check that we simply have a web page</p>
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/0a3b590f-a732-4f09-a2e9-1e8e7220d484">
</p>

<p>We can check that there is nothing, but looks like there is an ISS</p>
<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f1efbabf-57e5-49a9-a5e9-c788e445b7cd">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/13511608-fd5c-4385-bb95-141e4c50281c">
</p>

<p>By doing a more agressive scan we can see that, yeah, there is indeed a ISS, that has a WebDav, so let's check with <strong>davtest</strong> if it's vulnerable (it is as we can see :D )</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/1ead5a97-d0be-48b8-a0c8-b6155624c66f">
</p>

<p>Here i've tryed to upload something manually with <strong>Curl</strong> using a POST method, looks like it is possible, had to check in the browser too to double check it.
I tried using <strong>cadaver</strong> as well, I could join the session but for some reason it wouldn't let me upload a file using the tool itself, so I decided to work with Curl directly.</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/c2bf54ac-38af-49b9-a8a3-52f7fa21f1a8">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/63bc5fb7-b962-4837-9066-c65a87921375">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/fb397ad1-8b46-4289-9ad7-ca62ce1e8615">
</p>

<p>Keeping in mind that the file upload and the RCE is present, it was simply a matter of preparing a reverse shell and uploading it.</p>

<h2>Gaining access</h2>

<p>So first, let's prepare our reverse shell with <strong>msfvenom</strong>:</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/2958441f-5c34-4591-976b-bc80ba9a119f">
</p>

<p>Then we upload the payload to the machine. We are going to use <strong>--data-binary</strong>as an argument, becouse it will send the data as a raw binary. I've tryed to use <strong>-d</strong>before, but it corrupts
  the payload becouse it's urlencoding it</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/7047d54e-a465-4404-bc9c-a4625f410eb1">
</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/3c5a54ec-3782-478c-a408-65cc11857cb1">
</p>

<p>Now we rename test.txt by test.aspx to execute the shell</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/dffc95d7-fa02-4e47-a8ce-d4fc39b65d25">
</p>

<p>We prepare our terminal with <strong>Netcat</strong>, execute the payload, and we are in!</p>

<p align="center">
  <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/2b5b1645-f5eb-45e3-9ecb-af7836a35cd3">
</p>

<p>Here I tried going directly to user.txt but I realised that I don't have permissions to enter the user folder, so I just checked to see what I can do. I went to the root directory and noticed that there is a folder that is not common to have on a Windows system, and I saw that I had read and write permissions on it. I took a look at the privileges that my user had and saw that he is vulnerable to Token Theft. So here I knew what to do.</p>
