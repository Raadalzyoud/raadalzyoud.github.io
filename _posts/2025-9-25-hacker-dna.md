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
![Desktop View](/assets/hackerdna/Book-Cipher-2.png){: width="600" height="350" }

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
...
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

![Desktop View](/assets/hackerdna/Book-Cipher-1.png){: width="600" height="350" }
