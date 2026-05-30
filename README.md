# 📊 IKXARI ERP Dashboard — Poori Guide (Simple Bhasa)

> Yeh file batati hai: **kaunsa data kahaan se aata hai**, aur **har cheez kaise calculate hoti hai** — ekdum aasan bhasa me. Koi bhi padhke samajh jaye.

---

## 🏗️ Pehle Basic — Yeh Kaise Chalta Hai?

```
Google Sheet (tera data)
        ↓
Apps Script (Code.gs) — data uthata hai, hisaab lagata hai
        ↓
Website (index.html) — sundar dashboard dikhata hai
```

3 hisse hain:
1. **Google Sheet** — yahaan tera saara data hai (sales, stock, expense)
2. **Code.gs** — yeh "dimaag" hai. Sheet se data leke, JSON me badal deta hai
3. **index.html** — yeh "chehra" hai. Data ko chart, table, card me dikhata hai

Har **60 second** me website apne aap naya data le aati hai. Tujhe refresh nahi karna padta.

---

## 📁 Sheet Tabs — Kaunsa Tab Kis Kaam Ka

| Tab Naam | Kya Hai Ismें |
|----------|---------------|
| **Gofrugal_Raw_Data** | Gofrugal software ka kaccha (raw) data. Yahaan tu entry karta hai |
| **Summarize_Data** | Raw data ka साफ version (formula se banta). **Saari SALES yahaan se aati hai** |
| **Current_Stock** | Aaj kitna maal stock me hai. **Saara STOCK + IMS yahaan se** |
| **Expenses_Sheet** | Kharche (rent, salary, etc.) |
| **Daily_Operations** | Roz ka total (sirf summary) |

> ⚠️ **Bahut zaroori:** `Summarize_Data` me formula ka range **hamesha open rakhna** — jaise `K6:K` (end number mat daalna `K285` jaise). Warna naya row add karne pe dashboard ud jata hai.

---

## 💰 SALES Ka Data (Summarize_Data se)

**Kahaan se:** `Summarize_Data` tab

**Kaunse column:**
- Date → kab bika
- Bill No → kaunsa bill
- Item Name → kya bika
- Sold Qty → kitne piece bike
- Item Amt (with Discount and Ex GST) → **Net Sales** (asli bikri, bina tax)
- Gross Profit → munafa
- Landing Cost (In GST) → ek piece ki lagat
- GST on Selling → tax

**Kaise calculate:**
```
Net Sales      = saare bills ka "Item Amt" jod do
Gross Profit   = saare "Gross Profit" jod do
Total COGS     = Landing Cost × Qty (har item ka, phir jod do)
Total GST      = saare "GST on Selling" jod do
```

---

## 📦 STOCK Ka Data (Current_Stock se)

**Kahaan se:** `Current_Stock` tab (Gofrugal se export karke daalta hai)

**Kaunse column:**
- B → Brand (kaunsa brand)
- D → Type of Wear (kaisa kapda)
- F → Fabric (kaunsa material)
- M → Item Name (item ka naam)
- R → Landing Cost (ek piece ki lagat)
- U → MRP (tag price)
- BB → **Closing Stock** (abhi kitna bacha hai) ← sabse important
- BJ → Stock In (kitna maal aaya tha)
- BH → Distributor Name (kahaan se aaya)

**Meter Wala Niyam:**
> Kuch maal "meter" me aata hai (jaise 2.5 meter, 33 meter). Hum usko **1 piece** ginte hain. Matlab jis bhi row me stock 0 se zyada hai, woh = 1 unit.

---

## 🧮 IMS (Inventory) Kaise Calculate Hota — STEP BY STEP

Yeh dashboard ka sabse important hissa. Batata hai **kya khatam hone wala hai, kya zyada pada hai**.

### Step 1: Roz kitna bikta hai? (Average nikalo)
```
Maan le pichle 30 din me ek item 60 piece bika
Average roz ki bikri = 60 ÷ 30 = 2 piece per din
```

### Step 2: Maximum kitna rakhna chahiye? (Max Level)
```
Max Level = Average roz bikri × Lead Time × Safety Factor
```
- **Lead Time** = naya maal aane me kitne din lagte (default 10 din)
- **Safety Factor** = thoda extra rakhna (default 1.2 yaani 20% zyada)

**Example:**
```
Average roz = 2 piece
Lead Time = 10 din
Safety = 1.2

Max Level = 2 × 10 × 1.2 = 24 piece
```
Matlab: is item ke 24 piece hone chahiye.

### Step 3: Abhi kitna % bacha hai?
```
Stock % = (Abhi ka stock ÷ Max Level) × 100
```
**Example:** Abhi 6 piece hai, Max 24 hai
```
Stock % = (6 ÷ 24) × 100 = 25%
```

