# 📦 Split APK Files

## 📌 Overview

This folder contains the extracted split APK files of the **Sanchar Saathi application**.

Unlike a single APK, modern Android apps are often distributed as **split APKs**, where the application is divided into multiple components based on device configuration.


---

## 📁 Files Included

* `base.apk` → Core application code
* `split_config.bn.apk` → Bengali resources
* `split_config.en.apk` → English resources
* `split_config.gu.apk` → Gujarati resources
* `split_config.hi.apk` → Hindi resources
* `split_config.kn.apk` → Kannada resources
* `split_config.mr.apk` → Marathi resources
* `split_config.ta.apk` → Tamil resources
* `split_config.te.apk` → Telugu resources
* `split_config.xxhdpi.apk` → Screen density resources

---

## ⚙️ Why Split APKs Are Used

Split APKs are used to:

* Reduce app size for users
* Deliver only required resources (language, DPI, etc.)
* Optimize performance across different devices

---

## 📥 Pulling Split APKs from Device

If extracting from a device, use:

### Step 1: Find APK path

```bash id="p1f2aa"
adb shell pm path com.dot.app.sancharsaathi
```

### Step 2: Pull all APKs

Example:

```bash id="v0k91s"
adb pull /data/app/.../base.apk
adb pull /data/app/.../split_config.en.apk
adb pull /data/app/.../split_config.hi.apk
adb pull /data/app/.../split_config.xxhdpi.apk
```

## 🧠 Relevance in Analysis

* `base.apk` → Used for:

  * Decompilation
  * Static analysis
  * Code-level investigation

* Split APKs → Contain:

  * Language resources
  * UI assets

👉 Core logic resides in **base.apk**
