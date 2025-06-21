# 🌐 HTTP in Detail (TryHackMe Room)

## 1. 🧩 What is HTTP(S)?

* **HTTP**: *HyperText Transfer Protocol* — بروتوكول لنقل صفحات الإنترنت.
* **HTTPS**: النسخة الآمنة منه (*Secure*).

  * من ضمن التحدي، لو الرابط ملوش شهادة صحيحة بيظهر عليه قفل أحمر. **الكلمة السرية (flag)**: `THM{INVALID_HTTP_CERT}`

---

## 2. 📨 HTTP Requests & Responses

* الطلب (request) بيبدأ بسطر: `GET /path HTTP/1.1`
* السيرفر بيرد برد (response): `HTTP/1.1 200 OK`
* الهدر الهام: `Content-Length` — بيحدد طول البيانات حتى تعرف تكتمل الاستجابة.
* مثال فعلّي:

```http
GET / HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
```

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234
<html>…</html>
```

---

## 3. 🛠 HTTP Methods

| Method | Use Case                            |
| ------ | ----------------------------------- |
| GET    | جلب المعلومات (مثل قراءة مقال)      |
| POST   | إرسال بيانات جديدة (مثل تسجيل حساب) |
| PUT    | تحديث بيانات (مثل تغيير الإيميل)    |
| DELETE | حذف بيانات (مثل صورة أو مستخدم)     |

**أمثلة من التحدي:**

* إنشاء مستخدم → `POST`
* تحديث إيميل → `PUT`
* حذف صورة → `DELETE`
* قراءة مقال → `GET`

---

## 4. 📋 HTTP Status Codes

* **2xx (نجاح)**

  * `200 OK`
  * `201 Created` → إنشاء بيانات جديدة
* **3xx (تحويل)**
* **4xx (خطأ من العميل)**

  * `401 Unauthorized` → محتاج تسجيل دخول
  * `404 Not Found` → الصفحة مش موجودة
* **5xx (خطأ من السيرفر)**

  * `503 Service Unavailable` → السيرفر مش شغال أو في صيانة

---

## 5. 🧾 Headers

* **Request headers**:

  * `Host`: اسم الموقع
  * `User-Agent`: نوع المتصفح
  * `Content-Length`: طول البيانات المرسلة
* **Response headers**:

  * `Content-Type`: نوع البيانات (HTML, JSON, الخ)
  * `Set-Cookie`: يخزن كوكيز في جهازك
  * `Cache-Control`: يتحكم في الكاش

---

## 6. 🍪 Cookies

* عبارة عن بيانات صغيرة يخزنها المتصفح ← السيرفر يرسلها بواسطة هيدر `Set-Cookie`
* بعدها المتصفح يرسل الكوكيز دي مع كل طلب بعدين
* تستخدم للمصادقة والاحتفاظ بحالة الجلسة، لكن لو اتسرقت بتنفع للمهاجم عشان يدخل مكانك

---

## 7. ⚙️ عملي: Coded Requests & Flags

* `GET /room` → **`THM{YOU’RE_IN_THE_ROOM}`**
* `GET /blog?id=1` → **`THM{YOU_FOUND_THE_BLOG}`**
* `DELETE /user/1` → **`THM{USER_IS_DELETED}`**
* `PUT /user/2` مع `username=admin` → **`THM{USER_HAS_UPDATED}`**
* `POST /login` مع `username=thm&password=letmein` → **`THM{HTTP_REQUEST_MASTER}`**

**أمثلة الطلبات:**

```http
GET /room HTTP/1.1
Host: tryhackme.com

DELETE /user/1 HTTP/1.1
Host: tryhackme.com
Content-Length: 0

PUT /user/2 HTTP/1.1
Host: tryhackme.com
Content-Length: 14

username=admin
```

---

## 📝 الخلاصة

* اتعلمنا HTTP/HTTPS: إيه هما، إزاي بيشتغلوا
* شفنا محتوى الطلب والاستجابة، لهدر زي `Content-Length` و `Content-Type`
* شرحنا الطرق الشائعة (`GET`, `POST`, `PUT`, `DELETE`)
* فهمنا رموز الاستجابة (`200`, `201`, `401`, `404`, `503`)
* خدنا فكرة عن الهدرز والكوكيز
* خلّصنا برؤوس عملي ونزلنا flags خطوة بخطوة

---

**🔗 المرجع:** محتوى الغرفة "HTTP in Detail" في TryHackMe
