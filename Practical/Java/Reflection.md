---
title: رفلکشن در جاوا
tags:
  - java
  - reflection
  - runtime
  - annotations
  - proxy
  - generics
aliases:
  - Java Reflection
  - Reflection API
  - رفلکشن جاوا
created: 2026-08-25
---

# جزوه جامع رفلکشن (Reflection) در جاوا

> [!summary] تعریف کوتاه
> **رفلکشن** در جاوا مجموعه‌ای از APIها است که امکان بررسی و تعامل با ساختار کلاس‌ها در **زمان اجرا** (*Runtime*) را فراهم می‌کند.  
> با Reflection می‌توان اطلاعات کلاس‌ها، سازنده‌ها، متدها، فیلدها، Annotationها و نوع‌های Generic را بررسی کرد و در شرایط مشخص، آن‌ها را فراخوانی یا مقداردهی کرد.

---

## فهرست مطالب

- [[#مقدمه]]
- [[#کلاس Class و روش‌های دریافت آن]]
- [[#بررسی ساختار یک کلاس]]
- [[#دسترسی به فیلدها]]
- [[#فراخوانی متدها]]
- [[#کار با سازنده‌ها و ساخت شیء]]
- [[#تفاوت get و getDeclared]]
- [[#دسترسی به اعضای private و محدودیت‌های ماژول‌ها]]
- [[#بررسی Modifierها]]
- [[#کلاس والد و اعضای ارث‌بری‌شده]]
- [[#کار با Annotationها]]
- [[#Reflection و Generics]]
- [[#کلاس‌های داخلی، ناشناس و Enumها]]
- [[#Dynamic Proxy]]
- [[#Method Handles]]
- [[#مدیریت Exceptionها]]
- [[#کاربردهای واقعی]]
- [[#ملاحظات امنیتی، عملکردی و بهترین روش‌ها]]
- [[#جمع‌بندی]]

---

# مقدمه

در برنامه‌نویسی عادی، کامپایلر نوع کلاس، فیلدها و متدهایی که استفاده می‌کنیم را می‌داند:

```java
User user = new User();
user.setName("Ali");

System.out.println(user.getName());
```

اما گاهی در زمان نوشتن کد نمی‌دانیم با چه کلاسی، چه فیلدی یا چه متدی کار خواهیم کرد. برای مثال:

- یک Framework باید کلاس‌های دارای Annotation خاص را پیدا کند.
- یک ORM باید فیلدهای یک Entity را به ستون‌های پایگاه‌داده متصل کند.
- یک ابزار تست باید متدهای دارای `@Test` را اجرا کند.
- یک Serializer باید فیلدهای یک شیء را به JSON تبدیل کند.
- یک سیستم پلاگین باید کلاس‌ها را به‌صورت پویا بارگذاری کند.

در این موقعیت‌ها Reflection کاربرد دارد.

> [!important]
> Reflection ساختار موجود یک کلاس را **بررسی** و با اعضای آن تعامل می‌کند؛ اما به‌تنهایی برای اضافه‌کردن متد یا تغییر دائمی بایت‌کد کلاس طراحی نشده است.  
> برای تغییر یا تولید بایت‌کد معمولاً از ابزارهایی مانند **Byte Buddy**، **ASM**، **Javassist** یا **Java Instrumentation API** استفاده می‌شود.

---

# کلاس `Class` و روش‌های دریافت آن

نقطهٔ شروع Reflection، شیئی از نوع `Class<?>` است.

هر کلاس، Interface، Enum و Array در JVM یک شیء `Class` مرتبط دارد.

---

## ۱. استفاده از `.class`

وقتی نوع کلاس را در زمان کامپایل می‌دانیم:

```java
Class<String> stringClass = String.class;

System.out.println(stringClass.getName());
// java.lang.String
```

نمونه با کلاس خودمان:

```java
Class<User> userClass = User.class;
```

---

## ۲. استفاده از `getClass()`

وقتی یک نمونه از شیء داریم:

```java
User user = new User();

Class<?> userClass = user.getClass();

System.out.println(userClass.getName());
```

---

## ۳. استفاده از `Class.forName()`

وقتی نام کامل کلاس را به شکل `String` داریم:

```java
Class<?> userClass = Class.forName("com.example.User");
```

این روش برای بارگذاری پویا کاربرد دارد.

```java
try {
    Class<?> clazz = Class.forName("com.example.User");
    System.out.println(clazz.getSimpleName());
} catch (ClassNotFoundException e) {
    e.printStackTrace();
}
```

> [!warning]
> `Class.forName()` در حالت عادی می‌تواند باعث **بارگذاری و initialize شدن کلاس** شود.  
> اگر فقط بارگذاری کلاس را بدون initialization بخواهید، از نسخهٔ سه‌پارامتری استفاده کنید:

```java
Class<?> clazz = Class.forName(
    "com.example.User",
    false,
    Thread.currentThread().getContextClassLoader()
);
```

---

# بررسی ساختار یک کلاس

فرض کنید کلاس زیر را داریم:

```java
public class User {

    private String name;
    private int age;

    public User() {
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void introduce() {
        System.out.println(name + " is " + age + " years old.");
    }
}
```

اکنون می‌توان اطلاعات کلی آن را بررسی کرد:

```java
Class<User> clazz = User.class;

System.out.println(clazz.getName());
System.out.println(clazz.getSimpleName());
System.out.println(clazz.getPackageName());
System.out.println(clazz.isInterface());
System.out.println(clazz.isEnum());
System.out.println(clazz.isArray());
```

خروجی احتمالی:

```text
com.example.User
User
com.example
false
false
false
```

---

# دسترسی به فیلدها

فیلدها با کلاس `java.lang.reflect.Field` نمایش داده می‌شوند.

```java
import java.lang.reflect.Field;
```

---

## فیلدهای `public`

متد `getField()` فقط فیلدهای `public` را پیدا می‌کند؛ از جمله فیلدهای `public` ارث‌بری‌شده.

```java
Field field = clazz.getField("publicField");
```

مثال:

```java
public class Product {
    public String name = "Laptop";
}
```

```java
Product product = new Product();

Field field = Product.class.getField("name");

System.out.println(field.get(product));
// Laptop

field.set(product, "Mouse");

System.out.println(product.name);
// Mouse
```

---

## همهٔ فیلدهای تعریف‌شده در کلاس

متد `getDeclaredField()` می‌تواند به فیلدهای `private`، `protected` و package-private نیز دسترسی پیدا کند؛ اما فقط فیلدهایی را بررسی می‌کند که **مستقیماً در همان کلاس تعریف شده‌اند**.

```java
Field field = clazz.getDeclaredField("name");
```

```java
Field[] fields = clazz.getDeclaredFields();

for (Field field : fields) {
    System.out.println(
        field.getName() + " : " + field.getType().getSimpleName()
    );
}
```

خروجی:

```text
name : String
age : int
```

---

## خواندن و تغییر فیلد `private`

```java
import java.lang.reflect.Field;

User user = new User();

Field nameField = User.class.getDeclaredField("name");

nameField.trySetAccessible();

nameField.set(user, "Sara");

System.out.println(nameField.get(user));
// Sara
```

روش قدیمی‌تر:

```java
nameField.setAccessible(true);
```

> [!note]
> از Java 9 به بعد، سیستم ماژول‌ها (*JPMS*) ممکن است دسترسی بازتابی به پکیج‌های ماژول‌های دیگر را محدود کند.  
> متد `trySetAccessible()` به‌جای پرتاب Exception، در صورت ناموفق‌بودن مقدار `false` برمی‌گرداند.

```java
if (nameField.trySetAccessible()) {
    nameField.set(user, "Sara");
} else {
    System.out.println("Access was denied.");
}
```

---

## کار با Primitiveها

برای فیلدهای primitive، متدهای اختصاصی وجود دارد:

```java
public class Counter {
    private int count = 10;
}
```

```java
Counter counter = new Counter();

Field countField = Counter.class.getDeclaredField("count");
countField.trySetAccessible();

int currentValue = countField.getInt(counter);

countField.setInt(counter, 42);

System.out.println(currentValue); // 10
```

متدهای مشابه:

```java
field.getInt(object);
field.getLong(object);
field.getDouble(object);

field.setInt(object, value);
field.setBoolean(object, value);
```

---

# فراخوانی متدها

متدها با کلاس `java.lang.reflect.Method` نمایش داده می‌شوند.

```java
import java.lang.reflect.Method;
```

---

## فراخوانی متد `public`

```java
Method method = User.class.getMethod("introduce");

User user = new User("Ali", 25);

method.invoke(user);
```

خروجی:

```text
Ali is 25 years old.
```

---

## فراخوانی متد دارای پارامتر

```java
public class Calculator {

    public int add(int first, int second) {
        return first + second;
    }
}
```

```java
Calculator calculator = new Calculator();

Method addMethod = Calculator.class.getMethod(
    "add",
    int.class,
    int.class
);

Object result = addMethod.invoke(calculator, 5, 3);

System.out.println(result);
// 8
```

بهتر است مقدار بازگشتی را Cast کنیم:

```java
int result = (int) addMethod.invoke(calculator, 5, 3);
```

---

## فراخوانی متد `private`

```java
public class SecretService {

    private void printSecret() {
        System.out.println("Secret message");
    }
}
```

```java
SecretService service = new SecretService();

Method method = SecretService.class
    .getDeclaredMethod("printSecret");

method.trySetAccessible();

method.invoke(service);
```

---

## فراخوانی متد `static`

برای متد استاتیک نیازی به نمونهٔ شیء نیست. آرگومان اول `invoke` می‌تواند `null` باشد.

```java
public class MathUtil {

    public static int square(int number) {
        return number * number;
    }
}
```

```java
Method squareMethod = MathUtil.class.getMethod(
    "square",
    int.class
);

int result = (int) squareMethod.invoke(null, 5);

System.out.println(result);
// 25
```

---

# کار با سازنده‌ها و ساخت شیء

سازنده‌ها با کلاس `java.lang.reflect.Constructor` نمایش داده می‌شوند.

```java
import java.lang.reflect.Constructor;
```

---

## سازندهٔ بدون پارامتر

```java
Constructor<User> constructor = User.class.getConstructor();

User user = constructor.newInstance();
```

---

## سازندهٔ دارای پارامتر

```java
Constructor<User> constructor = User.class.getConstructor(
    String.class,
    int.class
);

User user = constructor.newInstance("Reza", 30);

user.introduce();
```

---

## سازندهٔ `private`

```java
public class DatabaseConfig {

    private DatabaseConfig() {
        System.out.println("Private constructor");
    }
}
```

```java
Constructor<DatabaseConfig> constructor =
    DatabaseConfig.class.getDeclaredConstructor();

constructor.trySetAccessible();

DatabaseConfig config = constructor.newInstance();
```

> [!warning]
> دسترسی بازتابی به سازندهٔ `private` می‌تواند الگوهایی مانند Singleton را نقض کند.  
> از این کار فقط در ابزارهای فنی، تست‌ها یا نیازهای کاملاً کنترل‌شده استفاده کنید.

---

## روش منسوخ‌شدهٔ `Class#newInstance()`

روش زیر منسوخ شده است:

```java
// استفاده نکنید
Object object = clazz.newInstance();
```

روش پیشنهادی:

```java
Object object = clazz
    .getDeclaredConstructor()
    .newInstance();
```

دلیل بهتر بودن روش جدید:

- Exceptionهای سازنده را دقیق‌تر مدیریت می‌کند.
- API جدیدتر و واضح‌تری است.
- از Java 9 به بعد، روش قدیمی `deprecated` شده است.

---

# تفاوت `get` و `getDeclared`

| متد | اعضای `private` را شامل می‌شود؟ | اعضای ارث‌بری‌شده را شامل می‌شود؟ |
|---|---:|---:|
| `getFields()` | ❌ | فقط `public`ها ✅ |
| `getDeclaredFields()` | ✅ | ❌ |
| `getMethods()` | ❌ | فقط `public`ها ✅ |
| `getDeclaredMethods()` | ✅ | ❌ |
| `getConstructors()` | ❌ | سازنده ارث‌بری نمی‌شود |
| `getDeclaredConstructors()` | ✅ | سازنده ارث‌بری نمی‌شود |

مثال:

```java
Field publicField = clazz.getField("fieldName");

Field anyDeclaredField = clazz.getDeclaredField("fieldName");
```

> [!tip]
> اگر عضو `private` است، معمولاً به `getDeclared...` نیاز دارید.  
> اگر هدف شما اعضای `public` قابل مشاهده، شامل موارد ارث‌بری‌شده، است از `get...` استفاده کنید.

---

# بررسی Modifierها

کلاس `Modifier` اطلاعاتی دربارهٔ modifierهای کلاس، فیلد، متد یا سازنده ارائه می‌دهد.

```java
import java.lang.reflect.Modifier;
```

```java
Field field = User.class.getDeclaredField("name");

int modifiers = field.getModifiers();

System.out.println(Modifier.isPrivate(modifiers)); // true
System.out.println(Modifier.isStatic(modifiers));  // false
System.out.println(Modifier.isFinal(modifiers));   // false
```

برای تبدیل modifierها به متن:

```java
System.out.println(Modifier.toString(modifiers));
// private
```

---

# کلاس والد و اعضای ارث‌بری‌شده

```java
public class Person {
    protected String nationalCode;
}

public class Employee extends Person {
    private String employeeCode;
}
```

```java
Class<Employee> employeeClass = Employee.class;

Class<?> superclass = employeeClass.getSuperclass();

System.out.println(superclass.getSimpleName());
// Person
```

برای بررسی فیلدهای تعریف‌شده در کلاس والد:

```java
Field[] parentFields = superclass.getDeclaredFields();

for (Field field : parentFields) {
    System.out.println(field.getName());
}
```

> [!note]
> `getDeclaredFields()` فقط اعضای همان کلاس را برمی‌گرداند.  
> برای پیمایش کامل سلسله‌مراتب وراثت، باید با یک حلقه `getSuperclass()` را ادامه دهید.

```java
Class<?> current = Employee.class;

while (current != null) {
    for (Field field : current.getDeclaredFields()) {
        System.out.println(
            current.getSimpleName() + "." + field.getName()
        );
    }

    current = current.getSuperclass();
}
```

---

# کار با Annotationها

Annotationها متادیتا هستند؛ یعنی اطلاعاتی دربارهٔ کد ارائه می‌دهند.

برای خواندن Annotation در زمان اجرا، باید retention آن برابر `RUNTIME` باشد.

---

## تعریف Annotation سفارشی

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface EntityInfo {
    String tableName();
}
```

استفاده:

```java
@EntityInfo(tableName = "users")
public class User {
}
```

خواندن با Reflection:

```java
Class<User> clazz = User.class;

if (clazz.isAnnotationPresent(EntityInfo.class)) {
    EntityInfo info = clazz.getAnnotation(EntityInfo.class);

    System.out.println(info.tableName());
    // users
}
```

---

## Annotation روی فیلد و متد

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Column {
    String value();
}
```

```java
public class User {

    @Column("user_name")
    private String username;
}
```

```java
Field field = User.class.getDeclaredField("username");

Column column = field.getAnnotation(Column.class);

System.out.println(column.value());
// user_name
```

---

# Reflection و Generics

به دلیل **Type Erasure**، اطلاعات Genericها همیشه به همان شکل کامل در زمان اجرا باقی نمی‌مانند.

برای مثال:

```java
List<String> names = new ArrayList<>();
List<Integer> numbers = new ArrayList<>();

System.out.println(names.getClass() == numbers.getClass());
// true
```

اما اگر نوع Generic در تعریف فیلد یا امضای متد ثبت شده باشد، می‌توان بخشی از اطلاعات آن را دریافت کرد.

```java
import java.util.List;

public class Report {

    private List<String> titles;
}
```

```java
import java.lang.reflect.Field;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;

Field field = Report.class.getDeclaredField("titles");

Type genericType = field.getGenericType();

if (genericType instanceof ParameterizedType parameterizedType) {
    Type[] types = parameterizedType.getActualTypeArguments();

    for (Type type : types) {
        System.out.println(type.getTypeName());
    }
}
```

خروجی:

```text
java.lang.String
```

انواع مهم در Reflection:

| نوع | کاربرد |
|---|---|
| `Type` | نوع عمومی |
| `Class<?>` | نوع کلاس معمولی |
| `ParameterizedType` | نوع دارای پارامتر Generic مانند `List<String>` |
| `TypeVariable<?>` | متغیر نوع مانند `T` |
| `WildcardType` | wildcard مانند `? extends Number` |
| `GenericArrayType` | آرایهٔ Generic مانند `T[]` |

---

# کلاس‌های داخلی، ناشناس و Enumها

## کلاس داخلی

نام باینری کلاس داخلی معمولاً با `$` نمایش داده می‌شود:

```java
class OuterClass {

    class InnerClass {
        void print() {
            System.out.println("Inner method called");
        }
    }
}
```

```java
Class<?> innerClass = Class.forName(
    "com.example.OuterClass$InnerClass"
);
```

برای ساختن نمونهٔ یک Inner Class غیر استاتیک، سازندهٔ آن به‌صورت پنهان یک نمونه از کلاس خارجی را نیز دریافت می‌کند:

```java
OuterClass outer = new OuterClass();

Class<?> innerClass = OuterClass.InnerClass.class;

Constructor<?> constructor = innerClass.getDeclaredConstructor(
    OuterClass.class
);

Object inner = constructor.newInstance(outer);

Method print = innerClass.getDeclaredMethod("print");

print.invoke(inner);
```

---

## کلاس ناشناس

```java
Object anonymousObject = new Object() {

    private void printMessage() {
        System.out.println("Anonymous method called");
    }
};
```

```java
Class<?> clazz = anonymousObject.getClass();

System.out.println(clazz.isAnonymousClass());
// true

Method method = clazz.getDeclaredMethod("printMessage");

method.trySetAccessible();

method.invoke(anonymousObject);
```

متدهای مفید:

```java
clazz.isAnonymousClass();
clazz.isLocalClass();
clazz.isMemberClass();
clazz.getEnclosingClass();
clazz.getEnclosingMethod();
```

---

## Enumها

```java
enum Status {
    PENDING,
    APPROVED,
    REJECTED
}
```

```java
Class<Status> statusClass = Status.class;

System.out.println(statusClass.isEnum());
// true

Object[] values = statusClass.getEnumConstants();

for (Object value : values) {
    System.out.println(value);
}
```

خروجی:

```text
PENDING
APPROVED
REJECTED
```

---

# Dynamic Proxy

Dynamic Proxy در جاوا امکان ساخت یک پیاده‌سازی پویا از **Interfaceها** را فراهم می‌کند.

> [!important]
> `java.lang.reflect.Proxy` فقط برای **Interface**ها کار می‌کند، نه برای ساخت Proxy از یک کلاس عادی.  
> برای Proxy مبتنی بر کلاس، ابزارهایی مانند CGLIB یا Byte Buddy کاربرد دارند.

---

## مثال: لاگ‌کردن فراخوانی متدها

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

interface PaymentService {
    void pay(int amount);
}

class PaymentServiceImpl implements PaymentService {

    @Override
    public void pay(int amount) {
        System.out.println("Payment completed: " + amount);
    }
}
```

```java
class LoggingHandler implements InvocationHandler {

    private final Object target;

    public LoggingHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(
        Object proxy,
        Method method,
        Object[] args
    ) throws Throwable {

        System.out.println("Before: " + method.getName());

        Object result = method.invoke(target, args);

        System.out.println("After: " + method.getName());

        return result;
    }
}
```

```java
PaymentService original = new PaymentServiceImpl();

PaymentService proxy = (PaymentService) Proxy.newProxyInstance(
    PaymentService.class.getClassLoader(),
    new Class<?>[] { PaymentService.class },
    new LoggingHandler(original)
);

proxy.pay(500);
```

خروجی:

```text
Before: pay
Payment completed: 500
After: pay
```

کاربردهای رایج Dynamic Proxy:

- AOP
- لاگ‌گیری
- کنترل دسترسی
- مدیریت تراکنش
- Retry
- Cache
- ابزارهای Mock در تست

---

# Method Handles

`MethodHandle` بخشی از پکیج `java.lang.invoke` است و جایگزین مستقیم Reflection نیست، اما برای فراخوانی پویای متدها با کنترل نوع بیشتر و در برخی سناریوها عملکرد بهتر طراحی شده است.

```java
import java.lang.invoke.MethodHandle;
import java.lang.invoke.MethodHandles;
import java.lang.invoke.MethodType;

public class MethodHandleExample {

    public void greet(String name) {
        System.out.println("Hello, " + name);
    }

    public static void main(String[] args) throws Throwable {
        MethodHandles.Lookup lookup = MethodHandles.lookup();

        MethodType type = MethodType.methodType(
            void.class,
            String.class
        );

        MethodHandle greetHandle = lookup.findVirtual(
            MethodHandleExample.class,
            "greet",
            type
        );

        MethodHandleExample example = new MethodHandleExample();

        greetHandle.invoke(example, "Sara");
    }
}
```

خروجی:

```text
Hello, Sara
```

> [!note]
> `MethodHandles.Lookup` قوانین دسترسی را جدی‌تر و دقیق‌تر اعمال می‌کند.  
> برای دسترسی کنترل‌شده به اعضای `private` می‌توان از `MethodHandles.privateLookupIn(...)` استفاده کرد؛ البته با رعایت محدودیت‌های ماژول‌ها.

---

# مدیریت Exceptionها

استفاده از Reflection با Exceptionهای checked متعددی همراه است.

| Exception | علت |
|---|---|
| `ClassNotFoundException` | کلاس با نام داده‌شده پیدا نشد |
| `NoSuchFieldException` | فیلد موردنظر وجود ندارد |
| `NoSuchMethodException` | متد یا سازنده پیدا نشد |
| `IllegalAccessException` | دسترسی به عضو مجاز نیست |
| `InstantiationException` | ایجاد شیء ممکن نیست |
| `InvocationTargetException` | متد یا سازندهٔ فراخوانی‌شده Exception پرتاب کرده است |

نمونهٔ مناسب مدیریت خطا:

```java
try {
    Class<?> clazz = Class.forName("com.example.User");

    Object instance = clazz
        .getDeclaredConstructor()
        .newInstance();

    Method method = clazz.getMethod("introduce");

    method.invoke(instance);

} catch (ClassNotFoundException e) {
    System.err.println("Class was not found.");

} catch (NoSuchMethodException e) {
    System.err.println("Required constructor or method was not found.");

} catch (InstantiationException e) {
    System.err.println("Could not create an instance.");

} catch (IllegalAccessException e) {
    System.err.println("Access was denied.");

} catch (java.lang.reflect.InvocationTargetException e) {
    Throwable cause = e.getCause();

    System.err.println("Target method failed: " + cause.getMessage());
}
```

> [!tip]
> هنگام دریافت `InvocationTargetException`، علت واقعی خطا معمولاً در `getCause()` قرار دارد.

---

# کاربردهای واقعی Reflection

## ۱. Dependency Injection

فریم‌ورک‌هایی مانند Spring می‌توانند کلاس‌ها و وابستگی‌های آن‌ها را شناسایی کرده و به‌صورت خودکار مقداردهی کنند.

```java
@Service
public class UserService {
}
```

---

## ۲. ORM

ORMهایی مانند Hibernate اطلاعات فیلدها و Annotationها را بررسی می‌کنند تا شیء جاوا را به جدول و ستون‌های دیتابیس نگاشت کنند.

```java
@Entity
public class User {

    @Id
    private Long id;

    @Column(name = "user_name")
    private String username;
}
```

---

## ۳. Serialization و Deserialization

کتابخانه‌هایی مانند Jackson و Gson، ساختار فیلدهای یک شیء را بررسی می‌کنند تا آن را به JSON تبدیل کنند یا از JSON شیء بسازند.

```java
public class UserDto {
    private String username;
    private int age;
}
```

---

## ۴. ابزارهای تست

JUnit و ابزارهای مشابه از Annotationها و Reflection برای کشف و اجرای تست‌ها استفاده می‌کنند.

```java
@Test
void shouldAddNumbers() {
    assertEquals(5, 2 + 3);
}
```

---

## ۵. Plugin System

در سیستم‌های پلاگین، نام کلاس می‌تواند از فایل پیکربندی خوانده و در زمان اجرا بارگذاری شود.

```java
String className = "com.example.plugins.EmailPlugin";

Class<?> pluginClass = Class.forName(className);

Object plugin = pluginClass
    .getDeclaredConstructor()
    .newInstance();
```

---

# ملاحظات امنیتی، عملکردی و بهترین روش‌ها

## ۱. Reflection را فقط در مواقع ضروری استفاده کنید

استفادهٔ مستقیم و عادی از کلاس‌ها و متدها، معمولاً خواناتر، امن‌تر و قابل بررسی‌تر توسط کامپایلر است.

```java
// بهتر، اگر نوع را می‌دانیم
user.introduce();
```

به‌جای:

```java
// فقط در صورت نیاز به رفتار پویا
method.invoke(user);
```

---

## ۲. از شکستن کپسوله‌سازی پرهیز کنید

دسترسی به فیلدها و متدهای `private` می‌تواند قرارداد داخلی یک کلاس را نقض کند.

```java
field.trySetAccessible();
```

این کار ممکن است باعث شود:

- کد به پیاده‌سازی داخلی وابسته شود.
- با تغییر نسخهٔ کتابخانه خراب شود.
- تست‌پذیری و نگه‌داری کد کاهش یابد.
- محدودیت‌های ماژول‌ها مانع اجرا شوند.

---

## ۳. اعضای بازتابی را Cache کنید

جست‌وجوی مکرر متد یا فیلد هزینه دارد.

نامناسب:

```java
for (int i = 0; i < 1_000_000; i++) {
    Method method = User.class.getMethod("introduce");
    method.invoke(user);
}
```

بهتر:

```java
Method introduceMethod = User.class.getMethod("introduce");

for (int i = 0; i < 1_000_000; i++) {
    introduceMethod.invoke(user);
}
```

---

## ۴. دربارهٔ فیلدهای `final` احتیاط ویژه داشته باشید

تلاش برای تغییر فیلدهای `final` با Reflection قابل اتکا نیست و نباید مبنای طراحی برنامه قرار گیرد.

```java
private final String name = "Ali";
```

بهینه‌سازی‌های JVM ممکن است مقدار چنین فیلدهایی را inline کنند؛ به‌خصوص در مورد `static final`ها. همچنین محدودیت‌های نسخه‌های جدید جاوا این کار را دشوارتر یا غیرممکن می‌کنند.

> [!warning]
> تغییر Reflection-based فیلدهای `final` ممکن است در یک محیط کار کند و در محیط دیگر نتیجهٔ غیرمنتظره بدهد.  
> راه‌حل درست، طراحی API مناسب یا ساخت شیء جدید با مقدار دلخواه است.

---

## ۵. SecurityManager راه‌حل امروزی نیست

در گذشته از `SecurityManager` برای محدودسازی برخی دسترسی‌ها استفاده می‌شد، اما این سازوکار در نسخه‌های جدید جاوا **منسوخ شده** و نباید به‌عنوان راهکار امنیتی جدید انتخاب شود.

راهکارهای مناسب‌تر شامل موارد زیر هستند:

- رعایت کپسوله‌سازی
- استفاده از Java Module System
- محدودکردن دسترسی برنامه‌ها و وابستگی‌ها
- اعتبارسنجی ورودی‌ها
- به‌روزرسانی JVM و کتابخانه‌ها

---

## ۶. Reflection همیشه «کند» نیست، اما رایگان هم نیست

هزینهٔ Reflection به نوع عملیات، JVM، حجم فراخوانی‌ها و امکان بهینه‌سازی JIT بستگی دارد.

> [!tip]
> ابتدا کد خوانا و درست بنویسید. اگر Reflection در مسیر حساس عملکرد قرار دارد، اندازه‌گیری واقعی با ابزارهایی مانند **JMH** انجام دهید و در صورت نیاز Cache، `MethodHandle` یا طراحی جایگزین را بررسی کنید.

---

# جمع‌بندی

| اگر نیاز دارید... | ابزار یا API مناسب |
|---|---|
| دریافت اطلاعات کلاس در Runtime | `Class<?>` |
| خواندن یا تغییر فیلدها | `Field` |
| پیدا و فراخوانی‌کردن متد | `Method` |
| ساخت شیء از روی سازنده | `Constructor<?>` |
| بررسی Annotationها | `isAnnotationPresent()` و `getAnnotation()` |
| تحلیل نوع Generic ثبت‌شده در امضا | `Type` و `ParameterizedType` |
| ساخت پیاده‌سازی پویا از Interface | `Proxy` و `InvocationHandler` |
| فراخوانی پویای type-safe‌تر | `MethodHandle` |
| تغییر یا تولید بایت‌کد | Byte Buddy، ASM، Javassist یا Instrumentation |

> [!quote]
> Reflection ابزاری برای ساخت نرم‌افزارهای انعطاف‌پذیر و Frameworkهای پویاست؛ اما در کدهای معمولی باید با احتیاط و فقط در صورت وجود نیاز واقعی از آن استفاده شود.