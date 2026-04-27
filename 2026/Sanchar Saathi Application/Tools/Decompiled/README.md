# 🧬 Decompiled Files

## 📌 Overview

This folder contains the decompiled version of the **Sanchar Saathi application**.

The APK was decompiled to understand the internal structure, application logic, and configuration details.

---

## ⚙️ How Decompilation Was Done

The APK was decompiled using **APKTool**.

### Command Used

```bash id="decomp1"
apktool d base.apk -o decompiled
```

This generated:

* Smali code (low-level Android code)
* Resources (layouts, strings, assets)
* AndroidManifest.xml

---

## 📁 Folder Structure (Important)

The decompiled folder mainly contains:

* `smali/` → Core application logic in smali format
* `res/` → UI layouts, strings, and resources
* `assets/` → App assets
* `AndroidManifest.xml` → App configuration and permissions
* `lib/` → Native libraries (if present)

---

## 🧠 Why Decompilation Was Important

Decompilation helped in:

* Understanding internal logic
* Identifying key methods and workflows
* Analyzing permissions and components
* Supporting findings from MobSF

---

## 🔍 How It Was Used in This Project

The decompiled files were used for:

* Code-level analysis (smali + Java comparison using JADX)
* Identifying functions like:

  * `submitOfflineData()`
* Understanding:

  * Background processes
  * Data handling logic
  * Communication flow

---

## ⚠️ Note

* Smali code is low-level and not directly human-friendly
* JADX was used alongside this to view readable Java code
* Core logic is primarily found inside the `smali/` directory

---

## 📌 Key Insight

Most of the critical behavior of the application (background sync, data handling, communication logic) can be traced through the decompiled code, especially in smali files.

---

![Decompiled Folder](../images/decompiled_folder.png)

