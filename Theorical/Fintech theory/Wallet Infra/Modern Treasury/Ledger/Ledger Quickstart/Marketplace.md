# 📘 Marketplace Ledgers Tutorial  
## آموزش طراحی Ledger برای مارکت‌پلیس — مثال Rarebnb

در این بخش، سناریوی **Marketplace** را بررسی می‌کنیم.  
مثال مستندات Modern Treasury یک شرکت فرضی به نام **Rarebnb** است.

Rarebnb شبیه Airbnb است:

- یک **Guest / مسافر** اقامت رزرو می‌کند.
- یک **Owner / صاحب ملک** میزبان است.
- Rarebnb پول را از Guest می‌گیرد.
- سهم Owner را پرداخت می‌کند.
- و خودش کمیسیون برمی‌دارد.

---

# ۱) Use Case Overview

هدف این آموزش این است که ببینیم یک مارکت‌پلیس چطور می‌تواند با Ledgers:

1. مانده بدهی Guestها را نگه دارد.
2. مانده طلب Ownerها را نگه دارد.
3. Cash عملیاتی خودش را ردیابی کند.
4. Revenue یا کمیسیون خودش را ثبت کند.
5. رزرو، پرداخت Guest و پرداخت Owner را به‌شکل حسابداری دوطرفه ثبت کند.

---

# ۲) طراحی حساب‌ها و تراکنش‌ها

قبل از اینکه API صدا بزنیم، باید **Chart of Accounts** و منطق تراکنش‌ها را طراحی کنیم.

در Marketplace، معمولاً چهار نوع حساب اصلی داریم:

| Ledger Account | Normality | معنی |
|---|---:|---|
| `Cash` | Debit | پول واقعی Rarebnb در حساب بانکی/عملیاتی |
| `Guest Receivable` | Debit | مبلغی که Rarebnb باید از Guest بگیرد |
| `Owner Payable` | Credit | مبلغی که Rarebnb به Owner بدهکار است |
| `Revenue` | Credit | درآمد یا کمیسیون Rarebnb |

---

# ۳) مفهوم هر حساب

## ۳.۱) Cash — حساب نقدی شرکت

```text
normal_balance = debit
```

این حساب نشان‌دهنده پولی است که Rarebnb واقعاً در حساب عملیاتی خود نگه می‌دارد.

چون Cash دارایی شرکت است، حساب آن **debit-normal** است.

### افزایش Cash
وقتی Guest پرداخت می‌کند:

```text
Cash debit
```

### کاهش Cash
وقتی Rarebnb به Owner پول می‌دهد:

```text
Cash credit
```

---

## ۳.۲) Guest Receivable — طلب از Guest

```text
normal_balance = debit
```

وقتی Guest رزرو انجام می‌دهد، Rarebnb از Guest طلبکار می‌شود.

مثلاً Guest X رزرو ۲۰۰ دلاری انجام می‌دهد.  
پس Rarebnb می‌گوید:

> Guest X باید ۲۰۰ دلار به ما پرداخت کند.

این مبلغ در حساب `Guest X Receivable` ثبت می‌شود.

چون Receivable یعنی «طلب ما از دیگران»، از جنس دارایی است و **debit-normal** است.

---

## ۳.۳) Owner Payable — بدهی به Owner

```text
normal_balance = credit
```

وقتی Guest رزرو می‌کند، Rarebnb باید بخش عمده مبلغ را به Owner بدهد.

مثلاً از رزرو ۲۰۰ دلاری:

- ۱۰ دلار کمیسیون Rarebnb
- ۱۹۰ دلار سهم Owner

پس Rarebnb به Owner Y بدهکار می‌شود:

```text
Owner Y Payable = 190 USD
```

چون Payable یعنی «بدهی ما به دیگران»، حساب آن **credit-normal** است.

---

## ۳.۴) Revenue — درآمد Rarebnb

```text
normal_balance = credit
```

Revenue درآمد مارکت‌پلیس است.

در مثال:

- مبلغ رزرو: ۲۰۰ دلار
- کمیسیون Rarebnb: ۵٪
- درآمد Rarebnb: ۱۰ دلار

پس حساب Revenue با credit افزایش پیدا می‌کند.

---

# ۴) تراکنش‌های اصلی Marketplace

Rarebnb برای workflow ساده خود سه رویداد مالی اصلی دارد:

