---
tags:
  - java
  - io
  - input-output
  - variable
  - array
  - enum
created: 2026-08-25
status: completed
Author: Parsa Eftekharmanesh
---

---
tags:
  - java
  - data-types
  - operators
  - variables
  - cheat-sheet
---

# انواع داده‌ها، عملگرها، متغیرها و کامنت‌ها در Java

> [!abstract] سرفصل‌های این بخش
> ۱. **نوع داده‌ها (Data Types)**: اولیه‌ها (Primitives)، مرجع‌ها (References) و تبدیل نوع (Casting)  
> ۲. **عملگرها (Operators)**: حسابی، مقایسه‌ای، منطقی، بیتی، شرطی و تقدم عملگرها  
> ۳. **متغیرها (Variables)**: انواع، طول عمر و حوزهٔ دسترسی (Scope)  
> ۴. **کامنت‌ها (Comments)**: تک‌خطی، چندخطی و مستندسازی (Javadoc)  
> ۵. **مفاهیم تکمیلی**: داده‌های متنی، تو‌در‌تو، مقدار `null` و عملگر `instanceof`

---

## ۱. نوع داده‌ها (Data Types)

جاوا یک زبان **Statically-Typed** و **Strongly-Typed** است؛ یعنی نوع هر متغیر باید در زمان کامپایل مشخص باشد.

```
                  ┌── byte (8-bit)
                  ├── short (16-bit)
        ┌── صحیح ──┼── int (32-bit)
        │         └── long (64-bit)
        │
┌── Primitive ────┼── اعشاری ──┬── float (32-bit)
│               │           └── double (64-bit)
│               │
│               ├── کاراکتری ── char (16-bit Unicode)
│               └── منطقی   ── boolean (true/false)
│
└── Reference ─── String, Arrays, Classes, Interfaces (اشاره به Object روی Heap)
```

---

### ۱.۱. نوع‌های دادهٔ اصلی (Primitive Data Types)

| نوع داده | اندازه (بیت) | محدودهٔ مقادیر | مقدار پیش‌فرض | مثال |
|---|---|---|---|---|
| `byte` | ۸ | $-128$ تا $127$ | `0` | `byte a = 100;` |
| `short` | ۱۶ | $-32,768$ تا $32,767$ | `0` | `short b = 10000;` |
| `int` | ۳۲ | $-2^{31}$ تا $2^{31}-1$ | `0` | `int c = 1000000;` |
| `long` | ۶۴ | $-2^{63}$ تا $2^{63}-1$ | `0L` | `long d = 10000000000L;` |
| `float` | ۳۲ | $\approx \pm 3.4 \times 10^{38}$ (دقت ۶ تا ۷ رقم) | `0.0f` | `float e = 5.75f;` |
| `double` | ۶۴ | $\approx \pm 1.7 \times 10^{308}$ (دقت ۱۵ رقم) | `0.0d` | `double f = 19.99;` |
| `char` | ۱۶ | `\u0000` ($0$) تا `\uffff` ($65,535$) | `\u0000` | `char g = 'A';` |
| `boolean` | — | `true` یا `false` | `false` | `boolean h = true;` |

#### مثال: تعریف و استفاده از Primitives
```java
public class PrimitiveDataTypesExample {
    public static void main(String[] args) {
        byte a = 100;
        short b = 10000;
        int c = 1000000;
        long d = 10000000000L; // پسوند L برای long الزامی است
        float e = 5.75f;       // پسوند f برای float الزامی است
        double f = 19.99;
        char g = 'A';
        boolean h = true;

        System.out.println("byte: " + a);
        System.out.println("short: " + b);
        System.out.println("int: " + c);
        System.out.println("long: " + d);
        System.out.println("float: " + e);
        System.out.println("double: " + f);
        System.out.println("char: " + g);
        System.out.println("boolean: " + h);
    }
}
```

---

### ۱.۲. نوع‌های دادهٔ مرجع (Reference Data Types)

این نوع‌ها به آدرس شیء در حافظه (Heap) اشاره می‌کنند. شامل **کلاس‌ها**، **آرایه‌ها**، **رشته‌ها (`String`)** و **اینترفیس‌ها** هستند.

```java
public class ReferenceDataTypesExample {
    public static void main(String[] args) {
        String str = "Hello, Java!";
        int[] numbers = {1, 2, 3, 4, 5};
        String[] fruits = {"Apple", "Banana", "Cherry"};
        Car car = new Car(12, 2, 4);

        System.out.println("String: " + str);
        System.out.println("Car Price: " + car.getPrice());
        System.out.println("First number: " + numbers[0]);
        System.out.println("First fruit: " + fruits[0]);
    }
}
```

---

### ۱.۳. تبدیل نوع داده‌ها (Type Conversion)

#### ۱. تبدیل ضمنی / گسترش (Implicit / Widening)
بدون از دست رفتن داده به‌صورت خودکار توسط کامپایلر انجام می‌شود:
$$\text{byte} \to \text{short} \to \text{char} \to \text{int} \to \text{long} \to \text{float} \to \text{double}$$

#### ۲. تبدیل صریح / باریک‌سازی (Explicit / Narrowing)
نیاز به **Cast** دستی دارد و ممکن است باعث از دست رفتن داده (Truncation) یا سرریز (Overflow) شود:
$$\text{double} \to \text{float} \to \text{long} \to \text{int} \to \text{char} \to \text{short} \to \text{byte}$$

```java
public class TypeConversionExample {
    public static void main(String[] args) {
        // تبدیل ضمنی (Widening)
        int intValue = 100;
        double doubleValue = intValue; 

        // تبدیل صریح (Narrowing)
        double anotherDoubleValue = 9.78;
        int intValueFromDouble = (int) anotherDoubleValue; // بخش اعشاری حذف می‌شود (نتیجه: 9)

        System.out.println("Double value: " + doubleValue);
        System.out.println("Int value from double: " + intValueFromDouble);
    }
}
```

---

## ۲. عملگرها (Operators)

### ۲.۱. عملگرهای حسابی (Arithmetic)
برای محاسبات ریاضی پایه: `+` , `-` , `*` , `/` , `%`

> [!warning] تقسیم عدد صحیح
> تقسیم دو عدد صحیح (مانند `5 / 2`) برابر با `2` است و بخش اعشار حذف می‌شود. برای نتیجهٔ اعشاری باید حداقل یکی از عملوندها اعشاری باشد: `5.0 / 2 = 2.5`.

```java
public class ArithmeticOperatorsExample {
    public static void main(String[] args) {
        int a = 10, b = 5;
        System.out.println("a + b = " + (a + b)); // 15
        System.out.println("a - b = " + (a - b)); // 5
        System.out.println("a * b = " + (a * b)); // 50
        System.out.println("a / b = " + (a / b)); // 2
        System.out.println("a % b = " + (a % b)); // 0 (باقی‌مانده)
    }
}
```

---

### ۲.۲. عملگرهای مقایسه‌ای (Relational)
خروجی این عملگرها همیشه `boolean` است: `==` , `!=` , `>` , `<` , `>=` , `<=`

```java
public class RelationalOperatorsExample {
    public static void main(String[] args) {
        int a = 10, b = 20;
        System.out.println("a == b: " + (a == b)); // false
        System.out.println("a != b: " + (a != b)); // true
        System.out.println("a > b:  " + (a > b));  // false
        System.out.println("a < b:  " + (a < b));  // true
        System.out.println("a >= b: " + (a >= b)); // false
        System.out.println("a <= b: " + (a <= b)); // true
    }
}
```

---

### ۲.۳. عملگرهای منطقی و ارزیابی کوتاه‌مدت (Logical & Short-Circuit)

- `&&` : AND منطقی (و)
- `||` : OR منطقی (یا)
- `!` : NOT منطقی (نقیض)

> [!important] قاعدهٔ Short-Circuit Evaluation
> - در `condition1 && condition2` اگر شرط اول `false` باشد، **شرط دوم ارزیابی نمی‌شود**.
> - در `condition1 || condition2` اگر شرط اول `true` باشد، **شرط دوم ارزیابی نمی‌شود**.

```java
public class ShortCircuitEvaluation {
    public static void main(String[] args) {
        int a = 5, b = 10;

        // ارزیابی کوتاه‌مدت && (بخش دوم اجرا نمی‌شود، b تغییر نمی‌کند)
        boolean result1 = (a > 10) && (b++ > 10);
        System.out.println("Result1: " + result1); // false
        System.out.println("b: " + b);             // 10

        // ارزیابی کوتاه‌مدت || (بخش دوم اجرا نمی‌شود)
        boolean result2 = (a < 10) || (b++ > 10);
        System.out.println("Result2: " + result2); // true
        System.out.println("b: " + b);             // 10
    }
}
```

---

### ۲.۴. عملگرهای تخصیص و ترکیبی (Assignment & Compound)

```java
public class CompoundOperatorsExample {
    public static void main(String[] args) {
        int x = 10;
        x += 5; // x = x + 5  -> 15
        x -= 3; // x = x - 3  -> 12
        x *= 2; // x = x * 2  -> 24
        x /= 4; // x = x / 4  -> 6
        x %= 4; // x = x % 4  -> 2
        System.out.println("Final x: " + x);
    }
}
```

---

### ۲.۵. عملگرهای بیتی (Bitwise)

این عملگرها مستقیماً روی بیت‌های اعداد صحیح کار می‌کنند:

| عملگر | نام | عملکرد | مثال (`a=5: 0101`, `b=3: 0011`) |
|---|---|---|---|
| `&` | AND بیتی | هر دو بیت ۱ باشند $\to$ ۱ | `a & b = 1` (`0001`) |
| `\|` | OR بیتی | حداقل یک بیت ۱ باشد $\to$ ۱ | `a \| b = 7` (`0111`) |
| `^` | XOR بیتی | بیت‌ها متفاوت باشند $\to$ ۱ | `a ^ b = 6` (`0110`) |
| `~` | NOT بیتی (مکمل یک) | معکوس کردن تمام بیت‌ها | `~a = -6` |
| `<<` | شیفت چپ | شیفت به چپ و درج ۰ از راست ($a \times 2^k$) | `5 << 1 = 10` |
| `>>` | شیفت راست حسابی | شیفت به راست با حفظ بیت علامت | `5 >> 1 = 2` |
| `>>>` | شیفت راست منطقی | شیفت به راست و درج ۰ از چپ (بدون علامت) | `5 >>> 1 = 2` |

```java
public class BitwiseOperatorsExample {
    public static void main(String[] args) {
        int a = 5; // 00000101
        int b = 3; // 00000011

        System.out.println("a & b:   " + (a & b));   // 1
        System.out.println("a | b:   " + (a | b));   // 7
        System.out.println("a ^ b:   " + (a ^ b));   // 6
        System.out.println("~a:      " + (~a));      // -6
        System.out.println("a << 1:  " + (a << 1));  // 10
        System.out.println("a >> 1:  " + (a >> 1));  // 2
        System.out.println("a >>> 1: " + (a >>> 1)); // 2
    }
}
```

---

### ۲.۶. عملگر شرطی سه‌تایی (Ternary Operator `? :`)

جایگزین فشرده برای `if-else`:
$$\text{condition} \ ? \ \text{expression\_if\_true} \ : \ \text{expression\_if\_false}$$

```java
public class ConditionalOperatorExample {
    public static void main(String[] args) {
        int a = 10, b = 20;
        int max = (a > b) ? a : b;
        System.out.println("Maximum value: " + max); // 20
    }
}
```

---

### ۲.۷. عملگرهای افزایش و کاهش (`++` / `--`)

- **پیش‌افزایش (`++a`) / پیش‌کاهش (`--a`)**: ابتدا مقدار متغیر تغییر کرده، سپس در عبارت استفاده می‌شود.
- **پس‌افزایش (`a++`) / پس‌کاهش (`a--`)**: ابتدا مقدار فعلی در عبارت استفاده شده، سپس متغیر تغییر می‌کند.

```java
public class IncrementDecrementOperatorsExample {
    public static void main(String[] args) {
        int a = 5;
        System.out.println("a++: " + (a++)); // چاپ 5 ، سپس a = 6 می‌شود
        System.out.println("++a: " + (++a)); // a = 7 می‌شود، سپس چاپ 7
        System.out.println("a--: " + (a--)); // چاپ 7 ، سپس a = 6 می‌شود
        System.out.println("--a: " + (--a)); // a = 5 می‌شود، سپس چاپ 5
    }
}
```

---

### ۲.۸. تقدم عملگرها (Operator Precedence)

ترتیب اولویت اجرای عملگرها از بالا به پایین:

| اولویت | دسته | عملگرها | جهت ارزیابی |
|---|---|---|---|
| ۱ (بالاترین) | پسوند / پرانتز | `()`, `[]`, `.`, `expr++`, `expr--` | چپ به راست |
| ۲ | یگانی (Unary) | `++expr`, `--expr`, `+`, `-`, `!`, `~`, `(type)` | راست به چپ |
| ۳ | ضربی | `*`, `/`, `%` | چپ به راست |
| ۴ | جمعی | `+`, `-` | چپ به راست |
| ۵ | شیفت | `<<`, `>>`, `>>>` | چپ به راست |
| ۶ | رابطه‌ای | `<`, `>`, `<=`, `>=`, `instanceof` | چپ به راست |
| ۷ | برابری | `==`, `!=` | چپ به راست |
| ۸ | بیتی | `&` سپس `^` سپس `\|` | چپ به راست |
| ۹ | منطقی | `&&` سپس `\|\|` | چپ به راست |
| ۱۰ | سه‌تایی | `? :` | راست به چپ |
| ۱۱ (پایین‌ترین) | تخصیص | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, ... | راست به چپ |

```java
public class OperatorPrecedenceExample {
    public static void main(String[] args) {
        int result1 = 10 + 5 * 2;   // ضرب تقدم بالاتری دارد -> 10 + 10 = 20
        int result2 = (10 + 5) * 2; // پرانتز تقدم را تغییر می‌دهد -> 15 * 2 = 30
        System.out.println("result1: " + result1);
        System.out.println("result2: " + result2);
    }
}
```

---

## ۳. متغیرها (Variables)

متغیر مکانی نام‌گذاری‌شده در حافظه برای نگهداری داده است.

### ۳.۱. انواع متغیرها بر اساس محل تعریف و طول عمر

```
┌── Local Variable ────── در متد یا بلوک ── طول عمر: تا پایان متد/بلوک ── مقدار پیش‌فرض: ندارد
├── Instance Variable ─── در سطح کلاس    ── طول عمر: با عمر شیء         ── مقدار پیش‌فرض: دارد
└── Static Variable ───── با کلمه static ── طول عمر: با عمر برنامه      ── مقدار پیش‌فرض: دارد (مشترک)
```

| ویژگی | متغیر محلی (Local) | متغیر نمونه (Instance) | متغیر کلاسی / استاتیک (Static) |
|---|---|---|---|
| **محل تعریف** | داخل متد، سازنده یا بلوک | داخل کلاس، خارج متدها | داخل کلاس با کلمه `static` |
| **محل در حافظه** | Stack | Heap (درون شیء) | Metaspace / Class Area |
| **مقدار پیش‌فرض** | **ندارد** (پیش از مصرف باید مقداردهی شود) | دارد (`0`, `null`, `false`) | دارد (`0`, `null`, `false`) |
| **دسترسی** | فقط داخل همان متد/بلوک | از طریق شیء (`obj.var`) | از طریق نام کلاس (`Class.var`) |
| **اشتراک** | مستقل در هر فراخوانی | برای هر نمونه مستقل است | بین **تمامی نمونه‌ها مشترک** است |

```java
public class VariableTypesExample {
    // 1. Static Variable: مشترک بین تمام نمونه‌ها
    static int staticVariable = 20;

    // 2. Instance Variable: برای هر شیء جداگانه ایجاد می‌شود
    int instanceVariable = 10;

    public static void main(String[] args) {
        // 3. Local Variable: فقط در متد main معتبر است
        int localVariable = 5;

        VariableTypesExample obj1 = new VariableTypesExample();
        VariableTypesExample obj2 = new VariableTypesExample();

        obj1.instanceVariable = 30;
        obj2.instanceVariable = 40;

        System.out.println("Local: " + localVariable);               // 5
        System.out.println("obj1 Instance: " + obj1.instanceVariable); // 30
        System.out.println("obj2 Instance: " + obj2.instanceVariable); // 40
        System.out.println("Static: " + VariableTypesExample.staticVariable); // 20
    }
}
```

---

## ۴. کامنت‌ها (Comments)

کامنت‌ها برای مستندسازی کد بوده و کامپایلر آن‌ها را پردازش نمی‌کند.

### ۴.۱. کامنت تک‌خطی (`//`)
```java
// این یک کامنت تک‌خطی است
int count = 0;
```

### ۴.۲. کامنت چندخطی (`/* ... */`)
```java
/*
 * این یک کامنت چندخطی است
 * مناسب برای توضیحات مفصل یا غیرفعال‌سازی موقت بخش‌هایی از کد.
 */
int total = 100;
```

### ۴.۳. کامنت‌های مستندسازی Javadoc (`/** ... */`)
برای تولید خودکار داکیومنت HTML استاندارد پروژه:

| تگ Javadoc | شرح کاربرد |
|---|---|
| `@param <name> <desc>` | توضیح آرگومان ورودی متد |
| `@return <desc>` | توضیح مقدار بازگشتی متد |
| `@throws <exception>` / `@exception` | توضیح خطاهایی که متد ممکن است پرتاب کند |
| `@see <reference>` | ارجاع به کلاس یا متد دیگر |
| `@deprecated <desc>` | نشانه گذاری متد/کلاس منسوخ‌شده |
| `@author <name>` | نام نویسنده کد |

```java
/**
 * این کلاس ابزارهای محاسباتی پایه را ارائه می‌دهد.
 * 
 * @author Computer Science Department
 * @version 1.0
 */
public class Calculator {

    /**
     * مقدار ورودی را در عدد ۲ ضرب می‌کند.
     *
     * @param number عدد صحیح ورودی
     * @return دو برابر عدد ورودی
     * @throws IllegalArgumentException در صورت منفی بودن ورودی
     */
    public int doubleValue(int number) {
        if (number < 0) {
            throw new IllegalArgumentException("عدد نمی‌تواند منفی باشد");
        }
        return number * 2;
    }
}
```

---

## ۵. انواع داده از منظرهای تکمیلی

