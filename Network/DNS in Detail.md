
# 🧵 DNS in Detail (TryHackMe Room)

## 1. 🌐 What is DNS?
- DNS معناها **Domain Name System**.
- وظيفته ترجمة النطاقات (مثل `example.com`) إلى عناوين IP قابلة للوصول، بدلاً من حفظ هذا العنوان الطويل :contentReference[oaicite:1]{index=1}.

---

## 2. 🏛️ Domain Hierarchy
- **TLD (Top-Level Domain):**
  - نوعان: gTLD (`.com`, `.org`) وccTLD (`.uk`, `.ca`) :contentReference[oaicite:2]{index=2}.
- **Second-Level Domain (SLD):**
  - حتى 63 حرف، مسموح بها أحرف a–z، أرقام، و"-".
- **Subdomain:**
  - نفس القواعد، بنفس الحد الأقصى (63 حرف)، وتحت سقف 253 حرف إجمالاً :contentReference[oaicite:3]{index=3}.

**⚠️ Quiz Answers:**
- Max subdomain length: **63**
- محظور `_` في الأسماء
- Max domain length: **253**
- `.co.uk` هو **ccTLD** :contentReference[oaicite:4]{index=4}

---

## 3. 🗂️ DNS Record Types
1. **A** → يربط النطاق بـ IPv4.
2. **AAAA** → يربطه بـ IPv6.
3. **CNAME** → رابط إلى نطاق آخر.
4. **MX** → المسؤول عن إرسال البريد، معه قيمة أولوية.
5. **TXT** → نص حر يُستخدم للتحقق من الملكية، إعداد SPF، إلخ :contentReference[oaicite:5]{index=5}.

**⚠️ Record Answers:**
- لإرسال البريد: **MX**
- لعناوين IPv6: **AAAA** :contentReference[oaicite:6]{index=6}

---

## 4. 🔁 DNS Lookup Process
1. **Client Cache**: جهازك يبدأ.
2. **Recursive DNS Server** (غالبًا عند مزود الإنترنت):  
   - لو موجود نتيجة، يرجعها.
   - لو لا، يبدأ:
3. **Root Server**: يوجّه إلى TLD.
4. **TLD Server**: يوجّه إلى الخادم الموثوق (Authoritative).
5. **Authoritative DNS**: يعطي السجل المطلوب.
   - النتيجة تُخزّن في cache بـ TTL (Time To Live) :contentReference[oaicite:7]{index=7}.

**⚠️ Quiz Answers:**
- TTL يكبس زمن التخزين المؤقت
- الـ Recursive server عادةً من ISP
- الـ Authoritative هو المسؤول الأول والسجل يتم حفظه فيه :contentReference[oaicite:8]{index=8}

---

## 5. ⚙️ Practical DNS Queries
- **nslookup --type=A www.website.thm** → يظهر IP
- **nslookup --type=MX website.thm** → يظهر mail server + الأولوية
- **nslookup --type=TXT website.thm** → يظهر نص، يحتوي غالبًا على flag
- **nslookup --type=CNAME shop.website.thm** → يظهر alias

**Example Answers:**
- CNAME لـ `shop.website.thm`: `shops.myshopify.com`
- TXT لـ `website.thm`: `THM{7012BBA60997F35A9516C2E16D2944FF}`
- MX الأولوية: `30`
- A record لـ `www.website.thm`: `10.10.10.10` :contentReference[oaicite:9]{index=9}

---

## Bonus: 🛠️ Sample Dig/nslookup Commands

```bash
# A record
nslookup --type=A website.thm

# CNAME record
nslookup --type=CNAME shop.website.thm

# TXT record
nslookup --type=TXT website.thm

# MX record
nslookup --type=MX website.thm
