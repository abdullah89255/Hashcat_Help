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
---
Here are more advanced Hashcat examples for specialized and complex password-cracking tasks:

---

### **1. Combinator Attack (Mode `-a 1`)**
The combinator attack combines two dictionaries to form candidate passwords.

#### Example:
Combine `wordlist1.txt` and `wordlist2.txt` to crack MD5 hashes:
```bash
hashcat -m 0 -a 1 hash.txt wordlist1.txt wordlist2.txt
```
This will try combinations like:
```
password123 + hello = password123hello
hello + password123 = hellopassword123
```

---

### **2. Hybrid Attack (Mode `-a 6` and `-a 7`)**
#### **a. Dictionary + Mask (`-a 6`):**
Attach a mask to every word in the dictionary.

##### Example:
Use `rockyou.txt` and append two digits (`?d?d`) to each word to crack an NTLM hash:
```bash
hashcat -m 1000 -a 6 hash.txt rockyou.txt ?d?d
```
Tries passwords like:
```
password01
hello99
```

#### **b. Mask + Dictionary (`-a 7`):**
Prepend a mask to every word in the dictionary.

##### Example:
Prepend three lowercase letters (`?l?l?l`) to words in `rockyou.txt`:
```bash
hashcat -m 1000 -a 7 hash.txt ?l?l?l rockyou.txt
```
Tries passwords like:
```
abcpassword
xyzhello
```

---

### **3. Rule-Based Attack (Advanced)**
Rules are powerful for transforming words in a dictionary dynamically.

#### Example 1: Appending Numbers
Use the `best64.rule` to transform `rockyou.txt`:
```bash
hashcat -m 0 -a 0 -r /usr/share/hashcat/rules/best64.rule hash.txt rockyou.txt
```
Tries transformations like:
```
password -> password123
hello    -> hello!
```

#### Example 2: Custom Rule
Create a rule file (`custom.rule`) with transformations:
```
:            # No change
$1           # Append "1"
$2           # Append "2"
^pass        # Prepend "pass"
```

Run with the custom rule:
```bash
hashcat -m 0 -a 0 -r custom.rule hash.txt rockyou.txt
```
Tries passwords like:
```
password1
password2
passpassword
```

---

### **4. Distributed Cracking**
Distribute the workload across multiple devices or machines.

#### Example: Splitting the Keyspace
Split the keyspace manually for distributed cracking:
Machine 1:
```bash
hashcat -m 0 -a 3 -s 0 -l 500000 hash.txt ?a?a?a?a
```
Machine 2:
```bash
hashcat -m 0 -a 3 -s 500001 -l 1000000 hash.txt ?a?a?a?a
```

---

### **5. Advanced Mask Attacks**
#### Example 1: Custom Character Sets
Define custom character sets for efficient brute-forcing:
```bash
hashcat -m 0 -a 3 -1 ?l?u ?1?1?1?1?1?1
```
This tries all combinations of 6 characters using uppercase and lowercase letters.

#### Example 2: Pin Code Cracking
Crack a 6-digit numeric PIN:
```bash
hashcat -m 0 -a 3 hash.txt ?d?d?d?d?d?d
```

---

### **6. Cracking Password Lists (Multiple Hashes)**
#### Example:
Crack multiple hashes stored in `hashlist.txt` using `rockyou.txt`:
```bash
hashcat -m 0 -a 0 hashlist.txt rockyou.txt --username
```
The `--username` flag skips usernames and focuses only on the hashes.

---

### **7. Hashcat Tuning**
#### Example 1: Increase Performance
Optimize cracking with kernel tuning (`-O`) and workload profiles:
```bash
hashcat -m 0 -a 0 -O --optimized-kernel-enable --workload-profile 3 hash.txt wordlist.txt
```

#### Example 2: Limit GPU Usage
Limit GPU workload to avoid overheating:
```bash
hashcat -m 0 -a 0 --gpu-temp-abort=85 hash.txt wordlist.txt
```

---

### **8. Cracking Salty Hashes**
For salted hashes, specify the salt using `--separator` or append salts manually in the input file.

#### Example:
Crack salted SHA1 hashes (`salt:hash` format in `hashlist.txt`):
```bash
hashcat -m 110 --separator : hashlist.txt rockyou.txt
```

---

### **9. Incremental Mask Attack**
Start with smaller lengths and increment up:
```bash
hashcat -m 0 -a 3 hash.txt ?a?a?a?a --increment --increment-min=3 --increment-max=6
```
Tries lengths 3 to 6 with all characters.

---

### **10. Debugging and Advanced Output**
#### Example 1: Show Only Cracked Passwords
```bash
hashcat -m 0 -a 0 --show hash.txt
```

#### Example 2: Output Debug Information
Enable debugging to analyze failed attempts:
```bash
hashcat -m 0 -a 0 --debug-mode=1 --debug-file=debug.txt hash.txt wordlist.txt
```

---

### **11. Combined Hash Modes**
If the hash type is unknown, use `--hash-mode` with multiple modes:
```bash
hashcat -a 0 -m 0,1000 hash.txt wordlist.txt
```

---

### **12. Restore Sessions**
Resume cracking from a saved session:
```bash
hashcat --restore --session mysession
```

---

Here are even more **advanced Hashcat examples** for specific use cases, combining multiple techniques and features.

---

## **1. Hashcat for WPA/WPA2 Cracking**
Cracking Wi-Fi passwords using a captured `.cap` file.

