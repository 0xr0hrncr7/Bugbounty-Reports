# 📱 OTP Bypass – ShopClues (High)

**👋 Hello Hackers!!!**

Here’s another interesting bug bounty finding from my hunts — a **signup OTP bypass** I discovered in ShopClues. This one was fun because OTPs are supposed to be a strong verification step… but I found a way to completely **skip OTP verification** and still create an account.  

![WhatsApp Image 2025-09-05 at 3 46 19 PM](https://github.com/user-attachments/assets/33a2c474-bf0d-4846-b59a-5d3fe2bf9445)

---

# 🐞 **The Vulnerability**  
During the ShopClues signup flow, after entering email and phone number, the system sends an OTP to verify. That’s standard.  

But while playing with Burp Suite, I noticed something odd. The OTP verification request looked like this:  

```http
POST /user/signup/verifyotp HTTP/2
Host: login.shopclues.com
Content-Type: application/x-www-form-urlencoded

otp=1234&user_login=9xxxxxxxxx&csrf_test_name=1ab2c34d5exyz123451ab2c3
```

Out of curiosity, I removed the entire **`otp=1234` parameter** and forwarded the request:  

```http
POST /user/signup/verifyotp HTTP/2
Host: login.shopclues.com
Content-Type: application/x-www-form-urlencoded

user_login=9xxxxxxxxx&csrf_test_name=1ab2c34d5exyz123451ab2c3
```

👉 And boom… the account got created successfully **without OTP verification**.  

---

## ⚠️ **What It Could Do**  

**Attackers could:**

- Mass-create fake accounts without valid phone numbers.  
- Abuse referral programs, coupons, or promotions.  
- Flood the system with bots/spam users.  

## Impact: **High (P2)** 🔥



## 🔬 **Steps to Reproduce (PoC)**  

**1. Register with a valid phone number + email on ShopClues.**  

**2. Intercept the OTP verification request in Burp Suite.**  

![WhatsApp Image 2025-09-05 at 3 23 52 PM (1)](https://github.com/user-attachments/assets/e7981a8d-6f67-4955-a7ed-88d9fa4d26d9)
 
**3. Delete the `otp=1234&` parameter from the request body.**  

![WhatsApp Image 2025-09-05 at 3 23 52 PM](https://github.com/user-attachments/assets/7f510c76-4673-4a12-a393-45e7e7d97452)

**4. Forward the request.**  

**5. Observe that the account is created even though no OTP was provided.**  

![WhatsApp Image 2025-09-05 at 3 23 53 PM](https://github.com/user-attachments/assets/6686245c-2389-4177-9774-24a48277bab3)


---

## 🛠️ **Recommended Fix**
- Enforce OTP parameter validation strictly on the backend.  
- Reject any request missing/invalid OTP.  
- Add rate limiting to prevent brute forcing OTPs.  
- Ensure OTP validation is tied to the specific phone/email session.  

---

## 🧠 **What I Learned**
- Sometimes, the simplest tests (like removing a parameter) can uncover impactful bugs.  
- Never assume security controls are enforced server-side.  
- Writing a clear PoC with Burp helps a lot when reporting responsibly.  

---

**Signing off ✌️**

**[ROHAN S](https://www.linkedin.com/in/rohanscr7123/)**  

(**Aspiring Security Engineer**)  
