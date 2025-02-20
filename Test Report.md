# 📌 Penetration Testing Report - Booking System (Phase 1)

**Date:** _(Insert Date)_  
**Tester:** _(Your Name)_  
**Testing Method:** Gray-box Testing  
**Scope:** Registration Page  

## **1️⃣ Introduction**

### **1.1 Purpose of the Report**  
This penetration test assesses the security of the Booking System's **registration page**, identifying vulnerabilities and deviations that could lead to security risks. The system must comply with **GDPR regulations** and follow **Privacy by Design (PbD)** principles.

### **1.2 Scope of Testing**  
- **Target Application:** Booking System - Phase 1  
- **Test Environment:** Kali Linux, Docker  
- **Target URL:** `http://localhost:8000/register`  
- **Testing Tools:** OWASP ZAP, Burp Suite, SQLMap, Nikto, Nmap  

## **2️⃣ Summary of Findings**

### **Key Issues Identified**
| 🔹 **Severity** | 🚨 **Issue** | 🛠️ **Recommendation** |
|--------------|--------------------------------|--------------------------------|
| 🔴 **Critical** | SQL Injection in the registration form | Use **prepared statements** and **input sanitization** |
| 🔴 **Critical** | Broken authentication allows bypassing login | Implement **secure authentication mechanisms** and enforce **session validation** |
| 🟡 **Medium** | Weak password policy allows simple passwords | Enforce **password complexity** (uppercase, numbers, special characters) |
| 🟡 **Medium** | Session tokens are not HTTPOnly or Secure | Add **HTTPOnly and Secure flags** to session cookies |
| 🟢 **Low** | No CAPTCHA on registration page | Implement **Google reCAPTCHA** or similar challenge-response mechanism |

## **3️⃣ Findings and Categorization**

### **3.1 Critical Findings (🔴)**
#### **1️⃣ SQL Injection (SQLi) in Registration Form**
- **Description:** The registration form does not sanitize user inputs, allowing malicious SQL queries.
- **Impact:**  
  - Attackers can bypass authentication.  
  - Attackers may extract user credentials from the database.
- **Tested Payload:**
  ```sql
  ' OR '1'='1'; --
  ```
- **Recommendation:**  
  - Use parameterized queries to prevent SQL injection.  
  - Sanitize all user input fields before processing.

#### **2️⃣ Broken Authentication - Login Bypass**
- **Description:** Users can manipulate the authentication mechanism to log in as other users or admins.
- **Impact:**  
  - Unauthorized access to sensitive areas.  
  - Attackers can escalate privileges.
- **Tested Exploit:**  
  - Modified request headers in **Burp Suite** to bypass authentication.
- **Recommendation:**  
  - Enforce **multi-factor authentication (MFA)**.  
  - Implement **session validation checks**.

### **3.2 Medium Findings (🟡)**
#### **3️⃣ Weak Password Policy**
- **Description:** The system allows passwords like `"12345678"`, making brute-force attacks easy.
- **Impact:**  
  - Users may choose **weak passwords**, leading to **account takeovers**.
- **Recommendation:**  
  - Enforce **strong password policies**:  
    - Minimum **12 characters**  
    - **Uppercase & lowercase letters**  
    - **Numbers & special characters**  

#### **4️⃣ Missing HTTPOnly & Secure Flags in Session Cookies**
- **Description:** The authentication cookies do not have **HTTPOnly and Secure flags**, making them vulnerable to **XSS attacks**.
- **Impact:**  
  - Attackers can **steal session tokens** via JavaScript.
- **Recommendation:**  
  - Set **HTTPOnly and Secure flags** in cookies.  
  - Example fix in **Flask**:
    ```python
    response.set_cookie('session', value=session_id, httponly=True, secure=True)
    ```

### **3.3 Low Findings (🟢)**
#### **5️⃣ No CAPTCHA on Registration Page**
- **Description:** The system allows **automated bot registrations** without CAPTCHA.
- **Impact:**  
  - **Spam accounts** can flood the database.  
  - Increases **server load** from bot traffic.
- **Recommendation:**  
  - Implement **Google reCAPTCHA** or **hCaptcha**.

## **4️⃣ Tools & Methodology**

### **4.1 Tools Used**
- **OWASP ZAP** (Automated web vulnerability scanner)
- **Burp Suite** (Manual web proxy for testing)
- **SQLMap** (SQL injection automation tool)
- **Nmap** (Port scanning for service enumeration)

### **4.2 Attack Scenarios**
| 🔹 **Test** | ✅ **Method Used** | 🛠️ **Tool** |
|--------------|-------------------|--------------|
| **SQL Injection** | Manual payload insertion | SQLMap, Burp Suite |
| **Broken Authentication** | Testing weak passwords | Hydra, Burp Suite |
| **Session Hijacking** | Manipulating cookies | Burp Suite |
| **Sensitive Data Exposure** | Checking unencrypted data | Wireshark |

## **5️⃣ GDPR Compliance Check**
| ✅ **Requirement** | 🚨 **Status** |
|-------------------|-------------|
| **Data Encryption (at rest & transit)** | ❌ Not enforced |
| **User Rights (Data Deletion, Correction, Exporting)** | ⚠️ Partially implemented |
| **Minimization of Data Collection** | ✅ Implemented correctly |

📌 **Recommendation:** Encrypt all personal data at rest & in transit.

## **6️⃣ Conclusion & Recommendations**
### **Critical Fixes Needed**
1️⃣ **SQL Injection Prevention** → Implement **prepared statements & input sanitization**.  
2️⃣ **Strong Authentication Measures** → Enforce **secure password policies**.  
3️⃣ **Bot Protection** → Add **CAPTCHA to prevent spam registrations**.  

✅ **Final Security Rating:** **🚨 High Risk (System needs improvements before production use).**

## **7️⃣ References**
- **OWASP SQL Injection Guide**: [OWASP SQLi](https://owasp.org/www-community/attacks/SQL_Injection)  
- **GDPR Compliance Checklist**: [GDPR Compliance](https://gdpr.eu/checklist/)  
- **Password Security Best Practices**: [NIST Guide](https://www.nist.gov/)
