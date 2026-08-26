---
title: عبارات لامبدا در جاوا
tags:
  - java
  - lambda
  - functional-programming
  - functional-interface
  - stream-api
aliases:
  - Java Lambda Expressions
  - Lambda Expression
  - لامبدا در جاوا
created: 2026-08-25
---

# جزوه جامع عبارات لامبدا در جاوا

> [!summary] تعریف کوتاه
> **عبارت لامبدا** (*Lambda Expression*) روشی کوتاه برای پیاده‌سازی رفتار یک **رابط تابعی** (*Functional Interface*) است.  
> این قابلیت از **Java 8** اضافه شد و به‌ویژه در کنار `Stream API`، Collectionها، Callbackها و پردازش تابعی داده‌ها کاربرد فراوانی دارد.

---

## فهرست مطالب

- [[#مقدمه]]
- [[#رابط تابعی (Functional Interface)]]
- [[#نحو عبارت لامبدا]]
- [[#انواع بدنهٔ لامبدا]]
- [[#مثال‌های پایه]]
- [[#رابط‌های تابعی پرکاربرد در java.util.function]]
- [[#استفاده از لامبدا در Collection و Stream API]]
- [[#Method Reference یا ارجاع به متد]]
- [[#دسترسی به متغیرهای بیرونی و Effectively Final]]
- [[#Lambda در برابر Anonymous Class]]
- [[#دامنه و رفتار this]]
- [[#مزایا و محدودیت‌ها]]
- [[#بهترین روش‌ها]]
- [[#جمع‌بندی]]

---

# مقدمه

پیش از Java 8، برای ارسال یک رفتار به متدها—مثلاً مشخص‌کردن نحوهٔ مرتب‌سازی، اجرای یک Thread یا تعریف Listener—اغلب از **کلاس ناشناس** (*Anonymous Class*) استفاده می‌شد.

مثال قدیمی با کلاس ناشناس:

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello, World!");
    }
};

task.run();
```

همین کد با Lambda:

```java
Runnable task = () -> System.out.println("Hello, World!");

task.run();
```

لامبدا کد را کوتاه‌تر می‌کند، اما مهم‌تر از کوتاه‌بودن آن است که **رفتار را مانند داده** به متدها منتقل می‌کنیم.

---

# رابط تابعی (Functional Interface)

لامبدا به‌تنهایی نوع مشخصی ندارد؛ بلکه باید در جایی استفاده شود که کامپایلر نوع مورد انتظار را بداند. این نوع معمولاً یک **رابط تابعی** است.

> [!important]
> رابط تابعی، Interfaceای است که دقیقاً **یک متد انتزاعی** (*Abstract Method*) دارد.

نمونهٔ ساده:

```java
@FunctionalInterface
public interface Calculator {
    int calculate(int a, int b);
}
```

استفاده از آن با Lambda:

```java
Calculator add = (a, b) -> a + b;

System.out.println(add.calculate(5, 3));
// 8
```

---

## annotation `@FunctionalInterface`

نوشتن این annotation اجباری نیست، ولی پیشنهاد می‌شود:

```java
@FunctionalInterface
public interface MessagePrinter {
    void print(String message);
}
```

مزیت آن این است که اگر به‌اشتباه بیش از یک متد انتزاعی تعریف کنید، کامپایلر خطا می‌دهد:

```java
@FunctionalInterface
public interface InvalidInterface {

    void firstMethod();

    void secondMethod(); // خطای کامپایل
}
```

---

## آیا متدهای `default` و `static` مشکلی ایجاد می‌کنند؟

خیر. تعداد **متدهای انتزاعی** مهم است، نه تعداد کل متدها.

```java
@FunctionalInterface
public interface Converter {

    String convert(String input);

    default void printInfo() {
        System.out.println("Converter");
    }

    static void help() {
        System.out.println("Help");
    }
}
```

این Interface همچنان Functional Interface محسوب می‌شود؛ زیرا تنها یک متد انتزاعی به نام `convert` دارد.

---

# نحو عبارت لامبدا

ساختار کلی:

```java
(parameters) -> expression
```

یا:

```java
(parameters) -> {
    statements;
}
```

هر عبارت لامبدا سه بخش دارد:

1. **پارامترها**
2. عملگر فلش `->`
3. **بدنهٔ لامبدا**

---

## لامبدا بدون پارامتر

```java
() -> System.out.println("Hello, World!");
```

مثال کامل:

```java
Runnable task = () -> System.out.println("Hello, World!");

task.run();
```

---

## لامبدا با یک پارامتر

وقتی فقط یک پارامتر داریم، پرانتز اختیاری است:

```java
name -> System.out.println(name);
```

یا:

```java
(name) -> System.out.println(name);
```

نمونهٔ کامل:

```java
import java.util.function.Consumer;

Consumer<String> print = text -> System.out.println(text);

print.accept("Hello, Lambda!");
```

---

## لامبدا با چند پارامتر

برای چند پارامتر، پرانتز الزامی است:

```java
(a, b) -> a + b
```

```java
import java.util.function.BinaryOperator;

BinaryOperator<Integer> add = (a, b) -> a + b;

System.out.println(add.apply(5, 3));
// 8
```

---

## مشخص‌کردن نوع پارامترها

جاوا معمولاً نوع پارامترها را از نوع Functional Interface تشخیص می‌دهد:

```java
BinaryOperator<Integer> add = (a, b) -> a + b;
```

اما می‌توان نوع را صریحاً هم نوشت:

```java
BinaryOperator<Integer> add = (Integer a, Integer b) -> a + b;
```

> [!warning]
> نمی‌توان بعضی پارامترها را با نوع و بعضی را بدون نوع نوشت.

نادرست:

```java
// خطای کامپایل
BinaryOperator<Integer> add = (Integer a, b) -> a + b;
```

---

# انواع بدنهٔ لامبدا

## ۱. بدنهٔ تک‌عبارتی (*Expression Body*)

اگر بدنه فقط یک Expression باشد، نیازی به آکولاد و `return` نیست.

```java
BinaryOperator<Integer> multiply = (a, b) -> a * b;
```

در اینجا مقدار `a * b` به‌صورت خودکار بازگردانده می‌شود.

```java
import java.util.function.Function;

Function<String, Integer> getLength = text -> text.length();

System.out.println(getLength.apply("Java"));
// 4
```

---

## ۲. بدنهٔ چنددستوری (*Block Body*)

اگر چند دستور داشته باشیم، از آکولاد استفاده می‌کنیم:

```java
BinaryOperator<Integer> add = (a, b) -> {
    int result = a + b;
    return result;
};
```

در این حالت، اگر مقدار بازگشتی لازم باشد، باید `return` را صریحاً بنویسیم.

```java
import java.util.function.Function;

Function<Integer, String> checkNumber = number -> {
    if (number % 2 == 0) {
        return "Even";
    }

    return "Odd";
};
```

---

## Lambda بدون مقدار بازگشتی

اگر متد Functional Interface از نوع `void` باشد، لامبدا هم لازم نیست مقداری بازگرداند.

```java
import java.util.function.Consumer;

Consumer<String> print = message -> {
    System.out.println("Message:");
    System.out.println(message);
};

print.accept("سلام");
```

---

# مثال‌های پایه

## مثال ۱: اجرای یک `Runnable`

```java
Runnable task = () -> System.out.println("Task is running");

task.run();
```

خروجی:

```text
Task is running
```

---

## مثال ۲: جمع دو عدد

```java
import java.util.function.BinaryOperator;

BinaryOperator<Integer> add = (a, b) -> a + b;

int result = add.apply(10, 20);

System.out.println(result);
```

خروجی:

```text
30
```

---

## مثال ۳: بررسی زوج‌بودن عدد

```java
import java.util.function.Predicate;

Predicate<Integer> isEven = number -> number % 2 == 0;

System.out.println(isEven.test(10)); // true
System.out.println(isEven.test(7));  // false
```

---

## مثال ۴: تبدیل متن به حروف بزرگ

```java
import java.util.function.Function;

Function<String, String> toUpperCase = text -> text.toUpperCase();

System.out.println(toUpperCase.apply("java"));
```

خروجی:

```text
JAVA
```

---

## مثال ۵: تولید مقدار بدون ورودی

```java
import java.util.function.Supplier;

Supplier<String> messageSupplier = () -> "Welcome to Java Lambda";

System.out.println(messageSupplier.get());
```

---

# رابط‌های تابعی پرکاربرد در `java.util.function`

پکیج `java.util.function` مجموعه‌ای از Functional Interfaceهای آماده را فراهم می‌کند.

| رابط | ورودی | خروجی | متد اصلی | کاربرد |
|---|---:|---:|---|---|
| `Predicate<T>` | یک مقدار | `boolean` | `test()` | بررسی شرط |
| `Function<T, R>` | یک مقدار | یک نتیجه | `apply()` | تبدیل داده |
| `Consumer<T>` | یک مقدار | ندارد | `accept()` | انجام عملیات |
| `Supplier<T>` | ندارد | یک نتیجه | `get()` | تولید مقدار |
| `UnaryOperator<T>` | یک مقدار از نوع `T` | `T` | `apply()` | تبدیل هم‌نوع |
| `BinaryOperator<T>` | دو مقدار از نوع `T` | `T` | `apply()` | ترکیب دو مقدار |

---

## `Predicate<T>`

برای بررسی شرط استفاده می‌شود.

```java
import java.util.function.Predicate;

Predicate<String> isLongText = text -> text.length() > 5;

System.out.println(isLongText.test("Java"));        // false
System.out.println(isLongText.test("Lambda"));      // true
```

ترکیب شرط‌ها:

```java
Predicate<Integer> isPositive = number -> number > 0;
Predicate<Integer> isEven = number -> number % 2 == 0;

Predicate<Integer> isPositiveAndEven = isPositive.and(isEven);

System.out.println(isPositiveAndEven.test(8));  // true
System.out.println(isPositiveAndEven.test(-2)); // false
```

---

## `Function<T, R>`

یک مقدار را دریافت و به مقدار دیگری تبدیل می‌کند.

```java
import java.util.function.Function;

Function<String, Integer> stringLength = text -> text.length();

System.out.println(stringLength.apply("Java"));
// 4
```

ترکیب Functionها:

```java
Function<String, String> trim = text -> text.trim();
Function<String, String> upper = text -> text.toUpperCase();

Function<String, String> trimThenUpper = trim.andThen(upper);

System.out.println(trimThenUpper.apply("  java  "));
// JAVA
```

---

## `Consumer<T>`

یک مقدار می‌گیرد اما چیزی برنمی‌گرداند.

```java
import java.util.function.Consumer;

Consumer<String> printName = name ->
    System.out.println("Name: " + name);

printName.accept("Sara");
```

ترکیب Consumerها:

```java
Consumer<String> print = text -> System.out.println(text);
Consumer<String> printLength = text ->
    System.out.println("Length: " + text.length());

Consumer<String> combined = print.andThen(printLength);

combined.accept("Java");
```

خروجی:

```text
Java
Length: 4
```

---

## `Supplier<T>`

ورودی نمی‌گیرد و یک مقدار تولید می‌کند.

```java
import java.util.function.Supplier;

Supplier<Double> randomNumber = () -> Math.random();

System.out.println(randomNumber.get());
```

نمونهٔ کاربردی با `Optional`:

```java
String username = null;

String result = java.util.Optional.ofNullable(username)
    .orElseGet(() -> "Guest");

System.out.println(result);
// Guest
```

---

# استفاده از Lambda در Collectionها

## مرتب‌سازی یک لیست

```java
import java.util.ArrayList;
import java.util.List;

List<String> names = new ArrayList<>(
    List.of("Sara", "Ali", "Mohammad", "Reza")
);

names.sort((first, second) -> first.compareTo(second));

System.out.println(names);
```

خروجی:

```text
[Ali, Mohammad, Reza, Sara]
```

نسخهٔ کوتاه‌تر با Method Reference:

```java
names.sort(String::compareTo);
```

---

## مرتب‌سازی بر اساس طول متن

```java
List<String> names = new ArrayList<>(
    List.of("Ali", "Mohammad", "Reza", "Sara")
);

names.sort((first, second) ->
    Integer.compare(first.length(), second.length())
);

System.out.println(names);
```

نسخهٔ خواناتر:

```java
names.sort(
    java.util.Comparator.comparingInt(String::length)
);
```

---

## پیمایش عناصر با `forEach`

```java
List<String> names = List.of("Ali", "Sara", "Reza");

names.forEach(name -> System.out.println(name));
```

یا:

```java
names.forEach(System.out::println);
```

---

# استفاده از Lambda در Stream API

Lambdaها بخش مهمی از پردازش داده با Streamها هستند.

```java
import java.util.List;

List<String> names = List.of("John", "Jane", "Max", "Jack");

names.stream()
    .filter(name -> name.startsWith("J"))
    .forEach(System.out::println);
```

خروجی:

```text
John
Jane
Jack
```

---

## مثال: فیلتر، تبدیل و جمع

```java
import java.util.List;

List<Integer> numbers = List.of(1, 2, 3, 4, 5);

int sumOfEvenSquares = numbers.stream()
    .filter(number -> number % 2 == 0)
    .map(number -> number * number)
    .reduce(0, (total, number) -> total + number);

System.out.println(sumOfEvenSquares);
```

خروجی:

```text
20
```

توضیح:

1. فقط اعداد زوج انتخاب می‌شوند: `2` و `4`
2. به توان دو می‌رسند: `4` و `16`
3. با هم جمع می‌شوند: `20`

نسخهٔ بهتر با متدهای آماده:

```java
int sumOfEvenSquares = numbers.stream()
    .filter(number -> number % 2 == 0)
    .mapToInt(number -> number * number)
    .sum();
```

---

# Method Reference یا ارجاع به متد

گاهی لامبدا فقط یک متد موجود را فراخوانی می‌کند. در این حالت می‌توان از **Method Reference** با عملگر `::` استفاده کرد.

Lambda:

```java
names.forEach(name -> System.out.println(name));
```

Method Reference:

```java
names.forEach(System.out::println);
```

---

## انواع Method Reference

| نوع | شکل کلی | مثال |
|---|---|---|
| متد استاتیک | `ClassName::staticMethod` | `Integer::parseInt` |
| متد نمونهٔ یک شیء مشخص | `object::instanceMethod` | `System.out::println` |
| متد نمونهٔ هر شیء از یک نوع | `ClassName::instanceMethod` | `String::length` |
| سازنده | `ClassName::new` | `ArrayList::new` |

### نمونه‌ها

```java
Function<String, Integer> parser = Integer::parseInt;

System.out.println(parser.apply("42"));
// 42
```

```java
Function<String, Integer> lengthCalculator = String::length;

System.out.println(lengthCalculator.apply("Java"));
// 4
```

```java
Supplier<List<String>> listSupplier = ArrayList::new;

List<String> names = listSupplier.get();
```

> [!tip]
> از Method Reference فقط زمانی استفاده کنید که خوانایی کد را بهتر کند.  
> کوتاه‌تر بودن همیشه به معنی واضح‌تر بودن نیست.

---

# دسترسی به متغیرهای بیرونی و `effectively final`

Lambda می‌تواند به متغیرهای محلی متد بیرونی دسترسی داشته باشد؛ اما این متغیرها باید `final` یا **effectively final** باشند.

```java
public void printMessage() {
    String message = "Hello";

    Runnable task = () -> System.out.println(message);

    task.run();
}
```

متغیر `message` به‌صورت صریح `final` نیست، اما چون پس از مقداردهی تغییر نکرده، effectively final است.

نمونهٔ نادرست:

```java
public void printMessage() {
    String message = "Hello";

    Runnable task = () -> System.out.println(message);

    message = "Changed"; // خطای کامپایل

    task.run();
}
```

---

## تفاوت متغیر محلی با فیلد کلاس

محدودیت effectively final فقط برای متغیرهای **محلی** اعمال می‌شود، نه فیلدهای شیء.

```java
public class Counter {

    private int count = 0;

    public Runnable createTask() {
        return () -> {
            count++;
            System.out.println(count);
        };
    }
}
```

این کد معتبر است، چون `count` یک فیلد نمونه (*instance field*) است.

> [!warning]
> معتبر بودن تغییر فیلد به معنی Thread-safe بودن آن نیست.  
> اگر Lambda در چند Thread اجرا می‌شود، باید هم‌زمانی و ایمنی Threadها را جداگانه مدیریت کنید.

---

# Lambda در برابر Anonymous Class

## نمونهٔ کلاس ناشناس

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running...");
    }
};
```

## همان نمونه با Lambda

```java
Runnable task = () -> System.out.println("Running...");
```

| ویژگی | Lambda | Anonymous Class |
|---|---|---|
| مناسب برای Functional Interface | ✅ | ✅ |
| قابل استفاده برای کلاس Abstract | ❌ | ✅ |
| قابلیت Override چند متد | ❌ | ✅ |
| تعریف فیلد و متد اختصاصی | ❌ | ✅ |
| کوتاهی و خوانایی در منطق ساده | ✅ | معمولاً کمتر |
| ایجاد scope جدید برای `this` | ❌ | ✅ |

> [!important]
> Lambda جایگزین کامل کلاس ناشناس نیست.  
> Lambda فقط برای پیاده‌سازی یک Functional Interface کاربرد دارد، در حالی که Anonymous Class می‌تواند از یک کلاس abstract ارث‌بری کند یا Interfaceهای غیرتابعی را پیاده‌سازی کند.

---

# دامنه و رفتار `this`

یکی از تفاوت‌های مهم Lambda و Anonymous Class، معنای `this` است.

در Lambda، `this` به شیء کلاس خارجی اشاره می‌کند:

```java
public class Example {

    private String name = "Outer object";

    public void test() {
        Runnable task = () -> {
            System.out.println(this.name);
        };

        task.run();
    }
}
```

اما در Anonymous Class، `this` به خود شیء ناشناس اشاره می‌کند:

```java
public class Example {

    private String name = "Outer object";

    public void test() {
        Runnable task = new Runnable() {
            @Override
            public void run() {
                System.out.println(this.getClass().getName());
                System.out.println(Example.this.name);
            }
        };

        task.run();
    }
}
```

---

# مزایای استفاده از Lambda

## ۱. کاهش کدهای تکراری

Lambda نیاز به تعریف کلاس‌های کوچک و یک‌بارمصرف را کم می‌کند.

```java
names.forEach(name -> System.out.println(name));
```

---

## ۲. خوانایی بهتر در منطق‌های کوتاه

برای Callbackها، شرط‌ها، تبدیل‌ها و مرتب‌سازی‌های ساده، لامبدا کد را واضح‌تر می‌کند.

```java
numbers.removeIf(number -> number < 0);
```

---

## ۳. هماهنگی با Stream API

بسیاری از عملیات Stream مانند `filter`، `map`، `reduce` و `forEach` برای دریافت Functional Interface طراحی شده‌اند.

```java
List<String> upperNames = names.stream()
    .map(String::toUpperCase)
    .toList();
```

---

## ۴. انتقال رفتار به متدها

```java
public static int operate(
        int first,
        int second,
        BinaryOperator<Integer> operation
) {
    return operation.apply(first, second);
}
```

استفاده:

```java
int sum = operate(5, 3, (a, b) -> a + b);
int product = operate(5, 3, (a, b) -> a * b);

System.out.println(sum);     // 8
System.out.println(product); // 15
```

---

# محدودیت‌ها و نکات مهم

## ۱. Lambda فقط با Functional Interface کار می‌کند

نادرست:

```java
interface Invalid {
    void first();
    void second();
}

// این کار ممکن نیست:
// Invalid value = () -> System.out.println("Hello");
```

---

## ۲. Lambda نمی‌تواند مستقیماً checked exception جدیدی ایجاد کند

نوع exceptionهایی که Lambda می‌تواند پرتاب کند، باید با امضای متد Functional Interface سازگار باشد.

```java
@FunctionalInterface
interface FileTask {
    void execute() throws java.io.IOException;
}

FileTask task = () -> {
    throw new java.io.IOException("File error");
};
```

---

## ۳. از Lambdaهای طولانی اجتناب کنید

Lambda برای منطق کوتاه و متمرکز مناسب است.

نامناسب:

```java
Runnable task = () -> {
    // ده‌ها خط منطق پیچیده،
    // شرط‌های بسیار زیاد،
    // مدیریت خطاهای متعدد...
};
```

بهتر است منطق پیچیده در یک متد یا کلاس جدا قرار گیرد:

```java
Runnable task = this::generateReport;
```

---

## ۴. کارایی را بدون اندازه‌گیری قضاوت نکنید

> [!warning]
> این تصور که Lambda همیشه باعث افزایش کارایی می‌شود، دقیق نیست.

Lambdaها عمدتاً برای **بیان بهتر رفتار و خوانایی کد** طراحی شده‌اند. عملکرد نهایی به عواملی مانند JVM، نوع عملیات، حجم داده، Boxing/Unboxing، نوع Stream و طراحی کلی برنامه بستگی دارد.

برای تصمیم‌گیری دربارهٔ Performance، از ابزارهای اندازه‌گیری مانند **JMH** استفاده کنید، نه حدس.

---

# بهترین روش‌ها

1. **برای Functional Interfaceهای ساده، Lambda را به Anonymous Class ترجیح دهید.**

2. **برای لامبداهای بسیار کوتاه از بدنهٔ تک‌عبارتی استفاده کنید.**

   ```java
   Predicate<String> isEmpty = String::isEmpty;
   ```

3. **اگر Lambda پیچیده شد، آن را به یک متد نام‌دار منتقل کنید.**

   ```java
   users.stream()
       .filter(this::isEligibleUser)
       .toList();
   ```

4. **نام متغیرها را معنادار انتخاب کنید.**

   بهتر:

   ```java
   Predicate<User> isActiveUser = user -> user.isActive();
   ```

   ضعیف‌تر:

   ```java
   Predicate<User> p = u -> u.isActive();
   ```

5. **در استفاده از متغیرهای mutable و پردازش موازی احتیاط کنید.**

   ```java
   // نامناسب در parallel stream
   List<Integer> result = new ArrayList<>();

   numbers.parallelStream()
       .filter(number -> number % 2 == 0)
       .forEach(result::add);
   ```

   بهتر:

   ```java
   List<Integer> result = numbers.parallelStream()
       .filter(number -> number % 2 == 0)
       .toList();
   ```

---

# جمع‌بندی

| اگر نیاز دارید... | انتخاب مناسب |
|---|---|
| اجرای یک رفتار بدون ورودی و خروجی | `Runnable` |
| بررسی یک شرط و دریافت `boolean` | `Predicate<T>` |
| تبدیل یک مقدار به مقدار دیگر | `Function<T, R>` |
| انجام عملیاتی بدون خروجی | `Consumer<T>` |
| تولید یک مقدار بدون ورودی | `Supplier<T>` |
| ترکیب دو مقدار هم‌نوع و تولید همان نوع | `BinaryOperator<T>` |
| ارجاع ساده به یک متد موجود | Method Reference (`::`) |
| پیاده‌سازی کلاس abstract یا چند متد | Anonymous Class / کلاس نام‌دار |

> [!quote]
> Lambda ابزاری برای کوتاه‌کردن صرفِ کد نیست؛ هدف اصلی آن، بیان روشن و تابعیِ رفتارهای کوچک و قابل انتقال در برنامه است.