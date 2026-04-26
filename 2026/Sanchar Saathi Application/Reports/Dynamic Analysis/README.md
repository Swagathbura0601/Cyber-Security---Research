# 📊 Runtime Behavior Analysis (Log/Dumpsys Scenarios)

This section documents application behavior under three different runtime conditions based on collected logs.

---

## 🟢 1. Normal Launch Scenario

📄 **Log File:**
👉 [View Log](test1_normal_launch.txt)

### 📌 Observed Behavior

* Application starts cleanly (`com.dot.app.sancharsaathi`)
* Flutter engine initializes successfully
* MethodChannel and ConnectivityProvider are initialized
* Local database is initialized
* Connectivity status is **TRUE (online)**

### 🔄 Startup Flow

* MethodChannel becomes active
* ConnectivityProvider confirms network availability
* App checks for updates → no update found

### 📡 Backend/API Behavior

* Endpoint triggered multiple times:

```
https://ssapp.sancharsaathi.gov.in/clins/online/v1.1.0/knowYourConnection
```

* No DNS or network errors observed

### 📥 Data Handling

* `submitOfflineData()` is triggered
* Offline queue is empty → no pending data
* Execution completes successfully

### 📊 Interpretation

* Application works as expected under normal conditions
* Backend communication is stable
* No crash or anomaly observed
* This serves as a **baseline reference for healthy behavior**
  
<img width="941" height="312" alt="image" src="https://github.com/user-attachments/assets/7fb04e63-1d99-402b-9902-1eecd66ef526" />


---

## 🔴 2. Network-Off Scenario

📄 **Log File:**
👉 [View Log](test2_network_off.txt)

### 📌 Observed Behavior

* Application starts normally
* Connectivity status becomes **FALSE (offline)**

### ❌ Failure Points

* API calls fail due to DNS resolution issues:

  * `ceir.gov.in` lookup fails
  * DNS resolution error (`dns.google`)

* Request failure status:

  * Status code **101 (app-level failure)**

### 🔄 Request Flow

* App attempts:

  * `brands` request
  * `states` request

* Both fail repeatedly while network is unavailable

### 🔁 Recovery Behavior

* New app process detected (PID change)
* Connectivity restored → `_connectionStatus = true`
* API calls succeed afterward
* Data (brands/states) returned successfully

### 📊 Interpretation

* App does **not crash during network failure**
* Instead, it:

  * Continues attempting requests
  * Handles DNS failure gracefully
  * Recovers automatically once network returns

👉 This indicates **robust network error handling and recovery logic**

<img width="903" height="491" alt="image" src="https://github.com/user-attachments/assets/e65c6e52-f28c-42ce-be74-f182fb5394b9" />


---

## 🟡 3. Permission Denied Scenario

📄 **Log File:**
👉 [View Log](test3_permission_denied.txt)

### 📌 Observed Behavior

* App launches with `_connectionStatus = true`
* Focus is on permission transitions

---

### 📷 Camera / KYM Flow

#### Initial State:

* Camera permission → `permanentlyDenied`

#### After User Action:

* Permission → `granted`

#### Result:

* Scanner activity launches:

```
BarcodeCaptureActivity
```

👉 Confirms permission recovery works correctly

---

### 📞 Call & Phone Permissions

#### Initially Denied:

* `READ_PHONE_STATE` → denied
* `READ_CALL_LOG` → permanently denied

#### After Request:

* Both permissions granted

#### Final State:

* Permissions persist after granting

---

### 📡 Network/API Behavior

* `knowYourConnection` endpoint is called
* Requests eventually succeed
* One response observed:

```
["Not Found"]
```

👉 This appears to be a **business/data response**, not a failure

<img width="941" height="547" alt="image" src="https://github.com/user-attachments/assets/9a3cc787-cb75-4fa7-a23c-99e62b9c5f8e" />


---

### 📥 Offline Behavior

* Offline submission runs with:

  * Zero queued items
* No update logs found

---

### 📊 Interpretation

* Permission lifecycle works correctly:

  * Deny → Request → Grant → Persist

* Features dependent on permissions:

  * Resume normally after approval

* No crashes observed

👉 This confirms **stable permission handling and recovery flow**

---

## 🧠 Overall Observations

Across all three scenarios:

### ✅ Stability

* No fatal crashes observed
* App remains stable under all conditions

### 🔁 Recovery Handling

* Network recovery works automatically
* Permission recovery works correctly

### 📡 Backend Interaction

* API calls are:

  * Reattempted when failed
  * Successful after recovery

### ⚠️ Key Insight

The application is designed to:

* Handle failures gracefully
* Recover automatically
* Maintain operational continuity

```

