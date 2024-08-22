# GetSimple!
--------------------------------------------------
![image](https://github.com/user-attachments/assets/98fdba4f-c132-4a1e-8cef-820c139e3de4)
Nohting interesting in view source

For this box, I first run nmap scan with the following command

  ![image](https://github.com/user-attachments/assets/2b30bd33-7bd9-41b8-beee-3f5338e78e29)
  
The reason I used the parameters in the picture are follows:
  1. sudo = some commands need sudo priveleges
  2. -sV = service version detection
  3. -sC = nmap script to try out know vulns
  5. -sS = 
  6. -O = operating system detection
  7. -v =

Side by side I did directory enumeration with dirbuster and found /admin with status code 301 and moved to /admin/.
Visiting /admin takes us to /admin/index.php and has a username and passowrd field.
Upon intial try, it is not vulnerable to SQLi. Also, none of the website word contains password. But using common password admin with username admin, it logged in.
![image](https://github.com/user-attachments/assets/3fed0e17-3bec-47ad-8dda-cbb4f96821c5)
roaming through the website, there is no way to upload any files, /upload.php only lists uploaded files but no way to upload. Therefore, the site is not vulnerable to File upoad.
But the site contains php scripts for themes. And those can be edited by admin. Therefore we put our own php reverse shell code into functions.php file of the theme. accessing it throu URI, we get a reverse shell.
we grabbed our user.txt.
running   sudo -l  we see that user www can access /usr/bin/php without any password as a root. But we need a php file to evlevate with code. from the system, it does not allow www to add any new php file. But we luckily can add our privelege escalatoin php code into anohter php file of the theme editing it form admin panel.
    `<?php
system('/bin/bash');
?>
`
`
sudo /usr/bin/php /path/to/your/script.php
`
After we replace with our own code, and then running the php file from the system, we are elevated and we grabbed our root.txt
![image](https://github.com/user-attachments/assets/c35fe29d-2b3c-4ffc-b631-d789c36b8aaf)