### ۵.۱. داده‌های متنی (`String`)
کلاس `String` در جاوا یک نوع مرجع است اما به دلیل پرکاربرد بودن، رفتار ویژه‌ای دارد (Immutable بودن و بهره‌مندی از String Pool):

```java
public class StringExample {
    public static void main(String[] args) {
        String greeting = "Hello, World!";
        String name = "Java";
        String message = greeting + " Welcome to " + name + "!";
        System.out.println(message); // Hello, World! Welcome to Java!
    }
}
```

---

### ۵.۲. آرایه‌های چندبعدی / تو‌در‌تو (Nested Data Types)
جاوا آرایهٔ چندبعدی حقیقی ندارد، بلکه «آرایه‌ای از آرایه‌ها» را پیاده‌سازی می‌کند:

```java
public class NestedDataTypesExample {
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };

        System.out.println("Element at [1][2]: " + matrix[1][2]); // سطر 1، ستون 2 -> 6
    }
}
```

---

### ۵.۳. مقدار `null` و عملگر `instanceof`

> [!warning] خطای NullPointerException
> تلاش برای فراخوانی متد یا دسترسی به فیلد روی مرجعی که مقدار آن `null` است منجر به `NullPointerException` (NPE) می‌شود.

- **`null`**: مقدار پیش‌فرض متغیرهای مرجع که به هیچ شیئی اشاره نمی‌کنند.
- **`instanceof`**: بررسی اینکه آیا یک شیء نمونه‌ای از یک کلاس یا پیاده‌ساز یک اینترفیس است یا خیر (روی `null` مقدار `false` برمی‌گرداند).

```java
public class InstanceOfAndNullExample {
    public static void main(String[] args) {
        String str = null;

        if (str == null) {
            System.out.println("The string reference is null.");
        }

        String message = "Hello";
        boolean isString = message instanceof String;
        System.out.println("message instanceof String: " + isString); // true

        // بررسی روی null همیشه false است:
        System.out.println("null instanceof String: " + (str instanceof String)); // false
    }
}
```


  

---
tags:
  - java
  - oop
  - class
  - object
  - methods
  - inheritance
  - polymorphism
---

# آشنایی با Function و Classها در Java

> [!abstract] سرفصل‌ها
> - مبانی برنامه‌نویسی شیءگرا (OOP)
> - Class، Object، Field و Constructor
> - متدها، پارامترها، Overloading و Recursion
> - Encapsulation، Inheritance و Polymorphism
> - Abstract Class، Interface و Inner Class

---

## ۱. مقدمه‌ای بر برنامه‌نویسی شیءگرا (OOP)

**برنامه‌نویسی شیءگرا** روشی برای طراحی برنامه است که داده‌ها و رفتارهای مرتبط را در واحدهایی به نام **شیء (Object)** کنار هم قرار می‌دهد.

هر شیء معمولاً شامل دو بخش است:

- **State / وضعیت:** اطلاعات و ویژگی‌های شیء؛ مانند رنگ، مدل و قیمت خودرو.
- **Behavior / رفتار:** کارهایی که شیء انجام می‌دهد؛ مانند روشن شدن یا نمایش اطلاعات.

```text
Class (نقشه / قالب)
        ↓
Object (نمونهٔ واقعی ساخته‌شده از قالب)
        ├── Fields  → وضعیت و داده
        └── Methods → رفتار
```

چهار اصل مشهور OOP:

| اصل | مفهوم |
|---|---|
| **Encapsulation** | مخفی‌سازی و کنترل دسترسی به داده‌ها |
| **Inheritance** | ارث‌بری ویژگی‌ها و رفتارها از کلاس والد |
| **Polymorphism** | رفتار متفاوت اشیای مختلف از طریق یک نوع مشترک |
| **Abstraction** | نمایش ویژگی‌های ضروری و پنهان کردن جزئیات غیرضروری |

---

## ۲. تعریف Class در جاوا

کلاس یک **Blueprint** یا قالب برای ساخت اشیاء است. درون کلاس می‌توان فیلد، متد، سازنده و کلاس داخلی تعریف کرد.

```java
class Car {
    // Fields: وضعیت شیء
    String color;
    String model;
    int year;

    // Methods: رفتار شیء
    void startEngine() {
        System.out.println("Engine started!");
    }

    void stopEngine() {
        System.out.println("Engine stopped!");
    }
}
```

---

### ۲.۱. فیلدها (Fields)

فیلدها متغیرهایی هستند که در سطح کلاس تعریف شده و وضعیت هر شیء را نگهداری می‌کنند.

```java
class Car {
    String color;
    String model;
    int year;
}
```

هر شیء از `Car` مقادیر فیلدهای مخصوص به خود را دارد:

```java
Car car1 = new Car();
car1.color = "Red";

Car car2 = new Car();
car2.color = "Blue";

System.out.println(car1.color); // Red
System.out.println(car2.color); // Blue
```

---

### ۲.۲. متدها (Methods / Functions)

متدها رفتارهای یک کلاس را مشخص می‌کنند. در Java، چیزی که معمولاً Function نامیده می‌شود، درون کلاس تعریف می‌شود و **Method** نام دارد.

فرم کلی تعریف متد:

```java
accessModifier returnType methodName(parameters) {
    // Method Body
}
```

| بخش | توضیح |
|---|---|
| `accessModifier` | سطح دسترسی؛ مانند `public` یا `private` |
| `returnType` | نوع خروجی؛ مانند `int`، `String` یا `void` |
| `methodName` | نام متد |
| `parameters` | ورودی‌های متد؛ اختیاری |
| `return` | بازگرداندن خروجی در متدهای غیر `void` |

```java
class Calculator {
    int add(int a, int b) {
        return a + b;
    }

    void printWelcome() {
        System.out.println("Welcome!");
    }
}
```

---

### ۲.۳. سازنده‌ها (Constructors)

**Constructor** متدی ویژه است که هنگام ساخت شیء با `new` فراخوانی می‌شود و برای مقداردهی اولیهٔ فیلدها کاربرد دارد.

ویژگی‌ها:

- نام آن دقیقاً با نام کلاس یکسان است.
- هیچ نوع خروجی—even `void`—ندارد.
- امکان Overloading دارد.
- اگر سازنده‌ای تعریف نکنید، کامپایلر معمولاً یک سازندهٔ بدون پارامتر ایجاد می‌کند.

```java
class Car {
    String color;
    String model;
    int year;

    Car(String color, String model, int year) {
        this.color = color;
        this.model = model;
        this.year = year;
    }

    void displayInfo() {
        System.out.println(
            "Color: " + color +
            ", Model: " + model +
            ", Year: " + year
        );
    }
}
```

> [!tip] کاربرد `this`
> `this` به شیء فعلی اشاره دارد. وقتی نام پارامتر و فیلد برابر باشد، از آن برای تمایز استفاده می‌کنیم:
>
> ```java
> this.color = color;
> ```

---

## ۳. Object و ایجاد نمونه از Class

برای استفاده از یک کلاس، باید از آن یک شیء بسازیم:

```java
public class Main {
    public static void main(String[] args) {
        Car myCar = new Car("Red", "Toyota", 2020);

        myCar.displayInfo();
        // Color: Red, Model: Toyota, Year: 2020
    }
}
```

### ۳.۱. Declaration و Creation

```java
Car myCar = new Car("Red", "Toyota", 2020);
```

این خط دو بخش مهم دارد:

| بخش | نام | وظیفه |
|---|---|---|
| `Car myCar` | Declaration | تعریف متغیر مرجع از نوع `Car` |
| `new Car(...)` | Creation | ساخت شیء در Heap و فراخوانی Constructor |

```java
Car myCar; // فقط Declaration؛ مقدار فعلی مرجع null است

myCar = new Car("Blue", "BMW", 2024); // Creation و Assignment
```

> [!warning] تفاوت با Primitive
> در `int number = 10;` خود مقدار مستقیماً در متغیر نگهداری می‌شود.  
> اما در `Car myCar = new Car(...)`، متغیر `myCar` یک **Reference** است که به شیء ساخته‌شده در Heap اشاره می‌کند.

---

### ۳.۲. مفهوم Reference

چند Reference می‌توانند به یک شیء اشاره کنند:

```java
Car car1 = new Car("Red", "Toyota", 2020);
Car car2 = car1; // کپی شیء نیست؛ کپی Reference است

car2.color = "Blue";

System.out.println(car1.color); // Blue
System.out.println(car2.color); // Blue
```

```text
car1 ──┐
       ├──> Car Object { color = "Blue", ... }
car2 ──┘
```

---

## ۴. Encapsulation یا کپسوله‌سازی

کپسوله‌سازی یعنی پنهان کردن داده‌های داخلی کلاس و فراهم کردن دسترسی کنترل‌شده به آن‌ها از طریق متدهای عمومی.

```java
class Car {
    private String color;
    private int price;

    public void setColor(String color) {
        this.color = color;
    }

    public String getColor() {
        return color;
    }

    public void setPrice(int price) {
        if (price < 0) {
            throw new IllegalArgumentException("Price cannot be negative.");
        }
        this.price = price;
    }

    public int getPrice() {
        return price;
    }
}
```

| Access Modifier | دسترسی |
|---|---|
| `private` | فقط داخل همان کلاس |
| *(بدون modifier)* | فقط داخل همان package |
| `protected` | package و کلاس‌های فرزند |
| `public` | از همه‌جا |

> [!important] مزیت Encapsulation
> به‌جای دسترسی مستقیم و کنترل‌نشده به فیلدها، از Getter و Setter استفاده می‌کنیم تا بتوانیم اعتبارسنجی، لاگ‌گیری یا محدودیت‌های لازم را اعمال کنیم.

---

## ۵. متدها و Functionها در Java

### ۵.۱. پارامتر و آرگومان

- **Parameter**: متغیر تعریف‌شده در امضای متد.
- **Argument**: مقدار واقعی ارسال‌شده هنگام فراخوانی متد.

```java
class Calculator {
    int multiply(int a, int b) { // a و b: Parameter
        return a * b;
    }
}

public class Main {
    public static void main(String[] args) {
        Calculator calculator = new Calculator();

        int result = calculator.multiply(4, 5); // 4 و 5: Argument
        System.out.println(result); // 20
    }
}
```

---

### ۵.۲. ارسال آرگومان‌ها: Java همیشه Pass by Value است

> [!danger] نکتهٔ مهم
> Java **همیشه Pass by Value** است؛ حتی برای Objectها.  
> در حالت Object، «کپیِ Reference» به متد ارسال می‌شود، نه خود Object و نه Pass by Reference واقعی.

#### Primitive: کپی مقدار ارسال می‌شود

```java
public class Main {
    public static void main(String[] args) {
        int num = 10;

        modifyValue(num);

        System.out.println(num); // 10
    }

    static void modifyValue(int value) {
        value = 20; // فقط کپی محلی تغییر می‌کند
    }
}
```

#### Reference: کپی Reference ارسال می‌شود

در این حالت، Reference اصلی و Reference کپی‌شده به یک شیء اشاره دارند؛ بنابراین تغییر **فیلدهای شیء** قابل مشاهده است.

```java
class Car {
    String model;
}

public class Main {
    public static void main(String[] args) {
        Car myCar = new Car();
        myCar.model = "Toyota";

        modifyCar(myCar);

        System.out.println(myCar.model); // Honda
    }

    static void modifyCar(Car car) {
        car.model = "Honda"; // تغییر روی همان شیء
    }
}
```

اما اگر Reference محلی را به یک شیء جدید تغییر دهیم، Reference متغیر اصلی تغییر نمی‌کند:

```java
static void replaceCar(Car car) {
    car = new Car();       // فقط Reference محلی تغییر می‌کند
    car.model = "BMW";
}
```

---

### ۵.۳. متدهای `static`

متد استاتیک به شیء وابسته نیست و با نام کلاس فراخوانی می‌شود:

```java
class MathUtils {
    static int square(int x) {
        return x * x;
    }
}

public class Main {
    public static void main(String[] args) {
        int result = MathUtils.square(5);
        System.out.println(result); // 25
    }
}
```

> [!note] محدودیت متدهای Static
> یک متد `static` مستقیماً به فیلدها یا متدهای نمونه (غیر `static`) دسترسی ندارد؛ زیرا ممکن است هیچ شیئی از کلاس ساخته نشده باشد.

---

### ۵.۴. Overloading در متدها

**Method Overloading** یعنی داشتن چند متد با نام یکسان و فهرست پارامترهای متفاوت.

تفاوت باید در یکی از موارد زیر باشد:

- تعداد پارامترها
- نوع پارامترها
- ترتیب نوع پارامترها

> [!warning] فقط تغییر Return Type کافی نیست
> این دو متد نمی‌توانند هم‌زمان تعریف شوند:
> ```java
> int add(int a, int b) { return a + b; }
> double add(int a, int b) { return a + b; } // ❌ خطا
> ```

```java
class Calculator {
    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }

    int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

---

### ۵.۵. متدهای بازگشتی (Recursion)

در Recursion، یک متد خودش را فراخوانی می‌کند. هر متد بازگشتی باید یک **Base Case** داشته باشد تا فراخوانی‌ها متوقف شوند.

```java
public class RecursionExample {
    static long factorial(int n) {
        if (n < 0) {
            throw new IllegalArgumentException("n must be non-negative");
        }

        // Base Case
        if (n == 0 || n == 1) {
            return 1;
        }

        // Recursive Case
        return n * factorial(n - 1);
    }

    public static void main(String[] args) {
        System.out.println(factorial(5)); // 120
    }
}
```

> [!warning] StackOverflowError
> نداشتن Base Case صحیح یا عمیق بودن بیش از اندازهٔ Recursion ممکن است باعث `StackOverflowError` شود.

---

### ۵.۶. Scope متغیرها در متدها

متغیر محلی فقط در همان متد یا بلوکی که تعریف شده قابل دسترسی است:

```java
void myFunction() {
    int localVar = 10;
    System.out.println(localVar);
}

// localVar در اینجا قابل دسترسی نیست. ❌
```

---

### ۵.۷. Lambda Expression — از Java 8

Lambda راهی کوتاه برای تعریف رفتار یک‌بارمصرف است و معمولاً همراه با **Functional Interface** استفاده می‌شود.

```java
@FunctionalInterface
interface MathOperation {
    int operation(int a, int b);
}

public class Main {
    public static void main(String[] args) {
        MathOperation addition = (a, b) -> a + b;

        System.out.println(addition.operation(5, 3)); // 8
    }
}
```

---

## ۶. Inheritance یا وراثت

وراثت به یک کلاس فرزند اجازه می‌دهد ویژگی‌ها و رفتارهای کلاس والد را دریافت کند.

```java
class Animal {
    void eat() {
        System.out.println("This animal is eating.");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("The dog is barking.");
    }
}
```

```java
Dog dog = new Dog();
dog.eat();  // از Animal به ارث رسیده
dog.bark(); // متعلق به Dog
```

| اصطلاح | توضیح |
|---|---|
| **Superclass / Parent** | کلاس والد؛ مانند `Animal` |
| **Subclass / Child** | کلاس فرزند؛ مانند `Dog` |
| `extends` | کلیدواژهٔ ارث‌بری از کلاس |
| `implements` | کلیدواژهٔ پیاده‌سازی Interface |

> [!note] نکته
> هر کلاس در Java فقط می‌تواند از **یک کلاس** ارث‌بری کند؛ اما می‌تواند چند Interface را پیاده‌سازی کند.

---

### ۶.۱. Override کردن متد

کلاس فرزند می‌تواند پیاده‌سازی متد ارث‌رسیده را تغییر دهد. این کار **Method Overriding** نام دارد.

```java
class Animal {
    void eat() {
        System.out.println("Animal is eating.");
    }
}

class Dog extends Animal {
    @Override
    void eat() {
        System.out.println("Dog is eating.");
    }
}
```

> [!tip] همیشه `@Override` بنویسید
> این Annotation باعث می‌شود کامپایلر اشتباهات احتمالی در امضای متد Overrideشده را تشخیص دهد.

---

### ۶.۲. کلیدواژهٔ `super`

`super` به عضو یا سازندهٔ کلاس والد اشاره می‌کند.

```java
class Animal {
    String name;

    Animal(String name) {
        this.name = name;
    }

    void eat() {
        System.out.println(name + " is eating.");
    }
}

class Dog extends Animal {
    Dog(String name) {
        super(name); // فراخوانی Constructor والد؛ باید اولین دستور Constructor فرزند باشد
    }

    @Override
    void eat() {
        super.eat(); // فراخوانی متد نسخهٔ والد
        System.out.println(name + " is eating dog food.");
    }
}
```

---

## ۷. Polymorphism یا چندریختی

چندریختی یعنی یک Reference از نوع والد یا Interface بتواند به اشیاء گوناگون از کلاس‌های فرزند اشاره کند.

```java
class Animal {
    void makeSound() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }
}

class Cat extends Animal {
    @Override
    void makeSound() {
        System.out.println("Meow");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal[] animals = {new Dog(), new Cat()};

        for (Animal animal : animals) {
            animal.makeSound(); // Bark سپس Meow
        }
    }
}
```

> [!important] Dynamic Method Dispatch
> با وجود اینکه نوع Reference برابر `Animal` است، انتخاب نسخهٔ Override‌شدهٔ متد بر اساس **نوع واقعی شیء در زمان اجرا** انجام می‌شود.

---

## ۸. Abstract Class و Interface

### ۸.۱. کلاس انتزاعی (Abstract Class)

از یک کلاس `abstract` نمی‌توان مستقیم Object ساخت. این کلاس معمولاً برای ایجاد یک پایهٔ مشترک بین کلاس‌های مرتبط استفاده می‌شود.

```java
abstract class Animal {
    abstract void makeSound(); // متد abstract بدون body

    void sleep() { // متد معمولی مجاز است
        System.out.println("Sleeping...");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }
}
```

```java
Animal animal = new Dog(); // ✅
Animal invalid = new Animal(); // ❌ Cannot instantiate abstract class
```

---

### ۸.۲. Interface

Interface یک **قرارداد** برای رفتارهاست. کلاسی که Interface را پیاده‌سازی می‌کند، باید متدهای آن را پیاده‌سازی کند.

```java
interface Animal {
    void makeSound();
}

