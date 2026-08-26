

# مفاهیم شی‌گرایی و Package در Java

> [!abstract] هدف
> در این جزوه با اصول مهم شی‌گرایی در Java آشنا می‌شویم:
>
> - `Interface`
> - `Abstract Class`
> - `Inheritance`
> - `Encapsulation`
> - `Polymorphism`
> - `Package` و `import`

---

کلاسی که یک Interface را پیاده‌سازی می‌کند، از کلیدواژهٔ `implements` استفاده می‌کند.

```java
public class MyClass implements MyInterface {

    @Override
    public void method1() {
        System.out.println("method1 implementation");
    }

    @Override
    public int method2(int value) {
        return value * 2;
    }
}
```

---

## ویژگی‌های اصلی Interface

- متدهای abstract در Interface به‌صورت پیش‌فرض `public abstract` هستند.
- فیلدهای Interface به‌صورت پیش‌فرض `public static final` هستند؛ یعنی ثابت‌اند.
- یک کلاس می‌تواند چندین Interface را پیاده‌سازی کند.
- یک Interface می‌تواند از یک یا چند Interface دیگر ارث‌بری کند.
- Interface از Java 8 می‌تواند متدهای `default` و `static` داشته باشد.
- از Java 9 می‌توان در Interface متد `private` نیز تعریف کرد.

> [!note]
> در Interface لازم نیست برای متد abstract، صراحتاً `public abstract` بنویسیم:
>
> ```java
> void run();
> ```
>
> معادل است با:
>
> ```java
> public abstract void run();
> ```

---

## فیلدهای Interface

تمام فیلدها در Interface ثابت هستند:

```java
public interface Configuration {

    int MAX_CONNECTIONS = 100;
    String APPLICATION_NAME = "My Application";
}
```

در واقع Java آن را این‌گونه در نظر می‌گیرد:

```java
public interface Configuration {

    public static final int MAX_CONNECTIONS = 100;
    public static final String APPLICATION_NAME = "My Application";
}
```

استفاده:

```java
System.out.println(Configuration.MAX_CONNECTIONS);
```

---

## پیاده‌سازی چند Interface

Java اجازهٔ ارث‌بری از چند کلاس را نمی‌دهد، اما یک کلاس می‌تواند چندین Interface را پیاده‌سازی کند.

```java
public interface Printable {
    void print();
}

public interface Savable {
    void save();
}

public class Document implements Printable, Savable {

    @Override
    public void print() {
        System.out.println("Printing document...");
    }

    @Override
    public void save() {
        System.out.println("Saving document...");
    }
}
```

```text
Document
   ├── implements Printable
   └── implements Savable
```

---

## متدهای `default`

از Java 8، Interface می‌تواند متدی با پیاده‌سازی پیش‌فرض داشته باشد.

```java
public interface Logger {

    default void log(String message) {
        System.out.println("[LOG] " + message);
    }
}
```

کلاس پیاده‌ساز می‌تواند متد را همان‌طور استفاده کند:

```java
public class FileService implements Logger {

    public void readFile() {
        log("Reading file...");
    }
}
```

یا آن را بازنویسی کند:

```java
public class CustomLogger implements Logger {

    @Override
    public void log(String message) {
        System.out.println("[CUSTOM LOG] " + message);
    }
}
```

> [!tip]
> یکی از هدف‌های اصلی `default method`، امکان افزودن متد جدید به یک Interface قدیمی بدون شکستن تمام کلاس‌های پیاده‌ساز آن است.

---

## تعارض Default Methodها

اگر یک کلاس دو Interface با متد `default` هم‌نام و هم‌امضا را پیاده‌سازی کند، باید آن متد را صراحتاً Override کند.

```java
public interface A {

    default void display() {
        System.out.println("Display from A");
    }
}

public interface B {

    default void display() {
        System.out.println("Display from B");
    }
}
```

