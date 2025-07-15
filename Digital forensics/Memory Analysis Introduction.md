# Memory Analysis Introduction




<details>
  <summary>Volatile Memory</summary>


# 🧠 Volatile Memory & RAM Forensics

## 📌 ما هي الذاكرة المتطايرة (Volatile Memory)؟

- الذاكرة المتطايرة تشير إلى البيانات المخزنة مؤقتًا أثناء تشغيل الجهاز، وتفقد عند إغلاقه أو إعادة تشغيله.
- أشهر أمثلتها: **الـ RAM (Random Access Memory)**.
- تحتوي على:
  - ملفات مفتوحة
  - العمليات الجارية
  - محتوى مشفر (قد يشمل مفاتيح التشفير)

✅ لذلك تعد من أولويات التحليل الجنائي الرقمي لأنها تزول بمجرد إيقاف الجهاز.

---

## 🏗️ Memory Hierarchy (تسلسل الذاكرة)


<img width="502" height="783" alt="image" src="https://github.com/user-attachments/assets/316b47ea-f447-4c1d-ad10-2ff66a18de4a" />





| المستوى        | السرعة | السعة | أمثلة                            |
|----------------|--------|-------|----------------------------------|
| CPU Registers  | 🔝 عالية | 🧠 صغيرة | سجلات المعالج                   |
| CPU Cache      | 🔝 عالية | 🧠 صغيرة | ذاكرة التخزين المؤقت            |
| RAM            | ✅ متوسطة | ✅ متوسطة | ذاكرة الوصول العشوائي           |
| Disk Storage   | ⬇️ أبطأ  | 📦 كبيرة | أقراص التخزين (HDD, SSD)        |

### 💡 Virtual Memory (الذاكرة الافتراضية)

<img width="947" height="661" alt="image" src="https://github.com/user-attachments/assets/c9691a95-4254-4862-aed1-3373c810773a" />




- نظام يستخدم **عناوين افتراضية** يتم ربطها برام أو **ملف Swap** على القرص.
- يساعد على تشغيل عدد أكبر من العمليات مما يمكن أن تتحمله RAM وحدها.

> ⚠️ بعض الأدلة قد تكون في RAM أو في ملفات Swap (على القرص).

---

## 🧬 RAM Structure (هيكلية RAM)

تنقسم إلى:

### 🔧 Kernel Space
- للـ OS والخدمات المنخفضة المستوى (مثال: التعريفات).

### 👤 User Space
- لكل عملية يطلقها المستخدم.
- يحتوي على:
  - **Stack**: البيانات المؤقتة مثل العناوين والقيم الراجعة.
  - **Heap**: الكائنات والذاكرة الديناميكية (مثل المفاتيح المشفرة).
  - **Text**: كود البرنامج التنفيذي.
  - **Data Sections**: متغيرات عامة وبيانات أخرى.

> 🎯 مثال: قد نجد مفاتيح التشفير في الـ Heap، أو أوامر Shell في الـ Stack.



<img width="1012" height="688" alt="image" src="https://github.com/user-attachments/assets/a16c4cb3-3531-40c9-90e3-4cfd292a5f69" />



---

## 🧪 لماذا تعتبر RAM مهمة للمحققين الجنائيين؟

تحليل RAM يعطي **صورة لحظية** لما كان يحدث على الجهاز، مثل:

- العمليات النشطة والكودات المحملة
- الاتصالات الشبكية المفتوحة
- المستخدمين المتصلين حاليًا
- محتوى مشفر مفكوك التشفير
- كود ضار محقون أو Fileless Malware

> 🛑 هذه المعلومات **تختفي بعد إيقاف التشغيل**، لذا يجب التقاطها مبكرًا أثناء التحقيق.

---

## 🎯 متى نستخدم Memory Forensics؟

- أثناء **الاستجابة للحوادث** (Incident Response).
- في حالات:
  - تحليل البرامج الضارة
  - اكتشاف البرمجيات الخبيثة غير المكتوبة على القرص
  - سرقة كلمات المرور أو الجلسات

---

## 🧰 أدوات شهيرة لتحليل الذاكرة:

- **Volatility**
- **Redline**
- **Rekall**

---



<img width="1643" height="632" alt="image" src="https://github.com/user-attachments/assets/ee1eede0-ab64-4885-9296-fec1e31c350f" />


  
</details>







<details>
  <summary>Memory Dumps</summary>

# 🧠 Memory Dump in Forensics

## 🧾 ما هو الـ Memory Dump؟