class Dog implements Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}
```

### مقایسهٔ Abstract Class و Interface

| ویژگی | Abstract Class | Interface |
|---|---|---|
| کلیدواژهٔ استفاده در کلاس فرزند | `extends` | `implements` |
| ارث‌بری چندگانه | خیر؛ فقط یک کلاس والد | بله؛ چند Interface ممکن است |
| Constructor | دارد | ندارد |
| Field نمونه | دارد | ندارد |
| متد Abstract | دارد | دارد |
| متد با پیاده‌سازی | دارد | `default` و `static` (از Java 8) |
| کاربرد اصلی | اشتراک‌گذاری state و رفتار مشترک | تعریف قرارداد رفتاری مستقل |

---

## ۹. کلاس‌های داخلی (Inner Classes)

کلاس داخلی، کلاسی است که در بدنهٔ کلاس دیگری تعریف می‌شود. یک Inner Class غیر‌استاتیک به اعضای—including `private`—کلاس بیرونی دسترسی دارد.

```java
class OuterClass {
    private int x = 10;

    class InnerClass {
        private int y = 5;

        int getOuterX() {
            return x;
        }

        int getY() {
            return y;
        }
    }
}

public class Main {
    public static void main(String[] args) {
        OuterClass outer = new OuterClass();

        // ساخت Inner Class غیر static
        OuterClass.InnerClass inner = outer.new InnerClass();

        System.out.println(inner.getOuterX()); // 10
        System.out.println(inner.getY());      // 5
    }
}
```

### انواع مهم کلاس داخلی

| نوع | ویژگی |
|---|---|
| **Inner Class** | غیر `static`؛ به شیء Outer وابسته است |
| **Static Nested Class** | با `static`؛ بدون نیاز به شیء Outer ساخته می‌شود |
| **Anonymous Class** | تعریف و ساخت هم‌زمان برای استفادهٔ کوتاه‌مدت |

---

## جمع‌بندی

```text
Class       → قالب ساخت
Object      → نمونهٔ واقعی Class
Field       → داده و وضعیت Object
Method      → رفتار Object
Constructor → مقداردهی اولیه هنگام new
Encapsulation → کنترل دسترسی به داده‌ها
Inheritance → دریافت قابلیت‌های Parent
Polymorphism → رفتار متفاوت پشت یک Reference مشترک
Abstraction → نمایش قرارداد یا ویژگی‌های ضروری
```
  

  

 ---
tags:
  - java
  - control-flow
  - loops
  - algorithms
---

# ساختارهای تکرار (Loops) و تکنیک‌های کنترل جریان در Java

> [!abstract] سرفصل‌ها
> - مفاهیم پایه و معماری حلقه‌ها در Java
> - تحلیل دقیق حلقه‌های `for`، `for-each`، `while` و `do-while`
> - کنترل جریان پیشرفته: `break`، `continue` و حلقه‌های برچسب‌دار (Labeled Loops)
> - تکنیک‌های کاربردی: پیمایش Collections/Maps، الگوریتم‌های مرتب‌سازی و Streaming I/O
> - جدول مقایسه جامع انواع حلقه و ملاحظات Performance / Clean Code

---

## ۱. مقدمه‌ای بر ساختارهای تکرار (Loops)

حلقه‌ها ابزارهایی برای تکرار بلاک‌های کدی بر اساس شروط منطقی یا تعداد تکرار معین هستند. استفاده صحیح از حلقه‌ها مانع از تکرار کد (DRY Principle) شده و امکان پردازش مجموعه‌ها، جریان‌های داده (Streams) و مدیریت چرخه‌های حیات سرویس‌ها را فراهم می‌کند.

```text
┌──────────────────────────────────────────────────────────┐
│                    انواع حلقه‌ها در Java                 │
└────────────────────────────┬─────────────────────────────┘
                             │
       ┌─────────────────────┼─────────────────────┐
       ▼                     ▼                     ▼
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│  حلقهٔ for   │      │ حلقهٔ while  │      │حلقه do-while │
│ (معین/تعداد) │      │ (نامعین/شرط) │      │(حداقل یک‌بار)│
└──────┬───────┘      └──────────────┘      └──────────────┘
       ▼
┌──────────────┐
│   for-each   │
│ (Collections)│
└──────────────┘
```

---

## ۲. حلقه `for` استاندارد و پیشرفته

حلقه `for` زمانی استفاده می‌شود که تعداد تکرار یا حدود پیمایش از قبل مشخص باشد یا نیاز به مدیریت مستقیم اندیس (Index) داشته باشیم.

### ۲.۱. ساختار کلی (Syntax)

```java
for (initialization; condition; update) {
    // دستوراتی که در هر چرخه اجرا می‌شوند
}
```

- **`initialization`**: مقداردهی اولیه متغیر(های) شمارنده (فقط یک‌بار در ابتدا).
- **`condition`**: شرط بولی؛ پیش از ورود به هر تکرار بررسی می‌شود (در صورت `true` بودن ادامه می‌یابد).
- **`update`**: گام شمارش (افزایش/کاهش یا محاسبه فرمول) که در انتهای هر دور اجرا می‌شود.

```java
// مثال ۱: چاپ اعداد ۱ تا ۵
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}

// مثال ۲: محاسبه مجموع اعداد ۱ تا ۱۰
int sum = 0;
for (int i = 1; i <= 10; i++) {
    sum += i;
}
System.out.println("Sum: " + sum); // خروجی: 55
```

---

### ۲.۲. استفاده از چندین متغیر در `for`

می‌توان درون بخش‌های `initialization` و `update`، چند متغیر از یک نوع را هم‌زمان تعریف و به‌روزرسانی کرد (با کاما `,` جدا می‌شوند):

```java
for (int i = 1, j = 1; i <= 3; i++, j = i * i) {
    System.out.println("i: " + i + ", i*i: " + j);
}
```

**خروجی:**
```text
i: 1, i*i: 1
i: 2, i*i: 4
i: 3, i*i: 9
```

---

### ۲.۳. حلقه‌های تودرتو (Nested Loops)

قرارگیری یک حلقه درون حلقه دیگر؛ مناسب برای ماتریس‌ها، الگوها و جداول ضرب.

```java
// چاپ ماتریس ۵ در ۵ جدول ضرب
for (int i = 1; i <= 5; i++) {
    for (int j = 1; j <= 5; j++) {
        System.out.print((i * j) + "\t");
    }
    System.out.println();
}
```

---

### ۲.۴. حلقه `for-each` (Enhanced for Loop)

این ساختار برای پیمایش عناصر آرایه‌ها و کالکشن‌ها بدون نیاز به تعریف دستی Index یا شیء Iterator استفاده می‌شود:

```java
int[] numbers = {10, 20, 30, 40, 50};

for (int number : numbers) {
    System.out.println(number);
}
```

> [!tip] خوانایی و ایمنی
> حلقه `for-each` خطر خطای شایع `ArrayIndexOutOfBoundsException` را به صفر می‌رساند و کد خواناتری تولید می‌کند.

---

### ۲.۵. پیمایش با `Iterator` و ساختارهای `Map`

برای ساختارهایی که مستقیماً `Iterable` نیستند (مانند `Map`) یا زمانی که حین پیمایش نیاز به حذف (`remove`) عنصر داریم:

```java
import java.util.*;

public class CollectionTraversal {
    public static void main(String[] args) {
        // ۱. پیمایش List با Iterator درون حلقه for
        List<String> list = new ArrayList<>(Arrays.asList("Apple", "Banana", "Cherry"));
        for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
            String item = it.next();
            System.out.println(item);
        }

        // ۲. پیمایش Map با EntrySet درون حلقه for-each
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 3);
        map.put("Banana", 2);
        map.put("Cherry", 5);

        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " -> " + entry.getValue());
        }
    }
}
```

---

## ۳. حلقه `while`

زمانی استفاده می‌شود که تعداد تکرار نامشخص بوده و اجرای حلقه به برقرار بودن یک شرط منطقی یا دریافت یک Event وابسته است.

### ۳.۱. ساختار کلی

```java
while (condition) {
    // کد تکرارشونده
}
```

```java
int i = 1;
while (i <= 5) {
    System.out.println(i);
    i++;
}
```

---

### ۳.۲. الگوهای پرکاربرد `while`

#### الف) اعتبارسنجی ورودی کاربر (Input Validation)
```java
import java.util.Scanner;

public class InputValidation {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int number;
        
        while (true) {
            System.out.print("Enter a number between 1 and 10: ");
            number = scanner.nextInt();
            if (number >= 1 && number <= 10) {
                break;
            }
            System.out.println("Invalid number. Try again.");
        }
        System.out.println("Valid input: " + number);
    }
}
```

#### ب) خواندن استریم فایل خط به خط (I/O Streaming)
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class FileReadExample {
    public static void main(String[] args) {
        try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### ج) اجرای چرخه‌ای مبتنی بر زمان (Timer / Polling)
```java
public class TimerPolling {
    public static void main(String[] args) {
        long startTime = System.currentTimeMillis();
        
        // اجرا به مدت ۵ ثانیه
        while (System.currentTimeMillis() - startTime < 5000) {
            System.out.println("Polling service status...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
        System.out.println("Timeout reached.");
    }
}
```

---

## ۴. حلقه `do-while`

تفاوت کلیدی با `while`: شرط در انتهای بلوک ارزیابی می‌شود؛ بنابراین **کد همواره حداقل یک‌بار اجرا خواهد شد**.

```java
do {
    // دستوراتی که حداقل یک‌بار اجرا می‌شوند
} while (condition);
```

```java
int i = 10;
do {
    System.out.println("This will be printed at least once");
} while (i < 5);
```

### الگوریتم کاربردی: مرتب‌سازی حبابی (Bubble Sort)
```java
public class BubbleSort {
    public static void main(String[] args) {
        int[] array = {64, 34, 25, 12, 22, 11, 90};
        int n = array.length;
        boolean swapped;

        do {
            swapped = false;
            for (int i = 0; i < n - 1; i++) {
                if (array[i] > array[i + 1]) {
                    int temp = array[i];
                    array[i] = array[i + 1];
                    array[i + 1] = temp;
                    swapped = true;
                }
            }
            n--;
        } while (swapped);

        for (int num : array) {
            System.out.print(num + " ");
        }
        // خروجی: 11 12 22 25 34 64 90 
    }
}
```

---

## ۵. کنترل جریان: `break`، `continue` و Labeled Loops

### ۵.۱. دستور `break`
خروج آنی از درونی‌ترین حلقه‌ای که دستور در آن فراخوانی شده است.

```java
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        break; // پایان حلقه در عدد ۵
    }
    System.out.println(i); // چاپ ۱ تا ۴
}
```

---

### ۵.۲. دستور `continue`
رد کردن دور فعلی حلقه و پرش مستقیم به ارزیابی شرط بعدی (یا اجرای update در حلقه `for`).

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue; // نادیده گرفتن عدد ۳
    }
    System.out.println(i); // خروجی: 1, 2, 4, 5
}
```

---

### ۵.۳. برچسب‌گذاری حلقه‌ها (Labeled Loops)

در حلقه‌های تودرتو، `break` و `continue` به طور پیش‌فرض فقط روی داخلی‌ترین حلقه اثر می‌گذارند. برای کنترل حلقه بیرونی از **Label** استفاده می‌شود:

```java
outerLoop:
for (int i = 1; i <= 5; i++) {
    for (int j = 1; j <= 5; j++) {
        if (j == 3) {
            break outerLoop; // شکستن و خروج کامل از حلقه بیرونی
        }
        System.out.println("i: " + i + ", j: " + j);
    }
}
```

> [!warning] Best Practice در خصوص Labelها
> استفاده بیش‌ازحد از برچسب‌ها (Labels) می‌تواند جریان کد را به سبک دستور خطرناک `goto` پیچیده و ناخوانا کند (Spaghetti Code). ترجیحاً منطق را به متدهای کوچک‌تر تفکیک کرده و از `return` استفاده کنید.

---

## ۶. مقایسه جامع ساختارهای تکرار

| ویژگی | `for` سنتی | `for-each` | `while` | `do-while` |
|---|---|---|---|---|
| **ارزیابی شرط** | در ورود به حلقه (Pre-test) | خودکار بر مبنای Iterator | در ورود به حلقه (Pre-test) | در خروج از حلقه (Post-test) |
| **حداقل دفعات اجرا** | `0` بار | `0` بار | `0` بار | **`1` بار** |
| **دسترسی به Index** | بله (`i`) | خیر | نیاز به شمارنده دستی | نیاز به شمارنده دستی |
| **امکان تغییر ساختار حین پیمایش** | بله (نیاز به مدیریت اندیس) | خیر (`ConcurrentModificationException`) | بله | بله |
| **بهترین کاربرد** | شمارش ثابت، آرایه‌های عددی | خواندن Lists / Sets / Arrays | ورودی شبکه/فایل، سرور Loops | منوهای کاربری، بررسی پس از پردازش |

---

## ۷. جمع‌بندی نکات فنی و معماری

```text
┌─────────────────────────────────────────────────────────────┐
│                       انتخاب حلقه بهینه                     │
├─────────────────────────────────────────────────────────────┤
│ • تعداد گام‌ها مشخص است؟                  ──> for          │
│ • فقط قصد خواندن عناصر مجموعه دارید؟     ──> for-each     │
│ • شرط توقف به رویداد/ورودی بستگی دارد؟   ──> while        │
│ • کد باید حداقل یک‌بار اجرا شود؟        ──> do-while     │
│ • خروج از چندین حلقه تودرتو؟             ──> Labeled break│
└─────────────────────────────────────────────────────────────┘
```

  

---
tags:
  - java
  - static
  - exceptions
  - assertions
  - error-handling
---

# `static`، مدیریت Exception و Assertion در Java

> [!abstract] سرفصل‌ها
> - مفهوم و کاربرد `static`
> - فیلد، متد، بلوک و کلاس داخلی `static`
> - سلسله‌مراتب خطاها و Exceptionها
> - `try`، `catch`، `finally`، `throw` و `throws`
> - Exception سفارشی و `try-with-resources`
> - Assertions و نحوه فعال‌سازی آن‌ها

---

# بخش اول: مفهوم `static` در Java

## ۱. `static` چیست؟

کلمهٔ کلیدی `static` مشخص می‌کند یک عضو متعلق به **خود کلاس (Class)** است، نه متعلق به یک نمونه یا شیء مشخص از آن کلاس.

```text
بدون static:
هر Object → داده/رفتار مخصوص خودش

با static:
Class → یک عضو مشترک برای همهٔ Objectها
```

```java
class User {
    static int totalUsers = 0; // متعلق به کلاس
    String username;           // متعلق به هر شیء
}
```

در این مثال:

- `totalUsers` فقط **یک نسخه** در سطح کلاس دارد.
- هر شیء `User` مقدار مستقل خود را برای `username` دارد.

> [!tip] قاعدهٔ ذهنی
> اگر داده یا رفتار به «همهٔ نمونه‌ها» مربوط است، احتمالاً باید `static` باشد.  
> اگر به وضعیت یک شیء خاص وابسته است، باید **Instance Member** باشد.

---

## ۲. فیلدهای `static`

### ۲.۱. تعریف فیلد static

فیلد `static` یک متغیر اشتراکی است که فقط یک‌بار برای کلاس ایجاد می‌شود.

```java
class MyClass {
    static int staticVar = 0; // Class Field
    int instanceVar = 0;      // Instance Field
}
```

| ویژگی | Static Field | Instance Field |
|---|---|---|
| تعلق دارد به | Class | Object |
| تعداد نسخه | یک نسخه برای کل کلاس | یک نسخه برای هر Object |
| دسترسی پیشنهادی | `ClassName.field` | `object.field` |
| زمان ایجاد | هنگام بارگذاری/مقداردهی کلاس | هنگام ساخت شیء با `new` |
| کاربرد | تنظیمات مشترک، شمارنده، ثابت‌ها | وضعیت اختصاصی هر شیء |

---

### ۲.۲. شمارش تعداد Objectهای ساخته‌شده

یکی از کاربردهای متداول `static`، نگهداری تعداد Instanceهای ساخته‌شده از یک کلاس است.

```java
class Counter {
    static int count = 0;

    Counter() {
        count++;
    }
}

public class Main {
    public static void main(String[] args) {
        Counter c1 = new Counter();
        Counter c2 = new Counter();
        Counter c3 = new Counter();

        System.out.println("Objects created: " + Counter.count);
        // خروجی: Objects created: 3
    }
}
```

```text
Counter.count
     │
     └── یک مقدار مشترک بین c1 ،c2 و c3
```

---

### ۲.۳. دسترسی به فیلد static

روش استاندارد و خوانا، استفاده از **نام کلاس** است:

```java
class AppConfig {
    static String environment = "production";
}

public class Main {
    public static void main(String[] args) {
        System.out.println(AppConfig.environment);
    }
}
```

امکان دسترسی از طریق Object نیز وجود دارد، اما توصیه نمی‌شود:

```java
AppConfig config = new AppConfig();

System.out.println(config.environment); // از نظر فنی درست، اما نامناسب
```

> [!warning] Best Practice
> برای عضوهای `static` همیشه از نام کلاس استفاده کنید:
>
> ```java
> AppConfig.environment
> ```
>
> نه از طریق Object:
>
> ```java
> config.environment
> ```

---

### ۲.۴. ثابت‌ها: `static final`

مقدارهایی که برای کل برنامه ثابت‌اند معمولاً با ترکیب `static final` تعریف می‌شوند.

```java
class MathConstants {
    public static final double PI = 3.141592653589793;
    public static final int MAX_RETRY = 3;
}
```

استفاده:

```java
System.out.println(MathConstants.PI);
System.out.println(MathConstants.MAX_RETRY);
```

> [!important] نام‌گذاری Constant
> بر اساس قرارداد Java، نام ثابت‌های `static final` با حروف بزرگ و `_` نوشته می‌شود:
>
> ```java
> public static final int MAX_CONNECTIONS = 100;
> ```

---

## ۳. متدهای `static`

### ۳.۱. تعریف متد static

متد `static` بدون ایجاد Object قابل فراخوانی است.

```java
class MathUtils {
    static int add(int a, int b) {
        return a + b;
    }
}

public class Main {
    public static void main(String[] args) {
        int result = MathUtils.add(5, 10);

        System.out.println(result); // 15
    }
}
```

نمونه‌های رایج در کتابخانهٔ Java:

```java
Math.sqrt(25);                  // 5.0
Math.max(10, 20);               // 20
Integer.parseInt("42");         // 42
System.currentTimeMillis();     // زمان فعلی
```

