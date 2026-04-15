## n8n-Code-Node-CodeExplain
এই কোডটি একটি **ডেটা পার্সিং ও ট্রান্সফর্মেশন স্ক্রিপ্ট** — মূলত ফর্ম থেকে আসা raw data কে clean করে সাজানো হচ্ছে।

---

## ধাপে ধাপে ব্যাখ্যা

### ১. ডেটা নেওয়া
```js
const data = $json
```
`$json` থেকে পুরো ফর্মের ডেটা একটি object হিসেবে নেওয়া হচ্ছে। (এটি n8n workflow-এর syntax)

---

### ২. নাম পার্স করা
```js
const cleanFull = data.full_name.trim().replace(/\s+/g, ' ')
const parts = cleanFull.split(" ")
const first = parts[0] || ""
const last = parts[1] || ""
```
- `.trim()` — আগে-পিছের extra space সরায়
- `.replace(/\s+/g, ' ')` — মাঝে একাধিক space থাকলে একটি করে দেয়
- `.split(" ")` — নামকে দুই ভাগে ভাগ করে, যেমন `"john doe"` → `["john", "doe"]`
- `first = "john"`, `last = "doe"`

---

### ৩. Email clean করা
```js
let email = data.email.trim().toLowerCase();
```
- Space সরানো + সব letter ছোট হাতে, যেমন `" John@Gmail.COM "` → `"john@gmail.com"`

---

### ৪. Amount পার্স করা
```js
const amountStr = String(rawAmount).trim().replace(/[^0-9.]/g, "");
const amount = parseFloat(amountStr) || 0;
```
- `[^0-9.]` — সংখ্যা ও দশমিক ছাড়া সব বাদ দেয়, যেমন `"৳1,500.00"` → `"1500.00"`
- `parseFloat` → সংখ্যায় রূপান্তর, যদি ব্যর্থ হয় তাহলে `0`

---

### ৫. Capitalize ফাংশন
```js
function cap(s){
  return s.charAt(0).toUpperCase() + s.slice(1).toLowerCase();
}
```
প্রথম অক্ষর বড়, বাকি ছোট করে। যেমন `"jOHN"` → `"John"`

---

### ৬. নাম ও তারিখ সাজানো
```js
const firstName = cap(first)   // "john" → "John"
const lastName = cap(last)     // "doe"  → "Doe"

const d = new Date(rawDate);
const niceDate = d.toDateString(); // "Wed Apr 15 2026"
```

---

### ৭. Subject ও Message তৈরি
```js
const subject = `Qoute Request from ${firstName} - Amount ${amount}`;
// → "Qoute Request from John - Amount 1500"

const cleanMsg = msg.charAt(0).toUpperCase() + msg.slice(1);
// বার্তার প্রথম অক্ষর বড় হাতে
```
> ⚠️ **একটি টাইপো আছে** — `"Qoute"` হওয়া উচিত `"Quote"`

---

### ৮. চূড়ান্ত output
```js
return { firstName, lastName, email, amount, niceDate, subject, cleanMsg }
```
সব clean করা ডেটা একটি object হিসেবে return করা হচ্ছে, যা পরের workflow step-এ ব্যবহার হবে।

---

**সংক্ষেপে বললে**, এই কোড ফর্ম থেকে আসা এলোমেলো ডেটাকে (বড়-ছোট হাতি নাম, extra space, টাকার চিহ্ন সহ amount) সুন্দর করে email পাঠানোর উপযুক্ত format-এ রূপান্তর করছে।
