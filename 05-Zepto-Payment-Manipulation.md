# 💸 Payment Manipulation – Zepto (Critical)

👋 Hello Hackers!!!
This was one of my most impactful bug bounty findings — a **payment bypass vulnerability** I discovered in Zepto’s web checkout flow. The issue allowed me to reduce the payable amount of an order from ₹179.99 to just ₹1.00. This was responsibly reported as it directly impacts business revenue and could be abused at scale.

---

🐞 **The Vulnerability**  
So here’s what happened:

The Zepto web checkout flow relied on **client-supplied values** for initializing payments. By intercepting the request to the payment gateway (in.juspay.hyperpay), I noticed that the **amount parameter** could be modified.

The vulnerable request looked like this:
```json
{
  "amount": "179.99"
}
```

By changing it to:
```json
{
  "amount": "1.00"
}
```

![WhatsApp Image 2025-08-31 at 7 18 38 PM (4)](https://github.com/user-attachments/assets/5d6a5b13-c3d5-484a-8250-1bfbefc09b31)


the backend happily accepted it and processed the order at **₹1.00 instead of ₹179.99**.

---

⚠️ **What It Could Do**  
Attackers could:
- Pay any arbitrary low amount for orders.
- Abuse this to get groceries or items for free/almost free.
- Cause direct and severe **financial loss** to Zepto.

Impact: **Critical (P1)** 🔥

---

🔬 **Steps to Reproduce (PoC)**
**1. Add items worth ₹179.99 to cart in the Zepto web app.**

**2. Intercept the payment initialization request using Burp Suite.**

![WhatsApp Image 2025-08-31 at 7 18 38 PM](https://github.com/user-attachments/assets/f084a205-a79f-4fcc-85ab-5926577e7c79)

**3. Locate the request to `https://api-js.mixpanel.com/track` initializing payment via `in.juspay.hyperpay`.**

![WhatsApp Image 2025-08-31 at 7 18 38 PM (3)](https://github.com/user-attachments/assets/6e18bb19-a78c-459a-be9c-80cdd45f5ff2)

**4. Modify the JSON payload:**

   ```json
   "amount": "1.00"
   ```
![WhatsApp Image 2025-08-31 at 7 18 38 PM (2)](https://github.com/user-attachments/assets/ecbc2dff-d1e2-43d2-8c9e-8a14a1858f08)

**5. Forward the request.**

**6. Observe that the order is successfully processed at **₹1.00**.**

![WhatsApp Image 2025-08-31 at 7 18 38 PM (4)](https://github.com/user-attachments/assets/fecc2992-7d52-4118-b4ef-177736e394f4)


---

🛠️ **Recommended Fix**
- Never trust **client-side values** for payments.
- Always recalculate order totals server-side before payment gateway handoff.
- Implement **HMAC/signature validation** on the amount field to ensure integrity.

---

🧠 **What I Learned**
- Reinforced why **client-side values are never trusted**.
- Understood how real-world payment gateway integrations can fail.
- Practiced building a clean PoC with Burp Suite.
- Learned to write reports with **clear evidence + reproduction steps**.

Signing off ✌️  
**ROHAN S**  
(Aspiring Security Engineer)
