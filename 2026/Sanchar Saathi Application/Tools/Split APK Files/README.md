# 📦 Split APK Files

## 📌 Overview

This folder contains the extracted split APK files of the **Sanchar Saathi application**.

Unlike a single APK, modern Android apps are often distributed as **split APKs**, where the application is divided into multiple components based on device configuration.

---

## 📁 Files Included

* `base.apk` → Core application code and main logic
* `split_config.bn.apk` → Bengali language resources
* `split_config.en.apk` → English language resources
* `split_config.gu.apk` → Gujarati language resources
* `split_config.hi.apk` → Hindi language resources
* `split_config.kn.apk` → Kannada language resources
* `split_config.mr.apk` → Marathi language resources
* `split_config.ta.apk` → Tamil language resources
* `split_config.te.apk` → Telugu language resources
* `split_config.xxhdpi.apk` → High-density screen resources

---

## ⚙️ Why Split APKs Are Used

Split APKs are used to:

* Reduce app size for users
* Deliver only required resources (language, DPI, etc.)
* Optimize performance across different devices

---

📥 Pulling Split APKs from Device

If extracting from a device, use:

Step 1: Find APK path
adb shell pm path com.dot.app.sancharsaathi
Step 2: Pull all APKs

Example:

adb pull /data/app/.../base.apk
adb pull /data/app/.../split_config.en.apk
adb pull /data/app/.../split_config.hi.apk
adb pull /data/app/.../split_config.xxhdpi.

---

## 🧠 Relevance in Analysis

During analysis:

* `base.apk` was used for:

  * Decompilation
  * Static and dynamic analysis

* Split APKs were mainly resource-specific and **did not contain core logic**

👉 Important:
For reverse engineering and security analysis, **base.apk is the primary target**

---

## ⚠️ Note

To install split APKs manually, all files must be installed together using:

* ADB (`adb install-multiple`)
* Or tools like APKMirror Installer

---

## 📸 Screenshot

Below shows the uploaded split APK files in the repository:

![Split APK Files](../images/split_apk_files.png)

