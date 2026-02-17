# 🧭 Nmap Cheat Sheet (HTB) — ქართულად

## 📌 რა არის Nmap

**Nmap (Network Mapper)** — ქსელის სკანირების ინსტრუმენტი, რომელიც გამოიყენება:

* ღია პორტების მოსაძებნად
* სერვისებისა და ვერსიების დასადგენად
* ოპერაციული სისტემის ამოსაცნობად
* უსაფრთხოების ტესტირებისა და პენეტრაციისთვის

HTB-ზე **Nmap არის პირველი ნაბიჯი თითქმის ყველა მანქანაზე**.

---

## 🧠 სკანირების ლოგიკა (HTB mindset)

```
Target
  ↓
Port Discovery
  ↓
Service Detection
  ↓
Version Detection
  ↓
Scripts / Enumeration
```

---

## 🔹 საბაზისო სკანირებები

### 🔸 ყველაზე მარტივი სკანი

```bash
nmap 10.10.10.10
```

📌 რას აკეთებს:

* TCP connect scan
* 1000 ყველაზე გავრცელებული პორტი

---

### 🔸 სწრაფი სკანი (HTB-friendly)

```bash
nmap -F 10.10.10.10
```

* მხოლოდ ~100 პორტი
* სწრაფია, მაგრამ ხშირად **არასაკმარისი**

---

## 🔹 TCP სკანები (ძალიან მნიშვნელოვანი)

### 🔸 SYN Scan (სტანდარტი HTB-ზე)

```bash
nmap -sS 10.10.10.10
```

✔️ სწრაფი
✔️ ნაკლებად შესამჩნევი

---

### 🔸 TCP Connect Scan

```bash
nmap -sT 10.10.10.10
```

---

## 🔹 ყველა პორტის სკანი (HTB MUST)

```bash
nmap -p- 10.10.10.10
```

📌 რატომ?

* HTB მანქანებზე ხშირად სერვისი **არ არის სტანდარტულ პორტზე**
* მაგალითად: `8080`, `1337`, `5000`, `9001`

⚠️ ნელია → გამოიყენე timing flags

```bash
nmap -p- --min-rate 5000 10.10.10.10
```

---

## 🔹 სერვისისა და ვერსიის აღმოჩენა

### 🔸 Version Detection

```bash
nmap -sV 10.10.10.10
```

შედეგი:

```
22/tcp  open  ssh     OpenSSH 7.6p1
80/tcp  open  http    Apache httpd 2.4.29
```

📌 ეს ინფორმაცია პირდაპირ გეუბნება:

* exploit research
* CVE ძებნა
* default creds

---

## 🔹 OS Detection

```bash
nmap -O 10.10.10.10
```

ან უფრო აგრესიულად:

```bash
nmap -A 10.10.10.10
```

⚠️ `-A` = **ხმაურიანი**, მაგრამ სასწავლოდ კარგია

---

## 🔹 NSE Scripts (HTB gold)

### 🔸 Default scripts

```bash
nmap -sC 10.10.10.10
```

---

### 🔸 Service + Scripts + Versions (HTB classic)

```bash
nmap -sC -sV 10.10.10.10
```

---

### 🔸 კონკრეტული სკრიპტების კატეგორიები

```bash
nmap --script vuln 10.10.10.10
```

```bash
nmap --script auth 10.10.10.10
```

```bash
nmap --script http-enum 10.10.10.10
```

---

## 🔹 UDP სკანი (ხშირად ავიწყდებათ)

```bash
nmap -sU 10.10.10.10
```

📌 ხშირად იპოვი:

* DNS (53)
* SNMP (161)
* NTP (123)

⚠️ ძალიან ნელია → გამოიყენე კონკრეტული პორტები

```bash
nmap -sU -p 53,161 10.10.10.10
```

---

## 🔹 Output ფაილები (HTB habit)

```bash
nmap -oA scan 10.10.10.10
```

შექმნის:

* `scan.nmap`
* `scan.gnmap`
* `scan.xml`

📌 კარგი პრაქტიკა writeups-ისთვის

---

## 🔹 Timing & Stealth

```bash
nmap -T4 10.10.10.10
```

Levels:

* `T0` → ძალიან ჩუმი
* `T3` → default
* `T4` → HTB-friendly
* `T5` → ხმაურიანი

---

## 🔥 HTB WORKFLOW — რეალური მაგალითები

### 🧪 Example 1 — Initial Recon

```bash
nmap -p- --min-rate 5000 10.10.10.10
```

👉 იპოვე ყველა ღია პორტი

---

### 🧪 Example 2 — Service Enumeration

```bash
nmap -p 22,80,8080 -sC -sV 10.10.10.10
```

👉 დეტალები სერვისებზე

---

### 🧪 Example 3 — Web-focused scan

```bash
nmap -p 80,443 --script http-enum,http-title,http-methods 10.10.10.10
```

---

### 🧪 Example 4 — Vulnerability scan

```bash
nmap --script vuln -p 80,445 10.10.10.10
```

---

### 🧪 Example 5 — Stealth full recon

```bash
nmap -sS -p- -sC -sV -T4 -oA fullscan 10.10.10.10
```

📌 **HTB writeup-level scan**

---

## 🧠 მნემონიკები

* **`-p-`** → *პორტები არ გამოგრჩეს*
* **`-sC`** → *Default scripts = Enumeration*
* **`-sV`** → *Exploit starts here*
* **`-A`** → *Learn mode*
* **`--script vuln`** → *Low-hanging fruit*

---

## ⚠️ ტიპური შეცდომები HTB-ზე

❌ მხოლოდ `nmap target`
❌ UDP-ის იგნორი
❌ output-ის არშენახვა
❌ `-p-` არ გამოყენება

---

## ✅ TL;DR (HTB Fast Start)

```bash
nmap -p- --min-rate 5000 10.10.10.10
nmap -p <found_ports> -sC -sV 10.10.10.10
```
