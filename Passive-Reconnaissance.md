# Passive Reconnaissance

---
## don't forget this after this room ``active reconnaissance`` 
https://tryhackme.com/room/activerecon
---
## will learn ...
![Critical](https://img.shields.io/badge/tool-Whois-red)
![Critical](https://img.shields.io/badge/tool-nslookup-green)
![Critical](https://img.shields.io/badge/tool-dig-red)
- DNSDumpster
- Shodan.io

---

> In passive reconnaissance, you rely on publicly available knowledge. It is the knowledge that you can access from publicly available resources without directly engaging with the target. Think of it like you are looking at target territory from afar without stepping foot on that territory.

### ``Whois``
> The WHOIS server replies with various information related to the domain requested. Of particular interest, we can learn:
> - Registrar: Via which registrar was the domain name registered?
> - Contact info of registrant: Name, organization, address, phone, among other things. (unless made hidden via a privacy service)
> - Creation, update, and expiration dates: When was the domain name first registered? When was it last updated? And when does it need to be renewed?
> - Name Server: Which server to ask to resolve the domain name?

``for example``
```c
user@TryHackMe$ whois tryhackme.com
[Querying whois.verisign-grs.com]
[Redirected to whois.namecheap.com]
[Querying whois.namecheap.com]
[whois.namecheap.com]
Domain name: tryhackme.com
Registry Domain ID: 2282723194_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.namecheap.com
Registrar URL: http://www.namecheap.com
Updated Date: 2021-05-01T19:43:23.31Z
Creation Date: 2018-07-05T19:46:15.00Z
Registrar Registration Expiration Date: 2027-07-05T19:46:15.00Z
Registrar: NAMECHEAP INC
Registrar IANA ID: 1068
Registrar Abuse Contact Email: abuse@namecheap.com
Registrar Abuse Contact Phone: +1.6613102107
Reseller: NAMECHEAP INC
Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
Registry Registrant ID: 
Registrant Name: Withheld for Privacy Purposes
Registrant Organization: Privacy service provided by Withheld for Privacy ehf
[...]
URL of the ICANN WHOIS Data Problem Reporting System: http://wdprs.internic.net/
>>> Last update of WHOIS database: 2021-08-25T14:58:29.57Z <<<
For more information on Whois status codes, please visit https://icann.org/epp
```




---

### ``nslookup``

> Find the IP address of a domain name using nslookup, which stands for Name Server Look Up. You need to issue the command nslookup DOMAIN_NAME, for example, nslookup tryhackme.com. Or, more generally, you can use nslookup OPTIONS DOMAIN_NAME SERVER. These three main parameters are:
> - OPTIONS contains the query type as shown in the table below. For instance, you can use A for IPv4 addresses and AAAA for IPv6 addresses.
> - DOMAIN_NAME is the domain name you are looking up.
> - SERVER is the DNS server that you want to query. You can choose any local or public DNS server to query. Cloudflare offers 1.1.1.1 and 1.0.0.1, Google offers 8.8.8.8 and 8.8.4.4, and Quad9 offers 9.9.9.9 and 149.112.112.112. There are many more public DNS servers that you can choose from if you want alternatives to your ISP’s DNS servers.



|Query type   |      	Result           |
|-------------|------------------------|
|A	          |    IPv4 Addresses      |
|AAAA	        |  IPv6 Addresses        |
|CNAME        |  	Canonical Name       |
|MX	          |  Mail Servers          |
|SOA	        |    Start of Authority  |
|TXT	        |     TXT Records        | 


``for example``
```
user@TryHackMe$ nslookup -type=A tryhackme.com 1.1.1.1
Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
Name:	tryhackme.com
Address: 172.67.69.208
Name:	tryhackme.com
Address: 104.26.11.229
Name:	tryhackme.com
Address: 104.26.10.229
```

> Let’s say you want to learn about the email servers and configurations for a particular domain. You can issue nslookup -type=MX tryhackme.com. Here is an example:

```
user@TryHackMe$ nslookup -type=MX tryhackme.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
tryhackme.com	mail exchanger = 5 alt1.aspmx.l.google.com.
tryhackme.com	mail exchanger = 1 aspmx.l.google.com.
tryhackme.com	mail exchanger = 10 alt4.aspmx.l.google.com.
tryhackme.com	mail exchanger = 10 alt3.aspmx.l.google.com.
tryhackme.com	mail exchanger = 5 alt2.aspmx.l.google.com.
```

We can see that tryhackme.com’s current email configuration uses Google. Since MX is looking up the Mail Exchange servers, we notice that when a mail server tries to deliver email ``@tryhackme.com``, it will try to connect to the ``aspmx.l.google.com``, which has order 1. If it is busy or unavailable, the mail server will attempt to connect to the next in order mail exchange servers, ``alt1.aspmx.l.google.com`` or ``alt2.aspmx.l.google.com``.



---
### ``dig``

>  you can use ``dig``, the acronym for “Domain Information Groper,” if you are curious. Let’s use ``dig`` to look up the MX records and compare them to ``nslookup``. We can use ``dig DOMAIN_NAME``, but to specify the record type, we would use ``dig DOMAIN_NAME TYPE``. Optionally, we can select the server we want to query using ``dig @SERVER DOMAIN_NAME TYPE``.
> - SERVER is the DNS server that you want to query.
> - DOMAIN_NAME is the domain name you are looking up.
> - TYPE contains the DNS record type, as shown in the table provided earlier.

``for example ``

```
user@TryHackMe$ dig tryhackme.com MX

; <<>> DiG 9.16.19-RH <<>> tryhackme.com MX
;; global options: +cmd
;; Got answer:
;; ->>HEADER<
```