| رویداد | Debit | Credit | توضیح |
|---|---|---|---|
| Guest Booking | Guest Receivable | Owner Payable + Revenue | Guest رزرو می‌کند؛ طلب از Guest و بدهی به Owner ثبت می‌شود |
| Guest Payment | Cash | Guest Receivable | Guest پول را پرداخت می‌کند؛ طلب از Guest صفر می‌شود |
| Owner Payout | Owner Payable | Cash | Owner پولش را می‌گیرد؛ بدهی Rarebnb به Owner صفر می‌شود |

---

# ۵) نکته مهم: Accrual Accounting

در این آموزش، Rarebnb از **Accrual Accounting** استفاده می‌کند.

یعنی درآمد وقتی ثبت می‌شود که **کسب شده**، نه الزاماً وقتی پول نقد وارد شده.

در مثال:

- Guest رزرو انجام می‌دهد.
- هنوز پول از Guest دریافت نشده.
- اما چون booking ثبت شده، Rarebnb درآمد کمیسیون را شناسایی می‌کند.

پس در همان تراکنش Booking:

```text
Revenue credit 10 USD
```

ثبت می‌شود.

---

## تفاوت با Cash Accounting

### Accrual Accounting
درآمد در زمان وقوع رویداد اقتصادی ثبت می‌شود.

```text
Booking happened → Revenue recognized
```

### Cash Accounting
درآمد فقط وقتی ثبت می‌شود که پول واقعاً جابه‌جا شود.

```text
Payment received → Revenue recognized
```

برای مارکت‌پلیس‌ها، مدل accrual معمولاً برای گزارش مالی دقیق‌تر مناسب‌تر است.

---

# ۶) Step 2 — ساخت Ledger Objects

اول باید خود Ledger ساخته شود.

## ساخت Ledger

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledgers \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Rarebnb Ledger",
    "description": "Represents our funds and user balances"
  }'
```

Modern Treasury یک `ledger_id` برمی‌گرداند.

از این به بعد هر حسابی که می‌سازیم باید به همین Ledger وصل شود.

---

# ۷) ساخت Ledger Accountها

برای این مثال چهار حساب ساخته می‌شود:

1. Cash Account
2. Revenue
3. Guest X Receivable
4. Owner Y Payable

---

## ۷.۱) Cash Account

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_accounts \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Cash Account",
    "description": "Tracks Rarebnb cash",
    "normal_balance": "debit",
    "currency": "USD",
    "ledger_id": "<ledger_id>"
  }'
```

این حساب برای نگهداری پول واقعی Rarebnb است.

---

## ۷.۲) Revenue Account

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_accounts \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Revenue",
    "description": "Tracks Rarebnb revenue",
    "normal_balance": "credit",
    "currency": "USD",
    "ledger_id": "<ledger_id>"
  }'
```

این حساب درآمد Rarebnb را ثبت می‌کند.

---

## ۷.۳) Guest X Receivable

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_accounts \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Guest X Receivable",
    "description": "Tracks fees to be collected from Guest X for a stay",
    "normal_balance": "debit",
    "currency": "USD",
    "ledger_id": "<ledger_id>"
  }'
```

این حساب نشان می‌دهد Guest X چقدر به Rarebnb بدهکار است.

در سیستم واقعی معمولاً برای هر Guest یک Receivable جدا دارید:

```text
Guest #1001 Receivable
Guest #1002 Receivable
Guest #1003 Receivable
```

یا اگر granularity بیشتری بخواهید، حتی می‌توانید per-booking حساب بسازید.

---

## ۷.۴) Owner Y Payable

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_accounts \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Owner Y Payable",
    "description": "Tracks amount owed to Owner Y for hosting a guest",
    "normal_balance": "credit",
    "currency": "USD",
    "ledger_id": "<ledger_id>"
  }'
