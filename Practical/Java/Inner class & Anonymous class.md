---
title: کلاس‌های داخلی و ناشناس در جاوا
tags:
  - java
  - inner-class
  - anonymous-class
  - design-patterns
  - oop
aliases:
  - Inner Classes
  - Anonymous Classes
  - کلاس داخلی جاوا
created: 2026-08-25
---

# جزوه جامع کلاس‌های داخلی و ناشناس در جاوا

> [!summary] تعریف کوتاه
> در جاوا می‌توان یک کلاس را داخل کلاس، متد یا حتی یک بلوک تعریف کرد.  
> این قابلیت برای **سازمان‌دهی بهتر کد**، **کپسوله‌سازی** و پیاده‌سازی رفتارهای کوچک و موقت کاربرد دارد.

---

## فهرست مطالب

- [[#مقدمه]]
- [[#دسته‌بندی کلاس‌های داخلی]]
- [[#کلاس داخلی غیر استاتیک (Non-static Inner Class)]]
- [[#کلاس داخلی استاتیک (Static Nested Class)]]
- [[#کلاس محلی (Local Inner Class)]]
- [[#کلاس ناشناس (Anonymous Class)]]
- [[#تفاوت انواع کلاس‌های داخلی]]
- [[#دسترسی به متغیرهای محلی و Effectively Final]]
- [[#کاربردهای رایج]]
- [[#الگوی Builder با کلاس داخلی استاتیک]]
- [[#Singleton با کلاس داخلی استاتیک]]
- [[#نکات، محدودیت‌ها و بهترین روش‌ها]]
- [[#جمع‌بندی]]

---

## مقدمه

در جاوا، یک کلاس می‌تواند داخل کلاس دیگری تعریف شود. به این کلاس‌ها معمولاً **Nested Class** گفته می‌شود.

به‌طور کلی، Nested Classها دو دسته هستند:

1. **Static Nested Class**  
   کلاس تو در توی استاتیک

2. **Inner Class**  
   کلاس داخلی غیر استاتیک؛ به یک نمونه از کلاس خارجی وابسته است.

علاوه بر این‌ها، جاوا دو نوع کلاس داخلی با محدودهٔ محدودتر نیز ارائه می‌دهد:

- **Local Inner Class**: تعریف‌شده درون متد، سازنده یا بلاک
- **Anonymous Class**: کلاسی بدون نام که هم‌زمان با ساخت شیء تعریف می‌شود.

> [!note]
> در گفتگوهای روزمره معمولاً همهٔ این موارد را «کلاس داخلی» می‌نامند؛  
> اما از نظر دقیق فنی، `static nested class` یک **Inner Class واقعی** محسوب نمی‌شود؛ زیرا به نمونهٔ کلاس خارجی وابسته نیست.

---

## دسته‌بندی کلاس‌های داخلی

```text
OuterClass
│
├── Static Nested Class
│
└── Inner Class
    │
    ├── Member Inner Class (غیر استاتیک)
    ├── Local Inner Class
    └── Anonymous Class
```

| نوع | نام دارد؟ | نیازمند نمونهٔ کلاس خارجی؟ | محل تعریف |
|---|---:|---:|---|
| Member Inner Class | بله | بله | مستقیم داخل کلاس |
| Static Nested Class | بله | خیر | مستقیم داخل کلاس با `static` |
| Local Inner Class | بله | معمولاً بله | داخل متد، سازنده یا بلاک |
| Anonymous Class | خیر | بسته به محل استفاده | هم‌زمان با ساخت شیء |

---

# کلاس داخلی غیر استاتیک (Non-static Inner Class)

این نوع کلاس مستقیماً داخل کلاس خارجی تعریف می‌شود و **استاتیک نیست**.

کلاس داخلی غیر استاتیک به یک شیء از کلاس خارجی متصل است؛ بنابراین می‌تواند به تمام اعضای کلاس خارجی، حتی اعضای `private` و غیر استاتیک، دسترسی داشته باشد.

```java
public class OuterClass {

    private String outerField = "Outer field";

    public class InnerClass {

        public void display() {
            System.out.println(outerField);
        }
    }

    public void createInnerInstance() {
        InnerClass inner = new InnerClass();
        inner.display();
    }
}
```

---

## ایجاد نمونه از کلاس داخلی غیر استاتیک

برای ساختن `InnerClass` از بیرون کلاس خارجی، ابتدا باید یک شیء از `OuterClass` بسازیم.

```java
OuterClass outer = new OuterClass();

OuterClass.InnerClass inner = outer.new InnerClass();

inner.display();
```

### چرا از `outer.new InnerClass()` استفاده می‌کنیم؟

زیرا شیء `InnerClass` به یک شیء مشخص از `OuterClass` وابسته است.

```java
public class OuterClass {

    private String name;

    public OuterClass(String name) {
        this.name = name;
    }

    public class InnerClass {
        public void printOuterName() {
            System.out.println(name);
        }
    }
}
```

```java
OuterClass firstOuter = new OuterClass("First");
OuterClass secondOuter = new OuterClass("Second");

OuterClass.InnerClass firstInner = firstOuter.new InnerClass();
OuterClass.InnerClass secondInner = secondOuter.new InnerClass();

firstInner.printOuterName();   // First
secondInner.printOuterName();  // Second
```

هر شیء داخلی به نمونهٔ خارجی خودش متصل است.

---

## دسترسی به اعضای همنام کلاس خارجی

اگر کلاس داخلی و خارجی فیلدی با نام یکسان داشته باشند، برای اشاره به عضو کلاس خارجی از این ساختار استفاده می‌کنیم:

```java
OuterClass.this.fieldName
```

مثال:

```java
public class OuterClass {

    private String message = "پیام کلاس خارجی";

    public class InnerClass {

        private String message = "پیام کلاس داخلی";

        public void printMessages() {
            System.out.println(message);
            System.out.println(OuterClass.this.message);
        }
    }
}
```

خروجی:

```text
پیام کلاس داخلی
پیام کلاس خارجی
```

---

# کلاس داخلی استاتیک (Static Nested Class)

کلاس داخلی استاتیک در واقع یک کلاس تو در تو است که به نمونهٔ کلاس خارجی وابسته نیست.

```java
public class OuterClass {

    private static String outerStaticField = "Outer static field";

    public static class StaticInnerClass {

        public void display() {
            System.out.println(outerStaticField);
        }
    }
}
```

ساخت نمونه:

```java
OuterClass.StaticInnerClass inner =
    new OuterClass.StaticInnerClass();

inner.display();
```

در این حالت نیازی به ساختن شیء از `OuterClass` نداریم.

---

## محدودیت دسترسی در Static Nested Class

یک کلاس داخلی استاتیک به اعضای استاتیک کلاس خارجی دسترسی مستقیم دارد:

```java
public class OuterClass {

    private static String staticValue = "Static value";

    public static class StaticInnerClass {
        public void printValue() {
            System.out.println(staticValue);
        }
    }
}
```

اما به اعضای غیر استاتیک دسترسی مستقیم ندارد:

```java
public class OuterClass {

    private String instanceValue = "Instance value";

    public static class StaticInnerClass {
        public void printValue() {
            // خطا: به عضو غیر استاتیک دسترسی مستقیم نداریم.
            // System.out.println(instanceValue);
        }
    }
}
```

برای دسترسی به عضو غیر استاتیک، باید صریحاً یک نمونه از کلاس خارجی دریافت شود:

```java
public class OuterClass {

    private String instanceValue = "Instance value";

    public static class StaticInnerClass {

        public void printValue(OuterClass outer) {
            System.out.println(outer.instanceValue);
        }
    }
}
```

> [!important]
> در یک متد `static` نمی‌توان بدون داشتن یک شیء از کلاس خارجی، عضو غیر استاتیک یا کلاس داخلی غیر استاتیک را ایجاد کرد.

---

# کلاس محلی (Local Inner Class)

کلاس محلی درون یک متد، سازنده یا بلاک تعریف می‌شود.

محدودهٔ دسترسی آن فقط همان بلوک است و بیرون از آن قابل استفاده نیست.

```java
public class OuterClass {

    public void methodWithInnerClass() {

        class LocalInnerClass {

            public void display() {
                System.out.println("Local inner class in a method");
            }
        }

        LocalInnerClass localInner = new LocalInnerClass();
        localInner.display();
    }
}
```

---

## ویژگی‌های Local Inner Class

- فقط در همان متد یا بلاک تعریف‌شده قابل استفاده است.
- نمی‌توان آن را از بیرون متد ساخت.
- می‌تواند به اعضای کلاس خارجی دسترسی داشته باشد.
- می‌تواند به متغیرهای محلی `final` یا **effectively final** دسترسی داشته باشد.
- نمی‌تواند دارای modifierهایی مانند `public`، `private` یا `protected` باشد.

مثال:

```java
public class Calculator {

    public void calculate() {

        class Adder {
            public int add(int a, int b) {
                return a + b;
            }
        }

        Adder adder = new Adder();

        int result = adder.add(5, 3);

        System.out.println("Result: " + result);
    }

    public static void main(String[] args) {
        Calculator calculator = new Calculator();
        calculator.calculate();
    }
}
```

خروجی:

```text
Result: 8
```

---

# کلاس ناشناس (Anonymous Class)

کلاس ناشناس یک کلاس بدون نام است که هنگام ساخت شیء تعریف می‌شود.

از کلاس ناشناس معمولاً برای پیاده‌سازی سریع موارد زیر استفاده می‌شود:

- Interfaceها
- Abstract Classها
- Override کردن رفتار یک کلاس موجود برای استفاده‌ای کوتاه و محدود

ساختار کلی:

```java
Type variable = new Type() {
    // بدنهٔ کلاس ناشناس
};
```

---

## پیاده‌سازی Interface با Anonymous Class

```java
public class OuterClass {

    public void createAnonymousClass() {

        Runnable runnable = new Runnable() {

            @Override
            public void run() {
                System.out.println("Anonymous class implementing Runnable");
            }
        };

        new Thread(runnable).start();
    }
}
```

در این مثال:

- یک Interface به نام `Runnable` داریم.
- بدون تعریف یک کلاس جداگانه، متد `run()` را پیاده‌سازی کرده‌ایم.
- سپس آن را به `Thread` داده‌ایم.

---

## کلاس ناشناس برای Listener

کلاس‌های ناشناس در برنامه‌های رابط کاربری مانند Swing بسیار رایج بودند.

```java
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;

public class ButtonExample {

    private JButton button;

    public ButtonExample() {
        button = new JButton("Click Me");

        button.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent event) {
                System.out.println("Button clicked!");
            }
        });
    }
}
```

---

## کلاس ناشناس در برابر Lambda Expression

اگر Interface فقط **یک متد انتزاعی** داشته باشد (*Functional Interface*)، اغلب Lambda خواناتر است.

کلاس ناشناس:

```java
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running...");
    }
};
```

Lambda Expression:

```java
Runnable runnable = () -> System.out.println("Running...");
```

> [!tip]
> برای پیاده‌سازی‌های ساده از Functional Interfaceها، معمولاً Lambda انتخاب بهتری است.  
> اگر به تعریف فیلد، متد کمکی، initializer یا override کردن چند متد نیاز دارید، کلاس ناشناس می‌تواند مناسب‌تر باشد.

---

## Anonymous Class با Abstract Class

```java
abstract class Animal {
    abstract void makeSound();
}

public class Main {

    public static void main(String[] args) {

        Animal dog = new Animal() {

            @Override
            void makeSound() {
                System.out.println("Woof!");
            }
        };

        dog.makeSound();
    }
}
```

---

## محدودیت‌های کلاس ناشناس

کلاس ناشناس:

- نامی ندارد.
- فقط همان‌جا که ساخته شده قابل استفاده است.
- نمی‌توان Constructor با نام خودش برای آن تعریف کرد.
- معمولاً برای منطق‌های کوتاه مناسب است.
- می‌تواند به متغیرهای محلی `final` یا `effectively final` دسترسی داشته باشد.
- می‌تواند فیلد و متد جدید داشته باشد، اما اگر مرجع آن از نوع Interface یا کلاس والد باشد، مستقیماً به اعضای اختصاصی آن دسترسی ندارید.

> [!warning] اصلاح یک تصور رایج
> این جمله که «کلاس ناشناس نمی‌تواند متغیر جدید اضافه کند» درست نیست.  
> کلاس ناشناس می‌تواند فیلد و متد تعریف کند؛ اما چون نوع مرجع معمولاً Interface یا کلاس والد است، اعضای اختصاصی آن از طریق همان مرجع در دسترس نیستند.

مثال:

```java
Runnable task = new Runnable() {

    private int count = 0;

    @Override
    public void run() {
        count++;
        System.out.println("Count: " + count);
    }

    private void reset() {
        count = 0;
    }
};
```

در مثال بالا `count` و `reset()` در کلاس ناشناس تعریف شده‌اند، اما از طریق متغیر `task` نمی‌توان `reset()` را فراخوانی کرد؛ چون نوع `task` برابر `Runnable` است.

---

# تفاوت انواع کلاس‌های داخلی

| ویژگی | Inner غیر استاتیک | Static Nested | Local Inner | Anonymous |
|---|---|---|---|---|
| نام دارد | ✅ | ✅ | ✅ | ❌ |
| وابسته به شیء Outer | ✅ | ❌ | در صورت استفاده از اعضای Outer | در صورت استفاده از اعضای Outer |
| دسترسی مستقیم به اعضای غیر استاتیک Outer | ✅ | ❌ | ✅ | ✅ |
| دسترسی به متغیر محلی متد | — | — | `final` / effectively final | `final` / effectively final |
| قابلیت استفادهٔ مجدد | بالا | بالا | محدود | بسیار محدود |
| محل تعریف | بدنهٔ کلاس | بدنهٔ کلاس | متد یا بلاک | در محل ایجاد شیء |

---

# دسترسی به متغیرهای محلی و `effectively final`

کلاس‌های محلی و ناشناس می‌توانند به متغیرهای محلی متد دسترسی داشته باشند؛ اما آن متغیر نباید پس از مقداردهی تغییر کند.

```java
public void printMessage() {

    String message = "Hello";

    class Printer {
        void print() {
            System.out.println(message);
        }
    }

    new Printer().print();
}
```

این متغیر **effectively final** است؛ یعنی با اینکه کلمهٔ `final` ندارد، بعد از مقداردهی تغییر نکرده است.

نمونهٔ نادرست:

```java
public void printMessage() {

    String message = "Hello";

    class Printer {
        void print() {
            System.out.println(message);
        }
    }

    message = "Changed"; // خطای کامپایل

    new Printer().print();
}
```

نمونهٔ صحیح با `final`:

```java
public void printMessage() {

    final String message = "Hello";

    Runnable task = new Runnable() {
        @Override
        public void run() {
            System.out.println(message);
        }
    };

    task.run();
}
```

---

# کاربردهای رایج

## ۱. پیاده‌سازی Listenerها و Callbackها

```java
public class Button {

    public void setOnClickListener(OnClickListener listener) {
        listener.onClick();
    }

    public interface OnClickListener {
        void onClick();
    }

    public static void main(String[] args) {
        Button button = new Button();

        button.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick() {
                System.out.println("Button clicked!");
            }
        });
    }
}
```

خروجی:

```text
Button clicked!
```

---

## ۲. پیاده‌سازی ساختارهای داده

کلاس داخلی `Node` معمولاً فقط برای پیاده‌سازی داخلی یک ساختار داده لازم است؛ بنابراین می‌توان آن را `private` تعریف کرد.

```java
public class BinaryTree {

    private Node root;

    private class Node {

        int value;
        Node left;
        Node right;

        Node(int value) {
            this.value = value;
        }
    }

    public void add(int value) {
        root = addRecursive(root, value);
    }

    private Node addRecursive(Node current, int value) {

        if (current == null) {
            return new Node(value);
        }

        if (value < current.value) {
            current.left = addRecursive(current.left, value);
        } else if (value > current.value) {
            current.right = addRecursive(current.right, value);
        }

        return current;
    }
}
```

> [!tip]
> اگر `Node` نیازی به اعضای غیر استاتیک `BinaryTree` ندارد، بهتر است آن را `private static class Node` بنویسیم تا وابستگی غیرضروری به شیء خارجی ایجاد نشود.

---

## ۳. مدیریت وضعیت با State Pattern

```java
public class StateMachine {

    private State currentState;

    public StateMachine() {
        currentState = new StartState();
    }

    public void changeState(State newState) {
        currentState = newState;
    }

    public void handleEvent() {
        currentState.handle();
    }

    private interface State {
        void handle();
    }

    private class StartState implements State {

        @Override
        public void handle() {
            System.out.println("Start State: Handling event...");
            changeState(new EndState());
        }
    }

    private class EndState implements State {

        @Override
        public void handle() {
            System.out.println("End State: Handling event...");
        }
    }

    public static void main(String[] args) {
        StateMachine machine = new StateMachine();

        machine.handleEvent();
        machine.handleEvent();
    }
}
```

خروجی:

```text
Start State: Handling event...
End State: Handling event...
```

---

# الگوی Builder با کلاس داخلی استاتیک

الگوی **Builder** برای ساخت اشیایی با پارامترهای اختیاری زیاد مناسب است و از سازنده‌های طولانی و مبهم جلوگیری می‌کند.

```java
public class Book {

    private final String author;
    private final String title;
    private final int price;
    private final TypeBook type;

    private Book(Builder builder) {
        this.author = builder.author;
        this.title = builder.title;
        this.price = builder.price;
        this.type = builder.type;
    }

    public static class Builder {

        private String author;
        private String title;
        private int price;
        private TypeBook type;

        public Builder author(String author) {
            this.author = author;
            return this;
        }

        public Builder title(String title) {
            this.title = title;
            return this;
        }

        public Builder price(int price) {
            this.price = price;
            return this;
        }

        public Builder type(TypeBook type) {
            this.type = type;
            return this;
        }

        public Book build() {
            return new Book(this);
        }
    }

    @Override
    public String toString() {
        return "Book{" +
            "author='" + author + '\'' +
            ", title='" + title + '\'' +
            ", price=" + price +
            ", type=" + type +
            '}';
    }
}

enum TypeBook {
    MATH,
    SCIENCE,
    LITERATURE,
    HISTORY
}
```

استفاده:

```java
public class Test {

    public static void main(String[] args) {

        Book book = new Book.Builder()
            .author("Ali")
            .title("The Great Book")
            .price(100)
            .type(TypeBook.MATH)
            .build();

        System.out.println(book);
    }
}
```

> [!note]
> `Builder` معمولاً `static` تعریف می‌شود؛ چون برای ساخته‌شدن، نیازی به یک شیء از `Book` ندارد.

---

# Singleton با Static Nested Class

یکی از روش‌های استاندارد و Thread-safe برای پیاده‌سازی Singleton، استفاده از یک کلاس داخلی استاتیک است.

```java
public class Singleton {

    private Singleton() {
    }

    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

استفاده:

```java
Singleton first = Singleton.getInstance();
Singleton second = Singleton.getInstance();

System.out.println(first == second);
// true
```

### مزایای این روش

- ایجاد تنبل (*Lazy Initialization*)؛ شیء هنگام اولین فراخوانی `getInstance()` ساخته می‌شود.
- Thread-safe بودن به کمک سازوکار بارگذاری کلاس در JVM.
- عدم نیاز به `synchronized`.
- خوانایی و سادگی مناسب.

---

# دسترسی کلاس داخلی به اعضای `private`

کلاس خارجی و کلاس داخلی می‌توانند به اعضای خصوصی یکدیگر دسترسی داشته باشند.

```java
public class OuterClass {

    private String secret = "Secret of outer class";

    public class InnerClass {

        private String innerSecret = "Secret of inner class";

        public void printSecrets() {
            System.out.println(secret);
            System.out.println(innerSecret);
        }
    }

    public void readInnerSecret() {
        InnerClass inner = new InnerClass();

        System.out.println(inner.innerSecret);
    }
}
```

> [!important]
> این دسترسی به دلیل رابطهٔ نزدیک بین کلاس خارجی و کلاس داخلی در زبان جاوا مجاز است؛ حتی اگر فیلدها `private` باشند.

---

# نکات، محدودیت‌ها و بهترین روش‌ها

## ۱. در صورت نیاز نداشتن به Outer، از `static` استفاده کنید

اگر کلاس تو در تو به اعضای نمونه‌ای کلاس خارجی نیاز ندارد، آن را `static` تعریف کنید.

```java
public class User {

    private String username;

    // بهتر: Address به username وابسته نیست.
    public static class Address {
        private String city;
        private String street;
    }
}
```

این کار از نگه‌داشتن ناخواستهٔ مرجع به شیء `Outer` جلوگیری می‌کند.

---

## ۲. از کلاس ناشناس برای منطق بزرگ استفاده نکنید

کلاس‌های ناشناس برای پیاده‌سازی‌های کوتاه مناسب‌اند.

نامناسب:

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        // ده‌ها خط منطق پیچیده...
    }
};
```

در چنین شرایطی، بهتر است یک کلاس نام‌دار بسازید:

```java
public class ReportGenerationTask implements Runnable {
    @Override
    public void run() {
        // منطق پیچیده و قابل تست
    }
}
```

---

## ۳. نام‌گذاری توصیفی برای کلاس‌های داخلی

از نام‌هایی استفاده کنید که مسئولیت کلاس را مشخص کنند:

```java
private class ButtonClickListener implements ActionListener {
    // ...
}
```

بهتر از نام مبهم:

```java
private class Handler {
    // ...
}
```

---

## ۴. مراقب نشت حافظه در کلاس‌های داخلی غیر استاتیک باشید

هر نمونهٔ کلاس داخلی غیر استاتیک، یک مرجع پنهان به شیء کلاس خارجی نگه می‌دارد.

اگر شیء داخلی مدت زیادی زنده بماند، ممکن است مانع آزادسازی شیء خارجی توسط Garbage Collector شود.

> [!warning]
> این موضوع به‌ویژه در برنامه‌های Android، Listenerها، Threadها و Callbackهای بلندمدت مهم است.  
> در صورت نیاز نداشتن به Outer، از `static nested class` استفاده کنید یا مرجع‌ها را با دقت مدیریت کنید.

---

## ۵. Lambda و `this` با Anonymous Class یکسان نیستند

در کلاس ناشناس، `this` به خود شیء کلاس ناشناس اشاره می‌کند:

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        System.out.println(this.getClass().getName());
    }
};
```

اما در Lambda، `this` به شیء کلاس خارجی اشاره می‌کند، نه به یک شیء Lambda مستقل.

> [!note]
> این تفاوت در Listenerها و Callbackهایی که از `this` استفاده می‌کنند اهمیت دارد.

---

# جمع‌بندی

کلاس‌های داخلی و ناشناس ابزارهایی برای نزدیک نگه‌داشتن منطق‌های مرتبط به یکدیگر هستند.

| اگر نیاز دارید... | انتخاب مناسب |
|---|---|
| به اعضای نمونه‌ای کلاس خارجی دسترسی داشته باشید | `Non-static Inner Class` |
| کلاسی مرتبط داشته باشید اما وابسته به Outer نباشد | `Static Nested Class` |
| کلاسی فقط در یک متد لازم باشد | `Local Inner Class` |
| یک Interface یا Abstract Class را سریع و کوتاه پیاده‌سازی کنید | `Anonymous Class` |
| یک Functional Interface با منطق کوتاه پیاده‌سازی شود | `Lambda Expression` |

> [!quote]
> از کلاس‌های داخلی برای افزایش انسجام و کپسوله‌سازی استفاده کنید؛ اما اگر کلاس به داده‌های Outer وابسته نیست، `static nested class` معمولاً انتخاب بهتر و ایمن‌تری است.