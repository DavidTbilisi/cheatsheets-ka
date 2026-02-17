# 🚀 FFUF Cheat Sheet (HTB) — ქართულად

## 📌 რა არის FFUF

**FFUF (Fuzz Faster U Fool)** — ძალიან სწრაფი web fuzzer, რომელიც გამოიყენება:

* hidden დირექტორიების მოსაძებნად
* ფაილების (php, bak, zip…) აღმოჩენისთვის
* subdomain-ების აღმოჩენისთვის
* parameters / virtual hosts fuzzing

👉 HTB-ზე: **Nmap → FFUF → Exploit**

---

## 🧠 FFUF-ის აზროვნება (HTB mindset)

```
HTTP Service
   ↓
Directories
   ↓
Files
   ↓
Parameters
   ↓
Virtual Hosts
```

---

## 🔹 საბაზისო სინტაქსი

```bash
ffuf -u URL -w WORDLIST
```

მაგალითი:

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt
```

📌 **FUZZ** — ის ადგილია, სადაც ffuf სიტყვებს ცდის.

---

## 🔹 Directory Enumeration (HTB #1 use case)

### 🔸 ძირითადი დირექტორიების სკანი

```bash
ffuf -u http://10.10.10.10/FUZZ -w /usr/share/wordlists/dirb/common.txt
```

---

### 🔸 ფილტრაცია response code-ით

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -mc 200
```

* `-mc 200` → მხოლოდ 200 OK

---

### 🔸 რამდენიმე სტატუს კოდის დაშვება

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -mc 200,301,302
```

📌 redirect-ები ხშირად საინტერესოა (login, admin)

---

## 🔹 File Enumeration (ძალიან ხშირი HTB-ზე)

### 🔸 PHP ფაილების ძებნა

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -e .php
```

---

### 🔸 Multiple extensions

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -e .php,.txt,.bak,.zip
```

📌 ხშირად იპოვი:

* `config.php.bak`
* `backup.zip`
* `db.sql`

---

## 🔹 Recursive fuzzing (advanced)

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -recursion -recursion-depth 2
```

⚠️ გამოიყენე მხოლოდ საჭიროებისას — ხმაურიანია

---

## 🔹 Filtering & Matching (ძალიან მნიშვნელოვანი)

### 🔸 ზომის მიხედვით გაფილტვრა

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -fs 4242
```

📌 როცა ყველა პასუხი ერთნაირი ზომისაა (fake 200)

---

### 🔸 სიტყვების რაოდენობით

```bash
ffuf -fw 10
```

---

### 🔸 regex match

```bash
ffuf -mr "admin"
```

---

## 🔹 Parameter Fuzzing (HTB gold)

### 🔸 GET parameters

```bash
ffuf -u "http://10.10.10.10/index.php?FUZZ=test" -w params.txt
```

📌 იპოვი:

* `id`
* `page`
* `file`
* `user`

---

### 🔸 POST parameters

```bash
ffuf -u http://10.10.10.10/login -X POST -d "FUZZ=test" -w params.txt
```

---

## 🔹 Value Fuzzing (LFI, SQLi prep)

```bash
ffuf -u "http://10.10.10.10/index.php?page=FUZZ" -w lfi.txt
```

მაგალითი payload-ები:

```
../../../../etc/passwd
php://filter/convert.base64-encode/resource=index.php
```

---

## 🔹 Virtual Host Enumeration (ხშირი HTB trick)

```bash
ffuf -u http://10.10.10.10 -H "Host: FUZZ.target.htb" -w subdomains.txt
```

📌 როცა:

* default page ჩანს
* nginx/apache vhost hint გაქვს

---

## 🔹 Authentication bypass probing

```bash
ffuf -u http://10.10.10.10/login -X POST \
-d "username=admin&password=FUZZ" \
-w passwords.txt
```

👉 response size / status code უნდა დააკვირდე

---

## 🔹 Threads & Speed (HTB tuning)

```bash
-t 40
```

სრული მაგალითი:

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -t 50
```

⚠️ ძალიან მაღალი thread → unstable responses

---

## 🔹 Output ფაილები (writeup habit)

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -o result.json
```

ან:

```bash
-o result.html
```

---

## 🔥 HTB WORKFLOW — რეალური მაგალითები

### 🧪 Example 1 — Basic web recon

```bash
ffuf -u http://10.10.10.10/FUZZ -w /usr/share/wordlists/dirb/common.txt
```

---

### 🧪 Example 2 — Directories + files

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -e .php,.bak,.zip
```

---

### 🧪 Example 3 — Filter fake 200

```bash
ffuf -u http://10.10.10.10/FUZZ -w wordlist.txt -fs 1234
```

---

### 🧪 Example 4 — Parameter discovery

```bash
ffuf -u "http://10.10.10.10/index.php?FUZZ=test" -w params.txt
```

---

### 🧪 Example 5 — LFI prep

```bash
ffuf -u "http://10.10.10.10/?file=FUZZ" -w lfi.txt
```

---

### 🧪 Example 6 — Virtual host attack

```bash
ffuf -u http://10.10.10.10 -H "Host: FUZZ.htb" -w subdomains.txt
```

---

## 🧠 მნემონიკები

* **FUZZ** → *აქ არის შეტევის ადგილი*
* **-e** → *ფაილების გაფართოებები*
* **-fs** → *fake response killer*
* **params fuzzing** → *logic bugs იწყება აქ*
* **vhost fuzzing** → *hidden app*

---

## ⚠️ ტიპური შეცდომები HTB-ზე

❌ FUZZ არასწორ ადგილას
❌ response size-ის იგნორი
❌ მხოლოდ directories, არა parameters
❌ vhost fuzzing-ის დავიწყება

---

## ✅ TL;DR (HTB Fast Start)

```bash
ffuf -u http://target/FUZZ -w common.txt
ffuf -u http://target/FUZZ -w common.txt -e .php,.bak
ffuf -u "http://target/index.php?FUZZ=test" -w params.txt
```
