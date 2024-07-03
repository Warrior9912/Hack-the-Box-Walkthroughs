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