---

### ۳.۲. کاربردهای مناسب متد static

از متد `static` زمانی استفاده کنید که عملیات مورد نظر به وضعیت هیچ Object خاصی وابسته نیست.

```java
class StringUtils {
    static boolean isBlank(String value) {
        return value == null || value.trim().isEmpty();
    }
}

public class Main {
    public static void main(String[] args) {
        System.out.println(StringUtils.isBlank("   ")); // true
    }
}
```

کاربردهای متداول:

- Utility Methodها؛ مانند تبدیل، اعتبارسنجی و محاسبات
- Factory Methodها
- متد اصلی برنامه: `main`
- متدهای مرتبط با Constantها یا تنظیمات مشترک
- عملیات‌هایی که نیازی به `this` و داده‌های Object ندارند

---

### ۳.۳. محدودیت‌های متد static

یک متد `static` نمی‌تواند مستقیماً به اعضای Instance دسترسی پیدا کند؛ زیرا هنگام اجرای آن، ممکن است هیچ Objectی وجود نداشته باشد.

```java
class User {
    String username = "ali";

    static void printUsername() {
        // System.out.println(username); // ❌ Compile Error
    }
}
```

همچنین استفاده از `this` و `super` در یک متد `static` غیرمجاز است:

```java
class User {
    static void test() {
        // System.out.println(this.username); // ❌
        // super.toString();                  // ❌
    }
}
```

راه‌حل: یک Object را به‌صورت Parameter دریافت کنید یا متد را از حالت `static` خارج کنید.

```java
class User {
    String username;

    User(String username) {
        this.username = username;
    }

    static void printUsername(User user) {
        System.out.println(user.username);
    }
}
```

---

### ۳.۴. پنهان‌سازی متد static؛ نه Override

متدهای نمونه می‌توانند `override` شوند؛ اما متدهای `static` واقعاً Override نمی‌شوند و در واقع **Method Hiding** رخ می‌دهد.

```java
class Parent {
    static void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    static void show() {
        System.out.println("Child");
    }
}

public class Main {
    public static void main(String[] args) {
        Parent parent = new Child();

        parent.show(); // Parent
    }
}
```

> [!warning] نکتهٔ مهم
> انتخاب متد `static` براساس **نوع Reference در زمان کامپایل** انجام می‌شود، نه نوع واقعی Object در زمان اجرا.  
> این رفتار با Polymorphism متدهای Instance تفاوت دارد.

---

## ۴. بلوک‌های `static`

### ۴.۱. تعریف و کاربرد

بلوک `static` کدی است که فقط یک‌بار، هنگام **Initialization کلاس** اجرا می‌شود.

```java
class AppConfig {
    static String environment;

    static {
        environment = "production";
        System.out.println("Static block executed");
    }
}
```

بلوک `static` معمولاً برای مقداردهی اولیهٔ پیچیده به فیلدهای `static` استفاده می‌شود.

```java
import java.util.List;

class Config {
    static final List<String> ALLOWED_ROLES;

    static {
        ALLOWED_ROLES = List.of("ADMIN", "USER", "VIEWER");
    }
}
```

---

### ۴.۲. ترتیب اجرا

اعضای `static` به ترتیب قرارگیری در کد مقداردهی و اجرا می‌شوند؛ سپس در صورت ایجاد Object، Constructor اجرا خواهد شد.

```java
class MyClass {
    static int value = print("Static field initialized");

    static {
        print("Static block 1");
    }

    static {
        print("Static block 2");
    }

    MyClass() {
        print("Constructor executed");
    }

    static int print(String message) {
        System.out.println(message);
        return 10;
    }
}

public class Main {
    public static void main(String[] args) {
        new MyClass();
    }
}
```

**خروجی:**

```text
Static field initialized
Static block 1
Static block 2
Constructor executed
```

> [!note] زمان بارگذاری
> مقداردهی `static` معمولاً با اولین استفادهٔ فعال از کلاس رخ می‌دهد؛ مثلاً هنگام فراخوانی یک متد static، دسترسی به فیلد static غیرثابت، یا ساخت Object از کلاس.

---

## ۵. کلاس داخلی `static` (Static Nested Class)

کلاس داخلی `static` درون یک کلاس دیگر تعریف می‌شود، اما به Object کلاس بیرونی وابسته نیست.

```java
class OuterClass {
    static int staticValue = 10;
    int instanceValue = 20;

    static class StaticNestedClass {
        void display() {
            System.out.println(staticValue);

            // System.out.println(instanceValue); // ❌
        }
    }
}
```

ساخت Object از آن:

```java
public class Main {
    public static void main(String[] args) {
        OuterClass.StaticNestedClass nested =
                new OuterClass.StaticNestedClass();

        nested.display(); // 10
    }
}
```

### تفاوت Static Nested Class و Inner Class

| ویژگی | Static Nested Class | Inner Class معمولی |
|---|---|---|
| وابستگی به Object بیرونی | ندارد | دارد |
| ساخت Object | `new Outer.Nested()` | `outer.new Inner()` |
| دسترسی مستقیم به عضو Instance بیرونی | ندارد | دارد |
| دسترسی به عضو Static بیرونی | دارد | دارد |
| کاربرد | گروه‌بندی منطقی کلاس‌ها | رفتار وابسته به یک Object خارجی |

---

# بخش دوم: Exception و Assertion در Java

## ۶. Exception چیست؟

**Exception** رخدادی غیرعادی در زمان اجرای برنامه است که جریان عادی اجرای کد را قطع می‌کند.

نمونه‌ها:

- تقسیم عدد صحیح بر صفر
- باز کردن فایل ناموجود
- دسترسی به اندیس نامعتبر آرایه
- استفاده از Reference با مقدار `null`
- قطع اتصال شبکه یا خطای پایگاه‌داده

```java
int result = 10 / 0; // ArithmeticException
```

---

## ۷. سلسله‌مراتب Exceptionها

تمام خطاها و Exceptionها از `Throwable` ارث می‌برند:

```text
Throwable
├── Error
│   ├── OutOfMemoryError
│   └── StackOverflowError
│
└── Exception
    ├── RuntimeException
    │   ├── NullPointerException
    │   ├── IllegalArgumentException
    │   ├── ArithmeticException
    │   └── ArrayIndexOutOfBoundsException
    │
    ├── IOException
    └── SQLException
```

| نوع | توضیح | نیاز به مدیریت اجباری؟ |
|---|---|---|
| `Error` | خطاهای جدی JVM یا محیط؛ معمولاً قابل بازیابی نیستند | خیر |
| Checked Exception | شرایط قابل پیش‌بینی خارجی؛ مانند فایل و شبکه | **بله** |
| Unchecked Exception | زیرکلاس `RuntimeException`؛ غالباً خطای طراحی یا منطق کد | خیر |

> [!warning] `Error` را Catch نکنید
> معمولاً نباید `Error`هایی نظیر `OutOfMemoryError` را مدیریت کنید. تمرکز باید روی علت اصلی، مانند Memory Leak یا پیکربندی نامناسب JVM باشد.

---

## ۸. `try`، `catch` و `finally`

### ۸.۱. ساختار کلی

```java
try {
    // کدی که ممکن است Exception ایجاد کند
} catch (SpecificException e) {
    // مدیریت Exception
} finally {
    // عملیات نهایی؛ مانند آزادسازی منابع
}
```

```java
public class ExceptionExample {
    public static void main(String[] args) {
        try {
            int result = 10 / 0;
            System.out.println(result);
        } catch (ArithmeticException e) {
            System.out.println("Calculation failed: " + e.getMessage());
        } finally {
            System.out.println("This block is executed.");
        }
    }
}
```

**خروجی:**

```text
Calculation failed: / by zero
This block is executed.
```

> [!note] رفتار `finally`
> بلاک `finally` تقریباً همیشه اجرا می‌شود، حتی اگر Exception رخ دهد یا در `try` دستور `return` داشته باشیم.  
> بااین‌حال در شرایط خاصی مانند `System.exit()` یا توقف JVM ممکن است اجرا نشود.

---

### ۸.۲. چندین `catch`

Exceptionهای اختصاصی‌تر باید قبل از Exceptionهای عمومی‌تر قرار بگیرند.

```java
try {
    int[] numbers = {1, 2, 3};
    System.out.println(numbers[10]);
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Invalid array index.");
} catch (Exception e) {
    System.out.println("Unexpected error: " + e.getMessage());
}
```

> [!danger] ترتیب اشتباه Catchها
> این کد کامپایل نمی‌شود، زیرا `Exception` همهٔ Exceptionهای بعدی را پوشش می‌دهد:
>
> ```java
> try {
>     // ...
> } catch (Exception e) {
>     // ...
> } catch (IOException e) { // ❌ Unreachable catch block
>     // ...
> }
> ```

---

### ۸.۳. Multi-catch

زمانی که منطق مدیریت چند Exception یکسان است:

```java
try {
    // ...
} catch (IOException | SQLException e) {
    System.out.println("Data access failed: " + e.getMessage());
}
```

در Multi-catch، متغیر `e` عملاً `final` است و نمی‌توان آن را مجدداً مقداردهی کرد.

---

## ۹. `throw` و `throws`

### `throw`: پرتاب واقعی Exception

برای ایجاد و پرتاب Exception به‌صورت دستی استفاده می‌شود.

```java
class AgeValidator {
    static void validate(int age) {
        if (age < 18) {
            throw new IllegalArgumentException(
                "Age must be at least 18."
            );
        }
    }
}
```

### `throws`: اعلام احتمال Exception در امضای متد

برای اعلام اینکه یک متد ممکن است Exception چک‌شده پرتاب کند:

```java
import java.io.IOException;

class FileService {
    static String readConfig() throws IOException {
        // خواندن فایل...
        return "config";
    }
}
```

| کلیدواژه | کاربرد | محل استفاده |
|---|---|---|
| `throw` | پرتاب یک Object از نوع `Throwable` | بدنهٔ متد یا بلاک |
| `throws` | اعلام Exception احتمالی | امضای متد |

---

## ۱۰. Exception سفارشی (Custom Exception)

Exception سفارشی برای خطاهای مشخص در Domain برنامه ایجاد می‌شود.

### Checked Custom Exception

```java
class OrderProcessingException extends Exception {
    public OrderProcessingException(String message) {
        super(message);
    }

    public OrderProcessingException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

استفاده:

```java
class OrderService {
    static void processOrder(int orderId)
            throws OrderProcessingException {

        if (orderId <= 0) {
            throw new OrderProcessingException(
                "Invalid order ID: " + orderId
            );
        }

        System.out.println("Order processed: " + orderId);
    }
}

public class Main {
    public static void main(String[] args) {
        try {
            OrderService.processOrder(-1);
        } catch (OrderProcessingException e) {
            System.err.println(e.getMessage());
        }
    }
}
```

### Unchecked Custom Exception

اگر خطا حاصل استفادهٔ اشتباه برنامه‌نویس از API است، معمولاً ارث‌بری از `RuntimeException` مناسب‌تر است:

```java
class InvalidPortException extends RuntimeException {
    public InvalidPortException(String message) {
        super(message);
    }
}
```

---

## ۱۱. Exception Chaining

هنگام تبدیل یک Exception سطح پایین به Exception معنی‌دارتر در لایهٔ بالاتر، علت اصلی را حفظ کنید.

```java
import java.io.IOException;

class ConfigService {
    static void loadConfig() {
        try {
            throw new IOException("File not found");
        } catch (IOException e) {
            throw new IllegalStateException(
                "Could not load application configuration",
                e
            );
        }
    }
}
```

```text
IllegalStateException
        │
        └── cause: IOException("File not found")
```

> [!important] چرا Chaining مهم است؟
> علت اصلی خطا در Stack Trace حفظ می‌شود؛ بنابراین Debug کردن در محیط‌های عملیاتی و سرویس‌های چندلایه بسیار ساده‌تر خواهد بود.

---

## ۱۲. `try-with-resources`

برای منابعی که `AutoCloseable` را پیاده‌سازی می‌کنند—مانند فایل، Socket، Stream و Connection دیتابیس—از `try-with-resources` استفاده کنید.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class FileReadExample {
    public static void main(String[] args) {
        try (BufferedReader reader =
                     new BufferedReader(new FileReader("data.txt"))) {

            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }

        } catch (IOException e) {
            System.err.println("Failed to read file: " + e.getMessage());
        }
    }
}
```

مزیت اصلی: `reader.close()` به‌صورت خودکار اجرا می‌شود؛ حتی اگر Exception رخ دهد.

> [!tip] Best Practice
> برای مدیریت منابع، `try-with-resources` را به `finally` و فراخوانی دستی `close()` ترجیح دهید.

---

## ۱۳. Assertions در Java

### ۱۳.۱. Assertion چیست؟

Assertion ابزاری برای بررسی **فرضیات داخلی توسعه‌دهنده** است. اگر شرط Assertion برقرار نباشد، `AssertionError` پرتاب می‌شود.

```java
assert condition;
assert condition : "error message";
```

```java
int value = 10;

assert value > 0 : "Value must be positive";

System.out.println("Value: " + value);
```

---

### ۱۳.۲. فعال‌سازی Assertion

Assertionها به‌صورت پیش‌فرض غیرفعال هستند.

```bash
# Compile
javac AssertionExample.java

# Run with assertions enabled
java -ea AssertionExample
```

فرم کامل‌تر:

```bash
java -enableassertions AssertionExample
```

غیرفعال‌سازی صریح:

```bash
java -da AssertionExample
```

---

### ۱۳.۳. Assertion برای چه چیزی مناسب است؟

| کاربرد مناسب | کاربرد نامناسب |
|---|---|
| بررسی invariant داخلی | اعتبارسنجی ورودی کاربر |
| بررسی پیش‌شرط‌های داخلی توسعه | مدیریت خطای فایل، شبکه یا دیتابیس |
| بررسی خروجی غیرممکن در منطق برنامه | منطق مهم Production |
| تست و Debug | جایگزین `if` و Exception |

نمونهٔ صحیح:

```java
static int getElement(int[] array, int index) {
    assert array != null : "array must not be null";
    assert index >= 0 && index < array.length : "Invalid index";

    return array[index];
}
```

نمونهٔ نامناسب:

```java
// ❌ غلط؛ چون در Production ممکن است Assertion غیرفعال باشد.
assert age >= 18 : "Age must be at least 18";
```

راه درست برای ورودی یا قانون تجاری:

```java
if (age < 18) {
    throw new IllegalArgumentException(
        "Age must be at least 18"
    );
}
```

---

## ۱۴. نکات کلیدی و Best Practiceها

> [!tip] Exception Handling
> - تا حد ممکن Exceptionهای مشخص را Catch کنید.
> - Exception را بی‌صدا نخورید (Empty Catch Block ننویسید).
> - علت اصلی خطا را با Exception Chaining حفظ کنید.
> - برای فایل، دیتابیس و Stream از `try-with-resources` استفاده کنید.
> - برای خطاهای قابل بازیابی یا مورد انتظار، پیام مناسب و قابل‌فهم بنویسید.

> [!warning] از Catch کردن بیش‌ازحد خودداری کنید
> این الگو اطلاعات خطا را از بین می‌برد:
>
> ```java
> try {
>     // ...
> } catch (Exception e) {
>     // هیچ کاری انجام نمی‌شود ❌
> }
> ```

> [!warning] `static` قابل تغییر و تست‌پذیری
> استفادهٔ زیاد از فیلدهای `static` قابل‌تغییر (`mutable`) می‌تواند باعث وابستگی سراسری، مشکل در تست‌های موازی و رفتار غیرقابل‌پیش‌بینی شود.  
> برای داده‌های وابسته به وضعیت، Instance Field و Dependency Injection انتخاب بهتری است.

---

## جمع‌بندی

```text
static
├── متعلق به Class است، نه Object
├── برای داده‌های مشترک، Constantها و Utility Methodها
├── با ClassName.member فراخوانی می‌شود
└── به اعضای Instance و this/super دسترسی مستقیم ندارد

Exception
├── Checked     → باید catch یا throws شود
├── Unchecked   → RuntimeException؛ اغلب خطای منطقی
├── try/catch   → مدیریت خطا
├── finally     → عملیات نهایی
├── throw       → پرتاب Exception
└── throws      → اعلام Exception احتمالی

Assertion
├── فقط برای فرضیات داخلی و توسعه
├── به‌طور پیش‌فرض غیرفعال است
└── با java -ea فعال می‌شود
```

 ---
tags:
  - java
  - arrays
  - data-structures
  - collections
  - java-8
---

# Arrays در Java

> [!abstract] سرفصل‌ها
> - مفهوم و ساخت آرایه
> - مقدار پیش‌فرض، `length` و ایندکس‌ها
> - آرایه‌های یک‌بعدی، چندبعدی و Jagged
> - پیمایش، کپی، مقایسه، جست‌وجو و مرتب‌سازی
> - آرایه‌های Object و تفاوت آن‌ها با Primitive
> - `varargs`، `final`، تبدیل به Collection و Stream
> - نکات عملکردی، محدودیت‌ها و هم‌زمانی

---

## ۱. آرایه چیست؟

**آرایه** ساختاری برای نگهداری مجموعه‌ای از مقادیر با **طول ثابت** و نوع مشخص است.

```java
int[] ports = {80, 443, 8080};
```

ویژگی‌های اصلی:

- همهٔ عناصر آرایه یک نوع مشخص دارند.
- اندازهٔ آرایه پس از ساخت تغییر نمی‌کند.
- عناصر با **Index** یا اندیس قابل دسترسی هستند.
- اولین اندیس `0` است.
- آرایه در Java یک **Object** است.
- آرایه می‌تواند داده‌های Primitive یا Referenceهای Object را نگهداری کند.

```text
int[] numbers = {10, 20, 30};

Index:              0    1    2
                 ┌────┬────┬────┐
numbers  ───────► │ 10 │ 20 │ 30 │
                 └────┴────┴────┘
```

> [!important] نکته
> طول آرایه با `length` مشخص می‌شود، نه با متد `length()`.
>
> ```java
> int size = numbers.length;
> ```
>
> در مقابل، برای `String` از متد `length()` استفاده می‌کنیم:
>
> ```java
> int textLength = "Java".length();
> ```

---

## ۲. تعریف و ایجاد آرایه

### ۲.۱. تعریف (Declaration)

```java
int[] numbers;
String[] names;
boolean[] flags;
```