```

این حساب نشان می‌دهد Rarebnb چقدر به Owner Y بدهکار است.

در سیستم واقعی معمولاً برای هر Owner یک Payable جدا دارید:

```text
Owner #501 Payable
Owner #502 Payable
Owner #503 Payable
```

---

# ۸) Step 3 — ثبت Ledger Transactions

حالا می‌رسیم به ثبت رویدادهای مالی واقعی.

سناریو:

- Guest X یک اقامت ۲۰۰ دلاری رزرو می‌کند.
- Rarebnb از Owner کمیسیون ۵٪ می‌گیرد.
- کمیسیون Rarebnb برابر ۱۰ دلار است.
- سهم Owner برابر ۱۹۰ دلار است.

در Modern Treasury مقدارها برحسب کوچک‌ترین واحد پول هستند.

برای USD:

```text
1 USD = 100 cents
```

پس:

```text
200 USD = 20000
10 USD = 1000
190 USD = 19000
```

---

# ۹) تراکنش اول: Guest Booking

## معنی تجاری

Guest X رزرو انجام داده است.

در این لحظه:

- Guest X باید ۲۰۰ دلار پرداخت کند.
- Rarebnb از Owner Y باید ۱۹۰ دلار پرداخت کند.
- Rarebnb ۱۰ دلار درآمد کمیسیون شناسایی می‌کند.

---

## ثبت حسابداری

| Account | Direction | Amount | اثر |
|---|---:|---:|---|
| Guest X Receivable | Debit | 20000 | طلب از Guest افزایش می‌یابد |
| Revenue | Credit | 1000 | درآمد Rarebnb افزایش می‌یابد |
| Owner Y Payable | Credit | 19000 | بدهی به Owner افزایش می‌یابد |

تراکنش balanced است:

```text
Debit = 20000
Credit = 1000 + 19000 = 20000
```

پس حسابداری دوطرفه رعایت شده است.

---

## API

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Guest X Booking",
    "status": "pending",
    "external_id": "<booking_id>",
    "ledger_entries": [
      {
        "amount": 20000,
        "direction": "debit",
        "ledger_account_id": "<guest_x_account_id>"
      },
      {
        "amount": 1000,
        "direction": "credit",
        "ledger_account_id": "<revenue_account_id>"
      },
      {
        "amount": 19000,
        "direction": "credit",
        "ledger_account_id": "<owner_y_account_id>"
      }
    ]
  }'
```

> نکته: در متن نمونه‌ای که فرستادی، بین entry مربوط به Revenue و Owner Payable یک comma جا افتاده بود. در نسخه بالا JSON اصلاح شده است.

---

# ۱۰) چرا Booking اول `pending` است؟

در این سناریو، رزرو ایجاد شده ولی هنوز اقامت واقعاً انجام نشده است.

پس Modern Treasury آن را با وضعیت:

```json
"status": "pending"
```

ثبت می‌کند.

وقتی stay واقعاً اتفاق افتاد، تراکنش به `posted` تبدیل می‌شود.

---

## تبدیل Booking به Posted

```bash
curl --request PATCH \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions/<ledger_transaction_id> \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Updating Guest X Booking to posted",
    "status": "posted"
  }'
```

بعد از posted شدن، اثر مالی این booking نهایی و immutable می‌شود.

---

# ۱۱) تراکنش دوم: Guest Payment

## معنی تجاری

Guest X اقامتش را کامل کرده و حالا Rarebnb از او پول می‌گیرد.

Guest باید همان ۲۰۰ دلار را پرداخت کند.

---

## ثبت حسابداری

| Account | Direction | Amount | اثر |
|---|---:|---:|---|
| Cash | Debit | 20000 | Cash Rarebnb افزایش می‌یابد |
| Guest X Receivable | Credit | 20000 | طلب از Guest کاهش می‌یابد / صفر می‌شود |

تراکنش balanced است:

```text
Debit = 20000
Credit = 20000
```

---

## API

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Guest X Payment",
    "status": "pending",
    "external_id": "<guest_payment_id>",
    "ledger_entries": [
      {
        "amount": 20000,
        "direction": "debit",
        "ledger_account_id": "<cash_account_id>"
      },
      {
        "amount": 20000,
        "direction": "credit",
        "ledger_account_id": "<guest_x_account_id>"
      }
    ]
  }'
```

---

# ۱۲) چرا Guest Payment هم `pending` است؟

چون در دنیای واقعی وقتی پول از Guest دریافت می‌شود، ممکن است هنوز بانک آن را settle نکرده باشد.

الگوی خوب در سیستم‌های Production این است:

```text
Payment initiated → Ledger Transaction pending
Bank confirms/settles → Ledger Transaction posted
```

وقتی بانک تأیید کرد پول واقعاً وارد Cash Account شده، تراکنش را `posted` می‌کنیم.

---

## تبدیل Guest Payment به Posted

```bash
curl --request PATCH \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions/<ledger_transaction_id> \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Updating Guest X Payment to posted",
    "status": "posted"
  }'
