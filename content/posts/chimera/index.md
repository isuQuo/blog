---
title: "Operation Chimera 2019 - Digital Forensics Challenge"
date: 2020-02
---

Operation Chimera was a course created by Security Blue Team. It ran from the 1st of October to the 31st of November. Various modules were released during this time to give the user an insight into the world of security and the challenges that may lie ahead.

Security Blue Team can be found here: https://securityblue.team/

One of my favourite modules was the Digital Forensics module, which was definitely challenging, and provides a story for the user to follow. The challenge contains four flags that the user must find, in the format of x/4. After finding all of the flags, you must answer the given questions to receive a mark.

For the challenge, you are given an entirely fictional disk image. You must sift through the files in order to find out what malicious activity the user was up to.

The disk image directory tree is as follows:

```
.
├── Images
│   ├── desk stock img.mp3
│   ├── drupal 8 logo Stacked CMYK 300.png
│   ├── exploratory.jpg
│   ├── laptop.jpg
│   ├── office 2.jpg
│   ├── office pic1.jpg
│   ├── website-stock-photo.jpg
│   └── wireframe-design-guide.png
├── Payslips
│   └── readme
├── Saved Emails
│   ├── Form1.jpg
│   └── Website Update Report 01_10_2019.eml
├── WebDev work
│   ├── finished webpages
│   ├── Links.txt
│   ├── scan.xml
│   ├── to do list
│   ├── unfinished webpages
│   │   ├── Power Free Website Template - Free-CSS.com.zip
│   │   ├── templatemo_508_power
│   │   │   ├── css
│   │   │   │   ├── animate.css
│   │   │   │   ├── bootstrap.min.abc
│   │   │   │   ├── bootstrap.min.css
│   │   │   │   ├── font-awesome.css
│   │   │   │   ├── owl-carousel.css
│   │   │   │   ├── templatemo_misc.css
│   │   │   │   └── templatemo_style.css
│   │   │   ├── fonts
│   │   │   │   ├── flexslider-icon.eot
│   │   │   │   ├── flexslider-icon.svg
│   │   │   │   ├── flexslider-icon.ttf
│   │   │   │   ├── flexslider-icon.woff
│   │   │   │   ├── FontAwesome.otf
│   │   │   │   ├── fontawesome-webfont.eot
│   │   │   │   ├── fontawesome-webfont.svg
│   │   │   │   ├── fontawesome-webfont.ttf
│   │   │   │   └── fontawesome-webfont.woff
│   │   │   ├── img
│   │   │   │   ├── banner-bg.jpg
│   │   │   │   └── blog-post-1.jpg
│   │   │   ├── index.html
│   │   │   └── js
│   │   │       ├── bootstrap.js
│   │   │       └── main.js
│   │   └── to-do
│   ├── VERSION
│   └── WAF on OS Detection Nmap Scan.txt
└── Weekly Meeting Notes
    ├── Week 10
    │   ├── posidon.xml
    │   └── tue
    └── Week 9
        └── Friday
```

For each flag, the user is supposed to answer the following questions:

- Name of original file containing evidence
- Full file path (right-click > parent folder value + filename)
- File type
- Time found (Date - DD/MM/YYYY + Time)
- Method (+ commands) used to find file
- Explain how the information was hidden
- What information was included in the file?

I will be explaining my working that enabled me to find the flags and answer the given questions.

# Flag 1

As there are multiple directories to take a choosing from, we will go with the story in order to find the flags in order. To start off with, we will turn our attention to the "WebDev work" directory.

In this directory, we can see some interesting files, such as the "WAF on OS Detection Nmap Scan.txt" file. The file contains results from an Nmap scan on a DMZ web server for DicksonUnited.com. No open ports were found. We also see a "Scan.xml" file. Not much is interesting about this file, except that there is a comment at the top of the file that states it is a template for a local to local scan, approved by G.Burner on the 14th of June and conudtced by Price.T.

There are no files in the "finished webpages" directory. When taking a look in the "unfinished webpages" directory, we can see a number of sub directories and files, none of which are interesting to us. Using the file explorer to view the "to-do" sub directory shows that there is nothing in there. However, if we issue the command "ls -a", we can see a hidden file called ".a0415ns.zip". It is hidden because the filename is prepended with a period. We rename the file using the move command, which I have renamed to secret.zip.

Inside the archive is an "employeedump.txt" file. We cannot extract it as it is asking for a password. If we issue the command "fcrackzip -D -u -p /usr/share/wordlists/rockyou.txt secret.zip" to attempt to crack the zip archive, we find that the password is "vendy13031988".

