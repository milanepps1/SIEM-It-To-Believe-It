# 🔍 SIEM It to Believe It: Hands-On Wazuh + Hydra Brute Force & FIM Testing

As part of my goal to get reacquainted with SIEM tools and hands-on security practices, I decided to spin up **Wazuh** on my CentOS 9 lab environment and put its **File Integrity Monitoring (FIM)** and **Threat Detection** capabilities to the test.

## 🧠 Objective
Rebuild my comfort with SIEM platforms by simulating real-world security events like brute-force attacks and unauthorized file changes using open-source tools.

---

## 🛠️ Tools & Setup

- **SIEM Platform:** Wazuh (deployed on CentOS 9)
- **Brute Force Simulator:** [Hydra](https://github.com/vanhauser-thc/thc-hydra) (cloned and compiled from source)
- **Test Targets:**
  - `testfile.txt` — used to simulate file changes for integrity monitoring
  - `rockyou.txt` — modified for brute-force wordlist with 1 valid password
  - `testuser` — created as a temporary user for SSH brute-force testing

---

## ⚙️ What I Did

1. **Installed Wazuh Server & Agent**
2. **Enabled File Integrity Monitoring** and designated `testfile.txt` as a watch target.
3. **Created a test user** (`testuser`) on the system.
4. **Populated a wordlist** with many invalid entries and one correct password.
5. **Ran Hydra** to initiate an SSH brute-force attack:
   ```bash
   hydra -l testuser -P /path/to/rockyou.txt ssh://localhost
   ```
6. **Monitored activity** in both:
   - Wazuh’s **Threat Detection dashboard**
   - Wazuh Agent logs using `tail -f` on `/var/ossec/logs/ossec.log`

---

## ⚠️ What Happened

- Wazuh **successfully flagged the checksum change** on `testfile.txt` — verifying the **FIM** setup worked.
- Hydra's brute-force attempt triggered:
  - **authentication failure alerts**
  - **MITRE ATT&CK mappings (T1110 & T1078)**
  - **Rule ID 5763** indicating a brute force login attempt
- At first, I thought Wazuh **wasn’t updating alerts** in the dashboard—but it turns out `tail -f` was **holding the log file open in read mode**, delaying Wazuh's ability to parse and index those logs.
- Once I stopped `tail -f` and hit **Refresh**, the brute force alerts populated instantly.

---

## ✅ Key Takeaways

- SIEM tools like Wazuh offer **clear and timely visibility** into file tampering and login abuse—especially when properly configured.
- Simple tools like `Hydra` can help recreate **controlled adversarial simulations** to test alert coverage.
- **Log file handling matters**: holding files open can silently block detection pipelines from working correctly. This was a great reminder of how little things can impact big processes in log management.

---

## 📸 Screenshots

> Check out the dashboards, rule hits, and threat intel panels from Wazuh in the `/screenshots` folder.

---

## 🧠 Reflection

This exercise helped me reinforce concepts around:
- **File Integrity Monitoring (FIM)**
- **Brute force detection**
- **SIEM alert logic & tuning**
- **Behavioral detection mapped to MITRE ATT&CK**