```

---

# ۱۳) تراکنش سوم: Owner Payout

## معنی تجاری

Rarebnb باید سهم Owner Y را پرداخت کند.

از ۲۰۰ دلار رزرو:

- ۱۰ دلار کمیسیون Rarebnb
- ۱۹۰ دلار سهم Owner

پس Rarebnb به Owner Y مبلغ ۱۹۰ دلار پرداخت می‌کند.

---

## ثبت حسابداری

| Account | Direction | Amount | اثر |
|---|---:|---:|---|
| Owner Y Payable | Debit | 19000 | بدهی Rarebnb به Owner کاهش می‌یابد / صفر می‌شود |
| Cash | Credit | 19000 | Cash Rarebnb کاهش می‌یابد |

تراکنش balanced است:

```text
Debit = 19000
Credit = 19000
```

---

## API

```bash
curl --request POST \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Owner Y Payout",
    "status": "pending",
    "external_id": "<owner_payout_id>",
    "ledger_entries": [
      {
        "amount": 19000,
        "direction": "debit",
        "ledger_account_id": "<owner_y_account_id>"
      },
      {
        "amount": 19000,
        "direction": "credit",
        "ledger_account_id": "<cash_account_id>"
      }
    ]
  }'
```

---

## تبدیل Owner Payout به Posted

وقتی پرداخت واقعاً در بانک settle شد:

```bash
curl --request PATCH \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions/<ledger_transaction_id> \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Updating Owner Y Payout to posted",
    "status": "posted"
  }'
```

---

# ۱۴) بررسی وضعیت نهایی حساب‌ها

فرض کنیم هر سه transaction در نهایت `posted` شده‌اند.

## Guest X Receivable

اول با Booking بدهکار شد:

```text
Debit 200
```

بعد با Payment بستانکار شد:

```text
Credit 200
```

مانده نهایی:

```text
0
```

یعنی Guest دیگر بدهکار نیست.

---

## Owner Y Payable

اول با Booking بستانکار شد:

```text
Credit 190
```

بعد با Payout بدهکار شد:

```text
Debit 190
```

مانده نهایی:

```text
0
```

یعنی Rarebnb دیگر به Owner بدهکار نیست.

---

## Cash

با Guest Payment افزایش یافت:

```text
Debit 200
```

با Owner Payout کاهش یافت:

```text
Credit 190
```

مانده نهایی:

```text
10
```

یعنی Rarebnb در Cash خود ۱۰ دلار باقی دارد.

---

## Revenue

با Booking درآمد شناسایی شد:

```text
Credit 10
```

مانده نهایی:

```text
10
```

پس:

```text
Cash remaining = Revenue earned = 10 USD
```

---

# ۱۵) نمایش کل Flow به‌صورت ساده

```text
1) Guest Booking
   Guest Receivable +200
   Owner Payable    +190
   Revenue          +10

2) Guest Payment
   Cash             +200
   Guest Receivable -200

3) Owner Payout
   Owner Payable    -190
   Cash             -190

Final:
   Guest Receivable = 0
   Owner Payable    = 0
   Cash             = +10
   Revenue          = +10
