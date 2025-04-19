# SSRF (Server Side Request Forgery)

https://tryhackme.com/room/ssrfhr

---
> SSRF is a web security vulnerability where an attacker tricks a server into making requests to other systems (often internal systems) that it shouldn't have access to.

## **``Simple Example:``**
> Imagine a weather website that lets you check weather by URL. Normally you'd enter:

```
https://weather.com/api?city=London
```

> But an attacker could abuse it by entering:

```
https://weather.com/api?city=http://localhost/admin
```

 ``If the server blindly fetches this URL, it might reveal sensitive admin pages that should only be accessible from inside the company network.``

---

## Basic SSRF

> Basic SSRF (Server-Side Request Forgery) is when an attacker tricks a web server into making unauthorized requests—often to internal systems that should be restricted.

``Example``

- Imagine a company HR portal (hrms.thm) has a feature that fetches pages using a URL parameter like:

```
http://hrms.thm?url=localhost/copyright  
```

This is meant to load internal pages (e.g., the copyright page).

> But if the server doesn’t properly check the URL, an attacker could abuse it to fetch sensitive data, like:

```
http://hrms.thm?url=localhost/config  
```

![image](https://github.com/user-attachments/assets/3fc0f2ab-7f0a-4b48-8cd1-1775195a6a32)


---

## Types of SSRF - Blind

 To be Contuine......






















