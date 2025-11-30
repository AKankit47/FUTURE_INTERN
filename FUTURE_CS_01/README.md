# 🛡️ Web Application Security Testing Using OWASP Juice Shop  
### **A Beginner-Friendly Guide**

---

## 📌 Introduction  
In today’s digital world, web applications power everything—from online shopping to banking and education. As the number of applications grows, so does the risk of cyberattacks. Vulnerabilities such as **SQL Injection**, **Cross-Site Scripting (XSS)**, **Broken Authentication**, and **Access Control flaws** are still among the most exploited threats.

Testing these vulnerabilities on real websites is dangerous, illegal, and unethical.  
That’s where **OWASP Juice Shop** comes in.

OWASP Juice Shop is an intentionally vulnerable web application designed for safe, legal practice of web security testing. It includes OWASP Top 10 vulnerabilities, realistic attack scenarios, and step-by-step challenges that help beginners gain hands-on experience.

This guide covers how I used OWASP Juice Shop for penetration testing during my internship.

---

# 🔍 Overview of OWASP Juice Shop

OWASP Juice Shop is an insecure e-commerce web app built using:

- **Angular** frontend  
- **Node.js** backend  
- **REST APIs**  
- **100+ vulnerabilities**

It includes real-world weaknesses such as:

- SQL Injection  
- XSS (Reflected, Stored, DOM)  
- Broken Authentication  
- Insecure File Uploads  
- Security Misconfigurations  
- IDOR  
- Weak JWT Validation  

### Commonly Used By:

- Cybersecurity students  
- Bug bounty beginners  
- Pentesters  
- CTF players  
- Corporate training programs  

In short—Juice Shop is one of the best platforms for learning modern web security.

---

# ⚙️ Setup Methods

OWASP Juice Shop can be run on almost any system. Below are the most common methods:

---

## **1️⃣ Docker (Recommended)**

Fastest and simplest setup:

```bash
docker run --rm -p 3000:3000 bkimminich/juice-shop
Then visit:

👉 http://localhost:3000
```
#2️⃣ Node.js (Local Installation)
```bash
Copy code
git clone https://github.com/bkimminich/juice-shop.git
cd juice-shop
npm install
npm start
Runs on port 3000.

```

# 3️⃣ TryHackMe / HackTheBox
Hosted instances—no installation needed.
Perfect for beginners.

# 4️⃣ Cloud Deployment
Can be deployed on:

Heroku

Azure

AWS

Useful for remote access or group training.

# 5️⃣ Pre-Built Packages
Available for:

Windows

macOS

Linux

Download, open, and start testing.

Once running, the application looks like a normal web store—
but behind the scenes it contains numerous exploitable vulnerabilities.
---

# 🧨 SQL Injection (SQLi)
SQL Injection occurs when user input is not validated and is directly inserted into SQL queries.

1️⃣ Understanding SQL Injection
Vulnerable Query:

```sql
Copy code
SELECT * FROM users WHERE email = 'input' AND password = 'input';
If input is not sanitized, attackers can inject SQL code.
```
2️⃣ SQL Injection in Juice Shop (Login Bypass)
Step 1: Go to Login
Step 2: Enter payload:

```vbnet
Copy code
' OR 1=1--
This modifies the query:
```
```sql
Copy code
SELECT * FROM users WHERE email='' OR 1=1--' AND password='';
➡ Always true → login bypassed
Often logs in as admin.
```
3️⃣ Advanced SQL Injection (DB Enumeration)
Examples:

```pgsql
Copy code
' UNION SELECT sqlite_version(), NULL--
' UNION SELECT name, sql FROM sqlite_master--
Used to enumerate database structure.
```
4️⃣ Real-World Impact
SQLi can:

Bypass authentication

Dump sensitive data

Modify or delete tables

Gain admin access

Fully compromise server

5️⃣ How to Prevent SQL Injection<br>

✔ Use prepared statements<br>
✔ Use ORMs<br>
✔ Validate & sanitize user input<br>
✔ Apply least privilege DB permissions<br>
✔ Deploy WAF (ModSecurity, Cloudflare WAF)<br>
✔ Perform regular security tests (Burp, SQLMap, ZAP)<br>
---