```java
public class C implements A, B {

    @Override
    public void display() {
        A.super.display();

        System.out.println("Display from C");
    }
}
```

---

## متدهای `static`

متدهای static متعلق به خود Interface هستند، نه Objectهای ساخته‌شده از کلاس پیاده‌ساز.

```java
public interface StringUtils {

    static boolean isBlank(String value) {
        return value == null || value.isBlank();
    }
}
```

استفاده:

```java
boolean result = StringUtils.isBlank("   ");

System.out.println(result); // true
```

---

## متدهای `private` در Interface — Java 9+

متدهای private برای استفادهٔ داخلی در `default method`ها یا `static method`ها کاربرد دارند.

```java
public interface Validator {

    default boolean isValidEmail(String email) {
        return hasText(email) && email.contains("@");
    }

    private boolean hasText(String value) {
        return value != null && !value.isBlank();
    }
}
```

> [!warning]
> متد private در Interface توسط کلاس پیاده‌ساز قابل دسترسی یا Override نیست.

---

## ارث‌بری بین Interfaceها

یک Interface می‌تواند از Interfaceهای دیگر ارث‌بری کند.

```java
public interface Readable {
    void read();
}

public interface Writable {
    void write();
}

public interface FileHandler extends Readable, Writable {
    void close();
}
```

کلاسی که `FileHandler` را پیاده‌سازی کند، باید تمام متدها را پیاده‌سازی کند:

```java
public class TextFileHandler implements FileHandler {

    @Override
    public void read() {
        System.out.println("Reading...");
    }

    @Override
    public void write() {
        System.out.println("Writing...");
    }

    @Override
    public void close() {
        System.out.println("Closing...");
    }
}
```

---

## Functional Interface

**Functional Interface** اینترفیسی است که دقیقاً یک متد abstract دارد.

```java
@FunctionalInterface
public interface Calculator {

    int calculate(int a, int b);
}
```

این نوع Interface برای Lambda Expression مناسب است:

```java
public class Main {

    public static void main(String[] args) {
        Calculator add = (a, b) -> a + b;

        System.out.println(add.calculate(10, 20)); // 30
    }
}
```

نمونه‌های معروف در کتابخانهٔ Java:

| Interface | کاربرد |
|---|---|
| `Runnable` | اجرای یک Task بدون خروجی |
| `Callable<T>` | اجرای Task با خروجی |
| `Comparator<T>` | مقایسهٔ Objectها |
| `Predicate<T>` | بررسی شرط و تولید `boolean` |
| `Function<T, R>` | تبدیل یک نوع به نوع دیگر |
| `Consumer<T>` | دریافت ورودی بدون خروجی |

---

# ۲. Abstract Class

## تعریف

**Abstract Class** کلاسی است که نمی‌توان مستقیماً از آن Object ساخت.

این نوع کلاس می‌تواند هم متدهای abstract و هم متدهای دارای پیاده‌سازی داشته باشد.

```java
public abstract class MyAbstractClass {

    public abstract void abstractMethod();

    public void concreteMethod() {
        System.out.println("Concrete method implementation");
    }
}
```

کلاس فرزند:

```java
public class ConcreteClass extends MyAbstractClass {

    @Override
    public void abstractMethod() {
        System.out.println("Implementation of abstract method");
    }
}
```

---

## متد Abstract

متد abstract فقط امضا دارد و بدنه ندارد:

```java
public abstract void draw();
```

کلاسی که از Abstract Class ارث‌بری می‌کند، معمولاً باید آن را پیاده‌سازی کند:

```java
public abstract class Shape {

    public abstract void draw();
}
```

```java
public class Circle extends Shape {

    @Override
    public void draw() {
        System.out.println("Drawing a circle");
    }
}
```

> [!note]
> اگر کلاس فرزند متدهای abstract را پیاده‌سازی نکند، خودش نیز باید `abstract` تعریف شود.

---

## ویژگی‌های Abstract Class

