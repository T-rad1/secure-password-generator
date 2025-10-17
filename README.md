# 🔐 Secure Password Generator

A small Python script that generates cryptographically secure random passwords using Python's `secrets` module.

## Overview

This script produces strong random passwords composed of:
- lowercase letters (`a–z`)
- uppercase letters (`A–Z`)
- digits (`0–9`)
- punctuation/symbols (e.g. `!@#...`)

It is intended as a simple, audit-friendly utility to produce high-entropy passwords for user accounts, API keys, encryption keys, or other secrets.

## How it works (technical)

- The script uses Python's `secrets` module to select characters. `secrets` is specifically designed for cryptographic use and sources randomness from the operating system's secure random number generator (e.g. `/dev/urandom` on Unix).
- A character set is built by concatenating `string.ascii_lowercase`, `string.ascii_uppercase`, `string.digits`, and `string.punctuation`.
- For a requested length `n` (default `32`), the script selects `n` characters by repeatedly drawing a uniformly random index into this character set with `secrets.choice`.
- Because each character is chosen independently from a large uniform set using OS-provided cryptographic randomness, the resulting password is unpredictable and high-entropy.

## Entropy & Security (how strong is the password?)

Entropy is a measure of how many bits of unpredictability a password contains. For the default character set used here:

- Character set size = 26 (lowercase) + 26 (uppercase) + 10 (digits) + 32 (common punctuation) = **94** possible characters per position.
- Bits of entropy per character = `log2(94) ≈ 6.55` bits.
- For the default length of 32 characters:  
  **Total entropy ≈ 32 × 6.55 ≈ 209.7 bits.**

To put that in perspective:
- A 209.7-bit password means an attacker would on average need to try on the order of `2^209.7` different candidates to brute-force it — a number that is astronomically large and not feasible with any realistic compute resources today.
- Therefore, for brute-force or exhaustive-guessing attacks, a 32-character password from this character set is effectively infeasible to break.

## Threat model & limitations (what this *doesn't* protect against)

While the generated passwords are extremely strong against brute-force guessing, the script cannot protect you from other real-world attack vectors:

- **Phishing / social engineering:** If an attacker tricks a user into revealing the password, strength won’t help.
- **Reuse across services:** Reusing the same strong password on multiple services defeats the benefit — a breach at one service can expose the password for others.
- **Insecure storage/transit:** If you store the generated password in plaintext, send it over unencrypted channels, or save it in an unsafe place, it can be stolen.
- **Compromised endpoints:** If the machine generating or using the password is compromised (malware/keyloggers), the password can be captured.
- **Insider threats, backups, screenshots, cloud clipboard, etc.**
- **Implementation errors elsewhere in a system:** For example, poor password hashing or leaking logs on the server side are unrelated to generation strength.

## Practical recommendations / best practices

- **Use a password manager** (e.g., Bitwarden, 1Password, KeePassXC) to store and autofill generated passwords. This avoids reuse and makes long random passwords manageable.
- **Do not reuse passwords** between sites or services.
- **Prefer long random passwords** (32 characters is excellent) or passphrases (multiple dictionary words) for memorability if needed.
- **Keep secrets off insecure media** (avoid plain-text notes, screenshots, emails).
- **If storing user passwords on a server**, never store raw passwords — always store a salted, adaptive hash (e.g., Argon2 / bcrypt / scrypt) and follow up-to-date best practices for password storage.
- **Rotate credentials** if you suspect they may be exposed.
- **Use multi-factor authentication (MFA)** wherever possible — even a very strong password gains extra protection from a second factor.

## Usage

Run the script with Python:

```bash
python password_generator.py
