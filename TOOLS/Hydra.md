# Hydra


# Hydra - Password Brute Forcing Tool

## ما هي Hydra؟
Hydra هي أداة brute force لاختراق كلمات المرور عبر الإنترنت بسرعة، وتستخدم لتخمين كلمات مرور الدخول على أنظمة وخدمات متعددة مثل SSH وFTP وWeb Forms وغيرها.

تساعدك Hydra على تجربة قائمة كلمات مرور كبيرة (wordlist) بشكل تلقائي لمعرفة كلمة المرور الصحيحة على خدمة معينة.

## البروتوكولات المدعومة (أمثلة)
- SSH (v1, v2)
- FTP
- HTTP-GET, HTTP-POST, HTTP-FORM-GET, HTTP-FORM-POST
- SMB, SMTP, SNMP, Telnet
- MySQL, MS-SQL, Oracle, PostgreSQL
- RDP, VNC, SIP, LDAP، وأكثر من 50 بروتوكول

## أهمية استخدام كلمات مرور قوية
- كلمات المرور الشائعة أو القصيرة تكون عرضة للاختراق عبر الهجمات بالقوائم.
- أجهزة مثل كاميرات المراقبة وبرامج الويب تستخدم عادة بيانات افتراضية سهلة، يجب تغييرها فورًا.

## تثبيت Hydra
- تأتي مثبتة مسبقاً على Kali Linux و AttackBox في TryHackMe.
- على توزيعات أخرى:
  - Ubuntu/Debian: `sudo apt install hydra`
  - Fedora: `sudo dnf install hydra`
- يمكن تنزيلها من [مستودع THC-Hydra](https://github.com/vanhauser-thc/thc-hydra).

## الأوامر الأساسية لاستخدام Hydra

### 1. تخمين كلمة مرور FTP
```
hydra -l user -P passlist.txt ftp://MACHINE_IP
```
- `-l user`: اسم المستخدم.
- `-P passlist.txt`: ملف كلمات المرور.

### 2. تخمين كلمة مرور SSH
```
hydra -l <username> -P <full_path_to_passlist> -t 4 ssh://MACHINE_IP
```
- `-t 4`: عدد الخيوط (threads) لزيادة سرعة التخمين.
- مثال:
```
hydra -l root -P passwords.txt -t 4 ssh://10.10.10.10
```

### 3. تخمين كلمة مرور فورم ويب POST
```
hydra -l <username> -P <wordlist> MACHINE_IP http-post-form "/login.php:username=^USER^&password=^PASS^:F=Login failed" -V
```
- `http-post-form`: نوع الفورم (POST).
- `/login.php`: مسار صفحة تسجيل الدخول.
- `username=^USER^&password=^PASS^`: صيغة بيانات النموذج، حيث ^USER^ و ^PASS^ تستبدلان تلقائياً.
- `F=Login failed`: نص يظهر عند فشل تسجيل الدخول.
- `-V`: الوضع المفصل (Verbose) لعرض كل المحاولات.

## خيارات مهمة
| الخيار         | الوصف                                   |
|----------------|----------------------------------------|
| `-l`           | اسم المستخدم (login)                    |
| `-L`           | ملف أسماء المستخدمين (لو كنت تجرب عدة أسماء)    |
| `-p`           | كلمة مرور ثابتة (إذا تريد تجربة اسم مستخدم واحد مع كلمة واحدة)    |
| `-P`           | ملف كلمات المرور (wordlist)             |
| `-t`           | عدد الخيوط (threads)                   |
| `http-get-form`| استهداف فورم HTTP GET                  |
| `http-post-form`| استهداف فورم HTTP POST                 |
| `-V`           | عرض التفاصيل لكل محاولة                |
| `-w`           | الانتظار حتى انتهاء كل محاولات (wait)  |
| `-s`           | تحديد رقم البورت (default حسب البروتوكول) |

## نصائح
- جرب تعديل عدد الخيوط (`-t`) حسب قدرات النظام لتسريع أو تقليل الضغط.
- راقب الشبكة وأداء الخادم عند إجراء هجمات brute force.
- استخدم كلمات مرور وقوائم حديثة ومتنوعة لزيادة فرص النجاح.
- لا تستخدم Hydra على أنظمة بدون إذن قانوني.

---

*ملخص:*  
Hydra أداة قوية جداً لتجربة كلمات المرور على عدد كبير من الخدمات، وتتميز بالدعم الواسع للبروتوكولات والخيارات المرنة للتحكم.

