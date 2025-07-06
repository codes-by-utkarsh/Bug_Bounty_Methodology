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

```bash
# Create and navigate to a working directory
mkdir example
cd example
