
---

# 📘 آموزش: ساخت سیستم «پرداخت قبض و فاکتور» با Ledgers  
### نمونه پلتفرم: **Billably**

Billably یک پلتفرم B2B است که:

- از یک **خریدار** (Buyer) پول می‌گیرد  
- به یک **فروشنده/تأمین‌کننده** (Vendor) پول می‌دهد  
- و بابت این خدمت کارمزد می‌گیرد  

Ledger دقیقاً برای ثبت همین سه نوع عملیات طراحی می‌شود.

---

# ۱) طراحی حساب‌ها و منطق تراکنش‌ها

## حساب‌های لازم (Ledger Accounts)

| حساب | نوع | کاربرد |
|---|---|---|
| Cash | Debit | پول واقعی Billably در بانک |
| Buyer Receivable | Debit | پولی که خریدار به Billably بدهکار است |
| Vendor Payable | Credit | پولی که Billably باید به Vendor پرداخت کند |
| Revenue | Credit | کارمزدی که Billably از جریان فاکتورها کسب می‌کند |

### چرا این Normality ها؟
- Cash = دارایی → **debit-normal**
- Buyer Receivable = طلب از دیگران → **debit-normal**
- Vendor Payable = بدهی شرکت → **credit-normal**
- Revenue = درآمد → **credit-normal**

---

# ۲) نمونه تراکنش‌های واقعی Billably

این مدل سه رویداد اصلی دارد:

---

## رویداد ۱) صدور فاکتور توسط Vendor برای Buyer  
مثال: Valor برای Beta یک فاکتور 1000 دلاری صادر می‌کند.  
Billably هم 10 دلار کارمزد می‌گیرد.  
پس:

- Buyer → بدهکار 1010 دلار  
- Vendor → طلبکار 1000 دلار  
- Billably → 10 دلار درآمد

### Ledger Entries:
- Buyer Receivable → **debit 101000**
- Vendor Payable → **credit 100000**
- Revenue → **credit 1000**

این فقط ثبت «تعهدات» مالی است؛ هنوز پول جابه‌جا نشده.

---

## رویداد ۲) Billably پول را از Buyer می‌کشد (Funding Pull)

Buyer مبلغ کامل 1010 دلار را به Billably می‌دهد.

پس:

- Buyer Receivable صفر می‌شود  
- Cash شرکت 1010 دلار زیاد می‌شود  

### Ledger Entries:
- Buyer Receivable → **credit 101000**
- Cash → **debit 101000**

---

## رویداد ۳) Billably پول Vendor را واریز می‌کند (Remittance)

Billably 1000 دلار را به Valor پرداخت می‌کند.

پس:

- Vendor Payable کم می‌شود  
- Cash شرکت کم می‌شود  

### Ledger Entries:
- Vendor Payable → **debit 100000**
- Cash → **credit 100000**

---

## وضعیت نهایی
در پایان:

- Billably **۱۰ دلار در Cash اضافه دارد**  
- که برابر است با کارمزد (Revenue)

---

# ۳) خواندن موجودی‌ها (Read Balances)

همانند مثال Digital Wallet، Ledger همیشه موجودی‌ها را به‌صورت خودکار محاسبه می‌کند.

اگر بخواهیم بدانیم:

- Buyer چقدر بدهکار است  
- Vendor چقدر طلبکار است  
- Billably چقدر Cash دارد  

کافی است از **GET /ledger_accounts/{id}** استفاده کنیم.

### مثال موجودی Buyer Beta
سه نوع موجودی برمی‌گردد:

- pending_balance  
- posted_balance  
- available_balance  

در مثال:

```
amount = 300000  (یعنی 3000 دلار)
```

این یعنی مجموع بدهکاری‌های Beta تا این لحظه.

Ledger تمام بدهکار/بستانکارها را جمع زده و نتیجه را می‌دهد.

---

# ۴) Ledger Account Settlements  
(در ادامه‌ی آموزش اصلی می‌آید)

در محصولاتی مثل Billably معمولاً می‌خواهید:

- مانده یک حساب Payable  
- یا یک Receivable  
را «تسویه» کنید، بدون اینکه تراکنش اشتباه یا دوگانه ثبت شود.

Ledger Settlements برای همین است:

- حساب A (مثلاً Vendor Payable) خالی می‌شود  
- موجودی به حساب B منتقل می‌شود  

این ابزار برای عملیات‌هایی مثل batch payouts، sweep کردن حساب‌ها و closing periods ضروری است.

