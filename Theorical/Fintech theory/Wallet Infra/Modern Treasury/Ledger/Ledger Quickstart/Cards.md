این مستند مربوط به نحوه پیاده‌سازی سیستم صدور کارت (Card Issuing) با استفاده از Ledger API در Modern Treasury است. برای درک بهتر و کاربردی، مفاهیم را در قالب یک جزوه ساختاریافته به همراه مفاهیم دفترداری دوطرفه (Double-Entry Ledger) و با استفاده از فرمول‌های ریاضی و کدهای شفاف بررسی می‌کنیم.

---

# جزوه آموزشی: طراحی و پیاده‌سازی سیستم Ledger برای صدور کارت

هدف این راهنما، طراحی و پیاده‌سازی یک هسته مالی (Ledger) برای پشتیبانی از تراکنش‌های کارت (شامل سناریوهای Debit و Credit) با استفاده از مفاهیم حسابداری دوطرفه و کنترل سقف اعتبار (Credit Limit) است.

---

## بخش ۱: مفاهیم پایه‌ای دفترداری دوطرفه (Double-Entry Ledger)

در دفترداری دوطرفه، هر رویداد مالی (Transaction) باید حداقل شامل دو ثبت دفتری (Entry) باشد: **بدهکار (Debit)** و **بستانکار (Credit)**. 
معادله بنیادین حسابداری که همواره باید برقرار باشد:

$$\sum \text{Debits} = \sum \text{Credits}$$

### ماهیت حساب‌ها (Normality)
حساب‌ها بر اساس ماهیت به دو دسته کلی تقسیم می‌شوند:
1. **ماهیت بدهکار (Debit Normal):** افزایش در این حساب‌ها با ثبت **Debit** و کاهش آن‌ها با ثبت **Credit** انجام می‌شود (مانند دارایی‌ها و مطالبات).
2. **ماهیت بستانکار (Credit Normal):** افزایش در این حساب‌ها با ثبت **Credit** و کاهش آن‌ها با ثبت **Debit** انجام می‌شود (مانند بدهی‌ها، موجودی کیف‌پول کاربران و درآمد).

---

## بخش ۲: طراحی حساب‌ها (Chart of Accounts)

برای سیستم کارت، حساب‌های زیر را در Ledger تعریف می‌کنیم:

| نام حساب در لجر (Ledger Account) | ماهیت (Normality) | کاربرد |
| :--- | :--- | :--- |
| **Bank Balance (موجودی بانک)** | بدهکار (Debit) | ردیابی کل پول نقد موجود در حساب بانکی واسط پلتفرم |
| **User Receivable Balance (مطالبات از کاربر)** | بدهکار (Debit) | ردیابی بدهی کاربر به پلتفرم (مخصوص سناریوی **Credit Card**) |
| **User Wallet / Card Account (کیف‌پول کاربر)** | بستانکار (Credit) | ردیابی موجودی پیش‌پرداخت‌شده کاربر (مخصوص سناریوی **Debit Card**) |
| **Processor/Merchant Payable (بدهی به پردازشگر/پذیرنده)** | بستانکار (Credit) | ردیابی مبالغی که باید به شبکه کارت/پذیرنده پرداخت شود |
| **Revenue (درآمد)** | بستانکار (Credit) | ثبت کارمزدها و درآمدهای حاصل از تراکنش‌ها |

---

## بخش ۳: سناریوهای تراکنش (Transaction Flows)

### الف) سناریوی کارت‌های نقدی (Debit Card / Wallet)
در این حالت، کاربر ابتدا حساب خود را شارژ می‌کند (Prefund) و سپس خرج می‌کند.

#### ۱. شارژ کیف‌پول توسط کاربر (User Funding)
کاربر $100$ دلار به حساب پلتفرم واریز می‌کند.
* **بدهکار (Debit):** حساب بانکی پلتفرم (Bank Balance) $\leftarrow$ $100$ دلار (افزایش دارایی پلتفرم)
* **بستانکار (Credit):** حساب کیف‌پول کاربر (User Wallet) $\leftarrow$ $100$ دلار (افزایش بدهی پلتفرم به کاربر)

