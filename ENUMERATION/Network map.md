Use *nmap* to see the services and ports on the victim machine.
```
sudo nmap -sSVC -p- -oA nmap_full -Pn  <MACHINE IP>
```

![[Screenshot_2025-10-15_04-56-18.png]]

### **Scan Summary**

|Port|State|Service|Version / Info|
|---|---|---|---|
|**22/tcp**|open|ssh|OpenSSH 8.2p1 (Ubuntu)|
|**8009/tcp**|open|ajp13|Apache JServ Protocol v1.3|
|**8080/tcp**|open|http|Apache Tomcat 9.0.30|

## **Interpretation and Next Steps**

### **Port 22 (SSH)**

- Running **OpenSSH 8.2p1**, a fairly standard version on Ubuntu 20.04 LTS.
    
- Likely not immediately exploitable unless weak credentials or outdated configuration.
    
- ✅ Try standard usernames/passwords or SSH key brute-force only if authorized (e.g. in a CTF or pentest environment).
    

---

### **Port 8009 (AJP13 - Apache JServ Protocol)**

- This is **Apache JServ Protocol**, used internally by **Tomcat** to communicate between the web server (like Apache HTTPD) and the servlet engine.
    
- **Potentially vulnerable** if not firewalled or configured properly.
    

🧠 **Known Vulnerability:**

- **Ghostcat (CVE-2020-1938)** — affects Tomcat versions **< 9.0.31**.  
    You have **9.0.30**, so it **is vulnerable**.
    

💥 **Impact:**

- Allows **reading of arbitrary files** on the Tomcat server and sometimes **remote code execution (RCE)** if webapps are writable.

**Next step:** [[Tomcat]]
