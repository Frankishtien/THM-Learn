# OWASP Top 10

<details>
  <summary>[Severity 1] Command Injection Practical</summary>

> What strange text file is in the website root directory?

```
$ ls
```

```
css
drpepper.txt
evilshell.php
index.php
js 
```

> How many non-root/non-service/non-daemon users are there?  how to know that in linux

```
getent passwd | awk -F: '$3 >= 1000 && $7 != "/usr/sbin/nologin" && $7 != "/bin/false" && $1 != "nobody" {print $1}' | wc -l
```

```
0
```

> What user is this app running as?

```
$ whoami
```

```
www-data 
```

> What is the user's shell set as?

```
getent passwd www-data | cut -d: -f7
```

> What version of Ubuntu is running?

```
$ cat /etc/os-release
```

```
18.04.4 
```

> Print out the MOTD.  What favorite beverage is shown?

```
$ cat drpepper.txt
```

```
I love Dr Pepper 
```

</details>


<details>
   <summary>[Severity 2] Broken Authentication Practical</summary>


> What is the flag that you found in darren's account?

- if you try to rigister using ``darren``
  - user already exist
- now register using ``darren   `` and login you will found the flag

> do same to ``auther``





  
</details>



<details>
  <summary>[Severity 3] Sensitive Data Exposure (Challenge)</summary>

![image](https://github.com/user-attachments/assets/3998c50c-5e62-4c36-9e2d-d57e48981a08)


![image](https://github.com/user-attachments/assets/c32a4e6e-078b-4396-a759-683fd515eff1)


![image](https://github.com/user-attachments/assets/8f486f43-09ad-414f-b40a-2c12b57fd2de)

```
��������tablesessionssessionsCREATE TABLE sessions(
sessionID TEXT NOT NULL UNIQUE,
userID TEXT NOT NULL,
expiry INT NOT NULL,
PRIMARY KEY (sessionID))/Cindexsqlite_autoindex_sessions_1sessions�*�'�-tableusersusersCREATE TABLE users(
userID TEXT NOT NULL UNIQUE,
username TEXT NOT NULL UNIQUE,
password TEXT NOT NULL,
admin INT NOT NULL,
�h��GKHMMEY(user23023b67a32488588db1e28579ced7ecBobad0234829205b9033196ba818f7a872bJM4e8423b514eef575394ff78caed3254dAlice268b38ca7b84f44fa0a6cdc86e6301e0JMM   4413096d9c933359b898b6202288a650admin6eea9b7ef19179a06954edd0f6c05ceb
����mH%%$M23023b67a32488588db1e28579ced7ec$M4e8423b514eef575394ff78caed3254d#M  4413096d9c933359b898b6202288a650
�J����  Bob     Alice   admin
�$                              
```
> admin password hash is

```
6eea9b7ef19179a06954edd0f6c05ceb
```

> crack it on crackstation

```
qwertyuiop
```

```
THM{Yzc2YjdkMjE5N2VjMzNhOTE3NjdiMjdl}
```


</details>



<details>
    <summary>[Severity 4 XML External Entity - eXtensible Markup Language</summary>


   
</details>
























