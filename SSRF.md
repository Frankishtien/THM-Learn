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


![image](https://github.com/user-attachments/assets/72a557f5-9d12-40d2-b80e-f0ff30a2c150)