```

---

# ۱۶) مقایسه Marketplace با Bill Payments

از نظر الگوی حسابداری، Marketplace خیلی شبیه Bill Payments است.

## Bill Payments

```text
Buyer Receivable
Vendor Payable
Revenue
Cash
```

## Marketplace

```text
Guest Receivable
Owner Payable
Revenue
Cash
```

در واقع فقط اسم Actorها عوض شده:

| Bill Payments | Marketplace |
|---|---|
| Buyer | Guest |
| Vendor | Owner |
| Invoice | Booking |
| Funding Pull | Guest Payment |
| Remittance | Owner Payout |

---

# ۱۷) نکته مهم برای طراحی Production

در سیستم واقعی Rarebnb، احتمالاً این حساب‌ها را باید با granularity مناسب بسازی.

## گزینه ۱: حساب به‌ازای هر کاربر

```text
Guest #123 Receivable
Owner #987 Payable
```

مزیت:
- موجودی کلی هر کاربر سریع قابل خواندن است.
- مناسب برای داشبوردهای user-level.

---

## گزینه ۲: حساب به‌ازای هر Booking

```text
Booking #B001 Receivable
Booking #B001 Payable
```

مزیت:
- ردیابی دقیق هر booking آسان‌تر است.
- مناسب برای dispute، refund، cancellation و reconciliation.

---

## گزینه ۳: ترکیبی

برای بیشتر سیستم‌های واقعی، مدل ترکیبی بهتر است:

```text
Guest-level Receivable
Owner-level Payable
Metadata per booking
```

یعنی accountها را per-user نگه می‌داری، اما روی transactionها metadata می‌گذاری:

```json
{
  "bookingId": "booking_123",
  "guestId": "guest_x",
  "ownerId": "owner_y",
  "propertyId": "property_555",
  "transactionType": "booking"
}
```

---

# ۱۸) Metadata پیشنهادی برای Marketplace

## Ledger Account Metadata

برای Guest Receivable:

```json
{
  "accountType": "guest_receivable",
  "guestId": "guest_x"
}
```

برای Owner Payable:

```json
{
  "accountType": "owner_payable",
  "ownerId": "owner_y"
}
```

---

## Ledger Transaction Metadata

برای Booking:

```json
{
  "transactionType": "booking",
  "bookingId": "booking_123",
  "guestId": "guest_x",
  "ownerId": "owner_y",
  "propertyId": "property_555"
}
```

برای Guest Payment:

```json
{
  "transactionType": "guest_payment",
  "bookingId": "booking_123",
  "paymentId": "payment_abc"
}
```

برای Owner Payout:

```json
{
  "transactionType": "owner_payout",
  "bookingId": "booking_123",
  "payoutId": "payout_xyz"
}
```

---

# ۱۹) نکته برای سیستم کیف پول/کریپتو

برای کاری که شما در حوزه core wallet انجام می‌دهید، این Marketplace pattern خیلی نزدیک است به حالتی که:

- یک طرف payer است.
- یک طرف seller/provider است.
- پلتفرم fee برمی‌دارد.
- تسویه ممکن است با تأخیر انجام شود.
- available balance و posted balance مهم هستند.

در crypto marketplace هم مشابه همین مدل را می‌توان داشت:

```text
Buyer/Guest Receivable
Seller/Owner Payable
Platform Revenue
Custody/Hot Wallet Cash
```

البته در crypto به‌جای USD، ممکن است currencyهایی مثل:

```text
BTC
ETH
USDT
USDC
```

داشته باشید و برای هر asset باید balanced بودن جداگانه رعایت شود.

---

# ۲۰) جمع‌بندی

سناریوی Marketplace با Rarebnb سه رویداد اصلی دارد:

## ۱. Booking
Guest بدهکار می‌شود، Owner طلبکار می‌شود، Revenue ثبت می‌شود.

```text
Debit  Guest Receivable  200
Credit Revenue           10
Credit Owner Payable     190
```

## ۲. Guest Payment
Cash زیاد می‌شود، طلب از Guest صفر می‌شود.

```text
Debit  Cash              200
Credit Guest Receivable  200
```

## ۳. Owner Payout
بدهی به Owner صفر می‌شود، Cash کم می‌شود.

```text
Debit  Owner Payable     190
Credit Cash              190
```

## نتیجه نهایی

```text
Guest Receivable = 0
Owner Payable    = 0
Cash             = 10
Revenue          = 10
```

یعنی Rarebnb بعد از کل جریان مالی، فقط کمیسیون خودش را نگه داشته است.

----
عالی — این یعنی **فایل `image.png` فقط دیاگرام روابط Ledger را دارد** و بخشی که الان فرستادی درباره **Step 4: Read the Ledger Account Balances** در آن فایل نیست.  
پس من این بخش جدید را بر اساس **متنی که همین الان در پیام دادی** توضیح می‌دهم، نه بر اساس `image.png`.

---

# Step 4 — Read the Ledger Account Balances
## خواندن موجودی Ledger Accountها در سناریوی Marketplace

تا اینجا Rarebnb این کارها را انجام داده بود:

1. Booking را ثبت کرده
2. Payment را گرفته
3. Payout را انجام داده یا در جریان انجام دارد

حالا سؤال مهم این است:

> در هر لحظه چطور بفهمیم یک Guest چقدر بدهکار است یا یک Owner چقدر طلبکار است؟

جواب: با خواندن **Ledger Account balances**.

---

# ۱) ایده اصلی

چون همه فعالیت‌های مالی همان لحظه داخل Ledger ثبت می‌شوند، سیستم هر زمان بخواهد می‌تواند موجودی لحظه‌ای حساب‌ها را بگیرد.

مثلاً:

- اگر بخواهیم در UI اپ نشان بدهیم **Guest X الان چقدر بدهکار است**
- یا بخواهیم نشان بدهیم **Owner Y الان چقدر باید دریافت کند**

می‌توانیم مستقیم `Ledger Account` مربوطه را بخوانیم.

---

# ۲) API خواندن موجودی حساب

برای این کار از endpoint زیر استفاده می‌شود:

```bash
GET /ledger_accounts/<ledger_account_id>
```

مثال:

```bash
curl --request GET \
  -u ORGANIZATION_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_accounts/<guest_x_account_id>