- هو لقطة (Snapshot) لذاكرة الـ RAM في لحظة معينة.
- يحتوي على:
  - العمليات الجارية
  - الجلسات النشطة
  - الأنشطة الشبكية
  - أحيانًا: كلمات مرور ومفاتيح مشفرة

> 🎯 يستخدم في التحليل الجنائي، التحقيقات في البرمجيات الخبيثة، وعمليات Threat Hunting.

---

## 🎯 لماذا Memory Dump مهم؟

- أدوات مثل **Mimikatz** تستخدمه لاستخراج بيانات اعتماد (Credentials) من الذاكرة.
- يساعد المحللين على فهم **ما الذي كان يحدث** على النظام في وقت الالتقاط.
- يمكن للمهاجمين استخدام تقنيات لا تترك آثارًا على القرص، لذا **RAM analysis هو الخيار الوحيد** لرصدهم.

---

## 🛠️ كيفية إنشاء Memory Dump

### 🪟 Windows:

- أدوات:
  - Crash Dumps (افتراضيًا في `%SystemRoot%\MEMORY.DMP`)
  - Hibernation File (`hiberfil.sys`)
  - Sysinternals RAMMap
  - WinPmem, FTK Imager
- يمكن أخذ Dump كامل أو عملية محددة فقط.

### 🐧 Linux & macOS:

- أدوات:
  - **LiME (Linux Memory Extractor)**
  - **dd** مع الوصول إلى `/dev/mem` أو `/proc/kcore`

> 🔐 بعض الأنظمة تتطلب صلاحيات عالية أو تعطيل الحماية للوصول إلى هذه الملفات.

---

## 🧰 أنواع الـ Memory Dumps

| النوع               | الشرح                                                                          |
|---------------------|----------------------------------------------------------------------------------|
| Full Memory Dump    | يلتقط كل محتويات الـ RAM (User + Kernel)                                       |
| Process Dump        | يلتقط عملية واحدة (مفيد في الهندسة العكسية)                                    |
| Pagefile/Swap       | يلتقط بيانات تم تفريغها إلى القرص (مثل `pagefile.sys` في Windows)              |
| Hibernation File    | يلتقط محتويات RAM محفوظة عند دخول الجهاز وضع الإسبات (hiberfil.sys)              |

---

## ⚠️ تحديات الحصول على Memory Dump نظيف

المهاجمون قد يستخدمون تقنيات **Anti-Forensics** مثل:

### 🔍 تقنيات الإخفاء والتشويش:

| التقنية                       | الوصف                                                                 |
|-------------------------------|------------------------------------------------------------------------|
| Hidden Modules                | إزالة البرمجية الخبيثة من قائمة العمليات (Unlinked)                  |
| DKOM (Kernel Manipulation)    | تعديل بيانات الكيرنل لإخفاء العمليات والسواقات                        |
| Code Injection                | حقن كود خبيث في عمليات شرعية مثل `explorer.exe`                      |
| Memory Patching               | تعديل محتوى الذاكرة أو APIs لتضليل أدوات التحليل                     |
| API Hooking                  | اعتراض نتائج الدوال المهمة مثل `ReadProcessMemory`                     |
| Encrypted/Packed Payloads     | حملات مشفرة أو مضغوطة يتم فكها فقط أثناء التنفيذ                     |
| Trigger-based Execution       | لا يتم تشغيل الكود الخبيث إلا عند تحقق شروط معينة                     |

> ❗ هذه الأساليب تتطلب تحليل متقدم باستخدام أدوات مثل: **memory carving**, **kernel-level inspection**, و**behavior-based analysis**.

---

## 🛡️ ملاحظات مهمة

- بعض الأدلة لا يمكن الوصول لها إلا في RAM.
- Dump نظيف = دليل قوي.
- أدوات مثل Volatility يمكنها كشف الكثير من هذه التقنيات، لكنها تحتاج إلى تحليل متقدم.

---

ه



<img width="1705" height="735" alt="image" src="https://github.com/user-attachments/assets/9114cebc-2bc7-4044-9c2b-c2f34a13ef46" />


  
</details>





<details>
  <summary>Memory Analysis Attack Fingerprints</summary>



# 🧠 Memory-Based Threat Indicators in DFIR

تحليل الذاكرة (Memory Analysis) أداة قوية للكشف عن التهديدات التي لا تترك آثارًا على القرص. فهي تكشف الأنشطة التي تحدث "الآن"، مما يجعلها مثالية لاكتشاف الهجمات النشطة أو الأخيرة.

