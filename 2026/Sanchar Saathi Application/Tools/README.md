# 🛠️ Tools & Technologies Used

The following tools were used throughout the project in the exact sequence of the analysis workflow. Each tool was applied at a specific stage to perform static analysis, dynamic analysis, network inspection, and forensic validation.

---

## 1️⃣ Android Debug Bridge (ADB)

ADB was used to establish communication between the system and the Android device/AVD.

It was used for:

* Installing the APK
* Executing shell commands
* Capturing logs using `logcat`
* Running `dumpsys`
* Interacting with the application at runtime

---

## 2️⃣ Android Studio (AVD)

Android Studio was used to create and manage the Android Virtual Device (AVD).

It provided:

* A safe testing environment
* Ability to run and test the application
* Observation of runtime behavior and permission flows

---

## 3️⃣ Docker

Docker was used to set up MobSF in a containerized environment.

It helped in:

* Running MobSF without affecting the host system
* Creating an isolated analysis environment

---

## 4️⃣ MobSF (Mobile Security Framework)

MobSF was used for static analysis of the APK.

It provided:

* Permission analysis
* Component analysis (activities, services, receivers)
* Network security configuration review
* Identification of potential vulnerabilities

---

## 5️⃣ APKTool

APKTool was used for decompilation.

It helped in:

* Extracting AndroidManifest.xml
* Accessing application resources
* Inspecting configuration files

---

## 6️⃣ Cursor IDE

Cursor IDE was used to navigate and analyze the decompiled code.

It helped in:

* Searching for specific methods
* Understanding code structure
* Tracing application logic

---

## 7️⃣ Frida

Frida was used for dynamic analysis and runtime instrumentation.

It was used to:

* Detect running processes
* Attempt hooking into the application

Observation:

* Hooking attempts failed, indicating security protections

---

## 8️⃣ Logcat

Logcat was used through ADB to capture real-time logs.

It helped in:

* Monitoring runtime behavior
* Identifying errors and system events

---

## 9️⃣ Dumpsys

Dumpsys was used to extract system-level information.

It helped in:

* Analyzing app state
* Observing system interactions
* Understanding process behavior

---

## 🔟 Burp Suite

Burp Suite was used for network traffic analysis.

It was used to:

* Monitor requests and responses
* Attempt HTTPS interception

Observation:

* Full interception was not possible due to SSL pinning

---

## 1️⃣1️⃣ Magnet AXIOM

Magnet AXIOM was used for forensic analysis.

It helped in:

* Logical data extraction
* Structured forensic examination
* Timeline reconstruction

---

## 1️⃣2️⃣ MOBILedit Forensic

MOBILedit was used as a secondary forensic tool.

It helped in:

* Validating findings from Magnet AXIOM
* Confirming limited data accessibility

---

## 1️⃣3️⃣ Magisk

Magisk was used in the context of testing root-based capabilities.

Purpose:

* Evaluate access differences between rooted and non-rooted environments
* Understand limitations of logical forensic acquisition

---

## 1️⃣4️⃣ Odin

Odin was used for device-level operations.

Purpose:

* Flashing firmware or preparing device environment
* Supporting setup for advanced testing scenarios

---

## 🧠 Workflow Order

ADB → AVD → Docker → MobSF → APKTool → Cursor → Frida → Logcat → Dumpsys → Burp Suite → Magnet AXIOM → MOBILedit → Magisk → Odin

---