این شکل نیز معتبر است، اما توصیه نمی‌شود:

```java
int numbers[]; // معتبر، اما خوانایی پایین‌تر
```

> [!tip] Best Practice
> همیشه `[]` را کنار نوع داده بنویسید:
>
> ```java
> int[] numbers;
> ```
>
> این روش خواناتر و مطابق قراردادهای Java است.

---

### ۲.۲. ایجاد آرایه با `new`

```java
int[] numbers = new int[5];
```

آرایهٔ بالا پنج خانه دارد:

```text
Index:              0    1    2    3    4
                 ┌────┬────┬────┬────┬────┐
numbers  ───────► │ 0  │ 0  │ 0  │ 0  │ 0  │
                 └────┴────┴────┴────┴────┘
```

مقداردهی عناصر:

```java
numbers[0] = 10;
numbers[1] = 20;
numbers[2] = 30;
numbers[3] = 40;
numbers[4] = 50;

System.out.println(numbers[0]); // 10
```

---

### ۲.۳. مقداردهی اولیهٔ مستقیم

```java
String[] names = {"Alice", "Bob", "Charlie"};

System.out.println(names[1]); // Bob
```

این کد معادل است با:

```java
String[] names = new String[] {
    "Alice",
    "Bob",
    "Charlie"
};
```

اگر آرایه را جدا از زمان Declaration مقداردهی می‌کنید، باید از `new Type[]` استفاده کنید:

```java
String[] names;

names = new String[] {"Alice", "Bob", "Charlie"};
```

کد زیر نامعتبر است:

```java
String[] names;
names = {"Alice", "Bob", "Charlie"}; // ❌ Compile Error
```

---

## ۳. اندیس و دسترسی به عناصر

اندیس‌ها از صفر شروع می‌شوند و آخرین اندیس برابر است با:

$$
\text{lastIndex} = \text{array.length} - 1
$$

```java
int[] numbers = {10, 20, 30};

System.out.println(numbers[0]); // 10
System.out.println(numbers[2]); // 30
```

دسترسی به اندیس نامعتبر باعث `ArrayIndexOutOfBoundsException` می‌شود:

```java
int[] numbers = {10, 20, 30};

System.out.println(numbers[3]); // ❌ Runtime Exception
```

> [!warning] خطای متداول در حلقه
>
> ```java
> for (int i = 0; i <= numbers.length; i++) { // ❌
>     System.out.println(numbers[i]);
> }
> ```
>
> شرط درست:
>
> ```java
> for (int i = 0; i < numbers.length; i++) { // ✅
>     System.out.println(numbers[i]);
> }
> ```

---

## ۴. مقادیر پیش‌فرض آرایه

هنگام ساخت آرایه با `new`، Java به‌صورت خودکار عناصر را مقداردهی می‌کند.

| نوع عنصر | مقدار پیش‌فرض |
|---|---:|
| `byte`، `short`، `int`، `long` | `0` |
| `float` | `0.0f` |
| `double` | `0.0d` |
| `char` | `'\u0000'` |
| `boolean` | `false` |
| Object / Reference | `null` |

```java
int[] ports = new int[3];
boolean[] enabled = new boolean[2];
String[] hosts = new String[2];

System.out.println(ports[0]);    // 0
System.out.println(enabled[0]);  // false
System.out.println(hosts[0]);    // null
```

> [!warning] Local Variable با عنصر آرایه تفاوت دارد
> متغیر محلی پیش از استفاده باید مقداردهی شود:
>
> ```java
> int number;
> // System.out.println(number); // ❌ Compile Error
> ```
>
> اما عناصر آرایهٔ ایجادشده با `new` مقدار پیش‌فرض دارند:
>
> ```java
> int[] numbers = new int[1];
> System.out.println(numbers[0]); // ✅ 0
> ```

---

## ۵. پیمایش آرایه

### ۵.۱. حلقهٔ `for` معمولی

زمانی مناسب است که به اندیس نیاز دارید یا می‌خواهید عناصر را تغییر دهید.

```java
int[] numbers = {10, 20, 30, 40, 50};

for (int i = 0; i < numbers.length; i++) {
    System.out.println(
        "index=" + i + ", value=" + numbers[i]
    );
}
```

تغییر مقدار عناصر:

```java
for (int i = 0; i < numbers.length; i++) {
    numbers[i] *= 2;
}
```

---

### ۵.۲. حلقهٔ `for-each`

برای خواندن سادهٔ عناصر مناسب است.

```java
int[] numbers = {10, 20, 30};

for (int number : numbers) {
    System.out.println(number);
}
```

اما با تغییر متغیر حلقه، آرایه تغییر نمی‌کند:

```java
int[] numbers = {1, 2, 3};

for (int number : numbers) {
    number *= 2; // فقط یک کپی محلی تغییر می‌کند
}

System.out.println(Arrays.toString(numbers));
// [1, 2, 3]
```

> [!note] انتخاب نوع حلقه
> - به **اندیس** یا تغییر عناصر نیاز دارید → `for`
> - فقط می‌خواهید عناصر را بخوانید → `for-each`

---

## ۶. آرایه‌های چندبعدی

در Java، آرایهٔ دوبعدی در عمل یک **آرایه از آرایه‌ها** است.

### ۶.۱. آرایهٔ دوبعدی

```java
int[][] matrix = new int[3][4];
```

```text
matrix
  │
  ├── row 0 → [0, 0, 0, 0]
  ├── row 1 → [0, 0, 0, 0]
  └── row 2 → [0, 0, 0, 0]
```

مقداردهی و دسترسی:

```java
int[][] matrix = new int[2][3];

matrix[0][0] = 1;
matrix[0][1] = 2;
matrix[1][0] = 3;
matrix[1][1] = 4;

System.out.println(matrix[0][0]); // 1
System.out.println(matrix[1][1]); // 4
```

مقداردهی مستقیم:

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

پیمایش:

```java
for (int[] row : matrix) {
    for (int value : row) {
        System.out.print(value + " ");
    }

    System.out.println();
}
```

خروجی:

```text
1 2 3
4 5 6
7 8 9
```

---

### ۶.۲. آرایهٔ Jagged یا نامنظم

چون هر سطر یک آرایهٔ مستقل است، سطرها می‌توانند طول متفاوتی داشته باشند.

```java
int[][] jaggedArray = new int[3][];

jaggedArray[0] = new int[2];
jaggedArray[1] = new int[4];
jaggedArray[2] = new int[1];

jaggedArray[0][0] = 10;
jaggedArray[1][3] = 40;
jaggedArray[2][0] = 50;
```

یا به‌شکل ساده‌تر:

```java
int[][] serverPorts = {
    {80, 443},
    {22},
    {8080, 8081, 9090}
};
```

```text
serverPorts[0] → [80, 443]
serverPorts[1] → [22]
serverPorts[2] → [8080, 8081, 9090]
```

> [!tip] کاربرد Jagged Array
> برای داده‌هایی مناسب است که هر گروه تعداد عضو متفاوتی دارد؛ مانند پورت‌های سرویس‌ها، نمرات دانشجویان، یا آیتم‌های هر سفارش.

---

### ۶.۳. آرایهٔ سه‌بعدی

```java
int[][][] cube = new int[2][3][4];

cube[0][0][0] = 1;
cube[0][0][1] = 2;

System.out.println(cube[0][0][0]); // 1
```

> [!warning] خوانایی
> بیش از دو یا سه بعد، اغلب خوانایی کد را کاهش می‌دهد. در بسیاری از سناریوها بهتر است از Objectهای مدل‌سازی‌شده یا Collectionها استفاده شود.

---

## ۷. آرایه‌های Object

آرایه می‌تواند Object ذخیره کند. در این حالت، هر خانه یک **Reference** است.

```java
class Person {
    String name;

    Person(String name) {
        this.name = name;
    }
}

public class Main {
    public static void main(String[] args) {
        Person[] people = new Person[3];

        people[0] = new Person("Alice");
        people[1] = new Person("Bob");
        people[2] = new Person("Charlie");

        for (Person person : people) {
            System.out.println(person.name);
        }
    }
}
```

تا زمانی که Object ایجاد نشده باشد، خانه‌های آرایه `null` هستند:

```java
Person[] people = new Person[2];

System.out.println(people[0]); // null
```

پس این کد `NullPointerException` ایجاد می‌کند:

```java
Person[] people = new Person[2];

// System.out.println(people[0].name); // ❌
```

---

## ۸. کلاس کمکی `Arrays`

بیشتر عملیات‌های رایج روی آرایه‌ها از طریق `java.util.Arrays` انجام می‌شود.

```java
import java.util.Arrays;
```

### ۸.۱. نمایش آرایه با `toString`

چاپ مستقیم آرایه نتیجهٔ قابل‌خواندن نمی‌دهد:

```java
int[] numbers = {1, 2, 3};

System.out.println(numbers);
// [I@5e91993f  ← وابسته به JVM و غیرقابل‌استفاده
```

روش درست:

```java
System.out.println(Arrays.toString(numbers));
// [1, 2, 3]
```

برای آرایهٔ چندبعدی:

```java
int[][] matrix = {
    {1, 2},
    {3, 4}
};

System.out.println(Arrays.deepToString(matrix));
// [[1, 2], [3, 4]]
```

---

### ۸.۲. مرتب‌سازی با `sort`

```java
int[] numbers = {5, 3, 1, 4, 2};

Arrays.sort(numbers);

System.out.println(Arrays.toString(numbers));
// [1, 2, 3, 4, 5]
```

مرتب‌سازی یک بازه:

```java
int[] numbers = {9, 5, 3, 1, 7};

Arrays.sort(numbers, 1, 4);

System.out.println(Arrays.toString(numbers));
// [9, 1, 3, 5, 7]
```

> [!note] نکته
> در `Arrays.sort(array, fromIndex, toIndex)`، مقدار `toIndex` در بازه نیست.

---

### ۸.۳. جست‌وجو با `binarySearch`

`binarySearch` فقط روی آرایهٔ **مرتب‌شده** معتبر است.

```java
int[] numbers = {5, 1, 4, 2, 3};

Arrays.sort(numbers);

int index = Arrays.binarySearch(numbers, 3);

System.out.println(index); // 2
```

اگر مقدار پیدا نشود، خروجی منفی است:

```java
int index = Arrays.binarySearch(numbers, 10);

System.out.println(index); // یک عدد منفی
```

رابطهٔ محل درج پیشنهادی:

$$
\text{insertionPoint} = -(\text{result} + 1)
$$

```java
int result = Arrays.binarySearch(numbers, 10);

if (result >= 0) {
    System.out.println("Found at index: " + result);
} else {
    int insertionPoint = -(result + 1);
    System.out.println("Not found. Insert at: " + insertionPoint);
}
```

---

### ۸.۴. مقایسهٔ آرایه‌ها

استفاده از `==` فقط Referenceها را مقایسه می‌کند:

```java
int[] first = {1, 2, 3};
int[] second = {1, 2, 3};

System.out.println(first == second); // false
```

برای مقایسهٔ مقدار عناصر از `Arrays.equals()` استفاده کنید:

```java
System.out.println(Arrays.equals(first, second)); // true
```

برای آرایهٔ چندبعدی:

```java
int[][] firstMatrix = {{1, 2}, {3, 4}};
int[][] secondMatrix = {{1, 2}, {3, 4}};

System.out.println(Arrays.deepEquals(firstMatrix, secondMatrix));
// true
```

---

### ۸.۵. کپی‌کردن آرایه

#### با `clone()`

```java
int[] source = {1, 2, 3};

int[] copy = source.clone();

System.out.println(Arrays.toString(copy));
// [1, 2, 3]
```

#### با `Arrays.copyOf()`

```java
int[] source = {1, 2, 3};

int[] copy = Arrays.copyOf(source, 5);

System.out.println(Arrays.toString(copy));
// [1, 2, 3, 0, 0]
```

#### با `System.arraycopy()`

```java
int[] source = {1, 2, 3, 4, 5};
int[] destination = new int[5];

System.arraycopy(
    source,      // مبدأ
    0,           // اندیس شروع مبدأ
    destination, // مقصد
    0,           // اندیس شروع مقصد
    source.length
);

System.out.println(Arrays.toString(destination));
// [1, 2, 3, 4, 5]
```

| روش | کاربرد |
|---|---|
| `clone()` | کپی کامل و ساده |
| `Arrays.copyOf()` | کپی با امکان تغییر طول |
| `System.arraycopy()` | کپی یک بخش مشخص از آرایه |
| `Arrays.copyOfRange()` | کپی یک بازه از عناصر |

---

## ۹. کپی سطحی (Shallow Copy)

برای آرایه‌های Primitive، کپی آرایه مستقل از آرایهٔ اصلی است:

```java
int[] source = {1, 2, 3};
int[] copy = source.clone();

copy[0] = 99;

System.out.println(Arrays.toString(source)); // [1, 2, 3]
System.out.println(Arrays.toString(copy));   // [99, 2, 3]
```

اما در آرایه‌های Object، خود Objectها کپی نمی‌شوند؛ فقط Referenceها کپی می‌شوند.

```java
Person[] source = {
    new Person("Alice")
};

Person[] copy = source.clone();

copy[0].name = "Sara";

System.out.println(source[0].name); // Sara
```

```text
source[0] ──┐
            ├──► Person Object
copy[0] ────┘
```

> [!warning] نکته
> `clone()`، `copyOf()` و `arraycopy()` برای آرایه‌های Object معمولاً **Shallow Copy** هستند.  
> برای کپی مستقل Objectها باید هر Object را جداگانه کپی کنید.

---

## ۱۰. جست‌وجوی خطی (Linear Search)

برای آرایه‌های مرتب‌نشده، جست‌وجوی خطی انتخاب ساده‌ای است.

```java
public class LinearSearchExample {

    static int findIndex(int[] numbers, int target) {
        for (int i = 0; i < numbers.length; i++) {
            if (numbers[i] == target) {
                return i;
            }
        }

        return -1;
    }

    public static void main(String[] args) {
        int[] numbers = {20, 10, 40, 30};

        int index = findIndex(numbers, 40);

        System.out.println(index); // 2
    }
}
```

| الگوریتم | پیش‌نیاز | پیچیدگی زمانی |
|---|---|---:|
| Linear Search | ندارد | $O(n)$ |
| Binary Search | آرایه باید مرتب باشد | $O(\log n)$ |

---

## ۱۱. آرایه و `varargs`

`varargs` اجازه می‌دهد تعداد متغیری از آرگومان‌ها به متد ارسال شود. در پشت صحنه، `varargs` یک آرایه است.

```java
public class VarargsExample {

    static void printNumbers(int... numbers) {
        for (int number : numbers) {
            System.out.println(number);
        }
    }

    public static void main(String[] args) {
        printNumbers(10, 20, 30);
    }
}
```

فراخوانی بالا معادل ارسال آرایه است:

```java
int[] values = {10, 20, 30};

printNumbers(values);
```

قواعد مهم:

- در هر متد فقط یک `varargs` مجاز است.
- `varargs` باید آخرین Parameter متد باشد.

```java
static void log(String level, String... messages) {
    // درست
}

// static void invalid(String... items, int code) {} // ❌
```

---

## ۱۲. آرایهٔ `final`

`final` بودن آرایه یعنی Reference آن قابل تغییر نیست؛ نه اینکه محتوای آرایه غیرقابل تغییر باشد.

```java
final int[] numbers = {1, 2, 3};

numbers[0] = 99; // ✅ مجاز

// numbers = new int[] {4, 5, 6}; // ❌ غیرمجاز
```

```text
final int[] numbers
      │
      └── Reference ثابت است

محتوای Object آرایه همچنان می‌تواند تغییر کند.
```

---

## ۱۳. تبدیل Array و Collection

### ۱۳.۱. تبدیل آرایهٔ Object به `List`

```java
String[] names = {"Alice", "Bob", "Charlie"};

List<String> namesList = Arrays.asList(names);

System.out.println(namesList);
// [Alice, Bob, Charlie]
```

> [!warning] محدودیت `Arrays.asList()`
> لیست حاصل اندازهٔ ثابت دارد. تغییر مقدار ممکن است، اما `add` و `remove` مجاز نیستند.
>
> ```java
> List<String> namesList = Arrays.asList(names);
>
> namesList.set(0, "Sara"); // ✅
> // namesList.add("Reza"); // ❌ UnsupportedOperationException
> ```

برای یک `ArrayList` مستقل و قابل تغییر:

```java
List<String> namesList = new ArrayList<>(
    Arrays.asList(names)
);

namesList.add("Sara"); // ✅
```

از Java 9 به بعد:

```java
List<String> namesList = new ArrayList<>(
    List.of(names)
);
```

---

### ۱۳.۲. تلهٔ `Arrays.asList()` برای Primitive Array

```java
int[] ports = {80, 443};

List<int[]> list = Arrays.asList(ports);

System.out.println(list.size()); // 1
```

دلیل: `int[]` یک Object واحد است و به `Integer[]` تبدیل نمی‌شود.

راه صحیح برای تبدیل به `List<Integer>`:

```java
int[] ports = {80, 443};

List<Integer> portList = Arrays.stream(ports)
        .boxed()
        .toList();

System.out.println(portList);
// [80, 443]
```

---

### ۱۳.۳. تبدیل به `Set`

```java
String[] hosts = {"web-01", "web-02", "web-01"};

Set<String> uniqueHosts = new HashSet<>(
    Arrays.asList(hosts)
);

System.out.println(uniqueHosts);
// [web-01, web-02] - ترتیب تضمین‌شده نیست
```

---

## ۱۴. پردازش آرایه با Stream

از Java 8 می‌توان آرایه‌ها را با Stream پردازش کرد.

```java
import java.util.Arrays;

int[] numbers = {1, 2, 3, 4, 5};

Arrays.stream(numbers)
        .filter(number -> number % 2 == 0)
        .forEach(System.out::println);
```

خروجی:

```text
2
4
```

نمونهٔ محاسبهٔ مجموع مربع‌ها:

```java
int[] numbers = {1, 2, 3, 4, 5};

int sumOfSquares = Arrays.stream(numbers)
        .map(number -> number * number)
        .sum();

System.out.println(sumOfSquares); // 55
```

> [!note] چه زمانی Stream؟
> Stream برای پردازش‌های خوانا، فیلتر، تبدیل و تجمیع داده مناسب است.  
> در منطق‌های ساده یا Performance-critical، حلقهٔ `for` اغلب ساده‌تر و گاهی سریع‌تر است.

