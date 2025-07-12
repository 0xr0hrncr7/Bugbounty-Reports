## Silverlight Cross-Domain Policy Misconfiguration on `c.bing.com`

## 👋 Hello Hackers!!!


While casually poking around Microsoft subdomains, I discovered a misconfigured `clientaccesspolicy.xml` on `https://c.bing.com`. This file is used by legacy Silverlight apps to define cross-domain communication rules.

The policy was dangerously open — allowing **any origin and any HTTP headers**, effectively defeating same-origin policy (SOP) protections if any Silverlight endpoints are still around.

![f5be6e5e-2fcf-4128-a6c5-af44f9e95a0d](https://github.com/user-attachments/assets/e1abff10-5b07-4715-8677-a967d34c8a12)

---

### 🔍 Vulnerability Details

### Affected Endpoint : https://c.bing.com/clientaccesspolicy.xml


### Vulnerable Policy:
```
<access-policy>
  <cross-domain-access>
    <policy>
      <allow-from http-request-headers="*">
        <domain uri="*" />
      </allow-from>
      <grant-to>
        <resource path="/" include-subpaths="true"/>
      </grant-to>
    </policy>
  </cross-domain-access>
</access-policy>
```
### 🚨 Why this is a problem:

![6ce75f41-e6b2-4330-ab5b-8f53a5106acf](https://github.com/user-attachments/assets/5550169e-e2ac-44e8-bdd4-67795bc9cc11)

- `domain uri="*"`: Any website can send cross-origin requests.
- `http-request-headers="*"`: Any headers allowed — including sensitive ones like Authorization, Cookie, etc...

### ⚠️ Potential Impact:

Even though Silverlight is deprecated, if any legacy functionality is still reachable, this misconfiguration allows:
- SOP bypass
- Access to user-specific content
- Unauthorized interaction with backend APIs

### 💡 Takeaways
- Silverlight is dead… but its bugs are not.
- Misconfigured cross-domain policies still exist on major domains.
- Just because something's old doesn’t mean it’s harmless.

***Not a critical bug in 2025, but definitely an interesting edge case. It highlights how forgotten legacy configs can still create trust boundary issues.
Still hunting, still learning.***

**Signing off ✌️**

**[ROHAN S](https://www.linkedin.com/in/rohanscr7123/)** 

(**Aspiring Security Engineer**)