```

این API خود آبجکت Ledger Account را برمی‌گرداند و داخل آن فیلدی به نام `balances` دارد.

---

# ۳) سه نوع موجودی مهم

در پاسخ API، معمولاً این سه نوع balance را می‌بینیم:

- `pending_balance`
- `posted_balance`
- `available_balance`

هر کدام معنی متفاوتی دارند.

---

## ۳.۱) pending_balance

این موجودی، اثر تراکنش‌های **pending** را هم حساب می‌کند.

یعنی:

- اگر تراکنشی ثبت شده ولی هنوز نهایی نشده باشد
- این balance آن را نشان می‌دهد

برای UIهای عملیاتی خیلی مهم است، چون می‌خواهی بدانی **اگر همه چیز طبق انتظار پیش برود، وضعیت حساب چیست**.

---

## ۳.۲) posted_balance

این موجودی فقط تراکنش‌های **posted** را در نظر می‌گیرد.

یعنی فقط رویدادهای نهایی‌شده و قطعی حساب می‌شوند.

این balance برای:

- حسابداری رسمی
- گزارش‌گیری
- reconciliation

خیلی مهم است.

---

## ۳.۳) available_balance

این موجودی یعنی **الان واقعاً چقدر قابل استفاده / قابل خرج / قابل برداشت است**.

بسته به طراحی سیستم، ممکن است با `posted_balance` برابر باشد یا نباشد.

در بسیاری از سناریوها:

- اگر محدودیت خاصی نداشته باشیم
- و lock/hold نداشته باشیم

ممکن است `available_balance = posted_balance` باشد.

---

# ۴) مثال اول: Guest X Receivable بعد از Booking

فرض کن Guest X یک booking ثبت کرده ولی این booking هنوز `pending` است.

در این حالت، API برای حساب Guest X Receivable چیزی شبیه این برمی‌گرداند:

```json
{
  "id":"<guest_x_account_id>",
  "object":"ledger_account",
  "name":"Employer X Receivable",
  "description": "Tracks fees to be collected from Guest X for a stay",
  "ledger_id": "<ledger_id>",
  "normal_balance":"debit",
  "balances":{
    "pending_balance":{
      "credits":0,
      "debits":20000,
      "amount":20000,
      "currency":"USD",
      "currency_exponent":2
    },
    "posted_balance":{
      "credits":0,
      "debits":0,
      "amount":0,
      "currency":"USD",
      "currency_exponent":2
    },
    "available_balance":{
      "credits":0,
      "debits":0,
      "amount":0,
      "currency":"USD",
      "currency_exponent":2
    }
  }
}
```

---

# ۵) تفسیر این پاسخ

## Guest X Receivable از نوع debit-normal است

یعنی افزایش طلب با **debit** اتفاق می‌افتد.

در پاسخ بالا می‌بینیم:

```json
"pending_balance": {
  "credits": 0,
  "debits": 20000,
  "amount": 20000
}
```

یعنی:

- یک debit به مبلغ ۲۰۰ دلار ثبت شده
- چون حساب Receivable از نوع debit-normal است، این debit باعث افزایش مانده شده
- پس Guest X فعلاً **۲۰۰ دلار بدهکار است**

---

## چرا posted_balance هنوز صفر است؟

چون Booking هنوز `pending` است، نه `posted`.

پس:

```json
"posted_balance": {
  "amount": 0
}
```

یعنی از نظر نهایی‌شده هنوز هیچ طلب قطعی ثبت نشده است.

---

## چرا available_balance هم صفر است؟

در این مثال، چون booking هنوز pending است، سیستم هنوز آن را به عنوان مبلغ نهایی و قابل اتکا برای available در نظر نگرفته.

پس:

```json
"available_balance": {
  "amount": 0
}
```

---

# ۶) نتیجه تجاری این مثال

اگر Rarebnb بخواهد بعد از ثبت Booking در UI نشان دهد:

> Guest X currently owes $200

می‌تواند از `pending_balance.amount` استفاده کند.

یعنی در این لحظه:

- **pending due = 200 USD**
- **posted due = 0 USD**

---

# ۷) مثال دوم: Owner Y Payable بعد از posted شدن Booking

حالا فرض کنیم booking مربوط به Guest X بعداً `posted` شده و Rarebnb می‌خواهد نشان دهد که به Owner Y چقدر بدهکار است.

پاسخ API برای `Owner Y Payable` چیزی شبیه این است:

```json
{
  "id":"<owner_y_account_id>",
  "object":"ledger_account",
  "name":"Owner Y Payable",
  "description": "Tracks amount owed to Owner Y for hosting a guest",
  "ledger_id": "<ledger_id>",
  "normal_balance":"credit",
  "balances":{
    "pending_balance":{
      "credits":19000,
      "debits":0,
      "amount":19000,
      "currency":"USD",
      "currency_exponent":2
    },
    "posted_balance":{
      "credits":19000,
      "debits":0,
      "amount":19000,
      "currency":"USD",
      "currency_exponent":2
    },
    "available_balance":{
      "credits":19000,
      "debits":0,
      "amount":19000,
      "currency":"USD",
      "currency_exponent":2
    }
  }
}
```

---

# ۸) تفسیر این پاسخ

## Owner Y Payable از نوع credit-normal است

یعنی افزایش بدهی به Owner با **credit** اتفاق می‌افتد.

در پاسخ بالا:

```json
"credits": 19000,
"debits": 0,
"amount": 19000
```

یعنی Rarebnb به Owner Y مبلغ ۱۹۰ دلار بدهکار است.

---

## چرا هر سه balance برابرند؟

چون در این مرحله booking به `posted` تبدیل شده است.

پس اثر آن هم:

- در `pending_balance`
- هم در `posted_balance`
- و هم در `available_balance`

دیده می‌شود.

یعنی این بدهی حالا **نهایی و قابل پرداخت** است.

---

# ۹) تفاوت Guest Receivable و Owner Payable در خواندن balance

این دو مثال خیلی خوب تفاوت debit-normal و credit-normal را نشان می‌دهند.

---

## Guest Receivable

```text
normal_balance = debit
```

افزایش مانده با debit:

```text
debits 20000 → amount 20000
```

---

## Owner Payable

```text
normal_balance = credit
```

افزایش مانده با credit:

```text
credits 19000 → amount 19000
```

---

# ۱۰) یک قانون ذهنی مهم

برای خواندن `amount` همیشه باید ماهیت حساب را بفهمی.

## اگر حساب debit-normal باشد:
مثل:
- Cash
- Receivable
- Expense

آنگاه:

```text
amount = debits - credits
```

## اگر حساب credit-normal باشد:
مثل:
- Payable
- Revenue
- User Wallet liability

آنگاه:

```text
amount = credits - debits
```

به همین دلیل است که در هر دو مثال بالا `amount` مثبت است، با اینکه یکی debit دارد و دیگری credit.

---

# ۱۱) برای UI از کدام balance استفاده کنیم؟

این بستگی به use case دارد.

---

## نمایش «در حال انتظار»
اگر بخواهی وضعیت در جریان را نشان دهی:

- booking created
- payment initiated
- payout initiated

معمولاً از:

```text
pending_balance
```

استفاده می‌کنی.

---

## نمایش «قطعی»
اگر بخواهی فقط وضعیت نهایی و settle شده را نشان دهی:

```text
posted_balance
```

مناسب‌تر است.

---

## نمایش «قابل خرج / قابل برداشت»
اگر بخواهی تصمیم عملیاتی بگیری:

- آیا کاربر می‌تواند برداشت کند؟
- آیا می‌توان payout انجام داد؟
- آیا موجودی کافی هست؟

معمولاً:

```text
available_balance
```

را می‌خوانی.

---

# ۱۲) اتصال این مفهوم به Core Wallet

برای سیستم wallet شما این بخش خیلی مهم است، چون عملاً همان چیزی است که در product استفاده می‌شود.

مثلاً:

## در کیف پول کاربر
- `pending_balance`: تراکنش‌های در حال پردازش
- `posted_balance`: تراکنش‌های قطعی
- `available_balance`: مبلغ قابل برداشت

---

## در حساب settlement
- `posted_balance`: مبلغ settled
- `available_balance`: مبلغ قابل استفاده برای payout

---

## در liability account
- `posted_balance`: بدهی قطعی شما به کاربر
- `available_balance`: بدهی قابل خرج/انتقال

---

# ۱۳) Advanced Topics — Metadata

بعد از این، مستندات وارد بحث‌های پیشرفته‌تر می‌شود.

یکی از مهم‌ترین آن‌ها **Metadata** است.

Modern Treasury اجازه می‌دهد روی بیشتر objectها metadata کلید-مقدار بگذاری.

---

## نمونه metadata برای Marketplace

### روی Ledger

```json
{
  "productID": "rarebnb"
}
```

### روی Ledger Account

```json
{
  "accountType": "guest_receivable",
  "guestName": "Guest X",
  "guestId": "guest_123"
}
```

یا برای Owner:

```json
{
  "accountType": "owner_payable",
  "ownerName": "Owner Y",
  "ownerId": "owner_456"
}
```

### روی Ledger Transaction

```json
{
  "transactionType": "booking",
  "guestId": "guest_123",
  "ownerId": "owner_456",
  "bookingId": "booking_789"
}
```

---

## چرا metadata مهم است؟

چون در سیستم واقعی فقط balance کافی نیست.  
تو باید بتوانی بعداً بپرسی:

- همه تراکنش‌های مربوط به یک booking کدام‌اند؟
- همه تراکنش‌های یک guest چیست؟
- همه payoutهای یک owner کدام‌اند؟

با metadata، این queryها ساده می‌شوند.

---

# ۱۴) Advanced Topics — Ledger Account Categories

گاهی لازم نیست فقط موجودی یک account را بخوانی.  
گاهی لازم است مجموع چند account مشابه را بگیری.

مثلاً Rarebnb می‌خواهد بداند:

- مجموع تمام Guest Receivableها چقدر است؟
- مجموع تمام Owner Payableها چقدر است؟

برای این کار از **Ledger Account Categories** استفاده می‌شود.

---

## مثال‌ها

### Total Receivables

یک category از نوع `debit` که مجموع تمام حساب‌های Receivable را نگه می‌دارد:

- Guest X Receivable
- Guest Y Receivable
- Guest Z Receivable
- ...

این عدد نشان می‌دهد Rarebnb در مجموع چقدر باید از Guestها پول بگیرد.

---

### Total Payables

یک category از نوع `credit` که مجموع تمام حساب‌های Owner Payable را نگه می‌دارد:

- Owner A Payable
- Owner B Payable
- Owner C Payable
- ...

این عدد نشان می‌دهد Rarebnb در مجموع چقدر به Ownerها بدهکار است.

---

# ۱۵) چرا Ledger Account Categories مهم‌اند؟

برای چند دلیل:

## ۱. Reporting
مثلاً CFO می‌خواهد بداند:

- total liabilities to owners
- total receivables from guests

## ۲. Dashboard
در admin panel می‌خواهی aggregate balances را سریع نشان بدهی.

## ۳. Real-time monitoring
اگر total payables ناگهان بالا برود، شاید cash planning لازم باشد.

## ۴. Reconciliation
می‌توانی category-level balance را با سیستم‌های دیگر مقایسه کنی.

---

# ۱۶) خلاصه خیلی کوتاه

## Step 4 چه می‌گوید؟
با `GET /ledger_accounts/<id>` می‌توانی مانده لحظه‌ای هر حساب را بخوانی.

## چه balanceهایی مهم‌اند؟
- `pending_balance`
- `posted_balance`
- `available_balance`

## مثال Guest X
Booking هنوز pending است، پس فقط `pending_balance` عدد ۲۰۰ دلار را نشان می‌دهد.

## مثال Owner Y
Booking posted شده، پس هر سه balance عدد ۱۹۰ دلار را نشان می‌دهند.

## Advanced Topics بعدی
- Metadata برای جستجو و traceability
- Ledger Account Categories برای roll-up و aggregate balances

---

اگر بخواهی، در پیام بعدی می‌توانم همین بخش را هم به یکی از این دو شکل ادامه بدهم:

1. **به صورت جزوه آموزشی تمیز و یکدست از ابتدا تا انتهای Marketplace**
2. **به صورت mapping مستقیم برای پیاده‌سازی در Core Wallet با Java/Spring Boot domain model**