---

# 📊 جمع‌بندی کلی Flow در Billably

### مرحله ۱: Vendor فاکتور صادر می‌کند  
→ Buyer بدهکار می‌شود  
→ Vendor طلبکار  
→ Billably درآمد کسب می‌کند

### مرحله ۲: Billably از Buyer پول می‌گیرد  
→ Buyer Receivable صفر می‌شود  
→ Cash شرکت افزایش می‌یابد

### مرحله ۳: Billably پول Vendor را می‌دهد  
→ Vendor Payable صفر می‌شود  
→ Cash کاهش می‌یابد

### نتیجه:  
Billably تنها **کارمزد** را نگه می‌دارد.

---

عالی — این بخش، ادامه‌ی **Advanced Topics** در سناریوی **Bill Payments / Billably** است.  
من آن را مثل یک **جزوه آموزشی ساده، مهندسی‌محور و قابل پیاده‌سازی** توضیح می‌دهم.

---

# 📘 Bill Payments – Advanced Topics
تا اینجا فقط «ثبت حساب‌ها و تراکنش‌ها» را دیدیم.  
اما برای یک سیستم **Production-ready**، این‌ها کافی نیستند.

در عمل، شما به این قابلیت‌ها نیاز دارید:

1. **Metadata** برای جستجو و ردیابی
2. **Ledger Account Categories** برای موجودی‌های تجمیعی
3. **Ledger Account Settlements** برای تسویه امن مانده‌های انباشته

---

# ۱) Attaching Metadata
## متادیتا یعنی چه؟

Metadata یعنی اطلاعات کمکی به‌صورت **key-value** که روی اشیای Ledger ذخیره می‌کنید.

خودش عدد مالی نیست، اما برای:

- جستجو
- فیلتر
- گزارش‌گیری
- اتصال به مدل دامنه‌ی داخلی شما

خیلی مهم است.

---

## چه چیزهایی می‌توانند Metadata داشته باشند؟

طبق این بخش:

| Object | Metadata نمونه |
|---|---|
| Ledger | `productID` |
| Ledger Account | `accountType`, `vendorName`, `vendorId`, `buyerName`, `buyerId` |
| Ledger Transaction | `transactionType`, `invoiceNum`, `paymentId` |

---

## چرا در سیستم واقعی مهم است؟

فرض کن در سیستم Spring Boot خودت این موجودیت‌ها را داری:

- `Buyer`
- `Vendor`
- `Invoice`
- `Payment`
- `LedgerAccount`
- `LedgerTransaction`

تو معمولاً نمی‌خواهی فقط با UUIDهای Modern Treasury کار کنی.  
می‌خواهی بگویی:

- این حساب مربوط به کدام Vendor است؟
- این تراکنش مربوط به کدام Invoice است؟
- این Funding Pull مربوط به کدام Payment داخلی است؟

اینجاست که Metadata کمک می‌کند.

---

## مثال واقعی

### روی Ledger Account مربوط به Buyer:
```json
{
  "buyerId": "buyer_30001",
  "buyerName": "Beta Corp",
  "accountType": "buyer_receivable"
}
```

### روی Ledger Account مربوط به Vendor:
```json
{
  "vendorId": "vendor_10001",
  "vendorName": "Valor Inc",
  "accountType": "vendor_payable"
}
```

### روی Ledger Transaction مربوط به Invoice:
```json
{
  "transactionType": "invoice",
  "invoiceNum": "IN00123",
  "paymentId": "pay_789"
}
```

---

## مزیت مهم
Modern Treasury اجازه می‌دهد با Metadata جستجو کنی.

مثلاً:
- همه تراکنش‌های Buyer Beta
- فقط تراکنش‌هایی که `invoiceNum = IN00123`
- فقط Funding Pullها
- فقط تراکنش‌های مربوط به یک Vendor خاص

---

## نگاه معماری
اگر سیستم تو **on-prem core wallet / ledger** است، Metadata همان پلی است بین:

- **Ledger domain**
- **Business domain**

یعنی بدون اینکه مدل Ledger را خراب کنی، می‌توانی آن را به:
- Invoice service
- Buyer/Vendor service
- Payment orchestration
وصل کنی.

---

# ۲) Defining Ledger Account Categories
## مسئله چیست؟

گاهی فقط موجودی یک حساب مهم نیست؛  
بلکه **جمع چندین حساب مشابه** مهم است.

مثلاً Billably می‌خواهد بداند:

- مجموع همه بدهی‌هایش به Vendorها چقدر است؟
- مجموع همه طلب‌هایش از Buyerها چقدر است؟
- مجموع کل Cash عملیاتی شرکت چقدر است؟

اگر بخواهی هر بار این را دستی جمع بزنی:
- کند می‌شود
- گزارش‌گیری سخت می‌شود
- منطق تجمیع در اپلیکیشن پخش می‌شود

برای همین از **Ledger Account Category** استفاده می‌شود.

---

## Ledger Account Category چیست؟

یک دسته‌بندی برای چند حساب Ledger که موجودی آن‌ها را به‌صورت **roll-up** یا تجمیعی نگه می‌دارد.

یعنی مثل یک حساب «مجموع» عمل می‌کند، اما از روی چند حساب واقعی.

---

## نمونه Categoryها

| Category | Normality | کاربرد |
|---|---|---|
| Total Payable | Credit | جمع تمام Vendor Payableها |
| Total Receivable | Debit | جمع تمام Buyer Receivableها |
| Total Operating Cash | Debit | جمع همه Cash accountهای عملیاتی |

---

## مثال مفهومی

فرض کن این Vendorها را داری:

- Vendor #10001 Payable = \$2,000
- Vendor #10002 Payable = \$5,000
- Vendor #10003 Payable = \$1,500

پس:

- **Total Payable = \$8,500**

این عدد برای:
- داشبورد مالی
- ریسک نقدینگی
- گزارش بدهی جاری
خیلی مهم است.

---

## مثال برای Buyer Receivables

- Buyer A Receivable = \$3,000
- Buyer B Receivable = \$7,000
- Buyer C Receivable = \$2,000

پس:

- **Total Receivable = \$12,000**

یعنی Billably انتظار دارد 12,000 دلار از خریداران بگیرد.

---

## مثال برای Cash Accounts

گاهی شرکت بیش از یک حساب بانکی دارد:

- Bank1 Operating Cash = \$50,000
- Bank2 Operating Cash = \$30,000

پس:

- **Total Operating Cash = \$80,000**

---

## اهمیت در سیستم واقعی
در یک سیستم B2B واقعی، این دسته‌ها برای موارد زیر حیاتی‌اند:

- Treasury dashboard
- CFO reporting
- liquidity management
- reconciliation
- reserve checks
- end-of-day reporting

---

## نگاه مهندسی
در لایه domain خودت، Ledger Account Category تقریباً شبیه این است:

- aggregate view
- derived balance container
- reporting dimension

یعنی بهتر است منطق تجمیع را به خود ledger بسپاری، نه اینکه در اپلیکیشن هر بار SUM بگیری.

---

# ۳) Ledger Account Settlements
این مهم‌ترین بخش پیشرفته‌ی این سناریو است.

---

## مسئله‌ای که Settlement حل می‌کند

در خیلی از سیستم‌های invoice/payment، یک حساب در طول زمان **مانده انباشته** پیدا می‌کند.

مثلاً:
- Buyer Beta در طول ماه چندین فاکتور دارد
- هر فاکتور یک Receivable جدید ایجاد می‌کند
- آخر ماه می‌خواهی کل مانده‌ی Beta را یکجا تسویه کنی

اگر این کار را دستی انجام دهی، چند مشکل پیش می‌آید:

- شاید مبلغ را اشتباه حساب کنی
- شاید بعضی Entryها دوبار تسویه شوند
- شاید همزمانی باعث race condition شود
- شاید یک بخشی از مانده settle شود و بخشی نه

برای حل این‌ها از **Ledger Account Settlement** استفاده می‌شود.

---

## Settlement چیست؟

یک سازوکار استاندارد که:

1. به‌صورت **اتمیک** مقدار انباشته‌ی یک حساب را حساب می‌کند  
2. یک **Ledger Transaction** برای همان مقدار تولید می‌کند  
3. Entryهایی را که settle شده‌اند **tag** می‌کند تا دوباره settle نشوند  

---

## تعریف ساده
Settlement یعنی:

> «هرچه تا این لحظه در این حساب جمع شده، امن و یکجا به حساب مقابل منتقل کن.»

---

## مثال Billably
Buyer Beta در طول ماه چند فاکتور داشته و حالا مجموعاً **\$12,750** بدهکار است.

می‌خواهیم:

- مانده‌ی `Buyer Beta Receivable`
- به حساب `Cash`
تسویه شود.

