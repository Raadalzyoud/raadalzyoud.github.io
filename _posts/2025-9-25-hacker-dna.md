---
title: "HackerDNA-Write-Up's"
date: 2025-09-25 12:00:00 +0300
categories: [CTF, MISC, OSINT]
author: Ra'ad
description: "A detailed write-up's for HackerDNA Challenge's"
toc: true
comments: true
media_subpath: /assets/hackerdna/
---
## Book Cipher Challenge
You can view the Book Cipher Challenge [here](https://hackerdna.com/labs/book-cipher-challenge).
![Desktop View](/book1.png){: width="600" height="350" }

# Challenge Overview
This challenge presents a **book cipher**, a classical cryptography technique. The encrypted message is a series of **triplets** in the format: `Line:Word:Letter`.

---

Each triplet indicates a **specific letter** in a reference text (provided). By extracting letters according to these instructions, the hidden message is revealed.

**Example:**

- Cipher: `1:2:1`
- Reference Text Line 1: `"The Art of War by Sun Tzu"`
- Step:
  1. Go to **Line 1**
  2. Take **Word 2** → `"Art"`
  3. Take **Letter 1** → `"A"`

---

```text
Encrypted Message: 1:3:2 5:4:1 5:4:3 1:5:1 5:4:7 5:4:9 5:4:4 5:4:5 5:7:1 5:2:8 5:4:9 5:5:3 5:4:7 5:7:1 5:4:5 5:4:6 5:4:7 5:4:9 5:7:1 1:5:1 5:4:9 5:5:3 5:4:3 5:7:1 5:4:9 5:5:3 5:4:5 1:1:3 5:4:7 3:2:1 5:5:3 5:5:3 3:2:1 5:4:7 1:5:1 5:4:10
```

---

# Reference Text (Partial)

```text
Line 1: The Art of War by Sun Tzu
Line 5: Note: Reference numbers 0123456789 and symbols -_@#$%^&*()+={}[]|;:'"<>,.?/
Line 7: Sun Tzu said: The art of war is of vital importance to the State.
Line 8: It is a matter of life and death, a road either to safety or to ruin.
```

> **Note:** Full reference text is available in `reference_text.txt` provided in the challenge.

---

# How the Cipher Works

1. Each triplet `[Line:Word:Letter]` refers to:
   - **Line** number in the reference text (including blank lines)
   - **Word** number in that line
   - **Letter** number in that word

2. Example decoding:

- Cipher: `5:4:1`
- Line 5: `"Note: Reference numbers 0123456789 and symbols -_@#$%^&*()+={}[]|;:'"<>,.?/"`
- Word 4 → `"0123456789"`
- Letter 1 → `"0"`

3. Decoding each triplet sequentially reconstructs the hidden message.

---

# Automation

Use Python to automate decoding:

```python
# Load reference text
with open("reference_text.txt") as f:
    lines = [line.strip() for line in f]

# Encrypted message
cipher = "1:3:2 5:4:1 5:4:3 1:5:1 5:4:7 5:4:9 5:4:4 5:4:5 5:7:1 5:2:8 5:4:9 5:5:3 5:4:7 5:7:1 5:4:5 5:4:6 5:4:7 5:4:9 5:7:1 1:5:1 5:4:9 5:5:3 5:4:3 5:7:1 5:4:9 5:5:3 5:4:5 1:1:3 5:4:7 3:2:1 5:5:3 5:5:3 3:2:1 5:4:7 1:5:1 5:4:10"
cipher_list = cipher.split()

decoded = []

for triplet in cipher_list:
    line_idx, word_idx, letter_idx = map(int, triplet.split(":"))
    line = lines[line_idx-1]  # 1-indexed
    words = line.split()
    word = words[word_idx-1]
    letter = word[letter_idx-1]
    decoded.append(letter)

flag = "".join(decoded)
print(flag)
```

---

# Key Tips

- **Line Counting:** Include blank lines; cipher numbering counts them.
- **Word Splitting:** Words are space-separated.
- **Symbols & Numbers:** Count as part of words.
- **Automation:** Reduces errors and speeds up decoding.
- **Flag Format:** Hint suggests a **UUID format** (e.g., `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`).

---

# Conclusion

The Book Cipher Challenge is a **classical cryptography challenge**. By systematically mapping `[Line:Word:Letter]` triplets to the reference text:

1. Extract letters from the text according to triplets.
2. Combine letters to reveal the hidden message.
3. The final decoded output is the **flag**, which follows a UUID-like format.

> The actual flag depends on the full reference text provided.

![Desktop View](/book2.png){: width="600" height="350" }

---

## KeePass Breaker
You can view the KeePass Breaker [here](https://hackerdna.com/labs/keepass-breaker).
![Desktop View](/keepass1.png){: width="600" height="350" }

---

# Challenge Overview

**Objective :** Crack a modern KeePass 4.x encrypted vault and extract the hidden flag.  

This challenge provides a KeePass 4 `.kdbx` database (`challenge_vault.kdbx`) encrypted with a master password. The flag is stored as a password entry inside the vault.  

**Hint:** The flag is stored as a password entry inside the encrypted vault.  

---

# Tools Used

- [keepass4brute](https://github.com/r3nt0n/keepass4brute) – a modern KeePass 4 brute-force tool.  
- A `wordlist.txt` containing candidate passwords.  
- Kali Linux environment for running the brute-force attack.  

---

# Methodology

1. **Setup Environment**

```bash
git clone https://github.com/r3nt0n/keepass4brute
cd keepass4brute
chmod +x keepass4brute.sh
```

2. **Prepare Wordlist**

Use a simple wordlist or a custom dictionary containing common passwords, e.g., `wordlist.txt`.

3. **Run the Brute Force Attack**

```bash
./keepass4brute.sh challenge_vault.kdbx wordlist.txt
```

The tool iterates through the wordlist, testing each password against the encrypted KeePass vault.  

4. **Monitor Progress**

During the attack, the script outputs:

```
[+] Words tested: 83/208 - Attempts per minute: 177 - Estimated time remaining: 42 seconds
[+] Current attempt: PASSWORD
```
![Confused Guy](https://media.tenor.com/images/…/confused-black-guy-what-akward.gif)

5. **Password Found**

The brute-force attack successfully identified the master password:

```
[*] Password found: PASSWORD
```

---

# Accessing the Vault

Once the password is recovered, open the KeePass database using:

```bash
keepassxc challenge_vault.kdbx
```

Or programmatically using Python:

```python
from pykeepass import PyKeePass

kp = PyKeePass('challenge_vault.kdbx', password='PASSWORD')
for entry in kp.entries:
    print(entry.title, entry.username, entry.password)
```

The flag can then be retrieved from the corresponding password entry.

---

# Key Takeaways

- KeePass 4.x vaults use strong AES encryption, but weak master passwords remain a critical vulnerability.  
- Modern tools like `keepass4brute` simplify cracking in CTF or lab environments when legal and permitted.  
- Always use strong, unique master passwords for password managers to mitigate brute-force attacks.  
- This challenge reinforces both offensive and defensive cybersecurity skills, emphasizing password security assessment.  

---

**Flag:** Stored inside the vault entry (retrievable using the cracked password `PASSWORD`).

![Desktop View](/keepass2.png){: width="600" height="350" }
