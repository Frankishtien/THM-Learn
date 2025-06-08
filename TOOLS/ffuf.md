# ffuf

<details>
  <summary>Basics 🟧</summary>

## FFUF (Fuzz Faster U Fool) Summary

### Basic Info

FFUF is a fast web fuzzer written in Go, commonly used to brute-force:

* Hidden files and directories
* Parameters and endpoints

### Key Options

#### HTTP OPTIONS

* `-u` : Target URL (use `FUZZ` keyword to mark injection point)
* `-w` : Path to wordlist
* `-X` : HTTP method (GET, POST, etc.)
* `-d` : POST data
* `-H` : Add HTTP headers (`Name: Value`)
* `-b` : Cookie data
* `-x` : Proxy URL (e.g., [http://127.0.0.1:8080](http://127.0.0.1:8080))
* `-r` : Follow redirects
* `-timeout` : Set request timeout (default 10s)

#### MATCHER OPTIONS

* `-mc` : Match HTTP status codes
* `-ml` : Match number of lines in response
* `-mw` : Match number of words in response
* `-ms` : Match response size
* `-mr` : Match regex

#### FILTER OPTIONS

* `-fc` : Filter status codes
* `-fl` : Filter lines
* `-fw` : Filter words
* `-fs` : Filter size
* `-fr` : Filter regex

#### INPUT OPTIONS

* `-e` : File extensions to fuzz (e.g., .php,.txt)
* `-ic` : Ignore wordlist comments
* `-mode` : Multiple wordlist mode (clusterbomb, pitchfork)

#### OUTPUT OPTIONS

* `-o` : Save results to file
* `-of` : Output format (json, html, csv, etc.)
* `-debug-log` : Save internal logs to a file

### Common Usage

Minimum required:

```bash
ffuf -u http://MACHINE_IP/FUZZ -w /path/to/wordlist.txt
```

Using custom keyword:

```bash
ffuf -u http://MACHINE_IP/CUSTOM -w /path/to/wordlist.txt:CUSTOM
```

Example:

```bash
ffuf -u http://10.10.10.10/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
```

### Tips

* FUZZ is default keyword, but you can customize it.
* Use filtering and matching to reduce noise.
* Combine with Burp or proxy using `-x`.

### Practical Step

1. Deploy target machine.
2. Use ffuf with target IP and desired wordlist to discover hidden resources.

---

This file serves as a summarized reference guide for ffuf usage.

 ✅<details>

```
ffuf -u http://10.10.11.173/FUZZ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/big.txt 
```

```
favicon.ico
```

![image](https://github.com/user-attachments/assets/dcbd7568-887f-421a-bfab-74704d96fae2)

</details>




  
</details>



<details>
  <summary> Finding pages and directories 🟧</summary>


## FFUF Advanced Enumeration Strategy

### Objective

Efficient fuzzing of files, extensions, and directories using curated SecLists.

---

### 🔹 Q1: Enumerate Common Files (Generic Wordlist)

Use a general-purpose wordlist to find files:

```bash
ffuf -u http://10.10.11.173/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt
```

📝 *Note: This list includes many extensions which may be irrelevant. So we refine in later steps.*

---

### 🔹 Q2: Discover File Extensions (e.g. index.php, index.asp)

Enumerate using known extensions on the `index` filename:

```bash
ffuf -u http://10.10.11.173/indexFUZZ -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt
```

Sample from `web-extensions.txt`:

```
.asp
.aspx
.php
.cgi
.html
```

🎯 *Goal: Identify technologies in use (e.g., PHP, ASP.NET).*

---

### 🔹 Q3: Targeted File Fuzzing with Known Extensions

Now that we know the likely extensions, apply them to generic filenames:

```bash
ffuf -u http://10.10.11.173/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e .php,.txt
```

🧠 *Tip: Avoid 4-letter extensions like ****\`\`**** in this step if they're too noisy.*

---

### 🔹 Q4: Fuzz for Directories

Directory discovery can be performed independently of file extensions:

```bash
ffuf -u http://10.10.11.173/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
```

📁 *Useful for finding admin panels, API paths, and hidden folders.*

---

### Summary Table

| Step | Purpose                         | Command (Summary)                                  |
| ---- | ------------------------------- | -------------------------------------------------- |
| Q1   | Find generic files              | `raft-medium-files-lowercase.txt`                  |
| Q2   | Discover file extensions        | `indexFUZZ` with `web-extensions.txt`              |
| Q3   | Apply known extensions to names | `raft-medium-words-lowercase.txt` + `-e .php,.txt` |
| Q4   | Fuzz for directories            | `raft-medium-directories-lowercase.txt`            |

---

This strategy reduces noise and increases accuracy in file and directory discovery using ffuf.



✅✅

<details>


![image](https://github.com/user-attachments/assets/12524185-336e-420f-9b4d-bee1af3927cd)


```
ffuf -u http://10.10.11.173/indexFUZZ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/web-extensions.txt
```

![image](https://github.com/user-attachments/assets/db208fa9-f44b-450d-ba1a-ef14dc616fe4)

```
ffuf -u http://10.10.11.173/FUZZ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e .php,.txt
```

![image](https://github.com/user-attachments/assets/bef31cdd-bc20-4a8c-8431-344a445f4bef)

```
ffuf -u http://10.10.11.173/FUZZ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
```

![image](https://github.com/user-attachments/assets/c16e119a-a499-401d-a303-f66c87d9f07b)


</details>




  
</details> 


















