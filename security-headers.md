# security-headers


from : https://tryhackme.com/room/webapplicationbasics

---

## **``Content-Security-Policy (CSP)``**

> وظيفته: يمنع تحميل سكريبتات أو ملفات من مواقع مش موثوقة


``example``

```
Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.tryhackme.com; style-src 'self'
```


- ``default-src 'self'``: كل الموارد لازم تيجي من نفس الدومين (Self).

- ``script-src 'self' https://cdn.tryhackme.com``: السكريبتات مسموح تيجي من نفس الموقع أو من CDN معين.

- ``style-src 'self'``: ملفات الـ CSS لازم تيجي من نفس الدومين.

🎯 الهدف؟ تقليل احتمالية تحميل كود ضار من مصادر خارجية (بيحمي من XSS).





---

## **``Strict-Transport-Security (HSTS)``**


> وظيفته: يجبر المتصفح يستخدم HTTPS دايمًا بدل HTTP.

``EXAMPLE``

```
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
```


- ``max-age=63072000``: المدة اللي المتصفح يفتكر فيها المعلومة دي (بالثواني) = سنتين.

- ``includeSubDomains``: كمان طبق ده على كل السب دومينات.

- ``preload``: الموقع هيتسجل في قائمة مسبقة في المتصفحات (قبل ما المستخدم يزوره أصلاً).

🎯 الهدف؟ يمنع أي محاولة لجعل الزائر يتصل بالموقع عن طريق HTTP غير مؤمن.




---

## **``X-Content-Type-Options``**

> وظيفته: يمنع المتصفح يخمّن نوع الملف لو الـ header مش واضح.


```
X-Content-Type-Options: nosniff
```

- ``nosniff``: المتصفح مايحاولش يتخمن نوع الملف، يلتزم بس بـ Content-Type.


🎯 الهدف؟ يمنع المتصفح يشغل سكريبتات ضارة على إنها ملفات عادية.




---

## **``Referrer-Policy``**

> وظيفته: يتحكم في البيانات اللي بتتبعت مع الروابط لما المستخدم يضغط عليها.


```
Referrer-Policy: no-referrer
Referrer-Policy: same-origin
Referrer-Policy: strict-origin
Referrer-Policy: strict-origin-when-cross-origin
```

 Policy | بيكشف إيه؟
------------|----------------------------
no-referrer | مابيقولش أي حاجة عن الصفحة الأصلية
same-origin | يبعث الريفرر بس لو الصفحة اللي رايح لها في نفس الدومين
strict-origin | يبعث اسم الدومين فقط (بدون المسار)، بس لما يكون الانتقال من HTTPS لـ HTTPS
strict-origin-when-cross-origin | في نفس الدومين → يبعث كل الرابط، غير كده → بس اسم الدومين



الهدف؟ حماية خصوصية المستخدم، وتقليل معلومات ممكن يستغلها هكرز.


---


 **``أدوات مساعدة:``**
جرب الموقع ده 👉 ``https://securityheaders.io``
واكتب أي دومين (حتى موقعك لو عندك)، وهيقولك إن كان بيستخدم الهيدرز دي ولا لأ، ويدي درجة حماية كمان! 🔍💯


----


**``الخلاصه``**

Header | وظيفته الأساسية
--------|-------------------------------------------------
Content-Security-Policy | يمنع تحميل محتوى من مواقع غير موثوقة (XSS)
Strict-Transport-Security | يجبر الاتصال الآمن (HTTPS) ويحمي من Downgrade Attacks
X-Content-Type-Options | يمنع Guessing لمحتوى الملف (يحمي من بعض أنواع XSS)
Referrer-Policy | يتحكم في البيانات اللي بتتبعت لما المستخدم ينتقل بين صفحات
