Inside the "employeedump.txt" file, we find our first flag! It is labelled as "{Part 1 of 4}" at the top of the file. The file itself contains a csv list of PII employee data, 291 employees.

# Flag 2

Next, we take a look at the "Saved Emails" directory. Inside, we can see two files, "Form1.jpg" and a website update report e-mail, which only contains an attachment called "Form.jpg". It is a website uptime report sent by Price.T who is the Lead Web Developer for Dickson United.

We cannot open the .jpg file for some reason.

Using strings on Form.jpg, we see that it is infact a .png file. We also see a secret message embedded into the file that reads "Simon, I have usernames and passwords for the VPN. Still on my work PC. Don't want to risk e-mailing them just yet. When I do, the file is a .jpg image + password for extraction is password. Use steghide. Talk again soon.".

Interesting! What we're now looking for is a .jpg image that may contain further hidden information.

Let's head to the Images directory. Inside we can see a number of .jpg files to look at. Originally, I created a Python script that enumerated through all .jpg files on the disk and used steghide on them with the password "password". I found that the "laptop.jpg" file had a hidden "passwords.txt" file hidden within.

Using the command "steghide --extract -sf laptop.jpg -p password, we extract the passwords.txt file.

Inside the file contains our second flag at the top of the file, which reads {2 of 4}. The file itself contains 292 passwords. Coincidentally, we found 291 employees beforehand in our employeedump.txt file. The numbers are quite close.

# Flag 3

Next up, we'll take a look at the "Weekly Meeting Notes" directory.

Two sub directories, "Week 9" and "Week 10" contain more files within. There is nothing interesting in the "Week 9" folder. However, inside the "Week 10" folder, we can see a "posidon.xml" file. When attempting to open the .xml file, it will not open. Using the command "file" on the file, we see that it is infact a png file.

If we rename this file to a .png file, we get an image with our third flag. The image itself is an image of text which lists office locations, presumably the Dickson United offices. These locations may not be publicly known and may play a part for the malicious actor's plan.

# Flag 4

The fourth flag was admittedly hard to find. Looking through each file on the disk, I found a "bootstrap.css.abc" file in the "WebDev work/unfinished webpages/templatemp_508_power/css" directories.

Bootstrap files are css files, not abc files. Opening the file reveals the fourth flag in a multi-line comment at the top of the file. The comment also includes personal information for one of the employees. This information was planted here in case a forensic examiner found it, which in this case, we have, and it reveals that there is another employee carrying out malicious acts.

# Provide a brief conclusion on what information was being stolen, how it was hidden, and how the suspect was getting it out of the company:

Information being stolen includes a list of employees and their personal details (PII), a list of passwords which may be linked to the employees, and office locations which are supposedly locations of the suspect's current employer.

An e-mail was retrieved by the sender which was sent to the G-Mail address "otnacask18491@gmail.com". Attached to the e-mail was a file called "Form1.jpg". The addressee of the e-mail is a "Jonathan" and is meant to include a report for the website uptime. 

Further analysis of the attached file shows a hidden message. This hidden message is addressed to a "Simon" and let's the user know that the suspect has usernames and passwords for the VPN. The suspect will e-mail the information at a later date, but when he does, it will be in the form of a .jpg file. The suspect would like the user to use steghide to uncover the data with the password "password". 

A script was created to run steghide on all .jpg files found on the disk image with the provided password. It was revealed that an image named "laptop.jpg" contained the hidden information, a password list. 

At this point in time, we can assume the suspect is exfiltrating VPN usernames and passwords for the attacker to carry out their malicious deeds. 

As we still need to search for the employee data, we have come across a hidden, password-protected zip file. Using a zip cracking tool, we have uncovered the zip contains an employee dump. This confirms our suspicion that both employee data and passwords are being exfiltrated and potentially being used for malicious purposes. The employee dump also contains PII. 

Upon further analysis of the disk image, it was found that an incorrectly named file called 'posidon.xml', contained more information that may relate to the malicious activity. The file is actually a .png file, and has been renamed to conceal this information. The picture shows a list of office locations, which may be useful for the attacker. 

The suspect also appears to be working on a website, and within the files used to create the website, a "bootstrap.min.abc" was found. We belive this file may have been planted here intentionally. The information within the file details a 'Colin Andrews' and his relation to the exfiltration scheme. The suspect has listed Colin's personal information and has stated that this information is available in case Colin 'screws' the suspect. The suspect, knowing that he may be caught, has planted this evidence for us to inspect.