# 🧨 Cross-Site Scripting (XSS)
XSS allows an attacker to inject malicious JavaScript into a webpage.
This can lead to:

Cookie theft

Account hijacking

Defacement

Malware injection

Juice Shop includes Reflected, Stored, and DOM XSS.

1️⃣ Reflected XSS Example (Search Bar)
Payload:

```html
Copy code
"><iframe src=javascript:alert('XSS')>
If vulnerable, an alert box appears.
```
2️⃣ Stored XSS (Reviews Section)
Payload:

```html
Copy code
<img src=x onerror=alert('Stored XSS')>
Stored in DB → executes on every page load.
```
3️⃣ DOM XSS Example
```php-template
Copy code
http://localhost:3000/#/search?q=<script>alert(1)</script>
Executes purely on client-side.
```
4️⃣ Protection Against XSS
✔ Output encoding <br>
✔ Input sanitization (DOMPurify, Bleach)<br>
✔ CSP headers<br>
✔ HttpOnly cookies<br>
✔ Avoid dangerous JS functions (innerHTML, eval)<br>
✔ Use framework security features (Angular/React auto-escape)<br>
---

# 🔐 Weak JWT Verification
Modifying Tokens to Gain Admin Access<br>
Juice Shop uses JWT tokens for authentication.<br>
A major flaw: it accepts tampered or unsigned JWTs.<br>

1️⃣ Capturing the JWT
Browser DevTools → Local Storage → token<br>

2️⃣ Modifying the Payload
Original:

```json
Copy code
{ "email": "abc@test.com", "role": "customer" }
Modified:
```
```json
Copy code
{ "email": "abc@test.com", "role": "admin" }
Then remove the signature entirely:
```
```css
Copy code
header.payload.
```
3️⃣ Replay the Tampered Token
Replace token → refresh page →
Access:

```shell
Copy code
#/administration
➡ Admin access granted 🎉
```

4️⃣ Impact
Weak JWT validation allows attackers to:

Forge any identity

Escalate privileges

Access admin panels

Completely bypass authentication

5️⃣ Fixing Weak JWT Issues<br>
✔ Always validate JWT signatures<br>
✔ Use strong secrets<br>
✔ Set token expiration<br>
✔ Avoid storing roles in JWT<br>
✔ Perform authorization checks on server, NOT client<br>
---

# 🔓 Insecure Direct Object Reference (IDOR)
IDOR happens when apps expose internal identifiers (like user IDs) without authorization checks.

Juice Shop contains classic IDOR flaws.

1️⃣ Identify Vulnerable Endpoints
Example endpoint:

```bash
Copy code
/rest/user/whoami
2️⃣ Modify the User ID
Captured request:
```
```bash
Copy code
/rest/user/3
Changed to:
```
```bash
Copy code
/rest/user/1
➡ Displays another user’s data.
```
3️⃣ Real-World Impact
IDOR can allow attackers to:

Access other users’ personal info

Modify accounts

Access payment details

Download unauthorized files

Escalate privileges

4️⃣ Preventing IDOR<br>
✔ Server-side authorization checks<br>
✔ Avoid exposing raw IDs<br>
✔ Use indirect identifiers<br>
✔ Implement RBAC<br>
✔ Validate ownership of resources<br>
---

# 🎯 Final Summary
This task involved performing hands-on penetration testing on OWASP Juice Shop and exploring:

SQL Injection

Reflected, Stored, & DOM XSS

Weak JWT validation

IDOR vulnerabilities

Each vulnerability demonstrated how real-world applications can be compromised if secure coding practices are ignored.

OWASP Juice Shop is an excellent platform for learning practical web security and understanding modern attack techniques.

👨‍💻 Author<br>
Ankit Kumar<br>
Cybersecurity Intern | Future Intern<br>
🔗 LinkedIn: https://www.linkedin.com/in/ankit-ak47<br>
📰 Medium: https://medium.com/@ankitkumarbhambhoo<br>
