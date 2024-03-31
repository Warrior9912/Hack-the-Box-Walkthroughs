# "Lesson" Challenge Walkthrough on Hack the Box

## Introduction

This document serves as a walkthrough for the "Lesson" challenge, an exclusive VIP challenge available on Hack the Box.

## Getting Started

1. **Downloading and Extracting Files**
    - Begin by downloading the challenge `.zip` file and extracting its contents. The extracted folder should contain the following:
    - ![Initial Setup](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/13a46930-9bcf-42b1-abcd-86da9792584d)

2. **Exploring the Contents**
    - Within the challenge folder, participants will find:
        - A directory of libraries.
        - The `main` executable file.
        - The source code in `main.c`.
        - A `README.txt` file providing details on how to connect via netcat. It's recommended to use split terminal view for efficiency.

## Analyzing the Binary

1. **Determining the Architecture**
    - Identify whether the binary is compiled for x86 or x64 architecture.
    - ![Determining Architecture](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d5845c92-2eff-419f-8072-adc404f01f17)

2. **Investigating Security Measures**
    - Use GDB to open the `main` executable and run `checksec` to examine the binary's security features.
    - ![Security Protections](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/74a23648-134f-4164-bece-e24c60f69092)

## Code Examination

1. **Identifying Key Strings**
    - Review `main.c` to identify critical strings that trigger specific functionality, such as administrative access.
    - ![Admin Login](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/99741287-8402-4075-8e2d-2f0228d943c4)

2. **Buffer Size Assessment**
    - Analyze the buffer size defined in the code to understand its limitations.
    - ![Buffer Length](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/9698a055-e998-4b23-9cd7-e2c8c185630d)

## Identifying Vulnerabilities

1. **Unreachable Code**
    - Determine if there are any functions, such as `under_construction()`, that are not called anywhere within the code.
    - ![Unreachable Function](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/f72a5d23-c823-47cc-b708-b1cce9256edf)

2. **Potential for Overflow**
    - Assess the use of `scanf()` for any vulnerabilities that might allow for buffer overflow.
    - ![Buffer Overflow Risk](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/23cb9e5b-b605-4ce8-822b-0a28e199d1af)

## Exploitation

1. **Triggering a Crash**
    - Explore input patterns that cause the program to crash, such as inputting a long string of characters.
   
2. **Accessing Hidden Functions**
    - Use debugging tools to locate and invoke any functions that are not directly accessible through normal execution paths.
    - ![Function Address](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/d1f53504-fe03-4516-8f85-9643cb2b7e3b)

## Conclusion

Following the guidance provided in this walkthrough will lead participants to successfully complete the challenge and capture the flag. We wish all participants the best of luck.

![Challenge Completed](https://github.com/Warrior9912/Hack-the-Box-Walkthroughs/assets/34217036/1fcf0865-efef-4392-9212-d25a5d931156)


