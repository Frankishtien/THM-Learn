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
</details> 


















