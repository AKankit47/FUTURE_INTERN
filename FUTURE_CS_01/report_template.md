# 🛡️ Task 01 Report  
## Web Application Security Testing Using OWASP Juice Shop  
**Internship:** Future Intern – Cybersecurity Track  
**Intern:** Ankit Kumar  

---

# 📌 1. Introduction

This report documents the findings of my security assessment performed on **OWASP Juice Shop**, an intentionally vulnerable web application used for ethical hacking practice.  

The purpose of this task is to identify and exploit common web application vulnerabilities present in modern systems, understand their real-world impact, and document secure mitigation strategies.

During this assessment, I analyzed several critical vulnerabilities, including:

- SQL Injection (SQLi)  
- Cross-Site Scripting (Reflected, Stored, DOM)  
- Weak JWT Validation  
- Insecure Direct Object Reference (IDOR)

All exploits were performed in a fully legal, controlled environment as part of the Future Intern cybersecurity internship.

---

# 📌 2. Scope of Work

### **Target Application**
OWASP Juice Shop (Local Instance)

### **In-Scope Activities**
- Manual penetration testing  
- Input manipulation  
- JWT tampering  
- API request interception  
- Access control testing  

### **Out of Scope**
- Denial-of-service attacks  
- Automated brute-force  
- Server-side exploitation outside the app

---

# 🔧 3. Tools & Environment

### **Tools Used**
- Burp Suite Community Edition  
- Browser DevTools  
- SQLMap (limited use)  
- Docker / Node.js (application hosting)  
- TryHackMe Juice Shop instances (optional)

### **Environment**
- Operating System: Windows / Kali Linux  
- Browser: Chrome / Firefox  
- Juice Shop running on port **3000**

---

# 🧭 4. Methodology

I followed a structured penetration testing approach:

### **1. Recon & Enumeration**
- Understanding application endpoints
- Mapping API routes
- Observing parameter behavior

### **2. Input Testing**
- Injecting SQL payloads
- Entering malicious XSS scripts
- Modifying API request parameters

### **3. Exploitation**
- Bypassing authentication
- Triggering JS execution through XSS
- Forging JWT tokens
- Accessing other users’ resources

### **4. Verification**
- Reproducing vulnerabilities multiple times
- Confirming impact on application functionality

---

# 🚨 5. Vulnerabilities Identified

Below is a detailed breakdown of all vulnerabilities discovered.

---

# 🔥 5.1 SQL Injection (Authentication Bypass)

### **Description**
The login form fails to sanitize user input and is vulnerable to SQL Injection. This allows attackers to bypass authentication using malicious payloads.

### **Affected Endpoint**
POST /rest/user/login

markdown
Copy code

### **Steps to Reproduce**
1. Open the login page  
2. Enter this payload in the email field:  
' OR 1=1--

yaml
Copy code
3. Submit the form  
4. Application logs in without password verification

### **Impact**
- Allows login without valid credentials  
- May grant admin-level access  
- Full compromise of user accounts  

### **Severity:** **Critical**

### **Fix Recommendation**
- Use **parameterized queries**  
- Validate and sanitize all user inputs  
- Implement proper authentication checks  

---

# 🧨 5.2 Cross-Site Scripting (XSS)

OWASP Juice Shop contains all three types of XSS vulnerabilities.

---

## 🔹 A. Reflected XSS

### **Description**
The search bar reflects user input without encoding, enabling JavaScript execution.

### **Payload Used**
html
"><iframe src=javascript:alert('XSS')>
Impact
Session hijacking

Cookie theft

User impersonation

Severity: High
🔹 B. Stored XSS
Description
Review/comment sections store user inputs in the database without sanitization.

Payload Used
html
Copy code
<img src=x onerror=alert('Stored XSS')>
Impact
Persistent execution on every page load

Can affect all users including admins

Severity: Critical
🔹 C. DOM-Based XSS
Description
Search functionality processes input through insecure DOM manipulation.

Tested URL
php-template
Copy code
http://localhost:3000/#/search?q=<script>alert(1)</script>
Impact
Client-side script execution

Bypasses server-side protections

Severity: High
Fix Recommendation
Encode output before rendering

Sanitize HTML using DOMPurify

Avoid dangerous JS functions (innerHTML, eval)

Implement CSP headers

---

# 🔐 5.3 Weak JWT Verification (Privilege Escalation)
Description
The application accepts tampered or unsigned JWT tokens.
By modifying the payload and removing the signature, the attacker can elevate privileges.

Steps to Exploit
Capture JWT token from browser Local Storage

Modify payload:

json
Copy code
{ "email": "test@example.com", "role": "admin" }
Remove signature

Replace existing token in Local Storage

Reload application → Admin access granted

Impact
Full admin takeover

Ability to manage entire application

No password required

Severity: Critical
Fix Recommendation
Enforce strict signature validation

Use strong secret keys

Store roles server-side

Reject unsigned tokens immediately
---

# 🔓 5.4 Insecure Direct Object Reference (IDOR)
Description
Certain API endpoints expose user IDs and do not enforce authorization checks. Changing the ID returns another user's data.

Affected Endpoint Example
sql
Copy code
/rest/user/3  → attacker  
/rest/user/1  → another user  
Steps to Reproduce
Intercept /rest/user/<id> request

Modify ID to another integer

Forward the request

Receive another user’s details

Impact
Access to other users' profiles

Exposure of sensitive data (email, addresses)

Possible account manipulation

Severity: High
Fix Recommendation
Implement server-side access checks

Use indirect identifiers

Enforce RBAC for sensitive endpoints
---

# 📝 6. Conclusion
This task provided hands-on experience with real-world web vulnerabilities using OWASP Juice Shop.
Throughout the assessment, I gained practical knowledge of:

Exploiting SQL Injection

Triggering Reflected, Stored, and DOM XSS

Breaking JWT authentication

Exploiting IDOR for unauthorized access

Understanding secure coding and mitigation techniques

This exercise highlights the importance of:

Secure input validation

Strong authentication mechanisms

Proper session handling

Server-side authorization
---

OWASP Juice Shop serves as an excellent platform for learning web penetration testing in a controlled, ethical, and realistic environment.

🔗 7. References
OWASP Juice Shop GitHub Repository

OWASP Top 10 Documentation

PortSwigger Web Security Academy

TryHackMe: Juice Shop Room

Burp Suite Documentation

👨‍💻 Author
Ankit Kumar
Cybersecurity Intern – Future Intern
🔗 LinkedIn: https://www.linkedin.com/in/ankit-ak47 <br>
📰 Medium: https://medium.com/@ankitkumarbhambhoo
