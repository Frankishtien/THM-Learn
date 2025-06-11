# ffuf

<details>
  <summary>🟧 Basics </summary>

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
  <summary>🟧 Finding pages and directories </summary>


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





<details>
  <summary>🟧 Using filters </summary>


## FFUF Filtering & Matching Techniques

### Objective

Reduce noise and increase visibility of relevant results by using ffuf's filter and matcher options.

---

### 🔹 Q1: Hide 403 Forbidden Responses

By default, ffuf shows all matched responses. If you want to hide the ones with 403 status codes:

```bash
ffuf -u http://10.10.11.173/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fc 403
```

📌 *Use this when many entries are inaccessible but still detected.*

---

### 🔹 Q2: Show Only 200 OK Responses

To limit output only to valid and accessible content (HTTP 200):

```bash
ffuf -u http://10.10.11.173/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -mc 200
```

🧠 *Shorter and cleaner than excluding multiple status codes like 403, 500, etc.*

---

### 🔹 Q3: Focus on HTTP 500 (Internal Server Errors)

To detect bugs or unstable endpoints:

```bash
ffuf -u http://10.10.11.173/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -mc 500
```

🚨 *Useful for finding vulnerable endpoints or misconfigurations.*

---

### 🔹 Q4: Filter Zero-Length Responses

Zero-size 200 responses often indicate uninteresting or stub files:

```bash
ffuf -u http://10.10.11.173/config/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fc 403 -fs 0
```

🔍 *Ideal when you want to ignore blank or default stub responses.*

---

### 🔹 Q5: Filter Dotfiles Using Regex

Dotfiles (like `.htaccess`, `.php`) often trigger false positives. Use regex filter to exclude them:

```bash
ffuf -u http://10.10.11.173/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fr '/\..*'
```

⚠️ *This avoids hiding all 403s and targets only dot-prefixed files.*

---

### 🔍 Common Matchers & Filters in ffuf

#### Matcher Options:

* `-mc` : Match HTTP status codes (e.g. `-mc 200`)
* `-ms` : Match response size (bytes)
* `-ml` : Match line count
* `-mw` : Match word count
* `-mr` : Match regex

#### Filter Options:

* `-fc` : Filter by HTTP status codes (e.g. `-fc 403,404`)
* `-fs` : Filter by size (e.g. `-fs 0`)
* `-fl` : Filter by number of lines
* `-fw` : Filter by word count
* `-fr` : Filter by regex (e.g. `-fr '/\.git'`)

---

### Summary Table

| Use Case                   | Command Snippet |
| -------------------------- | --------------- |
| Hide 403s                  | `-fc 403`       |
| Show only 200s             | `-mc 200`       |
| Debug internal errors      | `-mc 500`       |
| Ignore zero-size responses | `-fs 0`         |
| Exclude dotfiles           | `-fr '/\..*'`   |

---

This advanced filtering gives more control over ffuf output, helps avoid noise, and lets you focus on what matters during enumeration.

✅✅

<details>

```
ffuf -u http://10.10.11.173/FUZZ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fc 403
```

![image](https://github.com/user-attachments/assets/bdc4fbc9-fab2-490a-968c-5c59ece25bb8)


```
ffuf -u http://10.10.11.173/FUZZ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -mc 200
```

![image](https://github.com/user-attachments/assets/671d80b8-6248-43a8-91e4-c8f17cac8b12)

```
ffuf -u http://10.10.11.173/FUZZ -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fr '/\..*'
```

![image](https://github.com/user-attachments/assets/1d4bdc73-aa76-4b30-858d-4d3f5fcd4d43)


  
</details>



  
</details>




<details>
  <summary>🟧 Fuzzing parameters</summary>

## FFUF Parameter Fuzzing Techniques

### Objective

Discover hidden or undocumented parameters, fuzz their values, and brute-force credentials using ffuf.

---

### 🔹 Q1: Discover Hidden GET Parameters

If you find a URL but don’t know what parameters it accepts, fuzz for them:

```bash
ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?FUZZ=1' \
     -c -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fw 39

ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?FUZZ=1' \
     -c -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -fw 39
```

📌 *`-fw 39`*\* filters responses that contain exactly 39 words — common for error or default pages.\*

---

### 🔹 Q2: Fuzz Parameter Values (0–255)

Once a parameter (e.g., `id`) is found, test its behavior with integer values:

```bash
ruby -e '(0..255).each{|i| puts i}' | ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33

ruby -e 'puts (0..255).to_a' | ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33

for i in {0..255}; do echo $i; done | ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33

seq 0 255 | ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33

cook '[0-255]' | ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33
```

🧠 *This helps spot valid IDs, error messages, or potential SQLi behavior.*

---

### 🔹 Q3: Brute-Force Passwords via POST Request

Test password values with ffuf in a login form:

```bash
ffuf -u http://10.10.39.214/sqli-labs/Less-11/ \
     -c -w /usr/share/seclists/Passwords/Leaked-Databases/hak5.txt \
     -X POST -d 'uname=Dummy&passwd=FUZZ&submit=Submit' \
     -fs 1435 -H 'Content-Type: application/x-www-form-urlencoded'
```

📌 *Use **\`\`** when fuzzing POST.*

---

### Summary Table

| Use Case                   | Command Highlights                |
| -------------------------- | --------------------------------- |
| Fuzz hidden GET parameters | `?FUZZ=1` with wordlist           |
| Fuzz parameter values      | `id=FUZZ` with generated numbers  |
| Brute-force password field | `passwd=FUZZ` in POST with -fs/-H |

---

Mastering parameter and value fuzzing with ffuf can uncover serious vulnerabilities like SQLi, XSS, file inclusion, and insecure auth logic.


✅

<details>

```
ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?FUZZ=1' -c -w /home/kali/Downloads/wordlists/SecLists/Discovery/Web-Content/burp-parameter-names.txt -fw 39
```

![image](https://github.com/user-attachments/assets/1d80d4a4-d67b-4915-953f-12885e191424)

----

```
for i in {0..255}; do echo $i; done | ffuf -u 'http://10.10.39.214/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33
```


![image](https://github.com/user-attachments/assets/fefd7d35-389b-491e-b4da-4683170adf6a)

---


```
ffuf -u http://10.10.39.214/sqli-labs/Less-11/ -c -w /home/kali/Downloads/wordlists/SecLists/Passwords/Leaked-Databases/hak5.txt -X POST -d 'uname=Dummy&passwd=FUZZ&submit=Submit' -fs 1435 -H 'Content-Type: application/x-www-form-urlencoded'
```

![image](https://github.com/user-attachments/assets/06a19736-d9fe-4896-81aa-09fc8ecedff3)


  
</details>



  
</details>