#### ۲. خرید با کارت (User Card Swipe)
کاربر $100$ دلار در یک فروشگاه خرید می‌کند.
* **بدهکار (Debit):** حساب کیف‌پول کاربر (User Wallet) $\leftarrow$ $100$ دلار (کاهش بدهی پلتفرم به کاربر)
* **بستانکار (Credit):** حساب بدهی به شبکه پرداخت (Processor Payable) $\leftarrow$ $100$ دلار (ایجاد بدهی پلتفرم به شبکه پرداخت)

---

### ب) سناریوی کارت‌های اعتباری (Credit Card)
در این حالت، پلتفرم به کاربر اعتبار می‌دهد و کاربر بعداً تسویه می‌کند. فرض کنید کاربر یک خرید $100$ دلاری انجام می‌دهد و پلتفرم $0.30$ دلار (۳۰ سنت) کارمزد دریافت می‌کند. در نتیجه سهم پردازشگر $99.70$ دلار خواهد بود.

#### ۱. خرید با کارت اعتباری (Card Swipe & Revenue Capture)
* **بدهکار (Debit):** حساب مطالبات از کاربر (User Receivable) $\leftarrow$ $100.00$ دلار (افزایش طلب پلتفرم از کاربر)
* **بستانکار (Credit):** حساب بدهی به پردازشگر (Processor Payable) $\leftarrow$ $99.70$ دلار (بدهی پلتفرم به پردازشگر)
* **بستانکار (Credit):** حساب درآمد (Revenue) $\leftarrow$ $0.30$ دلار (درآمد پلتفرم از کارمزد)

#### ۲. بازپرداخت بدهی توسط کاربر (User Repayment)
کاربر $100$ دلار بدهی خود را به پلتفرم پرداخت می‌کند.
* **بدهکار (Debit):** حساب بانکی پلتفرم (Bank Balance) $\leftarrow$ $100.00$ دلار (افزایش پول نقد پلتفرم)
* **بستانکار (Credit):** حساب مطالبات از کاربر (User Receivable) $\leftarrow$ $100.00$ دلار (کاهش طلب پلتفرم از کاربر)

#### ۳. تسویه حساب با پردازشگر (Processor Settlement)
پلتفرم بدهی خود را با شبکه پرداخت تسویه می‌کند (پول از حساب بانکی کسر می‌شود).
* **بدهکار (Debit):** حساب بدهی به پردازشگر (Processor Payable) $\leftarrow$ $99.70$ دلار (کاهش بدهی پلتفرم)
* **بستانکار (Credit):** حساب بانکی پلتفرم (Bank Balance) $\leftarrow$ $99.70$ دلار (کاهش پول نقد پلتفرم)

---

## بخش ۴: پیاده‌سازی با API (مثال‌های عملی)

### ۱. تعریف Ledger و حساب‌ها (Accounts)
ابتدا یک Ledger برای برنامه کارت ایجاد می‌کنیم:

```bash
curl --request POST \
  -u ORG_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledgers \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Card Ledger",
    "description": "دفتر کل برنامه صدور کارت"
  }'
```

سپس حساب‌های مورد نظر را با مشخص کردن ماهیت (`normal_balance`) می‌سازیم:

```bash
# ساخت حساب مطالبات از کاربر (بدهکار)
curl --request POST \
  -u ORG_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_accounts \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Customer Receivable",
    "normal_balance": "debit",
    "currency": "USD",
    "ledger_id": "<ledger_id>"
  }'
```

### ۲. ثبت تراکنش خرید اعتباری (Swipe Transaction)
ثبت خرید $100$ دلاری با کسر $0.30$ کارمزد (مبالغ در API به کوچک‌ترین واحد پولی یعنی سنت ارسال می‌شوند، بنابراین $100$ دلار معادل `10000` سنت است):

