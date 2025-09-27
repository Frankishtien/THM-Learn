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











