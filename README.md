# Hashcat_Help
Hashcat is a powerful tool with a wide range of options for cracking passwords. Below is a detailed guide covering the key features, options, and examples.

---

## **Basic Syntax**
```bash
hashcat [options] [hashfile|hash] [dictionary|mask|directory]
```

---

## **Help Menu**
To view all available options in Hashcat:
```bash
hashcat --help
```

---

## **Key Options and Their Usage**

### **1. Hash Mode (`-m`)**
Hashcat supports multiple hash algorithms. Use `-m` to specify the hash type.  
Run this to list all supported hash modes:
```bash
hashcat --help | grep "Hash modes"
```

Examples of common hash modes:
| **Hash Mode** | **Description**        | **Option (-m)** |
|---------------|------------------------|-----------------|
| MD5           | Basic hash             | 0               |
| SHA-1         | Basic hash             | 100             |
| bcrypt        | Password hashing       | 3200            |
| NTLM          | Windows authentication | 1000            |

### Example:
```bash
hashcat -m 0 hash.txt wordlist.txt
```

---

### **2. Attack Mode (`-a`)**
Hashcat provides different attack modes. Use `-a` to specify the attack type.

| **Mode** | **Description**                  | **Option (-a)** |
|----------|----------------------------------|-----------------|
| 0        | Dictionary attack                | 0               |
| 1        | Combinator attack                | 1               |
| 3        | Mask attack (brute-force-like)   | 3               |
| 6        | Dictionary + mask attack         | 6               |
| 7        | Mask + dictionary attack         | 7               |

### Example:
```bash
hashcat -a 0 -m 1000 hash.txt wordlist.txt
```

---

### **3. Output File (`-o`)**
Save cracked hashes to a file.
```bash
hashcat -m 0 -a 0 -o cracked.txt hash.txt wordlist.txt
```

---

### **4. Rules (`-r`)**
Rules modify the wordlist dynamically (e.g., appending numbers, replacing characters). Hashcat includes pre-defined rule files.

Example:
```bash
hashcat -m 0 -a 0 -r /usr/share/hashcat/rules/best64.rule hash.txt wordlist.txt
```

---

### **5. Masks (Mask Attack)**
Masks are used for custom brute-force attacks. You specify the character set and length.

| **Symbol** | **Character Set**      |
|------------|------------------------|
| `?l`       | Lowercase letters (a-z)|
| `?u`       | Uppercase letters (A-Z)|
| `?d`       | Digits (0-9)           |
| `?s`       | Special characters     |
| `?a`       | All of the above       |

Example: Cracking a 4-character alphanumeric password:
```bash
hashcat -m 0 -a 3 hash.txt ?l?l?d?d
```

---

### **6. Performance Options**
#### Use GPUs (`--opencl-device-types`):
```bash
hashcat --opencl-device-types 1,2
```
- `1` = CPU
- `2` = GPU

#### Optimize Performance (`-O`):
For optimized kernels (faster cracking):
```bash
hashcat -O -m 0 hash.txt wordlist.txt
```

---

### **7. Benchmarking**
Test your hardware's performance with different hash modes:
```bash
hashcat -b
```

---

### **8. Restore and Session**
#### Restore Cracking Session (`--restore`):
```bash
hashcat --restore
```

#### Save Session (`--session`):
```bash
hashcat --session mysession -m 0 hash.txt wordlist.txt
```

---

### **9. Examples for Common Tasks**
#### a. Dictionary Attack (Simple):
```bash
hashcat -m 0 -a 0 hash.txt wordlist.txt
```

#### b. Mask Attack:
```bash
hashcat -m 0 -a 3 hash.txt ?a?a?a?a?a
```

#### c. Hybrid Attack (Dictionary + Mask):
```bash
hashcat -m 0 -a 6 hash.txt wordlist.txt ?d?d
```

#### d. Custom Charset:
```bash
hashcat -m 0 -a 3 hash.txt -1 ?l?u ?1?1?1?1
```

---

### **10. Debugging and Verbosity**
#### Show Cracking Progress (`--status`):
```bash
hashcat -m 0 -a 0 --status hash.txt wordlist.txt
```

#### Debugging Output (`--debug-mode`):
```bash
hashcat -m 0 -a 0 --debug-mode 1 hash.txt wordlist.txt
```

---

### **11. Stop/Resume Cracking**
- Pause cracking: `Ctrl + C`
- Resume cracking: Use `--restore` with the same command.

---

### **12. Combine Multiple Dictionaries**
Combine two wordlists:
```bash
hashcat -m 0 -a 1 hash.txt wordlist1.txt wordlist2.txt
```

---

### **13. Example Workflow**
1. Identify the hash type using an online tool or Hashcat's wiki.
2. Select a wordlist, e.g., `/usr/share/wordlists/rockyou.txt`.
3. Choose an attack mode:
   - Dictionary for known patterns.
   - Mask for brute-force.
   - Hybrid for mixed approaches.

---

Hashcat is a highly versatile tool, and mastering it requires experimenting with various options. Let me know if you need help with a specific use case!
