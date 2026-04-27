# 🔐 Cyber Security Research – Sanchar Saathi Application

> 📌 Complete Mobile Application Security, Reverse Engineering, Runtime & Forensic Analysis

---

# 📌 Overview

This repository presents a comprehensive and structured analysis of the **Sanchar Saathi mobile application**, covering all major aspects of mobile security investigation including:

* Static Analysis
* Decompiled Code Analysis
* Runtime (Dynamic) Analysis
* Network Analysis
* Forensic Analysis

The objective of this project is to understand how the application operates internally, how it interacts with backend systems, and what level of data is accessible from a forensic perspective.

---

# 🎯 Objectives

* Analyze application permissions and configurations
* Reverse engineer internal logic using decompilation
* Observe runtime behavior under controlled scenarios
* Study network communication and security controls
* Evaluate forensic artefacts available on a user device
* Understand security vs forensic visibility trade-offs

---

# 🧭 Methodology

The analysis follows a structured pipeline:

APK → Static Analysis → Decompilation → Code Analysis → Runtime Analysis → Network Analysis → Forensic Analysis

Each phase builds on the previous stage to provide a complete understanding of the application.

---

# 🔍 1️⃣ Static Analysis (MobSF)

📄 👉 [Open Static Analysis](./Reports/Static%20Analysis/README.md)

Static analysis was performed using MobSF to analyze the APK without executing it.

### Key Areas Covered:

* Permissions requested by the application
* Activities, services, and broadcast receivers
* Exported components
* Network security configuration
* Hardcoded strings and potential vulnerabilities

### Observations:

* Presence of multiple sensitive permissions
* Secure network configuration enforced
* No major critical vulnerabilities visible statically

### Insight:

This phase provided a high-level understanding of the application structure and potential attack surface.

---

# 🧬 2️⃣ Decompiled Code Analysis

📄 👉 [Open Decompiled Section](./Tools/Decompiled/README.md)

The APK was decompiled using APKTool and analyzed alongside JADX.

### Decompiled Files:

* `AndroidManifest.xml`
* `apktool.yml`
* [`decompiled.zip`](./Tools/Decompiled/decompiled.zip)

---

## 🔁 Background Sync Mechanism

* Implemented using periodic scheduling
* Runs approximately every **15 minutes**
* Indicates continuous backend communication

---

## 📞 Call Log Filtering

* Only collects:

  * Incoming
  * Missed
  * Rejected

* Excludes:

  * Outgoing calls

👉 Indicates selective monitoring behavior

---

## 📅 Data Retention

* Call logs limited to approximately **29 days**

👉 Suggests controlled retention policy

---

## 📶 SIM & Device Tracking

* Extracts:

  * ICC ID
  * Subscription ID
  * SIM slot index

👉 Enables SIM → device → user linkage

---

## 🆔 Device Identification

* Uses `MediaDrm deviceUniqueId`
* Does not rely on IMEI

👉 Works without restricted permissions

---

## 🔐 Database Security

* Uses SQLCipher encryption

⚠️ Observation:

* Key handling may fallback to empty value

👉 Security depends on implementation of encryption key

---

## 🔄 Offline Data Handling

* Method: `submitOfflineData()`
* Supports offline → later synchronization

---

## 🧠 Architecture

* Hybrid:

  * Flutter → UI layer
  * Native Android → logic layer

---

# ⚙️ 3️⃣ Runtime (Dynamic) Analysis

📄 👉 [Open Dynamic Analysis](./Reports/Dynamic%20Analysis/README.md)

Runtime behavior was analyzed using logs and system tools under different conditions.

---

## 🟢 Normal Launch

📄 👉 [View Log](./Reports/Dynamic%20Analysis/test1_normal_launch.txt)

* App initializes successfully
* `_connectionStatus = true`
* Backend API triggered
* No errors observed

👉 Serves as baseline behavior

---

## 🔴 Network-Off Scenario

📄 👉 [View Log](./Reports/Dynamic%20Analysis/test2_network_off.txt)

* DNS failures observed
* Requests fail with status **101**
* App continues retrying
* Recovers automatically when network returns

👉 Demonstrates fault tolerance and retry mechanism

---

## 🟡 Permission Denied Scenario

📄 👉 [View Log](./Reports/Dynamic%20Analysis/test3_permission_denied.txt)

* Permissions initially denied
* User grants permissions
* Features resume successfully

👉 Confirms proper permission lifecycle

---

# 🌐 4️⃣ Network Analysis

📄 👉 [Open Network Analysis](./Reports/Network%20Analysis/README.md)

Network behavior was analyzed using runtime logs and Burp Suite.

---

## 🔗 Observed Endpoint

```text
https://ssapp.sancharsaathi.gov.in/clins/online/v1.1.0/knowYourConnection
```

---

## 🔒 Security Mechanisms

### HTTPS Enforcement

* Cleartext traffic disabled

---

### SSL Pinning

* Traffic interception failed
* Custom certificates rejected

👉 Prevents MITM attacks

---

## 🔄 Behavior

* Requests retried during failures
* Successful after recovery

---

## ⚠️ Hidden API Layer

* Endpoints not fully visible in smali
* Stored in Flutter snapshot

---

# 🧪 5️⃣ Forensic Analysis

📄 👉 [Open Forensic Analysis](./Reports/Forensic%20Analysis/README.md)

Forensic analysis was performed under non-rooted conditions.

---

## Tools Used

* Magnet AXIOM
* MOBILedit

---

## Findings

### ✅ Accessible

* App presence
* Package details
* Basic logs

---

### ❌ Not Accessible

* Internal databases
* Private storage (`/data/data/`)
* Sensitive user data

---

## 🔐 Security Controls

* Android sandboxing
* SQLCipher encryption

---

## 🧠 Key Insight

* Minimal local data storage
* Most processing likely server-side

👉 Strong privacy-focused design

---

# 📦 Split APK Analysis

📄 👉 [Open Split APK Section](./Tools/Split%20APK%20Files/README.md)

Files include:

* `base.apk`
* Language split APKs
* DPI-specific APK

---

## Insight

* Core logic resides in `base.apk`
* Other APKs contain only resources

---

# ⚙️ Tools Used

📄 👉 [Open Tools Section](./Tools/README.md)

Workflow:

ADB → AVD → Docker → MobSF → APKTool → Cursor → Frida → Logcat → Dumpsys → Burp Suite → Magnet AXIOM → MOBILedit → Magisk → Odin

---

# 🧠 Key Insights

* Hybrid communication model (API + SMS + system services)
* Continuous background activity
* Strong encryption and secure communication
* Controlled data collection
* Limited forensic visibility

---

# ⚖️ Final Conclusion

The Sanchar Saathi application demonstrates:

* Strong security mechanisms
* Controlled and structured data handling
* Robust runtime behavior
* Limited user-side forensic evidence

---

## 🔥 Core Takeaway

👉 **Security & Privacy are prioritized over forensic accessibility**

---

# 📌 Final Note

This repository represents a complete workflow from:

**APK Analysis → Reverse Engineering → Runtime Observation → Network Evaluation → Forensic Validation**

It reflects both technical depth and practical investigation methodology.

---
