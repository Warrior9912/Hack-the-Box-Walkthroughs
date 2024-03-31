In this walkthrough we are gonna be matching the challenge "Lesson", it is a VIP challenge.

First of all we download the .zip file and unzip it as we see in this image:

![1](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/13a46930-9bcf-42b1-abcd-86da9792584d)

Then, as we just get the directory "challenge" and we go inside of it, we see 4 things, a directory that has some libraries, a compiled program called main, a C file called also main, and a README.txt. As we can read on the
README.txt we see that to do this practice we have to connect with netcat to the IP that the machine gave when we start it. At this point I recomend to have two split terminals so we can manipulate the files with one and answer the questions that we are gonna se with the other.

As we see here, to see if the program is x86 or x64 bits we just run the binary:

![2](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/3fdaf77e-00c5-4c41-b0de-ebdd869e9716)

Getting this hint, we see that the binary it's x64 bits: 

![2 2](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d5845c92-2eff-419f-8072-adc404f01f17)

We got the first question, now we face a second one, the binary tells us to run:
<code>gdb ./main</code>

When we get this it runs an instance where we have to run the comand 'checsec' to see which protection is enabled, as we can see only NX is enabled

![2 7](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/74a23648-134f-4164-bece-e24c60f69092)

And we got our second question done! For the third one we have to check the inside of main.c, we can easily do this with:

<code>cat main.c</cat>

so as we can see in this image, we get that the input to print "Welcome Admin!" is "admin" or "AdMiN" or "ADMIN".

![3](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/99741287-8402-4075-8e2d-2f0228d943c4)

We are now in the fourth question now, which asks us what is the length of the "name" buffer, it gives us the ansewer on the same hint, 0x20 in HEX but if we want to get it in DEC the answer is 32:

![4](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/9698a055-e998-4b23-9cd7-e2c8c185630d)

Getting into the fifth question, the binary tells us which is the function that is never called, as we can see on the code, the function "under_construction()" is never called:

![5](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f72a5d23-c823-47cc-b708-b1cce9256edf)

Next one, we are close to finish. The sixth question asks us to writte which is the standard function that could trigger a Buffer Overflow. It is scanf() becouse it reads from the standard input until a newline character or EOF (End Of File) is encountered without checking the size of the input against the buffer's capacity.

![6](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/23cb9e5b-b605-4ce8-822b-0a28e199d1af)

For the next question, the binary tells us after how many bites does the Segmentation Fault occurs. As we could see, it happens after writting 40 'A'.

The eigth and last question asks us which is the address of "under_construction()". We can see this in the debugger by running:

<code>p under_construction</code>

![8](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d1f53504-fe03-4516-8f85-9643cb2b7e3b)

And we got our flag!

![9](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/1fcf0865-efef-4392-9212-d25a5d931156)