### Step 4: Rang (Color) Decide Karo
| Stock % | Rang | Matlab |
|---------|------|--------|
| 100% ya zyada | 🟣 Purple | Bahut zyada maal (Overstock) |
| 66% – 99% | 🟢 Green | Sahi hai (Healthy) |
| 33% – 65% | 🟡 Yellow | Dhyan do (Watch) |
| 10% – 32% | 🔴 Red | Khatam hone wala (Critical) |
| 10% se kam / 0 | ⚫ Black | Khatam (Stock-Out) |

### Step 5: Kitna Mangwana Hai? (Reorder)
```
Agar stock 66% se kam hai:
Reorder Qty = Max Level − Abhi ka stock
```
**Example:** Max 24, abhi 6
```
Reorder = 24 − 6 = 18 piece mangwao
```

---

## 🔄 SABSE ZAROORI BAAT — Kharidne Ke Baad Kya?

**Tera sawaal:** "Maine reorder dekha, maal kharid liya. Ab bhi wahi purana data aayega?"

**Jawab:** Nahi! Aise kaam karta hai:

```
1. Dashboard ne bola: "521011 SUIT — 18 piece mangwao"
2. Tu 18 piece kharidta hai
3. Naya maal Gofrugal me entry hota (jaise hamesha)
4. Tu Current_Stock sheet FIR SE export karta Gofrugal se
   (purana Current_Stock hata ke naya paste)
5. Ab us item ka stock 6 → 24 ho gaya
6. Dashboard 60 sec me naya data leta
7. Woh item ab 🟢 Green (Healthy) ho jata
8. Reorder list se APNE AAP HAT jata
```

> **Matlab:** Tujhe kuch "done" mark nahi karna. Bas **Current_Stock sheet ko regular update** karte rehna (Gofrugal se naya export). Stock badhega → reorder list apne aap sahi ho jayegi.

**Kitni baar update karu?**
- Roz ya 2-3 din me ek baar Current_Stock refresh karo
- Jitna fresh stock data, utni sahi reorder list

---

## 🛒 PURCHASE LOG (Current_Stock se)

**Kahaan se:** `Current_Stock` tab

Dikhata hai:
- **Total Received** — kitna maal aaya (Stock In jod ke)
- **Purchase Value** — us maal ki kitni keemat
- **Total Sold** — kitna becha
- **Unique Items** — kitne alag-alag product
- Neeche table: har item ka distributor, price, received, sold, closing stock

---

## 📈 BAAKI PAGES — Chhota Sa Matlab

| Page | Kya Dikhata |
|------|-------------|
| **Date Overview** | Sales, profit, kharcha ka graph (date choose karke) |
| **Summarize Data** | Saare bills ki list |
| **Expense Overview** | Kharche ka breakdown (rose chart) |
| **Vendor Performance** | Kaunsa supplier kitna becha |
| **GST Overview** | Tax ka hisaab (kitna dena hai) |
| **Item Movements** | Kaunsa item fast bika, kaunsa slow |
| **Inventory (IMS)** | Stock management (password: ikxariadmin) |
| **Customer CRM** | Kaunsa customer kitna kharidta |
| **Daily Report** | Ek din ka pura hisaab |
| **Purchase Log** | Kitna maal aaya |

---

## 🔐 Password
- **IMS page** kholne ke liye: `ikxariadmin`
- **Admin panel** (☕ icon double-click): `ikxariadmin`

---

## 🚀 Update Kaise Kare (Deploy)

### Code.gs badla to:
1. Apps Script editor kholo
2. Purana code hata ke naya paste karo → Save (Ctrl+S)
3. **Deploy → Manage deployments → ✏️ pencil → Version: New version → Deploy**
4. URL same rahega (HTML change nahi karna)

### index.html badla to:
1. GitHub repo kholo → `index.html` → ✏️ edit
2. Purana hata ke naya paste → **Commit**
3. 30-60 sec ruko → website hard refresh (Ctrl+Shift+R)

---

## ❓ Common Problem

**Q: Dashboard khaali ho gaya / data ud gaya?**
A: `Summarize_Data` ka formula range check karo. `K6:K285` ko `K6:K` karo (end number hata do).

**Q: IMS me "MOCK" dikha raha?**
A: Code.gs deploy nahi hua (New version). Ya Current_Stock sheet nahi mili.

**Q: Reorder me purana item dikh raha jo kharid liya?**
A: Current_Stock sheet refresh karo (naya Gofrugal export). Stock update hote hi hat jayega.

**Q: Naya maal kharida, dashboard me kab dikhega?**
A: Current_Stock update karo → 60 sec me apne aap aa jayega.

---

*Banaya: SOMNATH · Ikxari ERP — Beyond Ordinary*
