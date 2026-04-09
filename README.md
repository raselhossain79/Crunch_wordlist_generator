# Crunch Wordlist Generator - Complete Guide

## Table of Contents
1. [Overview](#overview)
2. [Installation](#installation)
3. [Basic Syntax](#basic-syntax)
4. [Pattern Symbols](#pattern-symbols)
5. [Core Options](#core-options)
6. [Practical Examples](#practical-examples)
7. [Special Characters](#special-characters)
8. [Advanced Usage](#advanced-usage)
9. [Common Mistakes](#common-mistakes)
10. [Best Practices](#best-practices)
11. [Warnings & Legal](#warnings--legal)

---

## Overview

**Crunch** is a powerful wordlist generation tool designed to create custom password lists and number sequences based on defined patterns. It's commonly used for:
- Penetration testing
- Password auditing
- Brute-force list generation
- Pattern-based security testing

---

## Installation

```bash
# On Debian/Ubuntu-based systems
sudo apt-get install crunch

# Check installation
crunch --help
```

---

## Basic Syntax

### Simple Generation
```bash
crunch <min_length> <max_length> [options]
```

### Example 1: Generate all 3-digit combinations
```bash
crunch 3 3 -o output.txt
```
**Output:**
```
000
001
002
...
999
```

### Example 2: Generate 4-digit combinations
```bash
crunch 4 4
```
This generates 10,000 combinations (0000-9999)

---

## Pattern Symbols

The `-t` (template) option allows you to define fixed and variable portions:

| Symbol | Meaning | Example |
|--------|---------|---------|
| `@` | Lowercase letter (a-z) | `pass@` → pass a, pass b, ... |
| `,` | Uppercase letter (A-Z) | `Pass,` → Pass A, Pass B, ... |
| `%` | Digit (0-9) | `pass%` → pass0, pass1, ... |
| `^` | Special character (!@#$%) | `pass^` → pass!, pass@, ... |

---

## Core Options

### `-t` (Template/Pattern)
Define which parts are fixed and which should be brute-forced.

```bash
crunch <length> <length> -t <pattern>
```

**Example:**
```bash
crunch 8 8 -t pass@@%%
```
This generates passwords like: `passaabb`, `passaabc`, `passba00`, etc.

---

### `-o` (Output)
Save the wordlist to a file.

```bash
crunch 4 4 -o output.txt
```

---

### `-s` (Start Point)
Specify where to begin the generation.

```bash
crunch 2 2 -s 50 -o output.txt
```
Output starts from: `50`, `51`, `52`, ...

---

### `-e` (End Point)
Specify where to stop the generation.

```bash
crunch 2 2 -e 55 -o output.txt
```
Output ends at: `52`, `53`, `54`, `55`

---

### `-b` (Bytes Limit)
Split output into multiple files based on size limit.

```bash
crunch 4 4 -b 1MB -o START
```
**Important:** When using `-b`, the output must be specified as `START` (not a filename). This creates files like: `START1`, `START2`, `START3`, etc.

---

### `-l` (Line Limit)
Split output into multiple files based on number of lines.

```bash
crunch 4 4 -l 50000 -o START
```
Creates new file after every 50,000 lines.

---

## Practical Examples

### Example 1: Generate Number Sequence with Unknown Digits
**Scenario:** Generate 11-digit numbers with fixed prefix and suffix, with 2 unknown digits in the middle.

```bash
crunch 11 11 -t 23456%%8554 -o number_list.txt
```

**Output:**
```
23456008554
23456018554
23456028554
...
23456998554
```
**Total combinations:** 100

---

### Example 2: Password Pattern with Mixed Characters
**Scenario:** Generate 8-character passwords starting with "Pass" followed by 2 lowercase letters and 2 digits.

```bash
crunch 8 8 -t Pass@@%% -o passwords.txt
```

**Output:**
```
Passaa00
Passaa01
Passaa02
...
Passzz99
```
**Total combinations:** 676,000 (26 × 26 × 10 × 10)

---

### Example 3: Custom PIN with Start/End Points
```bash
crunch 4 4 -s 5000 -e 5050 -o pin_list.txt
```

**Output:**
```
5000
5001
5002
...
5050
```

---

### Example 4: Split Large Wordlist
```bash
crunch 5 5 -b 10MB -o START
```

Creates: `START1`, `START2`, `START3`, etc. (each file ≤ 10MB)

---

## Special Characters

### Problem
The symbols `@`, `,`, `%`, and `^` have special meanings in Crunch. If you want them as **fixed** characters, you must escape them with a backslash `\`.

### Solution: Escape with Backslash

| Fixed Character | Command |
|-----------------|---------|
| Fixed `@` | `\@` |
| Fixed `,` | `\,` |
| Fixed `%` | `\%` |
| Fixed `^` | `\^` |

### Example 1: Fixed @ Symbol
```bash
crunch 4 4 -t "ab\@c"
```
**Output:**
```
ab@c
```

---

### Example 2: Fixed % Symbol
```bash
crunch 6 6 -t "pass\%%" 
```
**Output:**
```
pass%00
pass%01
...
pass%99
```

---

### Example 3: Mixed Fixed and Variable Special Characters
```bash
crunch 8 8 -t "num\@%%\@\@"
```
**Components:**
- `num` = fixed
- `\@` = fixed `@` character
- `%%` = brute-force 2 digits (00-99)
- `\@\@` = fixed `@@` (two @ symbols)

**Output Examples:**
```
num@00@@
num@01@@
num@02@@
...
num@99@@
```

---

### Example 4: Best Practice - Always Use Quotes
```bash
crunch 7 7 -t "data\@%%"
```
Using quotes with escape sequences provides the safest and most reliable approach.

---

## Advanced Usage

### Option 1: Split by File Size (-b)
```bash
crunch 6 6 -t "ab\@%%" -b 5MB -o START
```
**Components:**
- `6 6` = 6-character output
- `-t "ab\@%%"` = Pattern (ab@ fixed + 2 variable digits)
- `-b 5MB` = Split files at 5MB limit
- `-o START` = Create files: START1, START2, START3, etc.

**Output:**
- `START1` (5MB or less)
- `START2` (5MB or less)
- `START3` (remaining)

---

### Option 2: Split by Line Count (-l)
```bash
crunch 6 6 -t "ab\@%%" -l 100000 -o START
```
**Components:**
- `6 6` = 6-character output
- `-t "ab\@%%"` = Pattern (ab@ fixed + 2 variable digits)
- `-l 100000` = Split into files with 100,000 lines each
- `-o START` = Create files: START1, START2, START3, etc.

**Output:**
- `START1` (100,000 lines)
- `START2` (100,000 lines)
- `START3` (remaining lines)

---

### Character Sets with Symbols
```bash
crunch 10 10 -t "test\@%%\@@@"
```
**Components:**
- `test` = fixed
- `\@` = fixed `@`
- `%%` = variable digits (00-99)
- `\@` = fixed `@`
- `@@@` = variable lowercase letters (aaa-zzz)

---

### Using with Other Tools

**Pipe to Hydra (password cracking):**
```bash
crunch 4 4 | hydra -l admin -P - target.com http-post-form
```

**Pipe to John the Ripper:**
```bash
crunch 8 8 -t pass%%%% | john --wordlist=- hash.txt
```

---

## Common Mistakes

### ❌ Mistake 1: Wrong Symbol for Digits
```bash
# WRONG - @ is for letters, not digits
crunch 11 11 -t 91712@@3456

# CORRECT - % is for digits
crunch 11 11 -t 91712%%3456
```

---

### ❌ Mistake 2: Incorrect Length
```bash
# WRONG - pattern length (10) doesn't match specified length (11)
crunch 11 11 -t 9171203456

# CORRECT - pattern must be 11 characters total
crunch 11 11 -t 91712%%3456
```

---

### ❌ Mistake 3: Using Filename with -b Option
```bash
# WRONG - -b requires START
crunch 3 3 -b 1kb -o output.txt

# CORRECT - must use START
crunch 3 3 -b 1kb -o START
```

---

### ❌ Mistake 4: Not Escaping Special Characters
```bash
# WRONG - @ will be interpreted as letter symbol
crunch 4 4 -t ab@c

# CORRECT - escape the @ symbol
crunch 4 4 -t "ab\@c"
```

---

### ❌ Mistake 5: Forgetting to Escape in Quotes
```bash
# Without proper escaping
crunch 5 5 -t pass@

# With proper escaping
crunch 5 5 -t "pass\@"
```

---

## Best Practices

### 1. Always Use Quotes for Complex Patterns
```bash
crunch 8 8 -t "pass\@%%" -o output.txt
```

### 2. Test Pattern Length
Before generating large lists, verify your pattern length:
```bash
# Count pattern characters (using echo)
echo "91712%%3456" | wc -c
# Output should match your min/max length
```

### 3. Estimate Output Size
```bash
# Pattern: pass%% = 2 variable digits = 100 combinations
# Each line ~6 characters
# Estimated size: 100 × 6 = 600 bytes

crunch 6 6 -t "pass%%" -o test.txt
ls -lh test.txt
```

### 4. Use Size Limits for Large Lists
```bash
# Generate massive list but split into 100MB files
crunch 8 8 -t "pass%%%%" -b 100MB -o START
```

### 5. Redirect Output Wisely
```bash
# Save to file
crunch 4 4 > output.txt

# Pipe directly to another tool (no intermediate file)
crunch 4 4 | wc -l
```

---

## Warnings & Legal

### ⚠️ Important Legal Notice

**Crunch is a legitimate security tool, but misuse is illegal:**

- ❌ **Illegal:** Using generated wordlists to brute-force accounts you don't own
- ❌ **Illegal:** Attacking systems without explicit permission
- ❌ **Illegal:** Unauthorized password cracking
- ❌ **Illegal:** Data theft or unauthorized access

- ✅ **Legal:** Using on your own systems or authorized penetration tests
- ✅ **Legal:** Educational learning and skill development
- ✅ **Legal:** Authorized security assessments with written permission
- ✅ **Legal:** Bug bounty programs (with scope authorization)

**Only use Crunch for lawful purposes:**
- Penetration testing (with authorization)
- Security research
- Educational purposes
- Authorized password audits
- Bug bounty programs
- Your own system security testing

---

## Quick Reference Table

| Task | Command |
|------|---------|
| Generate 4-digit numbers | `crunch 4 4 -o output.txt` |
| Pattern: unknown digits | `crunch 11 11 -t 23456%%8554` |
| Pattern: letters + digits | `crunch 8 8 -t "pass@@%%"` |
| Fixed special character | `crunch 4 4 -t "ab\@c"` |
| Start from specific point | `crunch 2 2 -s 50 -o output.txt` |
| End at specific point | `crunch 2 2 -e 55 -o output.txt` |
| Split by file size | `crunch 5 5 -b 50MB -o START` |
| Split by line count | `crunch 5 5 -l 100000 -o START` |
| Count total lines | `crunch 3 3 \| wc -l` |

---

## Resources

- [Crunch Official Documentation](https://sourceforge.net/projects/crunch-wordlist/)
- [Crunch GitHub Repository](https://github.com/crunchsec/crunch)
- Related Tools: `hashcat`, `John the Ripper`, `Hydra`, `Burp Suite`

---