---

## 🧩 أهم الآثار (Artifacts) التي يتم البحث عنها في الذاكرة:

- 🔸 **Processes مشبوهة** لا توجد لها ملفات على القرص.
- 🔸 **DLL Injection**: حقن كود ضار في ذاكرة عملية شرعية.
- 🔸 **Process Hollowing**: استبدال ذاكرة عملية موثوقة بكود خبيث.
- 🔸 **API Hooking**: اعتراض واستبدال دوال النظام لإخفاء السلوك الخبيث.
- 🔸 **Rootkits**: تعمل في kernel space وتخفي ملفات أو اتصالات أو عمليات.

> ✳️ الأدوات الجنائية يمكنها اكتشاف:
> - مناطق ذاكرة غير اعتيادية
> - رؤوس PE غير متطابقة
> - تنفيذ كود في ذاكرة قابلة للكتابة (Writable)

---

## 🎯 أمثلة لهجمات يمكن كشفها من خلال تحليل الذاكرة:

### 🔐 T1003 - Credential Access

- استخراج بيانات اعتماد (credentials) مباشرة من الذاكرة.
- أدوات مثل **Mimikatz** تبرز هنا.

---

### 🌐 T1071 - Application Layer Protocol: C2

- **Malware بدون ملفات (Fileless)** تتواصل مع خوادم تحكم عبر HTTP/HTTPS/DNS.
- قد نجد:
  - **IP Addresses**
  - **Beacon Patterns**
  - **Decrypted Configs** في الذاكرة فقط.

---

### 📜 T1086 - In-Memory Script Execution

- PowerShell أو Python تُستخدم لتشغيل سكريبتات **مباشرة في RAM**.
- تحليل الذاكرة قد يكشف:
  - سكريبتات كاملة
  - أوامر مشفرة (Encoded Commands)
  - سجلات في عملية PowerShell

---

## 🛠️ تقنيات Persistence قابلة للكشف في RAM

### ⏰ T1053.005 - Scheduled Tasks

- مراقبة عمليات مثل `schtasks.exe`
- فحص الذاكرة للعثور على أسماء Tasks أو مسارات تنفيذ مشبوهة

---

### 🧱 T1543.003 - Malicious Windows Services

- الخدمات قد تعمل داخل `services.exe`
- تحليل الذاكرة يمكنه اكتشاف أسماء غير معتادة أو ملفات تنفيذية ضارة

---

### 🪟 T1547.001 - Registry Run Keys

- مفاتيح مثل:

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```


يتم استخدامها لتشغيل malware عند بدء النظام
- يمكن العثور عليها داخل سجلات Registry المحفوظة مؤقتًا في RAM

---

## 🧭 Lateral Movement (حركة جانبية) عبر الذاكرة

### 🔄 T1021.002 - SMB / PsExec

- **PsExec** يسمح بتنفيذ أوامر على أجهزة أخرى.
- مؤشرات في الذاكرة:
- أسماء Services
- أوامر تنفيذ عن بعد

---

### 🖥️ T1021.006 - WinRM Remoting

- خدمة `wsmprovhost.exe` تنفذ PowerShell عبر الشبكة.
- فحص الذاكرة قد يكشف عن تفاصيل جلسات التحكم عن بُعد.

---

### 💻 T1059.001 - Remote PowerShell Execution

- غالبًا ما تُستخدم PowerShell لأوامر عن بعد.
- المهاجمون يستخدمون أوامر مشفرة بـ Base64 أو مشوشة (Obfuscated)

---

### 🧾 T1047 - WMI Execution

- أوامر مثل:


```
wmic process call create
```

تُستخدم لإنشاء عمليات عن بُعد.
- يمكن أن نجدها كسلاسل أو Class References في الذاكرة.

---

## ✅ لماذا يعتبر تحليل الذاكرة مهم؟

- يكشف أنشطة غير مرئية في القرص.
- يساعد في التحقيقات الحية (Live Forensics).
- ضروري في حالة البرمجيات بدون ملفات (Fileless Malware).

---



<img width="1652" height="716" alt="image" src="https://github.com/user-attachments/assets/3ed2e3ef-ba9b-4d96-aa75-423d54adccc3" />



  
</details>









<details>
  <summary>Practical</summary>

<img width="933" height="826" alt="image" src="https://github.com/user-attachments/assets/0b89af65-3d82-428f-a5bd-915ab59d336d" />


```
THM{m3mory_analyst_g00d_job}
```

  
</details>





















