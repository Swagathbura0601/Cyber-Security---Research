# 🧬 Decompiled Code Analysis – Sanchar Saathi

## 📌 Overview

This section presents a complete reverse engineering analysis of the Sanchar Saathi mobile application based on decompiled code using Apktool, JADX, and Cursor IDE.

The purpose of this analysis is not just to extract code, but to understand:

* What data the application collects
* How frequently it communicates with backend systems
* How it identifies users and devices
* What security mechanisms are implemented
* Where privacy risks may exist

---

## ⚙️ Decompilation Process

Tools Used:

* Apktool → Extracted smali code, manifest, and resources
* JADX → Converted APK into readable Java code
* Cursor IDE → Used for structured exploration of decompiled files

Command:
apktool d base.apk -o decompiled

---

# 🔍 CODE-LEVEL FINDINGS

---

## 1️⃣ Background Sync Mechanism (15-Minute Cycle)

📂 File: `MyApp.smali`
📍 Lines: 108–114

### Code

```smali
const-wide/32 v0, 0x36ee80
invoke-static {v0, v1}, Ljava/util/Timer;->scheduleAtFixedRate(...)V
```

### Explanation

* `0x36ee80` = 900000 milliseconds = **15 minutes**
* The function `scheduleAtFixedRate()` ensures repeated execution

👉 This means:

* 4 sync cycles per hour
* 96 sync operations per day

### 🔎 Interpretation (Important)

This is not just a background task — it indicates **continuous backend synchronization**.

Practically:

* The app keeps communicating with servers even without user interaction
* Data flow is persistent and automated

This strongly suggests **server-side monitoring or periodic data updates at scale**.

---

## 2️⃣ Call Log Collection Logic

📂 File: `smali/Q3/b.smali`
📍 Lines: 335–358

### Code

```smali
const/4 v1, 0x1   # TYPE_INCOMING
const/4 v2, 0x3   # TYPE_MISSED
const/4 v3, 0x5   # TYPE_REJECTED

if-eq v0, v1, :collect
if-eq v0, v2, :collect
if-eq v0, v3, :collect

const/4 v4, 0x2   # TYPE_OUTGOING
if-eq v0, v4, :skip
```

### Explanation

The application filters call types explicitly:

✔ Collected:

* Incoming calls
* Missed calls
* Rejected calls

❌ Ignored:

* Outgoing calls

### 🔎 Interpretation (Important)

This is **intentional design**, not random filtering.

Reasoning:

* Incoming/missed/rejected calls represent **external communication attempts**
* Outgoing calls are initiated by the user and may not be relevant for monitoring

👉 This shows the app is focused on **tracking incoming communication patterns**, not full call history.

---

## 3️⃣ Call Data Retention (29-Day Window)

📂 File: `smali/Q3/b.smali`

### Code

```smali
invoke-static {}, Ljava/lang/System;->currentTimeMillis()J
sub-long v0, v0, v1
const-wide/32 v2, 0x9a7ec800
```

### Explanation

* `0x9a7ec800` ≈ **29 days**

👉 The app limits collection to recent call logs only.

### 🔎 Interpretation

* Not storing full historical data
* Still retains nearly **one month of communication data**

This reflects a **controlled retention policy**, but still involves sensitive data.

---

## 4️⃣ SIM Card Tracking (Deep Identity Linkage)

📂 File: `smali/Q3/b.smali`
📍 Lines: 470–702

### Code

```smali
invoke-virtual {p0}, Landroid/telephony/SubscriptionInfo;->getIccId()Ljava/lang/String;
invoke-virtual {p0}, Landroid/telephony/SubscriptionInfo;->getSubscriptionId()I
invoke-virtual {p0}, Landroid/telephony/SubscriptionInfo;->getSimSlotIndex()I
```

### Explanation

Extracts:

* ICC ID → unique SIM serial number
* Subscription ID → internal telecom identifier
* SIM Slot Index → SIM position

### 🔎 Interpretation (CRITICAL)

This enables:

* SIM → device → user mapping
* Tracking across dual SIM environments
* Strong identity linkage

👉 This is not just device data — this is **telecom-level identity mapping**.

---

## 5️⃣ Device Identification Without IMEI

### Code

```java
MediaDrm.getPropertyByteArray("deviceUniqueId");
```

### Explanation

* Generates a unique device identifier
* Does not require IMEI permission

### 🔎 Interpretation

* Bypasses strict permission restrictions
* Works across newer Android versions

👉 Allows **persistent device tracking without sensitive permission prompts**.

---

## 6️⃣ SQLCipher Database Encryption (With Weak Key Handling)