#### Step 1: Convert `.cap` to `.hccapx`
Convert the `.cap` file (captured handshake) to `.hccapx` using [hcxtools](https://github.com/ZerBea/hcxtools):
```bash
hcxpcapngtool -o output.hccapx capture.cap
```

#### Step 2: Crack the Password
Use a wordlist to crack the WPA hash:
```bash
hashcat -m 2500 -a 0 output.hccapx wordlist.txt
```

#### Step 3: Add Rules for Better Results
```bash
hashcat -m 2500 -a 0 -r /usr/share/hashcat/rules/best64.rule output.hccapx wordlist.txt
```

---

## **2. Cracking Multiple Types of Hashes**
Use a hash file containing multiple hash types. Hashcat will auto-detect the format with `--self-test-disable`.

#### Example:
```bash
hashcat -a 0 hashes.txt wordlist.txt --self-test-disable
```

---

## **3. Bruteforcing Long Passwords with Masks**
#### Example: Cracking a 12-character password with mixed alphanumeric and special characters
```bash
hashcat -m 0 -a 3 hash.txt ?a?a?a?a?a?a?a?a?a?a?a?a
```

#### Split Keyspace for Large Masks:
Split the workload for distributed cracking:
```bash
hashcat -m 0 -a 3 -s 0 -l 100000 hash.txt ?a?a?a?a?a?a?a?a?a?a?a?a
```

---

## **4. Cracking Passwords with Known Patterns**
If you know the password structure, use custom masks.

#### Example 1: Phone Numbers (10 Digits)
```bash
hashcat -m 0 -a 3 hash.txt ?d?d?d?d?d?d?d?d?d?d
```

#### Example 2: Year and Name
Crack passwords starting with a year followed by a name:
```bash
hashcat -m 0 -a 3 hash.txt 20?d?dJohn
```

---

## **5. Cracking Salted Hashes**
Some hashes are salted, requiring additional input.

#### Example: Format `salt:hash`
Prepare a hash file (`hashes.txt`) like this:
```
salt1:hash1
salt2:hash2
```
Run with the `--separator` option:
```bash
hashcat -m 120 --separator : hashes.txt wordlist.txt
```

---

## **6. Hybrid Rule + Mask Attack**
Combine a rule-based dictionary attack with a mask to create strong candidates.

#### Example:
Use `best64.rule` and append a mask:
```bash
hashcat -m 0 -a 6 -r /usr/share/hashcat/rules/best64.rule hash.txt wordlist.txt ?d?d?d
```

---

## **7. Advanced GPU Tuning**
Optimize GPU usage for better performance.

#### Example: Limit GPU Workload
```bash
hashcat -m 0 -a 3 --gpu-temp-abort=80 --workload-profile=3 hash.txt ?a?a?a?a?a?a?a?a
```

#### Example: Bypass Driver Crashes
```bash
hashcat -m 0 -a 0 --force hash.txt wordlist.txt
```

---

## **8. Cracking Passwords with Specific Character Sets**
Define custom character sets for masks.

#### Example 1: Uppercase Letters and Numbers Only
```bash
hashcat -m 0 -a 3 -1 ?u?d hash.txt ?1?1?1?1?1?1?1?1
```

#### Example 2: Exclude Special Characters
```bash
hashcat -m 0 -a 3 -1 ?l?u?d hash.txt ?1?1?1?1?1
```

---

## **9. Reverse Mask Attack**
If you suspect passwords are similar but reversed, preprocess the wordlist.

#### Example:
Reverse each word in `wordlist.txt` and save to `reversed.txt`:
```bash
rev wordlist.txt > reversed.txt
```
Run Hashcat with the reversed wordlist:
```bash
hashcat -m 0 -a 0 hash.txt reversed.txt
```

---

## **10. Cracking Passwords from PII (Personal Identifiable Information)**
Generate candidates from PII such as names, dates of birth, etc.

#### Example: Prepend and Append Years
Use `john` as the base password and try common years:
```bash
hashcat -m 0 -a 3 hash.txt ?d?d?d?djohn
```

---

## **11. Debugging Complex Hash Lists**
#### Example: Save Cracked Passwords to a File
```bash
hashcat -m 0 -a 0 -o cracked.txt hash.txt wordlist.txt
```

#### Example: Debug Uncracked Attempts
Enable debugging to analyze why candidates fail:
```bash
hashcat -m 0 -a 0 --debug-mode=1 --debug-file=failed_attempts.txt hash.txt wordlist.txt
```

---

## **12. Combining Rules and Custom Character Sets**
Apply custom rules and brute force with masks.

#### Example:
```bash
hashcat -m 0 -a 3 -r /usr/share/hashcat/rules/best64.rule -1 ?l?u?d hash.txt ?1?1?1?1
```

---

## **13. Cracking Specific Password Lengths**
#### Example: Crack MD5 passwords of exactly 10 characters:
```bash
hashcat -m 0 -a 3 --increment --increment-min=10 --increment-max=10 hash.txt ?a?a?a?a?a?a?a?a?a?a
```

---

## **14. Targeted Dictionary Attack with Rules**
Use targeted rules for common password transformations.

#### Example: Add years and symbols:
```bash
echo '$1$2$3$4$5' > targeted.rule
hashcat -m 0 -a 0 -r targeted.rule hash.txt wordlist.txt
```

---

Hashcat is versatile, and combining its features can crack even the toughest hashes. If you need help with more specific scenarios, feel free to ask!