---

## ۱۵. آرایه به‌عنوان Local Variable یا Field

اصطلاح «Global Variable» در Java معنای دقیق ندارد. آرایه معمولاً یکی از این حالت‌هاست:

| نوع | محل تعریف | طول عمر | نمونه |
|---|---|---|---|
| Local Array | درون متد یا بلاک | تا پایان اجرای Scope | `int[] values` داخل متد |
| Instance Field | درون کلاس، بدون `static` | تا زمانی که Object قابل دسترس است | هر Object آرایهٔ خود را دارد |
| Static Field | درون کلاس، با `static` | در سطح Class | میان همهٔ Objectها مشترک است |

```java
class ServerConfig {
    static final int[] DEFAULT_PORTS = {80, 443}; // Static Field

    private final String[] hosts; // Instance Field

    ServerConfig(String[] hosts) {
        this.hosts = hosts;
    }

    void printPorts() {
        int[] localPorts = {8080, 9090}; // Local Array

        System.out.println(Arrays.toString(localPorts));
    }
}
```

---

## ۱۶. آرایه‌ها و هم‌زمانی (Concurrency)

آرایهٔ معمولی Thread-safe نیست. اگر چند Thread هم‌زمان روی آن بنویسند، ممکن است **Race Condition** رخ دهد.

```java
class SharedCounter {
    private final int[] counters = new int[1];

    public synchronized void increment() {
        counters[0]++;
    }

    public synchronized int get() {
        return counters[0];
    }
}
```

> [!important] نکتهٔ عملی
> `synchronized` برای عملیات ساده مناسب است، اما برای شمارنده‌ها در محیط‌های concurrent معمولاً `AtomicInteger` انتخاب بهتری است:
>
> ```java
> import java.util.concurrent.atomic.AtomicInteger;
>
> AtomicInteger counter = new AtomicInteger(0);
>
> counter.incrementAndGet();
> ```

برای آرایه‌هایی که عناصرشان مستقل و هم‌زمان تغییر می‌کنند، `AtomicIntegerArray` نیز وجود دارد:

```java
import java.util.concurrent.atomic.AtomicIntegerArray;

AtomicIntegerArray metrics = new AtomicIntegerArray(10);

metrics.incrementAndGet(0);
```

---

## ۱۷. Serialization آرایه

خود آرایه‌ها در Java قابل Serialization هستند؛ اما اگر آرایه شامل Object باشد، تمام Objectهای موجود نیز باید `Serializable` باشند.

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.Arrays;

public class ArraySerializationExample {

    public static void main(String[] args)
            throws IOException, ClassNotFoundException {

        int[] ports = {80, 443, 8080};

        try (ObjectOutputStream output = new ObjectOutputStream(
                new FileOutputStream("ports.ser"))) {

            output.writeObject(ports);
        }

        try (ObjectInputStream input = new ObjectInputStream(
                new FileInputStream("ports.ser"))) {

            int[] restoredPorts = (int[]) input.readObject();

            System.out.println(Arrays.toString(restoredPorts));
        }
    }
}
```

> [!danger] نکتهٔ امنیتی
> از `ObjectInputStream` برای خواندن دادهٔ غیرقابل‌اعتماد استفاده نکنید؛ Deserialization جاوا می‌تواند ریسک امنیتی داشته باشد.  
> برای داده‌های خارجی معمولاً JSON، YAML یا فرمت‌های مشخص و اعتبارسنجی‌شده انتخاب امن‌تر و رایج‌تری هستند.

---

## ۱۸. Array یا `ArrayList`؟

| ویژگی | Array | `ArrayList` |
|---|---|---|
| طول | ثابت | پویا |
| نوع Primitive | پشتیبانی مستقیم؛ مانند `int[]` | نیازمند Wrapper؛ مانند `Integer` |
| دسترسی با اندیس | سریع؛ $O(1)$ | سریع؛ $O(1)$ |
| افزودن/حذف انتهایی | اندازه ثابت | معمولاً $O(1)$ amortized |
| امکانات API | محدود | متدهای فراوان |
| کاربرد مناسب | طول معلوم، Performance، داده‌های کم‌سطح | داده‌های با طول متغیر و عملیات Collection |

```java
import java.util.ArrayList;
import java.util.List;

List<String> services = new ArrayList<>();

services.add("nginx");
services.add("postgres");
services.add("redis");

System.out.println(services);
```

> [!warning] نکته
> `ArrayList` یک **Wrapper Class** نیست.  
> `Integer`، `Double` و `Boolean` نمونه‌هایی از Wrapper Class هستند.  
> `ArrayList` یک پیاده‌سازی از `List` است که در پشت‌صحنه معمولاً از آرایه استفاده می‌کند.

---

## ۱۹. خطاهای رایج

### ۱۹.۱. مقایسه با `==`

```java
int[] first = {1, 2};
int[] second = {1, 2};

System.out.println(first == second); // false
```

راه درست:

```java
System.out.println(Arrays.equals(first, second)); // true
```

---

### ۱۹.۲. تغییر اندازهٔ مستقیم

```java
int[] numbers = new int[3];

// numbers.length = 10; // ❌ غیرمجاز
```

راه درست:

```java
numbers = Arrays.copyOf(numbers, 10);
```

---

### ۱۹.۳. فراموشی `null` در آرایه‌های Object

```java
String[] hosts = new String[3];

// System.out.println(hosts[0].toUpperCase()); // ❌ NullPointerException
```

راه امن:

```java
if (hosts[0] != null) {
    System.out.println(hosts[0].toUpperCase());
}
```

---

## جمع‌بندی

```text
Array
├── ساختار داده با طول ثابت
├── اندیس‌ها از صفر شروع می‌شوند
├── length یک Field است، نه Method
├── می‌تواند Primitive یا Object Reference نگهداری کند
├── با new مقدارهای پیش‌فرض می‌گیرد
├── برای مقایسه از Arrays.equals استفاده می‌شود
├── برای مرتب‌سازی از Arrays.sort استفاده می‌شود
├── binarySearch به آرایهٔ مرتب نیاز دارد
├── کپی Object Array معمولاً Shallow Copy است
├── varargs در پشت‌صحنه Array است
└── برای اندازهٔ پویا معمولاً ArrayList مناسب‌تر است
```

> [!tip] چک‌لیست انتخاب
> - تعداد عناصر از ابتدا مشخص و ثابت است → `Array`
> - دادهٔ Primitive و کارایی مهم است → `int[]`، `long[]` و مشابه آن
> - افزودن و حذف پویا نیاز دارید → `ArrayList`
> - کلید و مقدار دارید → `Map`
> - یکتایی عناصر مهم است → `Set`


  

  

1---
tags:
  - java
  - enum
  - constants
  - oop
  - collections
---

# Enum در Java

> [!abstract] سرفصل‌ها
> - مفهوم و تعریف `enum`
> - متدهای داخلی Enum
> - فیلد، Constructor و متد سفارشی
> - `switch` و مقایسهٔ Enum
> - متد Abstract و پیاده‌سازی Interface
> - `EnumSet` و `EnumMap`
> - استفاده در Database، Spring و Serialization
> - نکات طراحی و Best Practiceها

---

## ۱. Enum چیست؟

`enum` مخفف **Enumeration** است و برای تعریف یک نوع داده با مجموعه‌ای **محدود، ثابت و مشخص** از مقادیر استفاده می‌شود.

برای مثال، وضعیت سفارش فقط می‌تواند یکی از مقدارهای زیر باشد:

```text
NEW
PROCESSING
SHIPPED
DELIVERED
CANCELED
```

تعریف آن با `enum`:

```java
public enum OrderStatus {
    NEW,
    PROCESSING,
    SHIPPED,
    DELIVERED,
    CANCELED
}
```

استفاده:

```java
OrderStatus status = OrderStatus.PROCESSING;
```

> [!important] کاربرد اصلی
> هرگاه یک متغیر فقط باید یکی از تعداد محدودی مقدار معتبر را داشته باشد، `enum` انتخاب مناسبی است.
>
> نمونه‌ها:
> - وضعیت سفارش: `NEW`، `CANCELED`
> - نقش کاربر: `ADMIN`، `USER`
> - محیط اجرا: `DEV`، `STAGING`، `PRODUCTION`
> - سطح لاگ: `DEBUG`، `INFO`، `WARN`، `ERROR`
> - روزهای هفته یا ماه‌ها

---

## ۲. چرا Enum بهتر از `String` یا عدد است؟

بدون Enum، ممکن است وضعیت را با `String` نگهداری کنیم:

```java
String status = "PROCESSING";
```

اما این کد خطای تایپی را در زمان کامپایل تشخیص نمی‌دهد:

```java
String status = "PROCESING"; // ❌ غلط املایی، اما Compile می‌شود
```

با Enum:

```java
OrderStatus status = OrderStatus.PROCESSING;

// OrderStatus invalid = OrderStatus.PROCESING; // ❌ Compile Error
```

| ویژگی | `String` / `int` | `enum` |
|---|---|---|
| Type Safety | ضعیف | قوی |
| تشخیص غلط تایپی | اغلب در Runtime | در Compile Time |
| خوانایی | کمتر | بیشتر |
| رفتار و متد اختصاصی | پراکنده | قابل نگهداری در Enum |
| امکان مقادیر نامعتبر | دارد | ندارد |

---

## ۳. تعریف Enum

ساختار پایه:

```java
public enum Day {
    SUNDAY,
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY
}
```

استفاده:

```java
Day today = Day.MONDAY;

System.out.println(today); // MONDAY
```

هر مقدار داخل `enum` یک **Object یکتا** از همان نوع است:

```java
Day first = Day.MONDAY;
Day second = Day.MONDAY;

System.out.println(first == second); // true
```

> [!note] قرارداد نام‌گذاری
> مقدارهای Enum معمولاً با حروف بزرگ و `_` نوشته می‌شوند:
>
> ```java
> public enum Environment {
>     DEVELOPMENT,
>     STAGING,
>     PRODUCTION
> }
> ```

---

## ۴. Enum در واقع یک Class است

یک `enum` در Java نوع خاصی از Class است که به‌شکل ضمنی از `java.lang.Enum` ارث‌بری می‌کند.

```text
java.lang.Object
        │
        └── java.lang.Enum<E>
                │
                └── YourEnum
```

بنابراین می‌تواند موارد زیر را داشته باشد:

- Field
- Constructor
- Method
- پیاده‌سازی Interface
- Static Method

اما محدودیت‌های مهمی نیز دارد:

- نمی‌تواند از Class دیگری `extends` کند.
- به‌شکل ضمنی `final` است؛ بنابراین قابل Extend شدن نیست.
- نمی‌توان با `new` از آن Object ساخت.
- Constructor آن نمی‌تواند `public` یا `protected` باشد.

```java
// OrderStatus status = new OrderStatus(); // ❌ غیرمجاز
```

---

## ۵. متدهای آمادهٔ Enum

فرض کنید:

```java
public enum Day {
    SUNDAY,
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY
}
```

### ۵.۱. `values()`

تمام مقدارهای Enum را در قالب آرایه برمی‌گرداند.

```java
for (Day day : Day.values()) {
    System.out.println(day);
}
```

خروجی:

```text
SUNDAY
MONDAY
TUESDAY
WEDNESDAY
THURSDAY
FRIDAY
SATURDAY
```

> [!note] نکته
> `values()` یک متد Static است که Compiler برای Enum تولید می‌کند؛ این متد به‌شکل مستقیم در `java.lang.Enum` تعریف نشده است.

---

### ۵.۲. `valueOf(String)`

یک نام `String` را به مقدار متناظر Enum تبدیل می‌کند.

```java
Day day = Day.valueOf("MONDAY");

System.out.println(day); // MONDAY
```

اگر نام وجود نداشته باشد، `IllegalArgumentException` رخ می‌دهد:

```java
Day day = Day.valueOf("HOLIDAY"); // ❌ IllegalArgumentException
```

نسخهٔ امن برای ورودی کاربر یا API:

```java
import java.util.Locale;
import java.util.Optional;

public class EnumUtils {

    static Optional<Day> parseDay(String input) {
        if (input == null || input.isBlank()) {
            return Optional.empty();
        }

        try {
            return Optional.of(
                Day.valueOf(input.trim().toUpperCase(Locale.ROOT))
            );
        } catch (IllegalArgumentException e) {
            return Optional.empty();
        }
    }
}
```

---

### ۵.۳. `name()`

نام دقیق ثابت Enum را به‌صورت `String` بازمی‌گرداند.

```java
Day day = Day.MONDAY;

System.out.println(day.name()); // MONDAY
```

> [!warning] `name()` برای نمایش به کاربر مناسب نیست
> `name()` به نام ثابت در کد وابسته است. برای نمایش در UI یا API بهتر است فیلد جداگانه مانند `displayName` یا `code` داشته باشید.

---

### ۵.۴. `ordinal()`

ترتیب ثابت Enum را از صفر برمی‌گرداند.

```java
Day day = Day.MONDAY;

System.out.println(day.ordinal()); // 1
```

```text
SUNDAY     → 0
MONDAY     → 1
TUESDAY    → 2
...
```

> [!danger] از `ordinal()` در Business Logic یا Database استفاده نکنید
> اگر ترتیب ثابت‌ها تغییر کند یا مقدار جدیدی میان آن‌ها اضافه شود، مقدار `ordinal` تغییر می‌کند و داده‌ها یا منطق برنامه ممکن است خراب شود.
>
> به‌جای آن، یک مقدار صریح تعریف کنید:
>
> ```java
> public enum Priority {
>     LOW(10),
>     MEDIUM(20),
>     HIGH(30);
>
>     private final int code;
>
>     Priority(int code) {
>         this.code = code;
>     }
>
>     public int getCode() {
>         return code;
>     }
> }
> ```

---

### ۵.۵. `toString()`

به‌شکل پیش‌فرض همان نام Enum را برمی‌گرداند:

```java
System.out.println(Day.MONDAY.toString()); // MONDAY
```

اما می‌توان آن را Override کرد:

```java
public enum Environment {
    DEVELOPMENT,
    STAGING,
    PRODUCTION;

    @Override
    public String toString() {
        return name().toLowerCase();
    }
}
```

```java
System.out.println(Environment.PRODUCTION); // production
```

> [!tip] Best Practice
> برای منطق برنامه و Serialization به `name()` متکی نباشید مگر اینکه نام‌های Enum بخشی از قرارداد رسمی شما باشند.  
> برای نمایش، یک `displayName` و برای ارتباط با API/Database یک `code` صریح تعریف کنید.

---

### ۵.۶. `compareTo()`

Enumها ترتیب تعریف‌شدن دارند و `Comparable` هستند.

```java
Day first = Day.MONDAY;
Day second = Day.FRIDAY;

System.out.println(first.compareTo(second)); // مقدار منفی
```

نتیجه:

- منفی → مقدار اول قبل از مقدار دوم تعریف شده است.
- صفر → هر دو یک مقدار هستند.
- مثبت → مقدار اول بعد از مقدار دوم تعریف شده است.

---

## ۶. Enum با Field، Constructor و Method

هر مقدار Enum می‌تواند داده‌های همراه خود داشته باشد.

```java
public enum Day {
    SUNDAY(1, false),
    MONDAY(2, true),
    TUESDAY(3, true),
    WEDNESDAY(4, true),
    THURSDAY(5, true),
    FRIDAY(6, true),
    SATURDAY(7, false);

    private final int dayNumber;
    private final boolean workingDay;

    Day(int dayNumber, boolean workingDay) {
        this.dayNumber = dayNumber;
        this.workingDay = workingDay;
    }

    public int getDayNumber() {
        return dayNumber;
    }

    public boolean isWorkingDay() {
        return workingDay;
    }
}
```

استفاده:

```java
Day day = Day.MONDAY;

System.out.println(day.getDayNumber()); // 2
System.out.println(day.isWorkingDay()); // true
```

> [!important] Constructor Enum
> Constructor در Enum همیشه خصوصی است؛ حتی اگر کلمهٔ `private` را ننویسید.
>
> ```java
> Day(int dayNumber) {
>     // به‌طور ضمنی private است.
> }
> ```

---

## ۷. پیدا کردن Enum از روی Code

در برنامه‌های واقعی، معمولاً یک Code ثابت برای API، Database یا Configuration نیاز دارید.

```java
import java.util.Arrays;

public enum Environment {
    DEVELOPMENT("dev"),
    STAGING("stg"),
    PRODUCTION("prod");

    private final String code;

    Environment(String code) {
        this.code = code;
    }

    public String getCode() {
        return code;
    }

    public static Environment fromCode(String code) {
        return Arrays.stream(values())
                .filter(environment -> environment.code.equals(code))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException(
                    "Unknown environment code: " + code
                ));
    }
}
```

```java
Environment environment = Environment.fromCode("prod");

System.out.println(environment); // PRODUCTION
```

برای Enumهای پرتعداد یا Performance-sensitive، از Map استفاده کنید:

```java
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

public enum Environment {
    DEVELOPMENT("dev"),
    STAGING("stg"),
    PRODUCTION("prod");

    private final String code;

    private static final Map<String, Environment> BY_CODE =
            Map.of(
                "dev", DEVELOPMENT,
                "stg", STAGING,
                "prod", PRODUCTION
            );

    Environment(String code) {
        this.code = code;
    }

    public static Environment fromCode(String code) {
        Environment environment = BY_CODE.get(code);

        if (environment == null) {
            throw new IllegalArgumentException(
                "Unknown environment code: " + code
            );
        }

        return environment;
    }
}
```

---

## ۸. استفاده از Enum در `switch`

### شکل کلاسیک

```java
Day day = Day.MONDAY;

switch (day) {
    case MONDAY:
        System.out.println("Start of the work week.");
        break;

    case FRIDAY:
        System.out.println("End of the work week.");
        break;

    default:
        System.out.println("Other day.");
}
```

در `switch` مربوط به Enum، نام Enum را در `case` نمی‌نویسیم:

```java
switch (day) {
    // case Day.MONDAY: // ❌
    case MONDAY:        // ✅
        break;

    default:
        break;
}
```

---

### Switch Expression؛ از Java 14 به بعد

```java
public class DayTypeExample {