- نمی‌توان مستقیماً از آن Object ساخت.
- می‌تواند فیلد، سازنده و متد concrete داشته باشد.
- می‌تواند متد abstract داشته باشد.
- می‌تواند `private`، `protected`، `public` و `static` داشته باشد.
- یک کلاس فقط می‌تواند از **یک کلاس** ارث‌بری کند.
- Abstract Class می‌تواند Interfaceها را پیاده‌سازی کند.

---

## سازنده در Abstract Class

با اینکه نمی‌توان مستقیماً از Abstract Class نمونه ساخت، سازندهٔ آن هنگام ساخت Object از کلاس فرزند اجرا می‌شود.

```java
public abstract class Animal {

    protected final String name;

    public Animal(String name) {
        this.name = name;
        System.out.println("Animal constructor");
    }

    public abstract void makeSound();
}
```

```java
public class Dog extends Animal {

    public Dog(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " says: Bark");
    }
}
```

```java
Dog dog = new Dog("Max");

// Animal constructor
```

---

## Partial Implementation

Abstract Class برای اشتراک‌گذاری منطق مشترک بین چند کلاس مناسب است.

```java
public abstract class PaymentProcessor {

    public final void processPayment(double amount) {
        validateAmount(amount);
        pay(amount);
        printReceipt(amount);
    }

    private void validateAmount(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException(
                    "Amount must be greater than zero"
            );
        }
    }

    protected abstract void pay(double amount);

    private void printReceipt(double amount) {
        System.out.println("Payment successful: " + amount);
    }
}
```

```java
public class CardPaymentProcessor extends PaymentProcessor {

    @Override
    protected void pay(double amount) {
        System.out.println("Paying by card: " + amount);
    }
}
```

در این مثال:

- منطق کلی پرداخت در کلاس والد مشترک است.
- جزئیات پرداخت در کلاس‌های فرزند مشخص می‌شود.

---

# ۳. تفاوت Interface و Abstract Class

| ویژگی | Interface | Abstract Class |
|---|---|---|
| ایجاد Object مستقیم | ندارد | ندارد |
| ارث‌بری چندگانه | کلاس می‌تواند چند Interface را پیاده‌سازی کند | کلاس فقط از یک کلاس ارث‌بری می‌کند |
| Constructor | ندارد | دارد |
| فیلد Instance | ندارد | دارد |
| فیلد ثابت | دارد؛ همه `public static final` هستند | دارد |
| متد Abstract | دارد | دارد |
| متد Concrete | `default` و `static` دارد | دارد |
| سطح دسترسی متد abstract | به‌صورت پیش‌فرض `public` | می‌تواند `public` یا `protected` باشد |
| کاربرد اصلی | تعریف قرارداد و قابلیت | اشتراک‌گذاری state و منطق مشترک |

> [!tip] قانون سرانگشتی
> - اگر هدف، تعریف یک **قابلیت یا قرارداد** است، از `Interface` استفاده کن.
> - اگر کلاس‌ها باید **وضعیت مشترک، سازنده یا منطق مشترک** داشته باشند، از `Abstract Class` استفاده کن.

---

## ترکیب Abstract Class و Interface

یک کلاس می‌تواند از یک Abstract Class ارث‌بری کند و هم‌زمان چند Interface را پیاده‌سازی کند.

```java
public interface Drawable {
    void draw();
}
```

```java
public abstract class Shape {

    protected String color;

    public Shape(String color) {
        this.color = color;
    }

    public abstract double area();
}
```

```java
public class Rectangle extends Shape implements Drawable {

    private final double width;
    private final double height;

    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }

    @Override
    public double area() {
        return width * height;
    }

    @Override
    public void draw() {
        System.out.println("Drawing a " + color + " rectangle");
    }
}
```

---

# ۴. Inheritance — وراثت

## تعریف

