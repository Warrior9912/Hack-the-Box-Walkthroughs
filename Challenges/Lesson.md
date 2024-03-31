<h1 align="center">Walkthrough: "Lesson" Challenge on Hack the Box</h1>

<h2>Introduction</h2>
<p>Welcome to the walkthrough for the "Lesson" challenge, a VIP-level challenge on Hack the Box. This guide aims to navigate you through the challenge, providing step-by-step instructions and insights to successfully complete it.</p>

<h2>Getting Started</h2>
<ol>
    <li>
        <strong>Download and Unzip the Challenge File</strong>
        <p>Begin by downloading the challenge <code>.zip</code> file. Once downloaded, unzip it to reveal its contents. The directory structure and initial setup can be seen below:</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/13a46930-9bcf-42b1-abcd-86da9792584d" alt="Initial Setup">
        </p>
    </li>
    <li>
        <strong>Exploring the Challenge Directory</strong>
        <p>Inside the "challenge" directory, you will find several key components:</p>
        <ul>
            <li>A directory containing various libraries.</li>
            <li>A compiled <code>main</code> program.</li>
            <li>The source code file <code>main.c</code>.</li>
            <li>A <code>README.txt</code> file which provides further instructions on connecting to the challenge IP via netcat. It's advisable to use split terminals for file manipulation and interaction with the challenge.</li>
        </ul>
    </li>
</ol>

<h2>Analyzing the Binary</h2>
<ol>
    <li>
        <strong>Determining the Architecture</strong>
        <p>To check whether the binary is x86 or x64, execute the binary. The architecture will influence our approach to solving the challenge.</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d5845c92-2eff-419f-8072-adc404f01f17" alt="Determining Architecture">
        </p>
    </li>
    <li>
        <strong>Inspecting Security Protections</strong>
        <p>Use the <code>gdb ./main</code> command to run the binary within GDB, then execute <code>checksec</code> to identify which protections are enabled. In this case, only NX is active.</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/74a23648-134f-4164-bece-e24c60f69092" alt="Security Protections">
        </p>
    </li>
</ol>

<h2>Diving into the Code</h2>
<ol>
    <li>
        <strong>Identifying the Admin Login</strong>
        <p>By examining <code>main.c</code>, we find the inputs that trigger the "Welcome Admin!" response. These inputs include "admin", "AdMiN", and "ADMIN".</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/99741287-8402-4075-8e2d-2f0228d943c4" alt="Admin Login">
        </p>
    </li>
    <li>
        <strong>Understanding Buffer Length</strong>
        <p>The length of the "name" buffer is given as 0x20 in hexadecimal, which equates to 32 in decimal.</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/9698a055-e998-4b23-9cd7-e2c8c185630d" alt="Buffer Length">
        </p>
    </li>
</ol>

<h2>Identifying Vulnerabilities</h2>
<ol>
    <li>
        <strong>Unreachable Function</strong>
        <p>The <code>under_construction()</code> function, as indicated by the code, is never called.</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f72a5d23-c823-47cc-b708-b1cce9256edf" alt="Unreachable Function">
        </p>
    </li>
    <li>
        <strong>Potential for Buffer Overflow</strong>
        <p>The standard function that could lead to a buffer overflow is identified as <code>scanf()</code>, due to its lack of input size verification.</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/23cb9e5b-b605-4ce8-822b-0a28e199d1af" alt="Buffer Overflow Risk">
        </p>
    </li>
</ol>

<h2>Exploiting the Vulnerability</h2>
<ol>
    <li>
        <strong>Triggering a Segmentation Fault</strong>
        <p>A segmentation fault occurs after inputting 40 'A' characters, indicating a potential buffer overflow point.</p>
    </li>
    <li>
        <strong>Locating the Function Address</strong>
        <p>To find the address of <code>under_construction()</code>, use <code>p under_construction</code> in GDB.</p>
        <p align="center">
            <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d1f53504-fe03-4516-8f85-9643cb2b7e3b" alt="Function Address">
        </p>
    </li>
</ol>

<h2>Conclusion</h2>
<p>Successfully exploiting these vulnerabilities and following the steps carefully will lead you to the flag and complete the challenge.</p>
<p align="center">
    <img src="https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/1fcf0865-efef-4392-9212-d25a5d931156" alt="Challenge Completed">
</p>


