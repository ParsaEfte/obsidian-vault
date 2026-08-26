---
title: Annotation ها در جاوا
tags:
  - java
  - annotations
  - reflection
  - annotation-processing
  - metadata
aliases:
  - Java Annotations
  - حاشیه‌نویسی در جاوا
  - Annotation Processing
created: 2026-08-25
---

# جزوه جامع Annotationها در جاوا

> [!summary] تعریف کوتاه
> **Annotation** یا حاشیه‌نویسی، نوعی **فراداده** (*Metadata*) است که به عناصر برنامه مانند کلاس، متد، فیلد، پارامتر یا پکیج اضافه می‌شود.  
> Annotation معمولاً منطق برنامه را مستقیماً اجرا نمی‌کند؛ بلکه اطلاعاتی در اختیار کامپایلر، ابزارهای ساخت، Frameworkها یا کدهای Reflection قرار می‌دهد.

---

## فهرست مطالب

- [[#مقدمه]]
- [[#اولین Annotation]]
- [[#Annotationهای آمادهٔ جاوا]]
- [[#ساخت Annotation سفارشی]]
- [[#قواعد تعریف Attributeها]]
- [[#Meta-Annotationها]]
- [[#RetentionPolicy و چرخهٔ عمر Annotation]]
- [[#Target و محل‌های مجاز استفاده]]
- [[#خواندن Annotation با Reflection]]
- [[#Annotationهای تکراری Repeatable]]
- [[#Inherited و ارث‌بری Annotation]]
- [[#پردازش Annotation در زمان کامپایل]]
- [[#Annotationها در Spring و Hibernate]]
- [[#Validation با Annotation]]
- [[#نکات مهم و بهترین روش‌ها]]
- [[#جمع‌بندی]]

---

# مقدمه

در جاوا، Annotationها راهی برای اضافه‌کردن اطلاعات توصیفی به کد هستند؛ بدون اینکه منطق اصلی برنامه تغییر کند.

برای مثال، Annotation زیر به کامپایلر اعلام می‌کند که متد `makeSound` باید متدی از کلاس والد را Override کند:

```java
@Override
public void makeSound() {
    System.out.println("Bark");
}
```

Annotationها در موارد زیر کاربرد زیادی دارند:

- بررسی و هشدار توسط کامپایلر
- پیکربندی Frameworkها
- Dependency Injection
- ORM و نگاشت شیء به پایگاه‌داده
- اعتبارسنجی داده‌ها
- تست‌نویسی
- تولید کد در زمان کامپایل
- ثبت لاگ، کنترل دسترسی و AOP
- پردازش Runtime با Reflection

> [!important]
> Annotation به‌تنهایی رفتار خاصی ایجاد نمی‌کند.  
> باید یک ابزار، کامپایلر، Framework یا کد Reflection وجود داشته باشد که آن Annotation را **بخواند و پردازش کند**.

---

# اولین Annotation

یک Annotation با علامت `@` شروع می‌شود و معمولاً پیش از عنصر هدف نوشته می‌شود.

```java
@Deprecated
public void oldMethod() {
    // ...
}
```

در این مثال، `@Deprecated` به کاربران این متد و کامپایلر اعلام می‌کند که استفاده از آن دیگر توصیه نمی‌شود.

Annotationها می‌توانند روی عناصر مختلفی قرار بگیرند:

```java
@MyAnnotation
public class MyClass {

    @MyAnnotation
    private String name;

    @MyAnnotation
    public void print(@MyAnnotation String message) {
        System.out.println(message);
    }
}
```

البته اینکه یک Annotation روی کدام عنصر قابل استفاده باشد، با `@Target` کنترل می‌شود.

---

# Annotationهای آمادهٔ جاوا

جاوا Annotationهای پرکاربردی را در اختیار ما قرار می‌دهد.

---

## `@Override`

این Annotation نشان می‌دهد متدی، متدی از کلاس والد یا Interface را Override کرده است.

```java
class Animal {

    public void makeSound() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {

    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}
```

مزیت اصلی آن، تشخیص خطاهای تایپی یا امضای اشتباه متد است:

```java
class Dog extends Animal {

    @Override
    public void makesSound() {
        // خطای کامپایل:
        // این متد در Animal وجود ندارد.
    }
}
```

> [!tip]
> هر زمان متدی را Override می‌کنید، از `@Override` استفاده کنید.

---

## `@Deprecated`

نشان می‌دهد یک کلاس، متد یا فیلد قدیمی شده و استفاده از آن توصیه نمی‌شود.

```java
public class UserService {

    @Deprecated
    public void saveUserOldWay() {
        // روش قدیمی
    }

    public void saveUser() {
        // روش جدید
    }
}
```

نسخهٔ کامل‌تر:

```java
public class UserService {

    @Deprecated(
        since = "2.0",
        forRemoval = true
    )
    public void saveUserOldWay() {
        // در نسخه‌های آینده حذف خواهد شد
    }
}
```

استفادهٔ بهتر همراه با Javadoc:

```java
/**
 * @deprecated از {@link #saveUser()} استفاده کنید.
 */
@Deprecated(since = "2.0", forRemoval = true)
public void saveUserOldWay() {
}
```

---

## `@SuppressWarnings`

برای خاموش‌کردن هشدارهای مشخص کامپایلر استفاده می‌شود.

```java
@SuppressWarnings("unchecked")
public void unsafeCast() {
    // ...
}
```

مثال رایج:

```java
import java.util.ArrayList;
import java.util.List;

public class Example {

    @SuppressWarnings("unchecked")
    public List<String> convert(List rawList) {
        return rawList;
    }
}
```

هشدارهای رایج:

```java
@SuppressWarnings("unchecked")
@SuppressWarnings("deprecation")
@SuppressWarnings("unused")
@SuppressWarnings("rawtypes")
@SuppressWarnings("serial")
```

برای چند هشدار:

```java
@SuppressWarnings({ "unchecked", "rawtypes" })
public void process() {
    // ...
}
```

> [!warning]
> `@SuppressWarnings` را تا حد امکان در کوچک‌ترین محدودهٔ ممکن قرار دهید.  
> خاموش‌کردن هشدار برای یک کلاس کامل ممکن است خطاهای مهم را پنهان کند.

---

## `@FunctionalInterface`

این Annotation تضمین می‌کند که Interface موردنظر یک **رابط تابعی** باشد؛ یعنی دقیقاً یک متد انتزاعی داشته باشد.

```java
@FunctionalInterface
public interface Calculator {

    int calculate(int first, int second);

    default void printInfo() {
        System.out.println("Calculator");
    }

    static void log() {
        System.out.println("Log");
    }
}
```

استفاده با Lambda:

```java
Calculator sum = (a, b) -> a + b;

System.out.println(sum.calculate(2, 3));
// 5
```

> [!note]
> متدهای `default`، `static` و متدهای ارث‌بری‌شده از `Object`، در شمارش متد انتزاعی رابط تابعی لحاظ نمی‌شوند.

---

## `@SafeVarargs`

برای حذف هشدارهای ناامن مربوط به Generic Varargs کاربرد دارد.

```java
@SafeVarargs
public static <T> List<T> listOf(T... items) {
    return List.of(items);
}
```

این Annotation فقط روی موارد زیر مجاز است:

- متدهای `static`
- متدهای `final`
- متدهای `private`
- سازنده‌ها

---

# ساخت Annotation سفارشی

برای تعریف Annotation از `@interface` استفاده می‌کنیم.

```java
public @interface MyAnnotation {

    String value() default "default value";

    int number() default 0;
}
```

استفاده:

```java
@MyAnnotation(value = "example", number = 5)
public class MyClass {
}
```

اگر Attributeای به نام `value` داشته باشیم، می‌توان نام آن را حذف کرد:

```java
@MyAnnotation("example")
public class MyClass {
}
```

اما اگر چند Attribute وجود داشته باشد، فقط زمانی می‌توان نام `value` را حذف کرد که سایر Attributeها از مقدار پیش‌فرض استفاده کنند.

```java
@MyAnnotation(value = "example", number = 10)
public class MyClass {
}
```

---

# قواعد تعریف Attributeها

مقادیر داخل Annotation در واقع متدهای بدون پارامتر هستند؛ نه فیلدهای معمولی.

```java
public @interface Column {

    String name();

    boolean nullable() default true;

    int length() default 255;
}
```

استفاده:

```java
@Column(
    name = "username",
    nullable = false,
    length = 50
)
private String username;
```

---

## نوع‌های مجاز برای Attribute Annotation

نوع بازگشتی Attributeها فقط می‌تواند یکی از موارد زیر باشد:

- نوع‌های primitive مانند `int`، `boolean` و `double`
- `String`
- `Class<?>`
- Enum
- Annotation دیگر
- آرایه‌ای از موارد بالا

نمونه:

```java
public @interface Config {

    String name();

    int timeout() default 30;

    boolean enabled() default true;

    Class<?> serviceType();

    Role role() default Role.USER;

    String[] tags() default {};
}
```

```java
enum Role {
    ADMIN,
    USER
}
```

استفاده:

```java
@Config(
    name = "payment",
    timeout = 60,
    serviceType = PaymentService.class,
    role = Role.ADMIN,
    tags = { "finance", "important" }
)
public class PaymentConfig {
}
```

> [!warning]
> نوع‌هایی مانند `List<String>`، `Map`، `Object` یا یک شیء معمولی، به‌عنوان مقدار Attribute Annotation مجاز نیستند.

این کد نامعتبر است:

```java
public @interface InvalidAnnotation {

    // نامعتبر
    List<String> values();
}
```

---

# Meta-Annotationها

**Meta-Annotation**ها Annotationهایی هستند که روی تعریف یک Annotation دیگر قرار می‌گیرند و رفتار آن را تعیین می‌کنند.

مهم‌ترین Meta-Annotationها:

| Annotation | کاربرد |
|---|---|
| `@Target` | تعیین محل‌های مجاز استفاده |
| `@Retention` | تعیین مدت نگهداری Annotation |
| `@Documented` | اضافه‌شدن Annotation به Javadoc |
| `@Inherited` | قابل‌مشاهده‌بودن Annotation کلاس والد در کلاس فرزند |
| `@Repeatable` | امکان استفادهٔ چندباره از یک Annotation |

---

# `RetentionPolicy` و چرخهٔ عمر Annotation

`@Retention` تعیین می‌کند Annotation تا چه مرحله‌ای حفظ شود.

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
public @interface RuntimeAnnotation {
}
```

سه نوع Retention داریم:

| Retention Policy | توضیح |
|---|---|
| `SOURCE` | فقط در سورس‌کد وجود دارد و کامپایلر آن را حذف می‌کند. |
| `CLASS` | در فایل `.class` ذخیره می‌شود، اما معمولاً در Runtime از Reflection در دسترس نیست. |
| `RUNTIME` | در فایل `.class` حفظ می‌شود و در Runtime با Reflection قابل خواندن است. |

---

## `RetentionPolicy.SOURCE`

برای Annotationهایی مناسب است که فقط ابزارهای کامپایل یا IDE به آن‌ها نیاز دارند.

```java
@Retention(RetentionPolicy.SOURCE)
public @interface SourceOnly {
}
```

نمونهٔ معروف:

```java
@Override
```

---

## `RetentionPolicy.CLASS`

Annotation در بایت‌کد کلاس باقی می‌ماند، اما به‌صورت معمول با Reflection قابل دریافت نیست.

```java
@Retention(RetentionPolicy.CLASS)
public @interface ClassLevelAnnotation {
}
```

---

## `RetentionPolicy.RUNTIME`

برای Annotationهایی که باید هنگام اجرای برنامه پردازش شوند.

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface RuntimeAnnotation {

    String value();
}
```

خواندن با Reflection:

```java
RuntimeAnnotation annotation =
    MyClass.class.getAnnotation(RuntimeAnnotation.class);
```

> [!important]
> اگر قصد دارید Annotation را با Reflection بخوانید، حتماً از `RetentionPolicy.RUNTIME` استفاده کنید.

---

# `Target` و محل‌های مجاز استفاده

`@Target` مشخص می‌کند Annotation روی چه عناصر برنامه‌ای قابل استفاده است.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
public @interface MyMethodAnnotation {
}
```

اکنون این Annotation فقط روی متدها معتبر است:

```java
public class UserService {

    @MyMethodAnnotation
    public void save() {
    }
}
```

---

## مقادیر مهم `ElementType`

| مقدار | محل استفاده |
|---|---|
| `TYPE` | کلاس، Interface، Enum و Annotation |
| `FIELD` | فیلد |
| `METHOD` | متد |
| `PARAMETER` | پارامتر متد یا سازنده |
| `CONSTRUCTOR` | سازنده |
| `LOCAL_VARIABLE` | متغیر محلی |
| `PACKAGE` | پکیج |
| `MODULE` | ماژول |
| `TYPE_PARAMETER` | پارامتر Generic مانند `<T>` |
| `TYPE_USE` | استفاده از یک نوع در هر محل مجاز |
| `ANNOTATION_TYPE` | تعریف Annotation دیگر |
| `RECORD_COMPONENT` | مؤلفه‌های Record |

نمونه‌ای با چند هدف:

```java
@Target({
    ElementType.TYPE,
    ElementType.METHOD
})
@Retention(RetentionPolicy.RUNTIME)
public @interface Audited {
}
```

استفاده:

```java
@Audited
public class PaymentService {

    @Audited
    public void pay() {
    }
}
```

---

# `@Documented`

اگر Annotation دارای `@Documented` باشد، استفاده از آن در خروجی Javadoc نمایش داده می‌شود.

```java
import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Documented
@Retention(RetentionPolicy.RUNTIME)
public @interface PublicApi {
}
```

```java
@PublicApi
public class PaymentService {
}
```

این ویژگی در Libraryها و APIهای عمومی مفید است.

---

# `@Inherited` و ارث‌بری Annotation

`@Inherited` باعث می‌شود Annotation تعریف‌شده روی یک کلاس والد، هنگام بررسی کلاس فرزند نیز قابل مشاهده باشد.

```java
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Inherited
@Retention(RetentionPolicy.RUNTIME)
public @interface EntityInfo {

    String tableName();
}
```

```java
@EntityInfo(tableName = "users")
class BaseUser {
}

class AdminUser extends BaseUser {
}
```

```java
EntityInfo info = AdminUser.class.getAnnotation(EntityInfo.class);

System.out.println(info.tableName());
// users
```

> [!warning]
> `@Inherited` فقط برای **کلاس‌ها** اثر دارد.  
> Annotationهای روی متدها، فیلدها، پارامترها و Interfaceها به این شکل به ارث نمی‌رسند.

---

# خواندن Annotation با Reflection

برای خواندن Annotationهای Runtime از API رفلکشن استفاده می‌کنیم.

ابتدا یک Annotation تعریف می‌کنیم:

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyMethodAnnotation {

    String description() default "No description";
}
```

کلاس هدف:

```java
public class MyClass {

    @MyMethodAnnotation(
        description = "Saves user information"
    )
    public void saveUser() {
        System.out.println("User saved");
    }
}
```

کد پردازش‌کننده:

```java
import java.lang.reflect.Method;

public class AnnotationProcessor {

    public static void main(String[] args) {
        for (Method method : MyClass.class.getDeclaredMethods()) {

            if (method.isAnnotationPresent(MyMethodAnnotation.class)) {
                MyMethodAnnotation annotation =
                    method.getAnnotation(MyMethodAnnotation.class);

                System.out.println(
                    "Method: " + method.getName()
                    + ", description: "
                    + annotation.description()
                );
            }
        }
    }
}
```

خروجی:

```text
Method: saveUser, description: Saves user information
```

---

## متدهای مهم Reflection برای Annotation

| متد | کاربرد |
|---|---|
| `isAnnotationPresent(...)` | بررسی وجود یک Annotation مشخص |
| `getAnnotation(...)` | دریافت یک Annotation مشخص |
| `getAnnotations()` | دریافت Annotationهای قابل مشاهده |
| `getDeclaredAnnotation(...)` | دریافت Annotation تعریف‌شده مستقیم روی عنصر |
| `getDeclaredAnnotations()` | دریافت تمام Annotationهای تعریف‌شده مستقیم |

نمونه:

```java
Class<User> clazz = User.class;

boolean exists = clazz.isAnnotationPresent(EntityInfo.class);

EntityInfo annotation = clazz.getAnnotation(EntityInfo.class);

for (var item : clazz.getDeclaredAnnotations()) {
    System.out.println(item.annotationType().getName());
}
```

---

# Annotation روی فیلدها و پارامترها

## Annotation روی فیلد

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Column {

    String value();
}
```

```java
public class User {

    @Column("user_name")
    private String username;
}
```

خواندن:

```java
import java.lang.reflect.Field;

Field field = User.class.getDeclaredField("username");

Column column = field.getAnnotation(Column.class);

System.out.println(column.value());
// user_name
```

---

## Annotation روی پارامتر

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface NotBlank {
}
```

```java
public class UserService {

    public void createUser(@NotBlank String username) {
        System.out.println(username);
    }
}
```

خواندن Annotation پارامترها:

```java
import java.lang.annotation.Annotation;
import java.lang.reflect.Method;

Method method = UserService.class.getMethod(
    "createUser",
    String.class
);

Annotation[][] parameterAnnotations =
    method.getParameterAnnotations();

for (Annotation annotation : parameterAnnotations[0]) {
    System.out.println(annotation.annotationType().getSimpleName());
}
```

خروجی:

```text
NotBlank
```

---

# Annotationهای تکراری (`@Repeatable`)

از Java 8 به بعد، می‌توان یک Annotation را چند بار روی یک عنصر استفاده کرد.

ابتدا Annotation اصلی و Annotation نگهدارنده (*Container*) را تعریف می‌کنیم:

```java
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
@Repeatable(Tasks.class)
public @interface Task {

    String value();
}
```

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
public @interface Tasks {

    Task[] value();
}
```

استفاده:

```java
@Task("Write code")
@Task("Write tests")
@Task("Deploy application")
public class ProjectService {
}
```

خواندن همهٔ Annotationها:

```java
Task[] tasks = ProjectService.class.getAnnotationsByType(Task.class);

for (Task task : tasks) {
    System.out.println(task.value());
}
```

خروجی:

```text
Write code
Write tests
Deploy application
```

> [!note]
> کامپایلر Annotationهای تکراری را در پشت‌صحنه داخل Annotation نگهدارنده، یعنی `@Tasks`، قرار می‌دهد.

---

# Annotation Processing در زمان کامپایل

همهٔ Annotationها برای Runtime نیستند. گاهی می‌خواهیم هنگام کامپایل:

- خطاهای سفارشی تولید کنیم.
- کد جدید بسازیم.
- کلاس‌ها یا فایل‌های پیکربندی تولید کنیم.
- قراردادهای پروژه را بررسی کنیم.

برای این کار از **Annotation Processing API** استفاده می‌شود.

کلاس پایهٔ اصلی:

```java
javax.annotation.processing.AbstractProcessor
```

> [!note]
> در پروژه‌های مدرن Java، ابزارهای رایجی مانند Lombok، MapStruct، Dagger و QueryDSL از Annotation Processing استفاده می‌کنند.

---

## تعریف Annotation برای پردازش کامپایل

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.SOURCE)
public @interface GenerateInfo {
}
```

کلاسی که Annotation را دارد:

```java
@GenerateInfo
public class User {
}
```

---

## ساخت یک Processor ساده

```java
import javax.annotation.processing.AbstractProcessor;
import javax.annotation.processing.RoundEnvironment;
import javax.annotation.processing.SupportedAnnotationTypes;
import javax.annotation.processing.SupportedSourceVersion;
import javax.lang.model.SourceVersion;
import javax.lang.model.element.Element;
import javax.lang.model.element.TypeElement;
import javax.tools.Diagnostic;

import java.util.Set;

@SupportedAnnotationTypes("com.example.GenerateInfo")
@SupportedSourceVersion(SourceVersion.RELEASE_21)
public class GenerateInfoProcessor extends AbstractProcessor {

    @Override
    public boolean process(
        Set<? extends TypeElement> annotations,
        RoundEnvironment roundEnv
    ) {
        for (Element element :
                roundEnv.getElementsAnnotatedWith(GenerateInfo.class)) {

            processingEnv.getMessager().printMessage(
                Diagnostic.Kind.NOTE,
                "Found @GenerateInfo on: "
                    + element.getSimpleName()
            );
        }

        return true;
    }
}
```

> [!important]
> Annotation Processor در زمان **کامپایل** اجرا می‌شود، نه هنگام اجرای برنامه.  
> بنابراین برای پردازش آن به Reflection نیازی نداریم.

---

## ثبت Processor

برای اینکه کامپایلر بتواند Processor را کشف کند، معمولاً فایلی با مسیر زیر ایجاد می‌شود:

```text
META-INF/services/javax.annotation.processing.Processor
```

محتوای فایل:

```text
com.example.GenerateInfoProcessor
```

بسیاری از پروژه‌ها برای ساده‌سازی این فرایند از کتابخانه‌هایی مانند `AutoService` استفاده می‌کنند.

---

# Annotationها در Frameworkها

## Spring Framework

Spring به‌شکل گسترده از Annotationها برای پیکربندی و مدیریت اجزای برنامه استفاده می‌کند.

```java
import org.springframework.stereotype.Service;

@Service
public class UserService {

    public void save() {
        System.out.println("User saved");
    }
}
```

چند Annotation پرکاربرد Spring:

| Annotation | کاربرد |
|---|---|
| `@Component` | معرفی یک Component عمومی |
| `@Service` | معرفی Service |
| `@Repository` | معرفی لایهٔ دسترسی به داده |
| `@Controller` | معرفی Controller در MVC |
| `@RestController` | Controller برای APIهای REST |
| `@Configuration` | کلاس تنظیمات |
| `@Bean` | تعریف Bean در یک کلاس Configuration |
| `@Autowired` | تزریق وابستگی |
| `@Value` | دریافت مقدار تنظیمات |
| `@Transactional` | مدیریت تراکنش |

نمونهٔ Constructor Injection:

```java
import org.springframework.stereotype.Service;

@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

> [!tip]
> در Spring مدرن، برای یک سازندهٔ واحد معمولاً نیازی به نوشتن `@Autowired` نیست.

---

## JPA و Hibernate

JPA و Hibernate با Annotationها، کلاس‌های جاوا را به ساختار دیتابیس نگاشت می‌کنند.

```java
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username", nullable = false)
    private String username;
}
```

> [!note]
> در اکوسیستم جدید Jakarta EE و Spring Boot جدید، معمولاً از پکیج `jakarta.persistence` استفاده می‌شود؛ نه `javax.persistence`.

---

# Validation با Annotation

در Bean Validation می‌توان قوانین اعتبارسنجی را مستقیم روی فیلدهای مدل تعریف کرد.

```java
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;

public class UserForm {

    @NotBlank(message = "نام کاربری الزامی است")
    @Size(min = 2, max = 30)
    private String username;

    @NotBlank(message = "ایمیل الزامی است")
    @Email(message = "فرمت ایمیل معتبر نیست")
    private String email;

    // getters و setters
}
```

Annotationهای رایج Validation:

| Annotation | کاربرد |
|---|---|
| `@NotNull` | مقدار نباید `null` باشد |
| `@NotEmpty` | رشته، Collection یا Map نباید خالی باشد |
| `@NotBlank` | رشته نباید `null`، خالی یا فقط شامل فاصله باشد |
| `@Size` | محدودسازی طول String یا اندازهٔ Collection |
| `@Min` / `@Max` | حداقل یا حداکثر مقدار عددی |
| `@Positive` | عدد باید مثبت باشد |
| `@Email` | بررسی فرمت ایمیل |
| `@Pattern` | بررسی با Regular Expression |

---

# Annotation برای Logging و Monitoring

می‌توان Annotation سفارشی برای مشخص‌کردن متدهایی که باید لاگ شوند ساخت.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {
}
```

استفاده:

```java
public class ReportService {

    @LogExecutionTime
    public void generateReport() {
        // عملیات سنگین
    }
}
```

در یک برنامهٔ ساده می‌توان با Reflection Annotation را بررسی کرد؛ اما در Frameworkهایی مانند Spring معمولاً این کار با **AOP** و Proxy انجام می‌شود.

---

# `@PostConstruct` و `@PreDestroy`

در Containerهایی مانند Spring، این Annotationها برای مدیریت چرخهٔ عمر Bean استفاده می‌شوند.

```java
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;

public class MyBean {

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("Bean is being destroyed");
    }
}
```

| Annotation | زمان اجرا |
|---|---|
| `@PostConstruct` | پس از ساخت Bean و تزریق وابستگی‌ها |
| `@PreDestroy` | پیش از نابودی Bean توسط Container |

> [!note]
> این Annotationها به‌تنهایی اجرا نمی‌شوند؛ باید توسط یک Container مانند Spring مدیریت شوند.

---

# تفاوت Reflection و Annotation Processing

| ویژگی | Reflection | Annotation Processing |
|---|---|---|
| زمان پردازش | Runtime | Compile Time |
| نیاز به `RetentionPolicy.RUNTIME` | بله | معمولاً خیر |
| امکان ساخت کد | محدود و غیرمستقیم | بله |
| هزینه در زمان اجرای برنامه | دارد | ندارد |
| نمونهٔ کاربرد | Spring، Hibernate، Serialization | Lombok، MapStruct، Dagger |

---

# نکات مهم و بهترین روش‌ها

## ۱. Annotation را با منطق برنامه اشتباه نگیرید

Annotation فقط اطلاعات توصیفی است:

```java
@LogExecutionTime
public void process() {
}
```

برای اینکه واقعاً زمان اجرا ثبت شود، باید کدی وجود داشته باشد که `@LogExecutionTime` را پردازش کند.

---

## ۲. Retention مناسب انتخاب کنید

- اگر Annotation فقط برای کامپایلر یا ابزار تولید کد است: `SOURCE`
- اگر باید در بایت‌کد باشد ولی Runtime لازم نیست: `CLASS`
- اگر باید با Reflection یا Framework خوانده شود: `RUNTIME`

---

## ۳. Target را محدود و دقیق تعریف کنید

اگر Annotation فقط برای فیلدها ساخته شده است:

```java
@Target(ElementType.FIELD)
```

از تعریف Target بسیار گسترده بدون نیاز واقعی خودداری کنید.

---

## ۴. Attributeها را ساده نگه دارید

مناسب:

```java
public @interface Cacheable {

    String key();

    int ttlSeconds() default 60;
}
```

نامناسب:

```java
public @interface ComplicatedConfig {
    // تنظیمات بسیار زیاد و پیچیده
}
```

اگر پیکربندی پیچیده است، استفاده از کلاس تنظیمات یا فایل Configuration مناسب‌تر است.

---

## ۵. از نام‌های واضح استفاده کنید

خوب:

```java
@RequiresAuthentication
@Audited
@Masked
```

ضعیف:

```java
@Process
@Special
@Flag
```

نام Annotation باید هدف و معنای آن را شفاف بیان کند.

---

## ۶. استفادهٔ نادرست از `@Inherited`

`@Inherited` جایگزین Override یا انتقال Annotation به متدهای فرزند نیست. این Annotation فقط هنگام جست‌وجوی Annotation روی **کلاس‌های فرزند** اثر می‌گذارد.

---

# جمع‌بندی

| مفهوم | توضیح |
|---|---|
| Annotation | فراداده‌ای برای توصیف اجزای برنامه |
| `@interface` | روش تعریف Annotation سفارشی |
| `@Target` | تعیین محل‌های مجاز استفاده |
| `@Retention` | تعیین عمر Annotation |
| `RUNTIME` | قابل خواندن با Reflection |
| `@Repeatable` | امکان استفادهٔ چندباره از Annotation |
| `@Inherited` | انتقال Annotation کلاس والد به کلاس فرزند |
| Reflection | پردازش Annotation در زمان اجرا |
| Annotation Processor | پردازش Annotation در زمان کامپایل |
| Spring / Hibernate | نمونه‌هایی از استفادهٔ گسترده از Annotationها |

> [!quote]
> Annotationها زمانی بیشترین ارزش را دارند که کد را توصیفی‌تر، پیکربندی را ساده‌تر و قراردادهای برنامه را شفاف‌تر کنند؛ نه زمانی که جایگزین طراحی درست یا منطق شفاف برنامه شوند.