**وراثت** مکانیزمی است که یک کلاس جدید را بر پایهٔ کلاس موجود ایجاد می‌کند.

- کلاس والد: `Parent`، `Base Class` یا `Superclass`
- کلاس فرزند: `Child`، `Derived Class` یا `Subclass`

```java
public class ParentClass {

    public void parentMethod() {
        System.out.println("Method from parent class");
    }
}
```

```java
public class ChildClass extends ParentClass {

    public void childMethod() {
        System.out.println("Method from child class");
    }
}
```

استفاده:

```java
ChildClass child = new ChildClass();

child.parentMethod();
child.childMethod();
```

---

## رابطهٔ IS-A

وراثت باید یک رابطهٔ منطقی **IS-A** ایجاد کند.

```text
Dog IS-A Animal
Circle IS-A Shape
Manager IS-A Employee
```

نمونه:

```java
public class Animal {

    public void eat() {
        System.out.println("Eating...");
    }
}
```

```java
public class Dog extends Animal {

    public void bark() {
        System.out.println("Bark");
    }
}
```

```java
Dog dog = new Dog();

dog.eat();
dog.bark();
```

> [!warning]
> از وراثت فقط برای اشتراک‌گذاری کد استفاده نکن. رابطهٔ واقعی بین دو کلاس باید از نوع **IS-A** باشد.
>
> برای رابطهٔ **HAS-A** معمولاً Composition انتخاب بهتری است.

---

## کلیدواژهٔ `super`

از `super` برای دسترسی به اعضای کلاس والد استفاده می‌شود.

### فراخوانی متد والد

```java
public class Parent {

    public void display() {
        System.out.println("Display from Parent");
    }
}
```

```java
public class Child extends Parent {

    @Override
    public void display() {
        super.display();
        System.out.println("Display from Child");
    }
}
```

خروجی:

```text
Display from Parent
Display from Child
```

### فراخوانی سازندهٔ والد

```java
public class Person {

    protected String name;

    public Person(String name) {
        this.name = name;
    }
}
```

```java
public class Employee extends Person {

    private final String employeeId;

    public Employee(String name, String employeeId) {
        super(name);
        this.employeeId = employeeId;
    }
}
```

> [!important]
> فراخوانی `super(...)` باید اولین دستور در Constructor کلاس فرزند باشد.

---

## Constructor Chaining

هنگام ساخت Object از کلاس فرزند، ابتدا Constructor والد اجرا می‌شود.

```java
public class Parent {

    public Parent() {
        System.out.println("Parent constructor");
    }
}
```

```java
public class Child extends Parent {

    public Child() {
        System.out.println("Child constructor");
    }
}
```

```java
new Child();
```

خروجی:

```text
Parent constructor
Child constructor
```

اگر `super()` را صریح ننویسیم، Java به‌طور خودکار آن را اضافه می‌کند؛ البته فقط زمانی که والد یک Constructor بدون پارامتر داشته باشد.

---

## محدودیت‌های وراثت

### وراثت تک‌کلاسی

هر کلاس فقط از یک کلاس می‌تواند ارث‌بری کند:

```java
public class Child extends Parent {
}
```

این کد مجاز نیست:

```java
// غیرمجاز
public class Child extends ParentA, ParentB {
}
```

اما پیاده‌سازی چند Interface مجاز است:

```java
public class Child extends Parent
        implements InterfaceA, InterfaceB {
}
```

---

## کلیدواژهٔ `final`

### کلاس `final`

نمی‌توان از کلاس `final` ارث‌بری کرد:

```java
public final class FinalClass {
}
```

```java
// غیرمجاز
public class ChildClass extends FinalClass {
}
```

### متد `final`

نمی‌توان متد `final` را Override کرد:

```java
public class Parent {

    public final void importantMethod() {
        System.out.println("Cannot override this method");
    }
}
```

```java
public class Child extends Parent {

    // غیرمجاز
    // @Override
    // public void importantMethod() {
    // }
}
```