یعنی:
- Buyer Receivable کم شود
- Cash زیاد شود

---

## درخواست API برای Settlement

نمونه‌ای که دادی:

```bash
POST /ledger_account_settlements
```

با بدنه:

```json
{
  "status": "pending",
  "allow_either_direction": false,
  "description": "Settle Beta Corp Buyer Receivable Aug 2025",
  "settled_ledger_account_id": <buyer_beta_account_id>,
  "contra_ledger_account_id": <cash_account_id>,
  "effective_at_upper_bound": "2025-08-31T00:00:00Z"
}
```

---

## معنی هر فیلد

### `status: "pending"`
یعنی Settlement ایجاد شده، ولی هنوز نهایی نشده.

---

### `allow_either_direction: false`
یعنی جهت تسویه باید مطابق منطق مورد انتظار باشد و سیستم آزاد نباشد در هر جهت دلخواه Entry بسازد.

در اینجا مهم است چون:
- Receivable باید کاهش یابد
- Cash باید افزایش یابد

---

### `settled_ledger_account_id`
حسابی که قرار است مانده‌اش settle شود.  
اینجا:

- `Buyer Beta Receivable`

---

### `contra_ledger_account_id`
حساب طرف مقابل که مانده به آن منتقل می‌شود.  
اینجا:

- `Cash`

---

### `effective_at_upper_bound`
خیلی مهم است.  
یعنی فقط Entryهایی را settle کن که تا این زمان ایجاد شده‌اند.

مثلاً:
- همه بدهی‌های Beta تا پایان آگوست 2025

این برای **period closing** و **monthly collection** فوق‌العاده مهم است.

---

# ۴) Settlement پشت صحنه چه کار می‌کند؟

طبق توضیح مستند:

## الف) مقدار انباشته را حساب می‌کند
مثلاً می‌بیند تا `2025-08-31` جمع Receivableهای Beta برابر است با:

- \$5,000
- \$3,500
- \$4,250

جمع:
- **\$12,750**

---

## ب) یک Ledger Transaction می‌سازد
مثلاً چیزی شبیه:

- Beta Receivable → credit 1275000
- Cash → debit 1275000

این تراکنش معمولاً اول در وضعیت **pending** ایجاد می‌شود.

---

## ج) Entryهای settled را tag می‌کند
یعنی سیستم علامت می‌زند که این Entryها قبلاً در Settlement لحاظ شده‌اند.

نتیجه:
- دوباره پرداخت نمی‌شوند
- duplicate settlement رخ نمی‌دهد
- audit trail شفاف می‌ماند

---

# ۵) چرا Settlement از تراکنش دستی بهتر است؟

اگر خودت دستی تراکنش بزنی، باید:

- مانده را حساب کنی
- مطمئن شوی چیزی جا نمانده
- مطمئن شوی چیزی دوبار settle نشده
- concurrency را کنترل کنی
- بین Payment و Ledger هماهنگی بسازی

Settlement این پیچیدگی را کم می‌کند.

---

# ۶) رابطه Settlement با جابه‌جایی واقعی پول
نکته مهم:

Settlement خودش لزوماً به معنی این نیست که پول فیزیکی همین لحظه جابه‌جا شده.  
بلکه:

- Ledger می‌فهمد چه مبلغی باید settle شود
- شما با سیستم بانکی / Payments واقعاً پول را حرکت می‌دهید
- بعد از رسیدن پول، Settlement را `posted` می‌کنید

---

## Flow واقعی

### مرحله ۱
Settlement را با وضعیت `pending` می‌سازی.

### مرحله ۲
سیستم asynchronously آن را پردازش می‌کند.

### مرحله ۳
از طریق webhook `finish_processing` می‌فهمی:
- settlement amount چقدر شد
- generated ledger_transaction_id چیست

### مرحله ۴
با استفاده از این amount، از Buyer پول می‌کشی:
- با Modern Treasury Payments
- یا با سیستم پرداخت خودت

### مرحله ۵
وقتی پول واقعاً به حسابت رسید:
```bash
PATCH /ledger_account_settlements/<settlement_id>
```

```json
{
  "status": "posted"
}
```

### مرحله ۶
در نتیجه:
- Ledger Transaction مربوطه هم `posted` می‌شود
- تسویه نهایی می‌شود

---

# ۷) مثال ساده‌ی عددی Settlement

فرض کن Buyer Beta در ماه آگوست این فاکتورها را داشته:

- Invoice 1 = \$400
- Invoice 2 = \$250
- Invoice 3 = \$350

جمع Receivable:
- **\$1,000**

حالا آخر ماه Settlement می‌سازی.

سیستم:

- Receivable Beta را **credit 1000**
- Cash را **debit 1000**

می‌کند.

نتیجه:
- Buyer Beta Receivable صفر می‌شود
- Cash شرکت 1000 دلار بیشتر می‌شود

---

# ۸) مزیت مهم: جلوگیری از دوبار پرداخت
مستند خیلی صریح می‌گوید که Settlement:

> all the Ledger Entries that are being settled, so that an entry cannot be paid out twice

یعنی Entryهایی که settle شدند علامت می‌خورند.  
پس اگر دوباره Settlement جدید بسازی، سیستم همان Entryها را دوباره در محاسبه نمی‌آورد.

این برای B2B payments حیاتی است، چون دوبار collect کردن یا دوبار remit کردن فاجعه مالی و عملیاتی است.

---

# ۹) یک نگاه domain-driven برای پیاده‌سازی
اگر بخواهم این را با زبان معماری backend توضیح دهم:

## Aggregateهای محتمل
- `BuyerAccount`
- `VendorAccount`
- `Invoice`
- `Settlement`
- `PaymentInstruction`

## جریان مناسب
- Invoice created → Ledger Transaction(invoice accrual)
- Settlement requested → Ledger Account Settlement created
- Settlement processed webhook → internal Payment initiated
- Bank confirms funds received → Settlement posted
- Settlement posted → balances finalized

---

## مدل خوب برای Spring Boot
برای سیستم تو، معمولاً این mapping خوب است:

### Internal DB
- `buyer`
- `vendor`
- `invoice`
- `payment`
- `ledger_account_mapping`
- `settlement_request`

### Metadata on Ledger
- `buyerId`
- `vendorId`
- `invoiceId`
- `invoiceNum`
- `paymentId`
- `transactionType`
- `settlementBatchId`

---

# ۱۰) تفاوت Transaction عادی با Settlement
## Transaction عادی
وقتی خودت دقیقاً می‌دانی چه مبلغی باید ثبت شود.

مثلاً:
- Vendor یک فاکتور 1000 دلاری صادر کرده
- fee = 10
- پس transaction amount دقیقاً معلوم است

---

## Settlement
وقتی مبلغ باید از روی مانده‌ی انباشته‌ی قبلی محاسبه شود.

مثلاً:
- تمام مانده‌های Buyer تا پایان ماه
- تمام بدهی Vendor تا امروز
- تمام fee accrualهای یک دوره

---

# ۱۱) بهترین کاربردهای Settlement در Bill Payment
Settlement مخصوصاً در این موارد مفید است:

- monthly buyer collection
- vendor batch payout
- daily sweeping receivables into operating cash
- closing unpaid balances at period end
- safely clearing accrued liabilities

---

# ۱۲) جمع‌بندی نهایی این بخش

## Metadata
برای اتصال Ledger به دنیای business:
- buyerId
- vendorId
- invoiceNum
- paymentId
- transactionType

## Ledger Account Categories
برای دیدن مجموع حساب‌های مشابه:
- Total Payable
- Total Receivable
- Total Operating Cash

## Ledger Account Settlements
برای تسویه امن و یکجای مانده‌های انباشته:
- amount را خودش حساب می‌کند
- transaction می‌سازد
- entryها را علامت می‌زند
- از دوبار settle شدن جلوگیری می‌کند
- با payment flow هماهنگ می‌شود

---

# ✅ نتیجه‌ی مهندسی
اگر بخواهیم این را خیلی خلاصه بگوییم:

- **Transactions** = ثبت رویدادهای مالی مشخص  
- **Categories** = دید تجمیعی برای گزارش و داشبورد  
- **Metadata** = اتصال ledger به business objects  
- **Settlements** = تسویه امن مانده‌های انباشته در دنیای واقعی  

---

اگر بخواهی، در پیام بعدی می‌توانم یکی از این 3 مورد را انجام بدهم:

1. **طراحی کامل Chart of Accounts برای Bill Payment system**
2. **ترجمه این سناریو به مدل‌های Java/Spring Boot entity + service**
3. **رسم فلوچارت end-to-end از Invoice → Receivable → Collection → Vendor Payout → Settlement**

اگر هدفت پیاده‌سازی است، گزینه 2 از همه کاربردی‌تره.