In this walkthrough we are gonna be matching the challenge "Lesson", it is a VIP challenge.

First of all we download the .zip file and unzip it as we see in this image:

![1](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/13a46930-9bcf-42b1-abcd-86da9792584d)

Then, as we just get the directory "challenge" and we go inside of it, we see 4 things, a directory that has some libraries, a compiled program called main, a C file called also main, and a README.txt. As we can read on the
README.txt we see that to do this practice we have to connect with netcat to the IP that the machine gave when we start it. At this point I recomend to have two split terminals so we can manipulate the files with one and answer the questions that we are gonna se with the other.

As we see here, to see if the program is x86 or x64 bits we just run the binary:

![2](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/3fdaf77e-00c5-4c41-b0de-ebdd869e9716)

Getting this hint, we see that the binary it's x64 bits: 

![2 2](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d5845c92-2eff-419f-8072-adc404f01f17)