---

# ۵. Method Overriding — بازنویسی متد

## تعریف

**Overriding** یعنی کلاس فرزند، پیاده‌سازی جدیدی از یک متد inherited ارائه کند.

```java
public class Animal {

    public void makeSound() {
        System.out.println("Animal sound");
    }
}
```

```java
public class Dog extends Animal {

    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}
```

---

## قوانین Overriding

- نام متد باید یکسان باشد.
- پارامترها باید یکسان باشند.
- نوع بازگشتی باید یکسان یا زیرنوعی از نوع بازگشتی والد باشد.
- سطح دسترسی نمی‌تواند محدودتر از متد والد شود.
- متدهای `final` قابل Override نیستند.
- متدهای `private` به ارث نمی‌رسند؛ بنابراین Override هم نمی‌شوند.
- متدهای `static` Override نمی‌شوند؛ بلکه **Method Hiding** رخ می‌دهد.
- استفاده از `@Override` قویاً توصیه می‌شود.

### مثال سطح دسترسی

```java
public class Parent {

    protected void display() {
        System.out.println("Parent");
    }
}
```

این حالت مجاز است؛ چون سطح دسترسی گسترده‌تر شده است:

```java
public class Child extends Parent {

    @Override
    public void display() {
        System.out.println("Child");
    }
}
```

اما این حالت مجاز نیست:

```java
public class Child extends Parent {

    // غیرمجاز: سطح دسترسی از protected به private کاهش یافته است
    // @Override
    // private void display() {
    // }
}
```

---

# ۶. Encapsulation — کپسوله‌سازی / محصورسازی

## تعریف

**Encapsulation** یعنی مخفی‌کردن جزئیات داخلی Object و کنترل دسترسی به داده‌ها و رفتارهای آن.

معمولاً با این الگو پیاده‌سازی می‌شود:

```text
private fields + public methods
```

```java
public class BankAccount {

    private double balance;

    public BankAccount(double initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException(
                    "Initial balance cannot be negative"
            );
        }

        this.balance = initialBalance;
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException(
                    "Deposit amount must be positive"
            );
        }

        balance += amount;
    }

    public void withdraw(double amount) {
        if (amount <= 0 || amount > balance) {
            throw new IllegalArgumentException(
                    "Invalid withdrawal amount"
            );
        }

        balance -= amount;
    }
}
```

استفاده:

```java
BankAccount account = new BankAccount(1_000);

account.deposit(500);
account.withdraw(200);

System.out.println(account.getBalance()); // 1300
```

---

## چرا Fieldها را `public` نکنیم؟

روش نامناسب:

```java
public class BankAccount {
    public double balance;
}
```

در این حالت هر بخشی از برنامه می‌تواند مقدار نامعتبر قرار دهد:

```java
account.balance = -1_000_000;
```

روش درست:

```java
private double balance;
```

و اعمال تغییرات از طریق متدهای کنترل‌شده:

```java
public void deposit(double amount) {
    // Validation
}
```

---

## Access Modifiers

| Modifier | همان Class | همان Package | Subclass | همه‌جا |
|---|:---:|:---:|:---:|:---:|
| `private` | ✅ | ❌ | ❌ | ❌ |
| بدون Modifier | ✅ | ✅ | ❌* | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

> [!note]
> سطح دسترسی بدون Modifier را **package-private** می‌نامند.

---

## Immutable Class — کلاس تغییرناپذیر

Object یک کلاس **Immutable** پس از ساخته‌شدن تغییر نمی‌کند.

نمونه:

```java
public final class User {

    private final String username;
    private final int age;

    public User(String username, int age) {
        this.username = username;
        this.age = age;
    }

    public String getUsername() {
        return username;
    }

    public int getAge() {
        return age;
    }
}
```

ویژگی‌های رایج Immutable Class:

