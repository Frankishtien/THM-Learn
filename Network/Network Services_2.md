# Network Services_2

<details>
  <summary>NFS</summary>

<details>
  <summary>understanding</summary>

# 🧠 ما هو NFS؟

**NFS** هو بروتوكول يسمح لجهاز بمشاركة ملفات أو فولدرات مع جهاز آخر على نفس الشبكة.
على سبيل المثال، إذا كان لديك سيرفر يحتوي على ملفات، يمكنك من جهاز آخر "تركيب" (mount) هذا الفولدر ورؤية الملفات وكأنها موجودة على جهازك.

---

# 🛠️ كيف يعمل NFS؟

1. **العميل (Client)** يطلب تركيب (mount) فولدر من السيرفر.
2. **السيرفر** يتحقق مما إذا كان العميل يملك صلاحيات.
3. إذا كانت الصلاحيات صحيحة، يقوم السيرفر بإرسال **file handle**.
4. عندما يطلب العميل فتح ملف، يرسل **RPC (Remote Procedure Call)** إلى **NFSD** (خدمة NFS على السيرفر).

### يتضمن الطلب:

* File Handle
* اسم الملف
* User ID و Group ID (لتحديد الصلاحيات)

بعدها يقرر السيرفر ما إذا كان هذا المستخدم يملك صلاحية القراءة أو الكتابة على الملف.

---

# 💻 من يمكنه استخدام NFS؟

يدعم NFS أنظمة تشغيل مختلفة، مثل:

* Linux (لينكس)
* Windows (ويندوز – خاصة Windows Server)
* macOS (ماك)
* UNIX (يونيكس)

مما يسمح بنقل الملفات بين الأنظمة بسهولة وبدون مشاكل.

---

# 📚 مصادر للتعمق:

* [Oracle Docs](https://docs.oracle.com/...)
* [Datto](https://www.datto.com/...)
* [Arch Wiki](https://wiki.archlinux.org/...)

---

# 🎯 NFS في مجال الهاكينج

في اختبارات الاختراق (مثل CTFs)، قد تجد أن السيرفر يشارك مجلدات عبر NFS، وغالبًا ما يكون الإعداد ضعيف الأمان:

* يمكنك العثور على ملفات تحتوي على كلمات مرور.
* أو تركيب المجلد بصلاحيات أعلى من المفترض.
* أو حتى الكتابة على السيرفر، مما قد يؤدي إلى:

  * **Privilege Escalation** (تصعيد الصلاحيات)
  * **Persistence** (الاحتفاظ بالوصول)

  
</details>
---
<details>
  <summary>enumerating</summary>


  
</details>
---
<details>
  <summary>exploiting</summary>
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------

<details>
  <summary>SMTP</summary>

<details>
  <summary>understanding</summary>
</details>
---
<details>
  <summary>enumerating</summary>
</details>
---
<details>
  <summary>exploiting</summary>
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------

<details>
  <summary>MYsql</summary>

<details>
  <summary>understanding</summary>
</details>
---
<details>
  <summary>enumerating</summary>
</details>
---
<details>
  <summary>exploiting</summary>
</details>


</details>

-------------------------------------------------------------------------------------------------------------------------------------------
