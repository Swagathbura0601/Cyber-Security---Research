# 🌐 Network Analysis – Sanchar Saathi

## 📌 Overview

This section analyzes the network behavior of the Sanchar Saathi application using:

* Runtime logs (normal, network-off, permission scenarios)
* Burp Suite interception attempts
* Static analysis (MobSF)
* Decompiled code insights

The focus is to understand:

* How the app communicates with backend systems
* How it behaves under failure conditions
* What security controls are implemented
* Whether traffic can be intercepted or analyzed

---

## 🛠️ Tools Used

* **Burp Suite** → Network interception and traffic analysis
* Log analysis → Runtime request/response behavior
* MobSF → Static network configuration review

---

## 🔗 Observed Backend Endpoint

```text
https://ssapp.sancharsaathi.gov.in/clins/online/v1.1.0/knowYourConnection
```

### 📌 Observation

* Triggered multiple times during normal execution
* Also observed in permission-related flow
* Called even after recovery from network failure

### 🧠 Interpretation

This endpoint appears to be a **core backend service**, likely responsible for:

* Validating connection state
* Fetching telecom/service-related data

---

## 🟢 Scenario 1: Normal Network Behavior

📄 Reference: `test1_normal_launch.txt`

### Observations

* `_connectionStatus = true`
* No DNS failures or exceptions
* Endpoint calls executed successfully
* No retries or fallback triggered

### Behavior Flow

1. App initializes connectivity provider
2. Confirms network availability
3. Calls backend API
4. Receives valid response

### 🧠 Interpretation

* Stable communication with backend
* No anomalies or delays
* Represents **baseline healthy network behavior**

---

## 🔴 Scenario 2: Network-Off Behavior

📄 Reference: `test2_network_off.txt`

### Observations

* `_connectionStatus = false`

* DNS failures observed:

  * `ceir.gov.in` lookup fails
  * `dns.google` resolution fails

* Request failure:

  * Status **101 (application-level error)**

---

### Request Pattern

* App continues triggering:

  * `brands`
  * `states`

* Requests fail repeatedly

---

### Recovery Behavior

* New process observed (PID change)
* Connectivity restored → `_connectionStatus = true`
* API calls succeed afterward

---

### 🧠 Interpretation

✔ The app **does not crash during network failure**
✔ It continues attempting requests
✔ Automatically recovers once network is restored

👉 This shows:

* Retry mechanism
* Fault tolerance
* Recovery logic

---

## 🟡 Scenario 3: Permission Flow Network Behavior

📄 Reference: `test3_permission_denied.txt`

### Observations

* Network remains active
* API endpoint is still triggered

### Response Example

```text
["Not Found"]
```

### 🧠 Interpretation

* This is a **valid backend response**, not a network error
* Indicates:

  * Server reachable
  * Request processed
  * Data condition not met

---

## 🔐 Burp Suite Analysis

### Attempt

* Traffic interception attempted using Burp Suite proxy

### Result

* Requests could **not be intercepted successfully**

### Observation

* SSL handshake / certificate validation fails

---

### 🧠 Interpretation

👉 The application implements **SSL Pinning**

Meaning:

* Only trusted certificates are accepted
* Custom proxy certificates (Burp) are rejected

---

### 🔍 Impact

✔ Prevents:

* MITM attacks
* Traffic inspection

❗ Limitation:

* Makes dynamic traffic analysis difficult

---

## 🔒 Network Security Configuration

### HTTPS Enforcement

```xml
<base-config cleartextTrafficPermitted="false" />
```

### Interpretation

* HTTP traffic blocked
* Only HTTPS allowed

---

### SSL Pinning

* Confirmed via Burp failure

### Interpretation

* Strong transport-layer security
* Prevents interception

---

## ⚠️ Hidden API Layer (Flutter)

### Observation

* API endpoints not fully visible in smali
* Stored in compiled Dart snapshot

### Interpretation

* Network logic partially hidden
* Requires tools like **ReFlutter** for deeper extraction

👉 Indicates **intentional abstraction of network layer**

---

## 🔄 Network + Offline Behavior Integration

### Observed

* `submitOfflineData()` triggered
* Offline queue processed

### Interpretation

* App supports:

  * Offline data collection
  * Later synchronization

👉 Combined with retry behavior:

* Data is not lost during failures
* Synced once network is restored

---

## 📊 Overall Network Architecture

The application uses:

* HTTPS-based API communication
* Background sync (15-minute interval)
* Retry + recovery mechanism
* Offline queue processing
* SSL pinning protection

---

## 🔥 Key Insight

The app is not a simple API-based system.

It is a:

👉 **Resilient, state-aware network architecture**

With:

* Continuous backend interaction
* Built-in recovery
* Secure communication
* Hidden API logic

---

## 📌 Conclusion

From network analysis:

* Communication is **secure (HTTPS + SSL pinning)**
* Failures are **handled gracefully**
* Recovery is **automatic**
* Traffic inspection is **restricted by design**

👉 Overall:
The application demonstrates a **robust, secure, and resilient network communication model**, with strong protections against interception but limited transparency for analysis.