- کلاس معمولاً `final` است.
- Fieldها `private final` هستند.
- Setter ندارد.
- وضعیت Object پس از Constructor تغییر نمی‌کند.
- برای Fieldهای Mutable مانند `List` باید **Defensive Copy** انجام شود.

### Immutable با Collection

```java
import java.util.List;

public final class Team {

    private final List<String> members;

    public Team(List<String> members) {
        this.members = List.copyOf(members);
    }

    public List<String> getMembers() {
        return members;
    }
}
```

```java
List<String> names = new ArrayList<>();
names.add("Ali");

Team team = new Team(names);

names.add("Sara");

System.out.println(team.getMembers()); // [Ali]
```

> [!important]
> `List.copyOf(...)` یک کپی غیرقابل‌تغییر می‌سازد و از تغییر وضعیت داخلی Object جلوگیری می‌کند.

---

# ۷. Polymorphism — چندشکلی

## تعریف

**Polymorphism** یعنی بتوان با یک مرجع عمومی، Objectهای متفاوت را مدیریت کرد و در زمان مناسب رفتار مخصوص هر Object اجرا شود.

دو نوع مهم چندشکلی در Java:

1. **Compile-Time Polymorphism** → `Method Overloading`
2. **Runtime Polymorphism** → `Method Overriding`

---

## Method Overloading — چندبارگذاری متد

چند متد با نام یکسان و پارامترهای متفاوت در یک کلاس تعریف می‌شوند.

```java
public class MathOperations {

    public int add(int a, int b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }

    public double add(double a, double b) {
        return a + b;
    }
}
```

```java
MathOperations math = new MathOperations();

System.out.println(math.add(2, 3));       // 5
System.out.println(math.add(2, 3, 4));    // 9
System.out.println(math.add(2.5, 3.5));   // 6.0
```

### قوانین Overloading

برای Overload شدن، حداقل یکی از موارد زیر باید فرق کند:

- تعداد پارامترها
- نوع پارامترها
- ترتیب نوع پارامترها

تغییر نوع بازگشتی به‌تنهایی کافی نیست:

```java
public int getValue() {
    return 10;
}

// غیرمجاز
// public String getValue() {
//     return "10";
// }
```

---

## Method Overriding و Runtime Polymorphism

```java
public class Animal {

    public void makeSound() {
        System.out.println("Animal sound");
    }
}
```

```java
public class Dog extends Animal {

    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}
```

```java
public class Cat extends Animal {

    @Override
    public void makeSound() {
        System.out.println("Meow");
    }
}
```

استفاده از مرجع والد:

```java
public class Main {

    public static void main(String[] args) {
        Animal dog = new Dog();
        Animal cat = new Cat();

        dog.makeSound(); // Bark
        cat.makeSound(); // Meow
    }
}
```

> [!important]
> نوع مرجع `Animal` است، اما متدی که اجرا می‌شود براساس نوع واقعی Object یعنی `Dog` یا `Cat` در **زمان اجرا** انتخاب می‌شود.
>
> این رفتار را **Dynamic Method Dispatch** یا **Late Binding** می‌نامند.

---

## Polymorphism با Interface

```java
public interface PaymentMethod {
    void pay(double amount);
}
```

```java
public class CardPayment implements PaymentMethod {

    @Override
    public void pay(double amount) {
        System.out.println("Paid by card: " + amount);
    }
}
```

```java
public class CashPayment implements PaymentMethod {

    @Override
    public void pay(double amount) {
        System.out.println("Paid by cash: " + amount);
    }
}
```

```java
public class PaymentService {

    public void process(PaymentMethod paymentMethod, double amount) {
        paymentMethod.pay(amount);
    }
}
```

```java
PaymentService paymentService = new PaymentService();

paymentService.process(new CardPayment(), 100);
paymentService.process(new CashPayment(), 200);
```

مزیت این روش:

- وابستگی کد به Interface است، نه پیاده‌سازی مشخص.
- افزودن روش پرداخت جدید ساده‌تر می‌شود.
- تست‌پذیری و نگهداری کد بهتر می‌شود.

---

## Casting در Polymorphism

### Upcasting

تبدیل Object فرزند به مرجع والد به‌صورت خودکار انجام می‌شود:

```java
Dog dog = new Dog();

Animal animal = dog;
```

### Downcasting

تبدیل مرجع والد به فرزند باید صریح باشد:

```java
Animal animal = new Dog();

Dog dog = (Dog) animal;
```

برای جلوگیری از `ClassCastException` از `instanceof` استفاده کن:

```java
Animal animal = new Dog();

if (animal instanceof Dog dog) {
    dog.makeSound();
}
```

> [!warning]
> Downcasting فقط زمانی مجاز است که Object واقعی از نوع موردنظر باشد.

---

# ۸. Package در Java

## تعریف

**Package** روشی برای سازمان‌دهی کلاس‌ها، Interfaceها، Enumها و Recordها در گروه‌های منطقی است.

اهداف اصلی Package:

- جلوگیری از تداخل نام کلاس‌ها
- خوانایی و نظم بهتر پروژه
- کنترل سطح دسترسی package-private
- مدیریت بهتر پروژه‌های بزرگ

نمونه:

```java
package com.example.myapp;
```

---

## نام‌گذاری Package

رسم رایج این است که نام Package با دامنهٔ اینترنتی معکوس شروع شود:

```text
com.example.myapp
ir.example.project
io.github.username.project
```

ویژگی‌های نام Package:

- تمام حروف کوچک
- بدون فاصله
- بهتر است کوتاه، معنادار و پایدار باشد
- معمولاً از نام شرکت، دامنه یا نام پروژه استفاده می‌شود

---

## ساختار دایرکتوری Package

نام Package باید با مسیر دایرکتوری هماهنگ باشد.

```text
my-project/
├── src/
│   └── com/
│       └── example/
│           └── myapp/
│               └── MyClass.java
```

محتوای فایل:

```java
package com.example.myapp;

public class MyClass {

    public void display() {
        System.out.println("Hello from MyClass!");
    }
}
```

---

## ترتیب `package` و `import`

در فایل Java، ترتیب استاندارد به این صورت است:

```java
package com.example.myapp;

import java.util.List;
import java.util.ArrayList;

public class Main {
}
```

ترتیب کلی:

```text
1. package
2. import
3. class / interface / enum / record
```

> [!important]
> دستور `package` باید اولین دستور فایل باشد؛ البته Comment و فضای خالی قبل از آن مشکلی ایجاد نمی‌کند.

---

## استفاده از `import`

برای استفاده از Classهای Package دیگر باید آن‌ها را Import کنیم.

```java
import com.example.myapp.utils.Utility;
```

```java
public class Main {

    public static void main(String[] args) {
        Utility.printMessage("Hello");
    }
}
```

### Import با `*`

```java
import com.example.myapp.utils.*;
```

این روش تمام Typeهای مستقیم همان Package را Import می‌کند.

> [!note]
> `import package.*` زیرپکیج‌ها را Import نمی‌کند.
>
> برای مثال:
>
> ```java
> import com.example.*;
> ```
>
> شامل Typeهای داخل `com.example.utils` نمی‌شود.

---

## Static Import

برای Import کردن عضو static یک Class استفاده می‌شود.

```java
import static java.lang.Math.PI;
import static java.lang.Math.max;
```

```java
public class Main {

    public static void main(String[] args) {
        System.out.println(PI);
        System.out.println(max(10, 20));
    }
}
```

> [!tip]
> از `static import` با احتیاط استفاده کن؛ استفادهٔ زیاد ممکن است خوانایی کد را کاهش دهد.

---

## Default Package

اگر در فایل Java دستور `package` وجود نداشته باشد، Class در **Default Package** قرار می‌گیرد.

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