    static String getDayType(Day day) {
        return switch (day) {
            case SATURDAY, SUNDAY -> "Weekend";

            case MONDAY, TUESDAY, WEDNESDAY,
                    THURSDAY, FRIDAY -> "Working day";
        };
    }
}
```

> [!tip] مزیت Switch Expression
> اگر تمام مقدارهای Enum پوشش داده شوند، `default` لازم نیست.  
> در صورت اضافه شدن مقدار جدید به Enum، Compiler کمک می‌کند تا `switch`های ناقص را شناسایی کنید.

---

## ۹. مقایسهٔ Enumها

برای مقایسهٔ Enumها از `==` استفاده کنید:

```java
OrderStatus current = OrderStatus.PROCESSING;

if (current == OrderStatus.PROCESSING) {
    System.out.println("Order is being processed.");
}
```

`equals()` نیز صحیح است:

```java
boolean same = current.equals(OrderStatus.PROCESSING);
```

اما `==` ترجیح دارد:

- واضح‌تر است.
- Null-safe است.
- به‌دلیل یکتا بودن Instanceهای Enum کاملاً درست عمل می‌کند.

```java
OrderStatus status = null;

System.out.println(status == OrderStatus.NEW); // false

// System.out.println(status.equals(OrderStatus.NEW)); // ❌ NPE
```

---

## ۱۰. متد Abstract در Enum

هر مقدار Enum می‌تواند پیاده‌سازی مخصوص خود را از یک متد Abstract داشته باشد.

```java
public enum Operation {
    PLUS {
        @Override
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS {
        @Override
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES {
        @Override
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE {
        @Override
        public double apply(double x, double y) {
            if (y == 0) {
                throw new ArithmeticException(
                    "Division by zero is not allowed."
                );
            }

            return x / y;
        }
    };

    public abstract double apply(double x, double y);
}
```

استفاده:

```java
double result = Operation.PLUS.apply(3, 4);

System.out.println(result); // 7.0
```

> [!note] کاربرد
> این روش نمونه‌ای از پیاده‌سازی **Strategy Pattern** با Enum است و زمانی مفید است که تعداد Strategyها محدود و از ابتدا مشخص باشد.

---

## ۱۱. پیاده‌سازی Interface توسط Enum

Enum می‌تواند یک یا چند Interface را پیاده‌سازی کند.

```java
public interface Printable {
    void print();
}
```

```java
public enum DocumentType implements Printable {
    WORD {
        @Override
        public void print() {
            System.out.println("Printing Word document.");
        }
    },
    PDF {
        @Override
        public void print() {
            System.out.println("Printing PDF document.");
        }
    },
    EXCEL {
        @Override
        public void print() {
            System.out.println("Printing Excel document.");
        }
    }
}
```

```java
Printable document = DocumentType.PDF;

document.print();
// Printing PDF document.
```

نسخهٔ ساده‌تر، در صورتی که رفتار همهٔ مقدارها مشترک باشد:

```java
public enum LogLevel implements Printable {
    DEBUG,
    INFO,
    WARN,
    ERROR;

    @Override
    public void print() {
        System.out.println("Log level: " + name());
    }
}
```

---

## ۱۲. Enum در سناریوهای واقعی

### ۱۲.۱. وضعیت سفارش

```java
public enum OrderStatus {
    NEW,
    PROCESSING,
    SHIPPED,
    DELIVERED,
    CANCELED;

    public boolean isFinal() {
        return this == DELIVERED || this == CANCELED;
    }

    public boolean canBeCanceled() {
        return this == NEW || this == PROCESSING;
    }
}
```

```java
OrderStatus status = OrderStatus.PROCESSING;

System.out.println(status.canBeCanceled()); // true
System.out.println(status.isFinal());       // false
```

> [!warning] نکتهٔ منطقی
> `SHIPPED` لزوماً وضعیت نهایی نیست؛ سفارش پس از ارسال ممکن است به `DELIVERED` برسد.  
> تعریف «نهایی» باید مطابق Ruleهای واقعی Domain پروژه باشد.

---

### ۱۲.۲. محیط اجرای برنامه

```java
public enum Environment {
    DEVELOPMENT,
    TEST,
    STAGING,
    PRODUCTION;

    public boolean isProduction() {
        return this == PRODUCTION;
    }
}
```

```java
if (environment.isProduction()) {
    System.out.println("Production-specific configuration enabled.");
}
```

---

### ۱۲.۳. نقش کاربر

```java
public enum UserRole {
    ADMIN("Administrator"),
    CUSTOMER("Customer"),
    GUEST("Guest");

    private final String displayName;

    UserRole(String displayName) {
        this.displayName = displayName;
    }

    public String getDisplayName() {
        return displayName;
    }
}
```

---

## ۱۳. `EnumSet`

`EnumSet` یک `Set` بهینه‌شده مخصوص Enumها است.

ویژگی‌ها:

- فقط برای نوع‌های Enum قابل استفاده است.
- تکراری نیست.
- بسیار سریع و کم‌مصرف است.
- ترتیب پیمایش مطابق ترتیب تعریف Enum است.

```java
import java.util.EnumSet;

EnumSet<Day> weekend = EnumSet.of(
    Day.SATURDAY,
    Day.SUNDAY
);

System.out.println(weekend.contains(Day.SATURDAY)); // true
```

تمام مقدارها:

```java
EnumSet<Day> allDays = EnumSet.allOf(Day.class);
```

بازه‌ای از مقدارها:

```java
EnumSet<Day> workDays = EnumSet.range(
    Day.MONDAY,
    Day.FRIDAY
);
```

کپی و حذف:

```java
EnumSet<Day> nonWorkingDays = EnumSet.complementOf(workDays);

System.out.println(nonWorkingDays);
// [SUNDAY, SATURDAY]
```

> [!tip] Best Practice
> هر وقت به `Set<SomeEnum>` نیاز دارید، به‌جای `HashSet` از `EnumSet` استفاده کنید.

---

## ۱۴. `EnumMap`

`EnumMap` یک `Map` بهینه‌شده است که Keyهای آن فقط از یک نوع Enum هستند.

```java
import java.util.EnumMap;
import java.util.Map;

Map<Day, String> schedules = new EnumMap<>(Day.class);

schedules.put(Day.MONDAY, "Team meeting");
schedules.put(Day.FRIDAY, "Deploy review");

System.out.println(schedules.get(Day.MONDAY));
// Team meeting
```

ویژگی‌ها:

- نسبت به `HashMap` برای Keyهای Enum معمولاً کم‌مصرف‌تر و سریع‌تر است.
- ترتیب Keyها مطابق ترتیب تعریف Enum است.
- `null` به‌عنوان Key مجاز نیست.

```java
// schedules.put(null, "Unknown"); // ❌ NullPointerException
```

> [!tip] Best Practice
> هر وقت Keyهای Map از یک Enum هستند، `EnumMap` انتخاب پیش‌فرض مناسبی است.

---

## ۱۵. Stream و Enum

برای پردازش همهٔ مقدارهای Enum:

```java
import java.util.Arrays;

long count = Arrays.stream(Day.values())
        .filter(day -> day.name().startsWith("S"))
        .count();

System.out.println(count); // 2
```

تبدیل به List:

```java
import java.util.Arrays;
import java.util.List;

List<Day> days = Arrays.asList(Day.values());
```

برای ساخت یک List غیرقابل‌تغییر:

```java
List<Day> days = List.of(Day.values());
```

---

## ۱۶. Enum و Database / JPA

اگر از JPA یا Hibernate استفاده می‌کنید، نوع ذخیره‌سازی Enum باید صریح تعیین شود.

```java
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Enumerated(EnumType.STRING)
    private OrderStatus status;
}
```

> [!danger] از `EnumType.ORDINAL` استفاده نکنید
>
> ```java
> @Enumerated(EnumType.ORDINAL) // ❌ پرریسک
> private OrderStatus status;
> ```
>
> زیرا تغییر ترتیب ثابت‌ها، داده‌های ذخیره‌شده را به مقدار اشتباه نگاشت می‌کند.
>
> انتخاب رایج‌تر و امن‌تر:
>
> ```java
> @Enumerated(EnumType.STRING)
> private OrderStatus status;
> ```

> [!note] نکتهٔ طراحی Database
> حتی `EnumType.STRING` نیز به نام ثابت‌های Java وابسته است. اگر قرارداد Database یا API باید پایدار و مستقل از نام کد باشد، از یک `code` مشخص و `AttributeConverter` استفاده کنید.

---

## ۱۷. Enum و Spring

Spring می‌تواند در بسیاری از موارد مقدار Query Parameter را به Enum تبدیل کند.

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class OrderController {

    @GetMapping("/orders")
    public String getOrderStatus(
            @RequestParam OrderStatus status
    ) {
        return "Order status: " + status;
    }
}
```

فراخوانی:

```text
GET /orders?status=PROCESSING
```

اگر مقدار ورودی نامعتبر باشد، معمولاً پاسخ `400 Bad Request` تولید می‌شود.

> [!tip] API Design
> برای APIهای عمومی، ورودی Enum را اعتبارسنجی و پیام خطای مناسب ارائه کنید. همچنین برای جلوگیری از وابستگی قرارداد API به `name()`، می‌توان از Codeهای پایدار استفاده کرد.

---

## ۱۸. Enum و Serialization

Enumها به‌صورت پیش‌فرض رفتار ویژه و امن‌تری در Serialization دارند:

- برای هر ثابت Enum فقط یک Instance وجود دارد.
- هنگام Deserialization، همان Instance موجود بازگردانده می‌شود.
- برخلاف Classهای عادی، نیاز معمول به پیاده‌سازی دستی `Serializable` ندارند.

```java
public enum ApplicationMode {
    SAFE,
    MAINTENANCE,
    NORMAL
}
```

> [!note] نکته
> با وجود امنیت نسبی Serialization برای خود Enum، استفاده از Java Native Serialization برای داده‌های غیرقابل‌اعتماد همچنان توصیه نمی‌شود.

---

## ۱۹. Singleton با Enum

Enum یک روش استاندارد و ساده برای پیاده‌سازی Singleton است.

```java
public enum ApplicationConfig {
    INSTANCE;

    public void reload() {
        System.out.println("Configuration reloaded.");
    }
}
```

استفاده:

```java
ApplicationConfig.INSTANCE.reload();
```

مزیت‌ها:

- Thread-safe بودن ایجاد Instance
- مقاومت بهتر در برابر Serialization
- جلوگیری طبیعی از ساخت چند Instance با Reflection در اغلب سناریوهای معمول

> [!note] کاربرد
> Singleton با Enum راه‌حلی قدرتمند است، اما استفادهٔ بیش‌ازحد از Singleton می‌تواند وابستگی سراسری ایجاد و تست‌پذیری را دشوار کند.

---

## ۲۰. محدودیت‌ها و Best Practiceها

### محدودیت‌ها

- Enum نمی‌تواند از یک Class دیگر ارث‌بری کند.
- Enum به‌طور ضمنی `final` است.
- ایجاد Instance جدید با `new` غیرممکن است.
- تعداد مقدارها در زمان Compile مشخص است.
- Constructor نمی‌تواند `public` یا `protected` باشد.

### Best Practiceها

> [!tip] راهنمای استفاده
> - برای مجموعهٔ محدود و مشخص از حالت‌ها از Enum استفاده کنید.
> - برای مقایسه، `==` را به `equals()` ترجیح دهید.
> - از `ordinal()` در Database، API و منطق تجاری استفاده نکنید.
> - برای Keyهای Enum در Map از `EnumMap` استفاده کنید.
> - برای Set از مقدارهای Enum از `EnumSet` استفاده کنید.
> - برای نمایش یا قراردادهای خارجی، `displayName` یا `code` صریح تعریف کنید.
> - رفتارهای مرتبط با یک مقدار را تا حد امکان داخل خود Enum نگه دارید.

---

## جمع‌بندی

```text
Enum
├── نوعی ویژه برای مجموعه‌ای محدود از مقدارهای ثابت
├── هر مقدار یک Object یکتا است
├── Type-safe و مناسب جایگزینی String / int ثابت
├── دارای values(), valueOf(), name(), ordinal(), compareTo()
├── قابلیت داشتن Field، Constructor و Method
├── قابلیت پیاده‌سازی Interface و Abstract Method
├── مناسب برای switch expression
├── EnumSet → Set بهینه برای Enumها
├── EnumMap → Map بهینه با Key از نوع Enum
├── EnumType.STRING → انتخاب امن‌تر در JPA
└── ordinal() → برای قراردادهای خارجی و DB نامناسب
```

 # Input و Output در Java

---
tags:
  - java
  - io
  - input-output
  - file-io
  - nio
  - networking
  - streams
---

> [!abstract] هدف این جزوه
> آشنایی با روش‌های دریافت و ارسال داده در Java، شامل:
> - ورودی و خروجی کنسول
> - خواندن و نوشتن فایل
> - Byte Stream و Character Stream
> - Object Serialization
> - Java NIO
> - Network I/O
> - مدیریت منابع و استثناءها

---

## ۱. مقدمه

عملیات **Input/Output** یا به‌اختصار **I/O** برای تبادل داده بین برنامه و منابع خارجی استفاده می‌شود.

منابع خارجی می‌توانند شامل موارد زیر باشند:

- کاربر و کنسول
- فایل‌ها
- شبکه
- پایگاه‌داده
- حافظه یا Streamهای دیگر

پکیج‌های مهم Java برای I/O:

| Package | کاربرد |
|---|---|
| `java.io` | I/O کلاسیک مبتنی بر Stream |
| `java.nio` | I/O مدرن و سریع‌تر |
| `java.nio.file` | کار با Path و فایل‌ها |
| `java.net` | ارتباطات شبکه‌ای |

---

## ۲. مفهوم Stream

در Java، داده‌ها معمولاً به‌صورت جریان یا **Stream** پردازش می‌شوند.

```text
Source ────────> Program ────────> Destination
Input Stream                      Output Stream
```

### دو نوع اصلی Stream

| نوع Stream | واحد پردازش | کاربرد |
|---|---|---|
| Byte Stream | بایت | فایل‌های باینری، تصویر، ویدئو |
| Character Stream | کاراکتر | فایل‌های متنی |

---

# ۳. ورودی و خروجی استاندارد

## ۳.۱. خروجی با `System.out`

کلاس `System` شامل Streamهای استاندارد برنامه است:

```java
System.in     // ورودی استاندارد، معمولاً Keyboard
System.out    // خروجی استاندارد، معمولاً Console
System.err    // خروجی خطا
```

### `println`

پس از چاپ، به خط بعد می‌رود:

```java
public class ConsoleOutputExample {

    public static void main(String[] args) {
        System.out.println("Hello, World!");
        System.out.println("Java I/O");
    }
}
```

### `print`

بدون رفتن به خط بعد:

```java
System.out.print("Hello ");
System.out.print("Java");

// Hello Java
```

### `printf`

برای چاپ قالب‌بندی‌شده:

```java
public class FormattedOutputExample {

    public static void main(String[] args) {
        String name = "Alice";
        int age = 30;

        System.out.printf(
                "Name: %s, Age: %d%n",
                name,
                |
|---|---|
| `%s` | رشته |
| `%d` | عدد کاربرد |
|---|---|
| `%s` | رشته |
| `%d` | عدد صحیح |
| `%f` | عدد اعشاری |
| `%c` | کاراکتر |
| `%b` | مقدار Boolean |
| `%n` | رفتن به خط بعد |

> [!tip] `%n` یا `\n`
> برای رفتن به خط بعد، `%n` مستقل از سیستم‌عامل است و معمولاً گزینهٔ مناسب‌تری از `\n` محسوب می‌شود.

---

## ۳.۲. دریافت ورودی با `Scanner`

کلاس `Scanner` برای دریافت ورودی از کنسول استفاده می‌شود.

```java
import java.util.Scanner;

public class ScannerExample {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter your name: ");
        String name = scanner.nextLine();

        System.out.print("Enter your age: ");
        int age = scanner.nextInt();

        System.out.printf(
                "Hello %s, you are %d years old.%n",
                name,
                age
        );
    }
}
```

### متدهای رایج `Scanner`

| متد | کاربرد |
|---|---|
| `next()` | خواندن یک کلمه |
| `nextLine()` | خواندن یک خط کامل |
| `nextInt()` | خواندن عدد صحیح |
| `nextDouble()` | خواندن عدد اعشاری |
| `nextBoolean()` | خواندن مقدار Boolean |
| `hasNextInt()` | بررسی معتبر بودن عدد صحیح |

### مشکل رایج ترکیب `nextInt()` و `nextLine()`

```java
int age = scanner.nextInt();
String name = scanner.nextLine(); // ممکن است رشتهٔ خالی بخواند
```

علت این است که `nextInt()` کاراکتر Enter را مصرف نمی‌کند.

راه‌حل:

```java
int age = scanner.nextInt();
scanner.nextLine(); // مصرف Enter

String name = scanner.nextLine();
```

> [!warning] بستن Scanner
> بستن `Scanner` متصل به `System.in` باعث بسته‌شدن ورودی استاندارد برنامه می‌شود. در برنامه‌های کوچک مشکلی ایجاد نمی‌کند، اما در برنامه‌های بزرگ بهتر است مدیریت آن آگاهانه انجام شود.

---

# ۴. File I/O با `java.io`

## ۴.۱. نوشتن متن با `FileWriter` و `BufferedWriter`

`FileWriter` برای نوشتن کاراکترها و `BufferedWriter` برای بهبود عملکرد استفاده می‌شود.

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class FileWriteExample {

    public static void main(String[] args) {
        try (BufferedWriter writer =
                     new BufferedWriter(new FileWriter("output.txt"))) {

            writer.write("Hello, File!");
            writer.newLine();
            writer.write("This is a new line.");

        } catch (IOException e) {
            System.err.println("Error writing file: " + e.getMessage());
        }
    }
}
```

### حالت Append

به‌صورت پیش‌فرض، محتوای قبلی فایل بازنویسی می‌شود.

برای اضافه‌کردن محتوا به انتهای فایل:

```java
try (BufferedWriter writer =
             new BufferedWriter(new FileWriter("output.txt", true))) {

    writer.newLine();
    writer.write("Appended text.");
}
```

---

## ۴.۲. خواندن متن با `FileReader` و `BufferedReader`

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class FileReadExample {

    public static void main(String[] args) {
        try (BufferedReader reader =
                     new BufferedReader(new FileReader("output.txt"))) {

            String line;

            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }

        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
        }
    }
}
```

### چرا `BufferedReaderReader` داده‌ها را در Buffer نگه میاکتر معمولاً کندتر است. `BufferedReader` داده‌ها را در Buffer نگه می‌دارد و تعداد دسترسی‌های مستقیم به فایل را کاهش می‌دهد.

---

# ۵. Java NIO و کلاس `Files`

از Java 7 به بعد، پکیج `java.nio.file` روش مدرن‌تری برای کار با فایل‌ها ارائه می‌دهد.

کلاس‌های مهم:

```java
Path
Paths
Files
```

> [!important]
> در کدهای جدید، استفاده از `Path` و `Files` معمولاً نسبت به `FileReader` و `FileWriter` خواناتر و انعطاف‌پذیرتر است.

---

## ۵.۱. نوشتن متن با `Files.writeString`

```java
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class NIOWriteExample {

    public static void main(String[] args) {
        Path path = Path.of("nio_output.txt");
        String content = "Hello, NIO File!";

        try {
            Files.writeString(
                    path,
                    content,
                    StandardCharsets.UTF_8
            );
        } catch (IOException e) {
            System.err.println("Error writing file: " + e.getMessage());
        }
    }
}
```

> [!note]
> `Path.of(...)` از Java 11 در دسترس است. در نسخه‌های قدیمی‌تر می‌توان از `Paths.get(...)` استفاده کرد.

---

## ۵.۲. خواندن متن با `Files.readString`

```java
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class NIOReadExample {

    public static void main(String[] args) {
        Path path = Path.of("nio_output.txt");

        try {
            String content = Files.readString(
                    path,
                    StandardCharsets.UTF_8
            );

            System.out.println(content);

        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
        }
    }
}
```

---

## ۵. java.nio.fileدن فایل خط‌به‌خط

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.stream.Stream;

public class NIOReadLinesExample {

    public static void main(String[] args) {
        Path path = Path.of("output.txt");

        try (Stream<String> lines = Files.lines(path)) {
            lines.forEach(System.out::println);
        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
        }
    }
}
```

