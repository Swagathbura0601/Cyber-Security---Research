# 🧬 Decompiled Code Analysis – Sanchar Saathi

## 📌 Overview

This section documents what I found after decompiling the Sanchar Saathi application using Apktool, JADX, and Cursor IDE.

Instead of just looking at tool outputs, I went into the actual code (smali + decompiled Java) to understand:

* what data the app is collecting
* how frequently it operates in the background
* how it communicates with external systems
* what security mechanisms are implemented

---

## ⚙️ Decompilation Setup

Tools used:

* **Apktool** → to extract smali code and resources
* **JADX** → to convert APK into readable Java code
* **Cursor IDE** → to navigate and inspect large codebase

Command used:
apktool d base.apk -o decompiled

---

# 🔍 Code-Level Analysis

---

## 1️⃣ Background Sync Behaviour

📂 File: `MyApp.smali`
📍 Lines: 108–114

### Code

```smali
const-wide/32 v0, 0x36ee80
invoke-static {v0, v1}, Ljava/util/Timer;->scheduleAtFixedRate(...)V
```

### What’s happening here

* `0x36ee80` is a hexadecimal value
* When converted → **900000 milliseconds (15 minutes)**
* `scheduleAtFixedRate()` means this task is not one-time, it keeps repeating

### Explanation

This basically tells us the app is running a background task every **15 minutes without stopping**.

If you think practically:

* 4 times every hour
* 96 times in a day

This strongly indicates **continuous communication with backend systems**, even when the user is not actively using the app.

---

## 2️⃣ Call Log Collection Logic

📂 File: `smali/Q3/b.smali`
📍 Lines: 335–358

### Code

```smali
const/4 v1, 0x1   # incoming
const/4 v2, 0x3   # missed
const/4 v3, 0x5   # rejected

if-eq v0, v1, :collect
if-eq v0, v2, :collect
if-eq v0, v3, :collect

const/4 v4, 0x2   # outgoing
if-eq v0, v4, :skip
```

### What’s happening here

* `v0` stores the call type
* The code checks which type of call it is
* Only certain types are allowed to move forward

### Explanation

The app is not collecting all call data blindly. It is selective:

✔ It collects:

* Incoming calls
* Missed calls
* Rejected calls

❌ It ignores:

* Outgoing calls

This shows **intentional filtering**, which means the developers designed it to focus only on specific communication patterns rather than everything.

---

## 3️⃣ Call Data Retention (Time Filtering)

📂 File: `smali/Q3/b.smali`

### Code

```smali
invoke-static {}, Ljava/lang/System;->currentTimeMillis()J
sub-long v0, v0, v1
const-wide/32 v2, 0x9a7ec800
```

### What’s happening here

* The app gets current system time
* Then subtracts a fixed value
* That value (`0x9a7ec800`) equals roughly **29 days**

### Explanation

This means the app only considers call logs from the **last 29 days**.

So:

* It is not storing very old data
* But still keeps nearly a month of call history

This is a **controlled retention model**, but still sensitive from a privacy perspective.

---

## 4️⃣ SIM Card Tracking

📂 File: `smali/Q3/b.smali`
📍 Lines: 470–702

### Code

```smali
invoke-virtual {p0}, Landroid/telephony/SubscriptionInfo;->getIccId()Ljava/lang/String;
invoke-virtual {p0}, Landroid/telephony/SubscriptionInfo;->getSubscriptionId()I
invoke-virtual {p0}, Landroid/telephony/SubscriptionInfo;->getSimSlotIndex()I
```

### What’s happening here

The app is pulling multiple identifiers related to SIM cards:

* ICC ID → unique SIM serial number
* Subscription ID → internal system identifier
* Slot index → which SIM slot is used

### Explanation

This is not just normal app behavior — this enables **SIM-level tracking**.

Which means:

* The app can identify which SIM is being used
* Even in dual SIM phones
* It can link SIM → device → user

This is very useful for telecom-related applications, but also **sensitive from a privacy angle**.

---

## 5️⃣ Device Identification (Without IMEI)

### Code

```java
MediaDrm.getPropertyByteArray("deviceUniqueId");
```

### What’s happening here

* This API generates a unique identifier for the device
* It does not require IMEI permission

### Explanation

Normally, accessing IMEI requires strict permissions.
This method bypasses that limitation and still gives a unique device identity.

So the app can:

* Identify device reliably
* Without triggering strict permission checks

---

## 6️⃣ Database Encryption (SQLCipher)

📂 File: `smali/a.smali`

### Code

```smali
invoke-static {v0, v1, v2}, Lnet/sqlcipher/database/SQLiteDatabase;->openDatabase(...)Lnet/sqlcipher/database/SQLiteDatabase;
```

### Additional Observation

```smali
if-nez v1, :use_key
const-string v1, ""
```

### What’s happening here

* The app is using SQLCipher → encrypted SQLite database
* A key is passed while opening database

But:

* If key is null → replaced with empty string

### Explanation

This is interesting:

✔ Encryption is implemented
❗ But key handling is weak

Meaning:

* Data is encrypted
* But if key management is poor, security can be reduced

So this is **secure implementation with a potential weakness**.

---

## 7️⃣ Network Security (HTTPS Enforcement)

### Code

```xml
<base-config cleartextTrafficPermitted="false" />
```

### Explanation

* HTTP traffic is blocked
* Only HTTPS is allowed

This ensures:

* Data is encrypted during transmission
* No plain text leaks

---

## 8️⃣ SSL Pinning

### Evidence

* Burp Suite interception failed

### Explanation

This means:

* App verifies server certificates strictly
* Does not trust custom/intercepted certificates

Result:
✔ Prevents MITM attacks
✔ Strong network security

---

## 9️⃣ Flutter ↔ Android Communication

### Code

```java
MethodChannel channel = new MethodChannel(flutterEngine.getDartExecutor(), "channel");
channel.invokeMethod("submitOfflineData", args);
```

### What’s happening here

* Flutter sends request
* Native Android code processes it
* Response is returned

### Explanation

This shows the app is built using:

* Flutter (UI layer)
* Native Android (core logic)

So it follows a **hybrid architecture**.

---

## 🔟 SMS-Based Communication

### Code

```java
SmsManager.sendTextMessage(number, null, message, sentIntent, deliveryIntent);
```

### Explanation

The app sends SMS to external services.

There are two layers:

1. SMS sent/delivered status
2. Actual reply from service

This means:

* Communication is not only internet-based
* Telecom network is also involved

---

## 📊 Final Understanding

### What the app does well

* Uses HTTPS only
* Implements SSL pinning
* Uses encrypted database
* Avoids unnecessary exposure in logs

### What raises concerns

* Access to call logs
* SIM-level tracking
* SMS interaction
* Device identification

---

## 📌 Conclusion

From the decompiled analysis, the app is:

* Technically secure in communication
* Carefully designed in data handling