> [!warning]
> در پروژه‌های واقعی و بزرگ از Default Package استفاده نکن.
>
> Classهای داخل Packageهای نام‌دار نمی‌توانند Classهای Default Package را Import کنند.

---

## Nested Package

در Java، چیزی به‌نام زیرپکیج واقعی با دسترسی ویژه وجود ندارد. برای Java، این دو Package مستقل هستند:

```text
com.example
com.example.utils
```

با وجود ساختار ظاهری، اعضای `com.example` به اعضای package-private در `com.example.utils` دسترسی ندارند.

---

## Classpath

**Classpath** مسیرهایی را مشخص می‌کند که Java باید Classها و Libraryها را در آن‌ها جست‌وجو کند.

### کامپایل

```bash
javac -d out src/com/example/myapp/Main.java
```

توضیح:

- `-d out`: فایل‌های `.class` را در پوشهٔ `out` قرار می‌دهد.
- ساختار Package به‌شکل خودکار در `out` ساخته می‌شود.

### اجرا

```bash
java -cp out com.example.myapp.Main
```

توضیح:

- `-cp out`: مسیر Classpath است.
- هنگام اجرا از نام کامل Class شامل Package استفاده می‌کنیم.

---

## مثال کامل Package

### ساختار پروژه

```text
myproject/
├── src/
│   └── com/
│       └── example/
│           └── myapp/
│               ├── Main.java
│               ├── HelloWorld.java
│               └── utils/
│                   └── Utility.java
└── out/
```

### `HelloWorld.java`

```java
package com.example.myapp;

public class HelloWorld {

    public void sayHello() {
        System.out.println("Hello, World!");
    }
}
```

### `Utility.java`

```java
package com.example.myapp.utils;

public class Utility {

    private Utility() {
        // جلوگیری از ساخت Object برای Utility Class
    }

    public static void printMessage(String message) {
        System.out.println(message);
    }
}
```

### `Main.java`

```java
package com.example.myapp;

import com.example.myapp.utils.Utility;

public class Main {

    public static void main(String[] args) {
        HelloWorld helloWorld = new HelloWorld();

        helloWorld.sayHello();

        Utility.printMessage("This is a utility message.");
    }
}
```

### کامپایل و اجرا

از ریشهٔ پروژه:

```bash
javac -d out \
  src/com/example/myapp/Main.java \
  src/com/example/myapp/HelloWorld.java \
  src/com/example/myapp/utils/Utility.java
```

```bash
java -cp out com.example.myapp.Main
```

خروجی:

```text
Hello, World!
This is a utility message.
```

---

# ۹. جمع‌بندی

```text
OOP in Java
├── Interface
│   ├── قرارداد رفتار
│   ├── implements
│   ├── multiple interfaces
│   ├── default / static methods
│   └── functional interface
│
├── Abstract Class
│   ├── عدم امکان نمونه‌سازی مستقیم
│   ├── abstract methods
│   ├── concrete methods
│   ├── fields
│   └── constructors
│
├── Inheritance
│   ├── extends
│   ├── IS-A relationship
│   ├── super
│   └── final
│
├── Encapsulation
│   ├── private fields
│   ├── کنترل دسترسی
│   ├── validation
│   └── immutability
│
├── Polymorphism
│   ├── overloading
│   ├── overriding
│   ├── dynamic dispatch
│   └── interface-based design
│
└── Package
    ├── organization
    ├── namespace
    ├── import
    └── classpath
```

> [!summary] نکتهٔ نهایی
> در طراحی Java معمولاً بهتر است:
>
> ```text
> Contract / Capability        → Interface
> Shared state / common logic  → Abstract Class
> واقعی بودن رابطهٔ IS-A        → Inheritance
> محافظت از state              → Encapsulation
> رفتار قابل‌تعویض             → Polymorphism
> سازمان‌دهی پروژه             → Package
> ```
```