> [!warning]
> `Files.lines(...)` یک Stream بازمی‌گرداند؛ بنابراین باید با `try-with-resources` بسته شود.

---

## ۵.۴. عملیات مهم کلاس `Files`

```java
Path source = Path.of("source.txt");
Path target = Path.of("target.txt");
```

### بررسی وجود فایل

```java
boolean exists = Files.exists(source);
```

### کپی

```java
import static java.nio.file.StandardCopyOption.REPLACE_EXISTING;

Files.copy(source, target, REPLACE_EXISTING);
```

### انتقال یا تغییر نام

```java
Files.move(source, target, REPLACE_EXISTING);
```

### حذف

```java
Files.deleteIfExists(target);
```

### ساخت دایرکتوری

```java
Files.createDirectories(Path.of("logs/app"));
```

---

# ۶. Byte I/O

Byte Stream برای پردازش داده‌های باینری مناسب است.

نمونه‌ها:

- تصویر
- فایل صوتی
- ویدئو
- فایل ZIP
- PDF
- داده‌های خام

کلاس‌های اصلی:

```java
InputStream
OutputStream
FileInputStream
FileOutputStream
```

---

## ۶.۱. نوشتن بایت با `FileOutputStream`

```java
import java.io.FileOutputStream;
import java.io.IOException;

public class ByteWriteExample {

    public static void main(String[] args) {
        try (FileOutputStream output =
                     new FileOutputStream("byte_output.dat")) {

            output.write(65); // مقدار ASCII مربوط به A

        } catch (IOException e) {
            System.err.println("Error writing bytes: " + e.getMessage());
        }
    }
}
```

> [!note]
> متد `write(int)` فقط ۸ بیت پایین مقدار را به‌عنوان یک Byte می‌نویسد.

---

## ۶.۲. خواندن بایت با `FileInputStream`

```java
import java.io.FileInputStream;
import java.io.IOException;

public class ByteReadExample {

    public static void main(String[] args) {
        try (FileInputStream input =
                     new FileInputStream("byte_output.dat")) {

            int data;

            while ((data = input.read()) != -1) {
                System.out.println("Read byte: " + data);
            }

        } catch (IOException e) {
            System.err.println("Error reading bytes: " + e.getMessage());
        }
    }
}
```

> [!important]
> متد `read()` مقدار `int` برمی‌گرداند، نه `byte`.  
> مقدار `-1` به‌معنای رسیدن به پایان Stream است.

---

## ۶.۳. کپی فایل با Buffer

خواندن و نوشتن بایت‌به‌بایت برای فایل‌های بزرگ مناسب نیست. بهتر است از Buffer استفاده کنیم:

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class FileCopyExample {

    public static void main(String[] args) {
        Path source = Path.of("source.bin");
        Path target = Path.of("copy.bin");

        try (
                InputStream input = Files.newInputStream(source);
                OutputStream output = Files.newOutputStream(target)
        ) {
            byte[] buffer = new byte[8192];
            int bytesRead;

            while ((bytesRead = input.read(buffer)) != -1) {
                output.write(buffer, 0, bytesRead);
            }

        } catch (IOException e) {
            System.err.println("Error copying file: " + e.getMessage());
        }
    }
}
```

---

# ۷. Character I/O

Character Stream برای داده‌های متنی استفاده می‌شود.

کلاس‌های مهم:

```java
Reader
Writer
FileReader
FileWriter
BufferedReader
BufferedWriter
```

---

## ۷.۱. نوشتن با `Writer`

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class WriterExample {

    public static void main(String[] args) {
        try (Writer writer =
                     new BufferedWriter(new FileWriter("text.txt"))) {

            writer.write("Hello, Buffered Writer!");
            writer.write(System.lineSeparator());
            writer.write("Second line.");

        } catch (IOException e) {
            System.err.println("Error writing text: " + e.getMessage());
        }
    }
}
```

---

## ۷.۲. خواندن با `Reader`

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.io.Reader;

public class ReaderExample {

    public static void main(String[] args) {
        try (Reader reader =
                     new BufferedReader(new FileReader("text.txt"))) {

            int data;

            while ((data = reader.read()) != -1) {
                System.out.print((char) data);
            }

        } catch (IOException e) {
            System.err.println("Error reading text: " + e.getMessage());
        }
    }
}
```

برای خواندن خط‌به‌خط، `BufferedReader` مناسب‌تر است:

```java
try (BufferedReader reader =
             new BufferedReader(new FileReader("text.txt"))) {

    String line;

    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

---

## ۷.۳. تعیین Charset

کلاس‌های قدیمی مانند `FileReader` و `FileWriter` ممکن است از Charset پیش‌فرض سیستم استفاده کنند. برای جلوگیری از مشکل Encoding، Charset را صریح تعیین کنید.

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class Utf8FileExample {

    public static void main(String[] args) throws IOException {
        Path path = Path.of("persian.txt");

        try (BufferedWriter writer =
                     Files.newBufferedWriter(
                             path,
                             StandardCharsets.UTF_8
                     )) {

            writer.write("سلام جاوا");
        }

        try (BufferedReader reader =
                     Files.newBufferedReader(
                             path,
                             StandardCharsets.UTF_8
                     )) {

            System.out.println(reader.readLine());
        }
    }
}
```

> [!tip] توصیه
> برای فایل‌های متنی، به‌خصوص فایل‌های فارسی، معمولاً `StandardCharsets.UTF_8` را صریح مشخص کنید.

---

# ۸. Serialization و Object I/O

Serialization یعنی تبدیل وضعیت یک Object به یک جریان بایتی، به‌منظور ذخیره‌سازی یا ارسال آن.

برای این کار از موارد زیر استفاده می‌شود:

```java
Serializable
ObjectOutputStream
ObjectInputStream
```

---

## ۸.۱. نوشتن Object

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.io.Serializable;

class Person implements Serializable {

    private static final long serialVersionUID = 1L;

    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{name='%s', age=%d}"
                .formatted(name, age);
    }
}

public class ObjectWriteExample {

    public static void main(String[] args) {
        Person person = new Person("Alice", 30);

        try (ObjectOutputStream output =
                     new ObjectOutputStream(
                             new FileOutputStream("person.ser")
                     )) {

            output.writeObject(person);

        } catch (IOException e) {
            System.err.println("Error writing object: " + e.getMessage());
        }
    }
}
```

---

## ۸.۲. خواندن Object

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;

public class ObjectReadExample {

    public static void main(String[] args) {
        try (ObjectInputStream input =
                     new ObjectInputStream(
                             new FileInputStream("person.ser")
                     )) {

            Person person = (Person) input.readObject();
            System.out.println(person);

        } catch (IOException | ClassNotFoundException e) {
            System.err.println("Error reading object: " + e.getMessage());
        }
    }
}
```

---

## ۸.۳. `transient`

فیلدهایی که با `transient` مشخص شوند، در Serialization ذخیره نمی‌شوند.

```java
class User implements Serializable {

    private static final long serialVersionUID = 1L;

    private String username;
    private transient String password;
}
```

پس از Deserialization:

```text
username → مقدار قبلی
password → null
```

> [!danger] هشدار امنیتی Serialization
> `ObjectInputStream` را روی داده‌های غیرقابل‌اعتماد مانند فایل آپلودی یا ورودی شبکه استفاده نکنید؛ Deserialization ناامن می‌تواند منجر به آسیب‌پذیری جدی شود.
>
> برای ارتباطات و ذخیره‌سازی مدرن معمولاً فرمت‌هایی مانند JSON یا Protocol Buffers انتخاب بهتری هستند.

---

# ۹. `InputStream` و `OutputStream`

## ۹.۱. نوشتن با `OutputStream`

```java
import java.io.IOException;
import java.io.OutputStream;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class OutputStreamExample {

    public static void main(String[] args) {
        try (OutputStream output =
                     Files.newOutputStream(Path.of("output_stream.txt"))) {

            output.write(
                    "Hello, Stream!"
                            .getBytes(StandardCharsets.UTF_8)
            );

        } catch (IOException e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}
```

## ۹.۲. خواندن با `InputStream`

```java
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class InputStreamExample {

    public static void main(String[] args) {
        try (InputStream input =
                     Files.newInputStream(Path.of("output_stream.txt"))) {

            int data;

            while ((data = input.read()) != -1) {
                System.out.print((char) data);
            }

        } catch (IOException e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}
```

> [!warning]
> تبدیل مستقیم هر بایت به `char` فقط برای متن‌های ساده مناسب است. برای متن UTF-8 بهتر است از `Reader` یا `InputStreamReader` استفاده شود.

---

# ۱۰. تبدیل Byte Stream به Character Stream

کلاس‌های زیر برای تبدیل استفاده می‌شوند:

```java
InputStreamReader
OutputStreamWriter
```

مثال:

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;

public class InputStreamReaderExample {

    public static void main(String[] args) throws IOException {
        try (BufferedReader reader =
                     new BufferedReader(
                             new InputStreamReader(
                                     System.in,
                                     StandardCharsets.UTF_8
                             )
                     )) {

            System.out.print("Enter text: ");
            String input = reader.readLine();

            System.out.println("You entered: " + input);
        }
    }
}
```

ساختار:

```text
InputStream
    └── InputStreamReader
            └── BufferedReader
```

---

# ۱۱. Network I/O

برای ارتباطات شبکه‌ای ساده، از موارد زیر استفاده می‌شود:

```java
Socket
ServerSocket
```

- `ServerSocket`: گوش‌دادن روی یک Port و قبول اتصال‌ها
- `Socket`: برقراری ارتباط بین Client و Server

---

## ۱۱.۱. Server ساده

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;

public class ServerExample {

    public static void main(String[] args) {
        try (
                ServerSocket serverSocket = new ServerSocket(12345);
                Socket clientSocket = serverSocket.accept();

                BufferedReader input =
                        new BufferedReader(
                                new InputStreamReader(
                                        clientSocket.getInputStream()
                                )
                        );

                PrintWriter output =
                        new PrintWriter(
                                clientSocket.getOutputStream(),
                                true
                        )
        ) {
            String message = input.readLine();

            System.out.println("Received: " + message);

            output.println("Hello, client!");

        } catch (IOException e) {
            System.err.println("Server error: " + e.getMessage());
        }
    }
}
```

---

## ۱۱.۲. Client ساده

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;

public class ClientExample {

    public static void main(String[] args) {
        try (
                Socket socket = new Socket("localhost", 12345);

                PrintWriter output =
                        new PrintWriter(
                                socket.getOutputStream(),
                                true
                        );

                BufferedReader input =
                        new BufferedReader(
                                new InputStreamReader(
                                        socket.getInputStream()
                                )
                        )
        ) {
            output.println("Hello, server!");

            String response = input.readLine();

            System.out.println("Received: " + response);

        } catch (IOException e) {
            System.err.println("Client error: " + e.getMessage());
        }
    }
}
```

### ترتیب اجرای برنامه

ابتدا Server را اجرا کنید:

```bash
java ServerExample
```

سپس در Terminal دیگر Client را اجرا کنید:

```bash
java ClientExample
```

> [!note] نکته
> `readLine()` تا دریافت کاراکتر پایان خط یا بسته‌شدن Connection منتظر می‌ماند. به همین دلیل Server و Client در مثال بالا از `println` استفاده می‌کنند.

---

# ۱۲. مدیریت منابع با `try-with-resources`

منابع I/O مانند موارد زیر باید بسته شوند:

- فایل
- Socket
- Stream
- Reader و Writer
- Connection

روش قدیمی:

```java
BufferedReader reader = null;

try {
    reader = new BufferedReader(new FileReader("example.txt"));

    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }

} catch (IOException e) {
    e.printStackTrace();

} finally {
    try {
        if (reader != null) {
            reader.close();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

روش پیشنهادی:

```java
try (BufferedReader reader =
             new BufferedReader(new FileReader("example.txt"))) {

    String line;

    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }

} catch (IOException e) {
    e.printStackTrace();
}
```

مزیت‌های `try-with-resources`:

- بستن خودکار منابع
- کاهش کدهای اضافی
- جلوگیری از Resource Leak
- بستن منابع حتی هنگام رخ‌دادن Exception

> [!important]
> هر کلاسی که `AutoCloseable` یا `Closeable` را پیاده‌سازی کند، می‌تواند در `try-with-resources` استفاده شود.

---

# ۱۳. مدیریت استثناء در I/O

بیشتر عملیات I/O ممکن است با `IOException` مواجه شوند.

نمونهٔ Exceptionهای رایج:

| Exception | علت |
|---|---|
| `IOException` | خطای عمومی I/O |
| `FileNotFoundException` | فایل وجود ندارد یا قابل دسترسی نیست |
| `EOFException` | پایان غیرمنتظرهٔ داده |
| `SocketException` | |
| `SocketException` | مشکل در Connection شبکه |
| `Invalid |

نمونهٔ مدیریت بهتر:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class SafeReadExample {

    public static void main(String[] args) {
        Path path = Path.of("config.properties");

        try {
            String content = Files.readString(path);
            System.out.println(content);

        } catch (IOException e) {
            System.err.println(
                    "Cannot read file " + path + ": " + e.getMessage()
            );
        }
    }
}
```

> [!tip] Best Practice
> در محیط Production معمولاً به‌جای `printStackTrace()` از Logging Frameworkهایی مانند SLF4J و Logback استفاده می‌شود.

---

# ۱۴. مقایسهٔ روش‌های مختلف I/O

| روش | کاربرد | نکته |
|---|---|---|
| `Scanner` | ورودی ساختاریافته از Console | ساده، اما نسبتاً کند |
| `System.out` | خروجی Console | مناسب Debug و برنامه‌های ساده |
| `FileReader` / `FileWriter` | متن | API قدیمی‌تر |
| `BufferedReader` / `BufferedWriter` | متن با Buffer | مناسب‌تر برای فایل |
| `InputStream` / `OutputStream` | بایت | مناسب فایل باینری |
| `Reader` / `Writer` | کاراکتر | مناسب متن |
| `Files` | API مدرن فایل | خوانا و کاربردی |
| `ObjectInputStream` | خواندن Object | برای دادهٔ غیرقابل‌اعتماد خطرناک |
| `Socket` | ارتباط Client | ارتباط نقطه‌به‌نقطه |
| `ServerSocket` | ساخت Server | گوش‌دادن روی Port |

---

# ۱۵. نکات مهم و Best Practiceها

> [!tip] چک‌لیست I/O در Java
> - برای منابع قابل‌بستن از `try-with-resources` استفاده کنید.
> - برای متن فارسی و چندزبانه، Charset را روی `UTF-8` تنظیم کنید.
> - برای فایل‌های متنی از `Reader` و `Writer` استفاده کنید.
> - برای فایل‌های باینری از `InputStream` و `OutputStream` استفاده کنید.
> - برای فایل‌های بزرگ، داده را با Buffer پردازش کنید.
> - در کدهای جدید، `Path` و `Files` را به `File` ترجیح دهید.
> - `ObjectInputStream` را روی داده‌های غیرقابل‌اعتماد استفاده نکنید.
> - از `printStackTrace()` در Production استفاده نکنید؛ Logging انجام دهید.
> - در شبکه، Timeout و مدیریت قطع Connection را در نظر بگیرید.
> - از خواندن کامل فایل‌های بسیار بزرگ با `readAllLines` یا `readString` خودداری کنید.

---

# ۱۶. جمع‌بندی

```text
Input / Output در Java
├── Standard I/O
│   ├── System.in
│   ├── System.out
│   └── Scanner
│
├── File I/O
│   ├── FileReader / FileWriter
│   ├── Buffered
│   ├── InputStream
│   / Path
│
├── Byte I/O
│   ├── InputStream
│   ├── OutputStream
│   ├── FileInputStream
│   └── FileOutputStream
│
├── Character I/O
│   ├── Reader
│   ├── Writer
│   ├── InputStreamReader
│   └── OutputStreamWriter
│
├── Object I/O
│   ├── Serializable
│   ├── ObjectInputStream
│   └── ObjectOutputStream
│
├── Network I/O
│   ├── Socket
│   └── ServerSocket
│
└── Resource Management
    └── try-with-resources
```

> [!summary] نکتهٔ نهایی
> در Java، برای پیاده‌سازی‌های جدید معمولاً این ترکیب انتخاب مناسبی است:
>
> ```java
> Path + Files + UTF_8 + try-with-resources
> ```
>
> و انتخاب نوع Stream باید بر اساس نوع داده انجام شود:
>
> ```text
> متن      → Reader / Writer
> باینری   → InputStream / OutputStream
> ```