📂 File: `smali/a.smali`

### Code

```smali
invoke-static {v0, v1, v2}, Lnet/sqlcipher/database/SQLiteDatabase;->openDatabase(...)
```

### Key Handling Code

```smali
if-nez v1, :use_key
const-string v1, ""
```

### Explanation

* Uses SQLCipher → AES-256 encryption
* Database is encrypted

### 🔎 Critical Interpretation

* If key is null → replaced with empty string
* This means:

  * Encryption exists
  * But **security depends on key quality**

👉 If key is empty or weak:
➡ encryption becomes ineffective

This is a **design-level security weakness**, not implementation absence.

---

## 7️⃣ HTTPS Enforcement

### Code

```xml
<base-config cleartextTrafficPermitted="false" />
```

### Explanation

* Blocks HTTP traffic
* Allows only HTTPS

### Interpretation

👉 Ensures encrypted communication in transit
👉 Prevents plaintext data exposure

---

## 8️⃣ SSL Pinning

### Evidence

* Burp Suite interception failed

### Explanation

* App validates certificates strictly
* Rejects custom certificates

### Interpretation

👉 Prevents:

* MITM attacks
* Traffic interception

Strong network-layer protection.

---

## 9️⃣ Flutter ↔ Android Communication

### Code

```java
MethodChannel channel = new MethodChannel(flutterEngine.getDartExecutor(), "channel");
channel.invokeMethod("submitOfflineData", args);
```

### Explanation

Flow:

1. Flutter sends request
2. Android processes
3. Result returned

### Interpretation

👉 App uses:

* Flutter → UI
* Native Android → logic

Hybrid architecture.

---

## 🔟 Hidden API Endpoints (Flutter Layer)

### Observation

* Endpoints not visible in smali
* Stored inside compiled Dart snapshot

### Interpretation

👉 Backend communication is hidden
👉 Requires tools like ReFlutter

This indicates **intentional abstraction of API logic**.

---

## 1️⃣1️⃣ Offline Data Submission

### Code Reference

```java
submitOfflineData(...)
```

### Explanation

* Triggered via Flutter → Android bridge
* Likely used for delayed data sync

### Interpretation

👉 App can:

* Collect data offline
* Send it later automatically

---

## 1️⃣2️⃣ SMS-Based Communication (Layered Model)

### Code

```java
SmsManager.sendTextMessage(number, null, message, sentIntent, deliveryIntent);
```

### Explanation

### Layer 1 (System-Level)

* SMS_SENT
* SMS_DELIVERED

### Layer 2 (Service-Level)

* Actual telecom server reply

### 🔎 Interpretation

Communication happens in 3 steps:

1. Message sent
2. Delivery confirmed
3. Service response received

👉 This creates:

* SMS logs
* Delivery reports
* Reply messages

👉 Strong forensic traceability.

---

## 1️⃣3️⃣ Binder IPC (Google Play Services)

### Code

```java
IBinder binder = serviceConnection.onServiceConnected(...);
```

### Explanation

* Communicates with Google Play services

### Interpretation

Used for:

* Updates
* Licensing
* Service integration

👉 App depends on **system-level services**, not standalone logic.

---

## 1️⃣4️⃣ Hybrid Communication Architecture (CORE INSIGHT)

The application uses **multiple communication layers simultaneously**:

* HTTPS → API communication
* SMS → telecom network communication
* Binder IPC → system-level communication

### 🔎 Interpretation (VERY IMPORTANT)

This is a **hybrid communication model**, meaning:

* Not dependent on a single channel
* More resilient and complex
* Harder to fully intercept or monitor

👉 This is one of the **most important architectural findings**.

---

## 1️⃣5️⃣ Permission Handling (Two-Step Flow)

### Code

```java
requestPermissions(...)
onRequestPermissionsResult(...)
```

### Explanation

1. Permission requested
2. User interaction
3. Callback returns result

### Interpretation

👉 Asynchronous permission handling
👉 Not immediate → event-driven

---

# 📊 FINAL ANALYSIS

## ✅ Security Strengths

* HTTPS enforced
* SSL pinning
* SQLCipher encryption
* Controlled logging
* No cleartext communication

## ⚠️ Privacy Considerations

* Call log monitoring
* SIM-level tracking
* SMS communication
* Device identification

---

## 📌 Final Conclusion

The application demonstrates:

* Strong security practices in communication and encryption
* Controlled but **deep access to user-level data**
* Complex hybrid communication architecture

👉 Overall:
The app is **technically secure**, but operates in a domain where
**functionality and user privacy are tightly intertwined**.
