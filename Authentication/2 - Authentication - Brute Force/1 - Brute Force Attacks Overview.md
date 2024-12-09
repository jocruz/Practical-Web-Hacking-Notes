
# 🔐 Brute Force Attacks Overview

## Key Takeaways

- **Brute Force Attacks** involve systematically guessing credentials using trial and error.
- Common targets include:
  - **Usernames**
  - **Passwords**
  - **Email addresses**
- Utilize **wordlists** from public sources or prior reconnaissance:
  - Examples: `[[Seclists]]`, `[[Custom Wordlists]]`

---

## Common Indicators to Monitor
- **Content Length**: Critical for identifying valid responses.
- **Status Codes**: Watch for codes like `302` for successful redirects.
- **Error Messages**: Provide clues (e.g., "Invalid Username").
- **Response Times**: Can indicate valid/invalid attempts.

---

## Commands to Use
### Finding Wordlists:
```bash
find /usr/share/seclists -iname "*subdomain*"
find /usr/share/seclists -iname "*username*"
```

### Running `ffuf` for Brute Force:
```bash
ffuf -request req.txt -request-proto https -mode clusterbomb \
-w /path/to/usernames.txt:FUZZUSER \
-w /path/to/passwords.txt:FUZZPASS -mc 302
```

---

## Related Notes
- [[Raw Notes - Brute Force]]
- [[2 - Lab - Brute Forcing Authentication]]

---

## Related Labs
- [[Lab - Username Enumeration via Subtly Different Responses]]
- [[2 - Lab - Brute Forcing Authentication]]

---

#brute-force #authentication 