```bash
curl --request POST \
  -u ORG_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "خرید کاربر با کارت اعتباری",
    "status": "posted",
    "ledger_entries": [
      {
        "amount": 10000,
        "direction": "debit",
        "ledger_account_id": "<user_receivable_id>"
      },
      {
        "amount": 9970,
        "direction": "credit",
        "ledger_account_id": "<processor_payable_id>"
      },
      {
        "amount": 30,
        "direction": "credit",
        "ledger_account_id": "<revenue_account_id>"
      }
    ]
  }'
```

---

## بخش ۵: کنترل سقف اعتبار (Credit Limit) و قفل تعادل (Balance Locking)

یکی از چالش‌های اصلی کارت‌های اعتباری، جلوگیری از تجاوز خرید کاربر از سقف اعتبار او است.

### مکانیزم Balance Locking
در Modern Treasury می‌توانید شرطی تعریف کنید که تراز حساب پس از اعمال تراکنش، از حد خاصی تجاوز نکند. اگر تراکنش پیشنهادی باعث شود موجودی حساب شرط را نقض کند، سیستم خطای `422 Unprocessable Entity` برمی‌گرداند و تراکنش ثبت نمی‌شود.

#### مثال سناریو:
* سقف اعتبار کاربر: $2,000$ دلار (`200000` سنت).
* بدهی فعلی کاربر (User Receivable): $1,000$ دلار.
* کاربر تلاش می‌کند خریدی به مبلغ $1,500$ دلار (`150000` سنت) انجام دهد.
* تراز جدید در صورت تایید تراکنش: $1,000 + 1,500 = 2,500$ دلار خواهد شد که بیشتر از سقف مجاز ($2,000$) است.

در API با استفاده از پارامتر `posted_balance_amount` و فیلتر `lte` (کمتر یا مساوی) این محدودیت را اعمال می‌کنیم:

```bash
curl --request POST \
  -u ORG_ID:API_KEY \
  --url https://app.moderntreasury.com/api/ledger_transactions \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "خرید با کنترل سقف اعتبار",
    "status": "posted",
    "ledger_entries": [
      {
        "amount": 150000,
        "direction": "debit",
        "ledger_account_id": "<user_receivable_id>",
        "posted_balance_amount": {"lte": 200000}
      },
      {
        "amount": 149970,
        "direction": "credit",
        "ledger_account_id": "<processor_payable_id>"
      },
      {
        "amount": 30,
        "direction": "credit",
        "ledger_account_id": "<revenue_account_id>"
      }
    ]
  }'
```
**نتیجه این درخواست:** تراکنش با خطا رد می‌شود زیرا بدهی جدید ($2,500$) بزرگتر از شرط تعیین شده (`lte: 200000`) است.

---

## بخش ۶: نکات معماری و کارایی (Performance & Hot Accounts)

### مدیریت قفل روی حساب‌های پرترافیک (Hot Accounts)
یکی از مفاهیم حیاتی در طراحی سیستم‌های با تراکنش بالا (High Throughput)، مدیریت قفل‌های پایگاه داده است. 

* **حساب پرترافیک (Hot Account):** حسابی است که تقریباً در تمامی تراکنش‌ها درگیر است؛ مانند حساب **بدهی به پردازشگر (Processor Payable)** یا **حساب درآمد پلتفرم (Revenue)**.
* **قفل همزمانی (Concurrency Lock):** استفاده از ویژگی Balance Locking باعث می‌شود تراکنش‌ها روی آن حساب به صورت همگام (Synchronous) پردازش شده و عملاً نوعی صف ایجاد شود که گلوگاه کارایی پلتفرم خواهد بود.

**توصیه مهم:** 
شروط Balance Locking را **فقط** روی حساب‌هایی اعمال کنید که ترافیک پایینی دارند (مانند حساب اختصاصی هر کاربر - `user_receivable`). هرگز روی حساب‌های کلان سیستم مانند `processor_payable` فیلتر قفل تراز قرار ندهید تا پلتفرم دچار افت شدید کارایی نشود.