
# 🐞 Bug Hunting Methodology

Bug hunting is an intricate process that requires persistence, knowledge, and the right tools. This guide outlines a clear, actionable methodology for discovering vulnerabilities, supplemented by practical tools and commands to streamline your workflow.

---

## 📌 Table of Contents

1. [Reconnaissance](#-reconnaissance)
   - Setup & Initialization
   - Subdomain Enumeration
   - Subdomain Status Check
   - Subdomain Takeover
   - URL Discovery
   - Search for Sensitive Files
   - Directory Bruteforcing
   - CORS Misconfiguration
   - Identify SQL Injection Points
2. [Low Hanging Fruits](#-low-hanging-fruits)
3. [Business Logic Vulnerabilities](#-business-logic-vulnerabilities)
4. [Conclusion](#-conclusion)

---

## 🔍 Reconnaissance

### 1️⃣ Setup & Initialization

\`\`\`bash
# Create and navigate to a working directory
mkdir example
cd example
\`\`\`

---

### 2️⃣ Subdomain Enumeration

#### ✅ Using Subfinder

\`\`\`bash
subfinder -d example.com -all -recursive > subfinder_subs.txt
cat subfinder_subs.txt | wc -l
\`\`\`

#### ✅ Using crt.sh + jq

\`\`\`bash
# Install jq if needed
sudo apt install -y jq

# Extract subdomains
curl -s "https://crt.sh/?q=%.example.com&output=json" | \
jq -r '.[].name_value' | sed 's/\\*\\.//g' | sort -u > crtsh_subs.txt
\`\`\`

#### ✅ GitHub Subdomains

\`\`\`bash
github-subdomains -d target.com -t YOUR_GITHUB_TOKEN -o github_subs.txt
\`\`\`

#### ✅ Using FFuF for Fuzzing

\`\`\`bash
ffuf -u https://FUZZ.target.com -w wordlist.txt -t 50 -mc 200,403 -o ffuf_subs.txt
\`\`\`

**Combine all results:**

\`\`\`bash
cat *_subs.txt | sort -u | anew all_subs.txt
\`\`\`

---

### 3️⃣ Subdomain Status Check

\`\`\`bash
# Check for alive subdomains
cat all_subs.txt | httpx-toolkit -ports 80,443,8080,8000,8888 -threads 200 > subdomains_alive.txt

# Count live subdomains
cat subdomains_alive.txt | wc -l
\`\`\`

---

### 4️⃣ Subdomain Takeover

\`\`\`bash
# Using Subzy
subzy run -dL subdomains_alive.txt

# Using Nuclei
nuclei -l subdomains_alive.txt -t /path/to/nuclei-templates/ -stats -v
\`\`\`

---

### 5️⃣ URL Discovery

\`\`\`bash
# Using Katana
katana -u subdomains_alive.txt -d 5 -kf -jc -fx -ef woff,pdf,css,png,svg,jpg,woff2,jpeg,gif -o allurls.txt
\`\`\`

---

### 6️⃣ Search for Sensitive Files

\`\`\`bash
cat allurls.txt | grep -E "\\.txt|\\.log|\\.cache|\\.secret|\\.db|\\.backup|\\.yml|\\.json|\\.gz|\\.rar|\\.zip|\\.config"
\`\`\`

---

### 7️⃣ Directory Bruteforcing

**Recommended Tools:**

- **FFuF**
  \`\`\`bash
  ffuf -u https://example.com/FUZZ -w wordlist.txt -H "User-Agent: CustomAgent" -r
  \`\`\`

- **Dirsearch**
  \`\`\`bash
  dirsearch -l subdomains_alive.txt -e conf,config,bak,backup,swp,old,db,sql,asp,aspx,py,rb,php,tar,zip,log,json,xml,js
  \`\`\`

- **Google Dorks**

  - [Dork King](https://dorkking.blindf.com/)
  - [Dork for Me](https://dorkforme.netlify.app/)

> **💡 NOTE:** Always brute-force directories on forbidden pages (403) and custom 404 pages; they often leak hidden resources.

---

### 8️⃣ CORS Misconfiguration

\`\`\`bash
# Using Corsy
python3 corsy.py -i subdomains_alive.txt -t 10 --headers "User-Agent: GoogleBot\\nCookie: SESSION=Hacked"
\`\`\`

---

### 9️⃣ Identify SQL Injection Points

\`\`\`bash
cat allurls.txt | grep -E "\\.php|\\.asp|\\.aspx|\\.jspx|\\.jsp" | grep '=' | sed 's/=.*$/=/' | sort -u > bsqli.txt
\`\`\`

---

## 🍃 Low Hanging Fruits

- **Broken Link Hijacking:**  
  Tool: \`broken-link-checker\`  
  \`\`\`bash
  blc -link- -rov --filter-level 0
  \`\`\`

- **Origin IP Disclosure:**  
  Use [shodan.io](https://www.shodan.io/) and SPF record validators like [kitterman.com/spf/validate.html](http://www.kitterman.com/spf/validate.html).

- **Session Management:**  
  Check if sessions persist after logout on other devices.

- **Long Password DOS Attack**

- **Web Cache Poisoning**

---

## 🏢 Business Logic Vulnerabilities

- **Price Manipulation**
- **Parameter Tampering**
- **Tamper HTTP Methods:**  
  Test GET ↔ POST ↔ PUT ↔ PATCH ↔ DELETE

---

## ✅ Conclusion

This comprehensive bug hunting methodology equips you with the tools and techniques needed to uncover vulnerabilities effectively.  
By following these steps and practicing consistently, you can conduct thorough penetration tests and improve the security posture of your targets.

**🔒 Happy Hunting & Hack Responsibly!**
