# 🧪 Forensic Analysis

Forensic analysis in this project was done to understand what kind of data we can actually extract from the Sanchar Saathi application from a user device. The analysis was carried out in a non-rooted environment, so it reflects real-world investigation conditions where full access is not available.

The main goal here was to check:

* What data is accessible
* What is restricted
* How the app limits forensic visibility

---

## 🔍 Approach

The analysis was done using logical acquisition methods only. No root access was used at any stage.

We relied on:

* ADB-based extraction
* Runtime observation
* Forensic tools for validation

This helped us understand the difference between **what exists** and **what is actually accessible**.

---

## 🛠️ Magnet AXIOM

Magnet AXIOM was used as the primary forensic tool.

The device was connected using USB debugging, and the acquisition was performed using the **ADB method**, since the device was not rooted.

Only **Quick Extraction** was available.

### Observations

* The device was detected successfully
* The application (`com.dot.app.sancharsaathi`) was identified
* Some basic application data was visible

But:

* No internal databases were accessible
* No private storage could be extracted
* No meaningful user data was found

### Interpretation

This clearly shows that the application data is protected under Android sandboxing, and without root access, deep-level extraction is not possible.

---

## 🛠️ MOBILedit

MOBILedit was used as a secondary tool to confirm the findings.

The device was detected and application-level extraction was performed.

### Observations

* Extraction completed successfully
* Only limited data was available
* No sensitive or structured data was retrieved

### Challenges

* Non-rooted device limited access
* No access to protected directories
* Application sandbox restrictions

### Interpretation

The results were almost the same as Magnet AXIOM.
This confirms that the limitation is not tool-specific — it is enforced by the system and application design.

---

## 🔐 What We Could NOT Access

During the analysis, the following were not accessible:

* Internal app storage (`/data/data/`)
* Encrypted databases
* Cached application data
* Any meaningful user-related data

Even though the app performs operations like call log handling and SIM tracking, this data is not available locally in a readable form.

---

## 📊 What We Could Access

Only limited information was available, such as:

* Application package details
* Installation presence
* Basic system-level traces

No strong forensic artefacts were recovered.

---

## 🧠 Final Understanding

From a forensic point of view, this app is designed in a way that:

* It stores very minimal data locally
* Sensitive data is either encrypted or not stored on the device
* Most processing likely happens on the server side

---

## ⚖️ Conclusion

This analysis clearly shows that:

* User-side forensic investigation is very limited
* Traditional extraction methods are not effective
* The application follows a privacy-focused design

So, in simple terms:

👉 More privacy = less evidence available

This creates a challenge for forensic investigations, especially when only logical access is available.

