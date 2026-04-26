# 📱 Static Analysis – MobSF

## 📌 Overview
This section presents a detailed static security analysis of the **Sanchar Saathi mobile application** using MobSF (Mobile Security Framework).

The objective of this analysis is to identify vulnerabilities, insecure configurations, permission risks, and potential attack surfaces within the application.

---

## 📊 Application Details

- **App Name:** Sanchar Saathi  
- **Version:** 1.5.0  
- **Package:** com.dot.app.sancharsaathi  
- **APK Size:** ~11.46 MB  
- **Target SDK:** 35  
- **Min SDK:** 28  

---

## 📈 Security Score & Risk Assessment

- **Security Score:** 65–67 / 100  
- **Risk Level:** Low Risk  
- **Grade:** A  


## 📄 Reports

📑 **Detailed Analysis Files**

- 🔍 [Complete Static Analysis Report](complete-static-analysis.pdf)  
- 📊 [MobSF Security Scorecard](MobSF-Security-Scorecard.pdf)  
- 📋 [Full Technical Report](sanchar-saathi-mobsf-report.pdf)  

👉 The application falls under **low-risk category**, but multiple medium-level issues were identified that could impact security and privacy.

---

## ⚠️ Vulnerability Analysis

### 🔴 High Severity
- Use of **CBC encryption with PKCS7 padding**
  - Vulnerable to padding oracle attacks  
  - Weak cryptographic implementation  
  - Needs migration to secure modes like GCM  

---

### 🟡 Medium Severity

- **External Storage Access**
  - App can read/write external storage  
  - Risk: Other apps can access sensitive data  

- **IP Address Disclosure**
  - Network-level exposure of internal endpoints  

- **Weak Hashing Algorithm**
  - SHA-1 used → vulnerable to collisions  

- **Hardcoded Sensitive Data**
  - Possibility of embedded secrets in code  

- **Temporary File Usage**
  - Sensitive data may be written insecurely  

- **Unprotected Components**
  - Exported services/receivers may be accessed externally  

---

### 🔵 Informational Issues

- Sensitive data logging  
- Clipboard data exposure risk  
- Firebase backend communication  
- Debug-related traces  

---

## 🔐 Permission Analysis

The application requests multiple **high-risk (dangerous) permissions**, including:

- SMS (READ_SMS, SEND_SMS)  
- Call Logs (READ_CALL_LOG)  
- Phone State  
- Camera Access  
- External Storage  

👉 These permissions increase the **attack surface** and may lead to:
- Data leakage  
- Privacy violations  
- Unauthorized access  

📌 Observation:
Although permissions are extensive, their usage aligns partially with app functionality but still requires strict control.

---

## 🧠 Code & Behavior Insights

- Use of **SQLite database with raw queries**
  - Risk: SQL Injection possibility  

- Use of **insecure random number generator**
  - Weak cryptographic practices  

- Root detection mechanisms present  
- Potential root access requests  

- Sensitive operations observed:
  - SMS access  
  - Call log access  
  - Contact data queries  

---

## 🌐 Network Security Analysis

- Secure configuration prevents cleartext traffic  
- SSL pinning implemented  
- Firebase communication detected  

👉 Indicates:
- Good protection against MITM attacks  
- Controlled network communication  

---

## 🧾 Certificate & Signing Analysis

- Application is digitally signed  
- Uses SHA-256 hashing  
- Valid certificate from trusted authority  

👉 Ensures authenticity and integrity of the application  

---

## 📊 Overall Security Posture

| Category            | Status        |
|--------------------|--------------|
| Cryptography       | ⚠️ Needs Improvement |
| Permissions        | ⚠️ High Exposure |
| Network Security   | ✅ Strong |
| Code Security      | ⚠️ Moderate Issues |
| Privacy Risk       | ⚠️ Medium |

---

## 📌 Conclusion

The application demonstrates a **generally secure architecture**, with no critical vulnerabilities, but multiple medium-level risks that require attention.

---

## 📁 Reference Files

All supporting analysis files are available in this directory:
- Static Analysis Report  
- Security Scorecard  
- MobSF Detailed Report  
