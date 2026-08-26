---
title: استریم‌ها در جاوا (Java Streams)
tags:
  - java
  - streams
  - java-8
  - functional-programming
  - collections
aliases:
  - Java Stream API
  - استریم جاوا
created: 2026-08-25
---

# استریم‌ها در جاوا (Java Streams)

> [!summary] تعریف کوتاه
> **Stream API** در جاوا، روشی تابعی و خوانا برای پردازش مجموعه‌ای از داده‌ها است.  
> با استریم‌ها می‌توان عملیات‌هایی مانند فیلترکردن، تبدیل، مرتب‌سازی، گروه‌بندی و محاسبه را به‌صورت زنجیره‌ای انجام داد.

---

## فهرست مطالب

- [[#مقدمه]]
- [[#ایجاد استریم]]
- [[#چرخهٔ پردازش استریم]]
- [[#عملیات میانجی (Intermediate Operations)]]
- [[#عملیات پایانی (Terminal Operations)]]
- [[#ویژگی‌ها و قوانین مهم Stream]]
- [[#Collectors و جمع‌آوری نتایج]]
- [[#گروه‌بندی و پارتیشن‌بندی]]
- [[#flatMap و ساختارهای تودرتو]]
- [[#استریم‌های اولیه (Primitive Streams)]]
- [[#استریم‌های بی‌نهایت]]
- [[#پردازش موازی]]
- [[#Optional در استریم‌ها]]
- [[#Method Reference و عملگر ::]]
- [[#مثال جامع]]
- [[#نکات و اشتباهات رایج]]

---

## مقدمه

استریم در جاوا **خودِ داده یا ساختار داده نیست**؛ بلکه یک مسیر پردازش برای داده‌ها است.

یک Stream می‌تواند از منابع مختلفی ایجاد شود:

- `Collection`ها مانند `List`، `Set` و `Map`
- آرایه‌ها
- فایل‌ها
- مقادیر ثابت
- تولیدکننده‌های بی‌نهایت مانند `Stream.iterate`

استریم‌ها از جاوا ۸ معرفی شدند و از سبک برنامه‌نویسی تابعی پشتیبانی می‌کنند.

```java
List<String> names = Arrays.asList("John", "Jane", "Jack", "Doe");

List<String> result = names.stream()
    .filter(name -> name.startsWith("J"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());

System.out.println(result);
// [JOHN, JANE, JACK]
```

---

## ایجاد استریم

### ۱. از Collectionها

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);

Stream<Integer> stream = list.stream();
```

برای پردازش موازی نیز می‌توان از `parallelStream()` استفاده کرد:

```java
Stream<Integer> parallelStream = list.parallelStream();
```

---

### ۲. از آرایه‌ها

```java
int[] array = {1, 2, 3, 4, 5};

IntStream stream = Arrays.stream(array);
```

برای آرایه‌های شیء:

```java
String[] names = {"Alice", "Bob", "Charlie"};

Stream<String> stream = Arrays.stream(names);
```

---

### ۳. با `Stream.of`

```java
Stream<String> stream = Stream.of("a", "b", "c");
```

---

### ۴. از Map

برای پردازش یک `Map`، معمولاً از `entrySet()`، `keySet()` یا `values()` استفاده می‌شود.

```java
Map<Integer, String> users = Map.of(
    1, "Alice",
    2, "Bob"
);

users.entrySet().stream()
    .forEach(entry ->
        System.out.println(entry.getKey() + ": " + entry.getValue())
    );
```

---

## چرخهٔ پردازش استریم

یک زنجیرهٔ معمول Stream معمولاً سه بخش دارد:

```java
source.stream()       // منبع داده
    .filter(...)      // عملیات میانجی
    .map(...)         // عملیات میانجی
    .collect(...);    // عملیات پایانی
```

| بخش | وظیفه |
|---|---|
| **Source** | منبع داده، مثل `List` یا آرایه |
| **Intermediate Operation** | تغییر یا فیلتر داده‌ها و ساخت Stream جدید |
| **Terminal Operation** | اجرای واقعی پردازش و تولید نتیجه |

> [!important] نکته
> عملیات‌های میانجی تا زمانی که یک عملیات پایانی اجرا نشود، معمولاً اجرا نمی‌شوند. به این رفتار **Lazy Evaluation** گفته می‌شود.

---

## عملیات میانجی (Intermediate Operations)

عملیات میانجی، یک Stream می‌گیرد و یک Stream جدید برمی‌گرداند.

ویژگی‌ها:

- قابل زنجیره‌کردن هستند.
- معمولاً Lazy هستند.
- تا رسیدن به عملیات پایانی، اجرا نمی‌شوند.

---

### `filter`

برای نگه‌داشتن عناصری که یک شرط را پاس می‌کنند.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

List<Integer> result = numbers.stream()
    .filter(number -> number > 2)
    .collect(Collectors.toList());

System.out.println(result);
// [3, 4, 5]
```

---

### `map`

برای تبدیل هر عنصر به یک مقدار جدید.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

List<Integer> doubled = numbers.stream()
    .map(number -> number * 2)
    .collect(Collectors.toList());

System.out.println(doubled);
// [2, 4, 6, 8, 10]
```

مثال با رشته:

```java
List<String> upperNames = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

---

### `sorted`

برای مرتب‌سازی عناصر.

```java
List<Integer> sortedNumbers = numbers.stream()
    .sorted()
    .collect(Collectors.toList());
```

مرتب‌سازی نزولی:

```java
List<Integer> descending = numbers.stream()
    .sorted(Comparator.reverseOrder())
    .collect(Collectors.toList());
```

مرتب‌سازی اشیا بر اساس یک فیلد:

```java
List<Person> sortedPeople = people.stream()
    .sorted(Comparator.comparing(Person::getAge))
    .collect(Collectors.toList());
```

---

### `distinct`

برای حذف عناصر تکراری.

```java
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3, 4);

List<Integer> uniqueNumbers = numbers.stream()
    .distinct()
    .collect(Collectors.toList());

System.out.println(uniqueNumbers);
// [1, 2, 3, 4]
```

> [!note]
> `distinct()` برای اشیا به پیاده‌سازی صحیح `equals()` و `hashCode()` وابسته است.

---

### `limit` و `skip`

`limit(n)` فقط اولین `n` عنصر را نگه می‌دارد:

```java
List<Integer> firstThree = numbers.stream()
    .limit(3)
    .collect(Collectors.toList());
```

`skip(n)` اولین `n` عنصر را نادیده می‌گیرد:

```java
List<Integer> afterFirstTwo = numbers.stream()
    .skip(2)
    .collect(Collectors.toList());
```

---

### `peek`

برای مشاهده یا دیباگ عناصر در میانهٔ زنجیره استفاده می‌شود.

```java
List<String> result = Stream.of("one", "two", "three", "four")
    .filter(word -> word.length() > 3)
    .peek(word -> System.out.println("بعد از فیلتر: " + word))
    .map(String::toUpperCase)
    .peek(word -> System.out.println("بعد از تبدیل: " + word))
    .collect(Collectors.toList());
```

> [!warning]
> از `peek()` برای تغییر دادن داده‌ها یا منطق اصلی برنامه استفاده نکنید؛ هدف اصلی آن مشاهده و دیباگ است.

---

## عملیات پایانی (Terminal Operations)

عملیات پایانی باعث اجرای Stream می‌شود و معمولاً یک نتیجه برمی‌گرداند یا عملی انجام می‌دهد.

> [!danger] قانون مهم
> پس از اجرای یک عملیات پایانی، Stream **مصرف می‌شود** و دیگر قابل استفاده نیست.

```java
Stream<Integer> stream = Stream.of(1, 2, 3);

stream.count();

// خطا: Stream قبلاً مصرف شده است
// stream.forEach(System.out::println);
```

---

### `forEach`

برای انجام عملی روی هر عنصر:

```java
names.stream()
    .forEach(System.out::println);
```

---

### `collect`

برای تبدیل خروجی Stream به ساختار داده‌ای دیگر، مثل `List`، `Set` یا `Map`.

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("J"))
    .collect(Collectors.toList());
```

> [!tip]
> در جاوا ۱۶ به بعد می‌توان به‌جای `collect(Collectors.toList())` از `toList()` نیز استفاده کرد:
>
> ```java
> List<String> result = names.stream()
>     .filter(name -> name.startsWith("J"))
>     .toList();
> ```
>
> خروجی `toList()` معمولاً **غیرقابل تغییر** است؛ پس برای لیست قابل تغییر، از `Collectors.toList()` استفاده کنید.

---

### `count`

برای شمارش عناصر:

```java
long count = names.stream()
    .filter(name -> name.startsWith("J"))
    .count();
```

---

### `reduce`

برای تبدیل چند مقدار به یک مقدار نهایی استفاده می‌شود.

محاسبهٔ مجموع:

```java
int sum = Stream.of(1, 2, 3, 4, 5)
    .reduce(0, Integer::sum);

System.out.println(sum);
// 15
```

محاسبهٔ حاصل‌ضرب:

```java
int product = Stream.of(1, 2, 3, 4, 5)
    .reduce(1, (a, b) -> a * b);
```

اگر مقدار اولیه ندهیم، خروجی `Optional` خواهد بود:

```java
Optional<Integer> sum = Stream.of(1, 2, 3)
    .reduce(Integer::sum);
```

---

### `findFirst` و `findAny`

```java
Optional<String> first = names.stream()
    .filter(name -> name.startsWith("J"))
    .findFirst();
```

```java
Optional<String> any = names.parallelStream()
    .filter(name -> name.startsWith("J"))
    .findAny();
```

- `findFirst()` ترتیب را حفظ می‌کند.
- `findAny()` مخصوصاً در پردازش موازی ممکن است سریع‌تر باشد.

---

### `anyMatch`، `allMatch` و `noneMatch`

```java
boolean hasNameStartingWithJ = names.stream()
    .anyMatch(name -> name.startsWith("J"));
```

```java
boolean allLongerThanTwo = names.stream()
    .allMatch(name -> name.length() > 2);
```

```java
boolean noneStartsWithZ = names.stream()
    .noneMatch(name -> name.startsWith("Z"));
```

---

## ویژگی‌ها و قوانین مهم Stream

### عدم تغییر منبع اصلی

استریم معمولاً دادهٔ اصلی را تغییر نمی‌دهد.

```java
List<String> names = new ArrayList<>(
    Arrays.asList("John", "Jane", "Jack")
);

List<String> upperNames = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

System.out.println(names);
// [John, Jane, Jack]

System.out.println(upperNames);
// [JOHN, JANE, JACK]
```

---

### Lazy بودن

کد زیر بدون عملیات پایانی اجرا نمی‌شود:

```java
Stream.of(1, 2, 3)
    .filter(number -> {
        System.out.println("Filtering: " + number);
        return number > 1;
    });
```

اما با اضافه کردن `collect()` یا `count()`، پردازش انجام می‌شود:

```java
long count = Stream.of(1, 2, 3)
    .filter(number -> {
        System.out.println("Filtering: " + number);
        return number > 1;
    })
    .count();
```

---

### یک‌بارمصرف بودن Stream

هر Stream فقط یک‌بار قابل استفاده است.

```java
Stream<String> stream = Stream.of("A", "B", "C");

stream.forEach(System.out::println);

// استفادهٔ مجدد مجاز نیست
// stream.count();
```

اگر دوباره به Stream نیاز دارید، یک Stream جدید بسازید:

```java
List<String> list = Arrays.asList("A", "B", "C");

long count = list.stream().count();
list.stream().forEach(System.out::println);
```

---

## Collectors و جمع‌آوری نتایج

کلاس `Collectors` ابزارهای متنوعی برای جمع‌کردن خروجی Stream ارائه می‌کند.

ابتدا import لازم:

```java
import java.util.stream.Collectors;
```

---

### جمع‌آوری در `List`

```java
List<String> list = stream.collect(Collectors.toList());
```

---

### جمع‌آوری در `Set`

برای حذف تکراری‌ها:

```java
Set<String> set = stream.collect(Collectors.toSet());
```

---

### اتصال رشته‌ها با `joining`

```java
String joined = Stream.of("Java", "Python", "Go")
    .collect(Collectors.joining(", "));

System.out.println(joined);
// Java, Python, Go
```

با پیشوند و پسوند:

```java
String joined = Stream.of("Java", "Python", "Go")
    .collect(Collectors.joining(", ", "[", "]"));

// [Java, Python, Go]
```

---

### جمع‌آوری در `Map`

```java
Map<Integer, String> map = Stream.of("Java", "Python", "Go")
    .collect(Collectors.toMap(
        String::length,
        Function.identity()
    ));
```

> [!warning] کلید تکراری
> اگر کلیدهای تولیدشده تکراری باشند، `toMap` خطا می‌دهد.  
> برای حل آن باید تابع ادغام مشخص شود:

```java
Map<Integer, String> map = Stream.of("Java", "Ruby", "Python")
    .collect(Collectors.toMap(
        String::length,
        Function.identity(),
        (first, second) -> first
    ));
```

---

## گروه‌بندی و پارتیشن‌بندی

### گروه‌بندی با `groupingBy`

برای دسته‌بندی عناصر بر اساس یک ویژگی استفاده می‌شود.

```java
List<String> names = Arrays.asList(
    "John", "Jane", "Jack", "Doe", "James"
);

Map<Integer, List<String>> groupedByLength = names.stream()
    .collect(Collectors.groupingBy(String::length));

System.out.println(groupedByLength);
```

خروجی احتمالی:

```text
{3=[Doe], 4=[John, Jane, Jack], 5=[James]}
```

> [!note]
> ترتیب کلیدها در `HashMap` تضمین‌شده نیست. اگر ترتیب مهم است، از `TreeMap` یا `LinkedHashMap` استفاده کنید.

---

### مثال گروه‌بندی افراد بر اساس سن

```java
import java.util.*;
import java.util.stream.Collectors;

public class GroupingByExample {

    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 20),
            new Person("Bob", 25),
            new Person("Charlie", 30),
            new Person("David", 25),
            new Person("Eve", 20)
        );

        Map<Integer, List<Person>> peopleByAge = people.stream()
            .collect(Collectors.groupingBy(Person::getAge));

        peopleByAge.forEach((age, persons) -> {
            System.out.println("Age: " + age);
            persons.forEach(person ->
                System.out.println("- " + person.getName())
            );
            System.out.println();
        });
    }

    static class Person {
        private final String name;
        private final int age;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public int getAge() {
            return age;
        }
    }
}
```

خروجی:

```text
Age: 20
- Alice
- Eve

Age: 25
- Bob
- David

Age: 30
- Charlie
```

---

### پارتیشن‌بندی با `partitioningBy`

برای تقسیم عناصر به **دو گروه** بر اساس یک شرط بولی استفاده می‌شود.

```java
Map<Boolean, List<String>> partitioned = names.stream()
    .collect(Collectors.partitioningBy(
        name -> name.startsWith("J")
    ));
```

- کلید `true`: نام‌هایی که با `J` شروع می‌شوند.
- کلید `false`: سایر نام‌ها.

---

### مثال پارتیشن‌بندی افراد بر اساس سن

```java
Map<Boolean, List<Person>> partitionedPeople = people.stream()
    .collect(Collectors.partitioningBy(
        person -> person.getAge() > 25
    ));

partitionedPeople.forEach((isOlderThan25, persons) -> {
    System.out.println("Older than 25: " + isOlderThan25);

    persons.forEach(person ->
        System.out.println("- " + person.getName())
    );

    System.out.println();
});
```

خروجی:

```text
Older than 25: false
- Alice
- Bob
- David
- Eve

Older than 25: true
- Charlie
```

> [!tip] تفاوت `groupingBy` و `partitioningBy`
> - `groupingBy`: می‌تواند تعداد زیادی گروه بسازد.
> - `partitioningBy`: فقط دو گروه با کلیدهای `true` و `false` می‌سازد.

---

### گروه‌بندی پیشرفته با Downstream Collector

در مثال زیر، نام‌ها ابتدا بر اساس طول دسته‌بندی می‌شوند و سپس تعداد اعضای هر گروه محاسبه می‌شود:

```java
Map<Integer, Long> countByLength = names.stream()
    .collect(Collectors.groupingBy(
        String::length,
        Collectors.counting()
    ));

System.out.println(countByLength);
```

---

## `flatMap` و ساختارهای تودرتو

`map` هر عنصر را به یک مقدار تبدیل می‌کند؛ اما `flatMap` هر عنصر را به یک Stream تبدیل کرده و سپس همهٔ Streamها را در یک Stream تخت ادغام می‌کند.

### تفاوت `map` و `flatMap`

```java
List<List<String>> listOfLists = Arrays.asList(
    Arrays.asList("a", "b", "c"),
    Arrays.asList("d", "e", "f")
);
```

استفاده از `map`:

```java
Stream<Stream<String>> nestedStreams = listOfLists.stream()
    .map(Collection::stream);
```

استفاده از `flatMap`:

```java
List<String> flattenedList = listOfLists.stream()
    .flatMap(Collection::stream)
    .collect(Collectors.toList());

System.out.println(flattenedList);
// [a, b, c, d, e, f]
```

---

## استریم‌های اولیه (Primitive Streams)

جاوا برای جلوگیری از هزینهٔ تبدیل بین نوع‌های اولیه و Wrapperها، Streamهای مخصوصی دارد:

| نوع داده | Stream مخصوص |
|---|---|
| `int` | `IntStream` |
| `long` | `LongStream` |
| `double` | `DoubleStream` |

---

### `IntStream`

```java
IntStream intStream = IntStream.range(1, 10);

int sum = intStream.sum();

System.out.println(sum);
// 45
```

> [!note]
> `IntStream.range(1, 10)` شامل `1` تا `9` است و عدد `10` را شامل نمی‌شود.

برای شامل‌کردن انتهای بازه:

```java
IntStream.rangeClosed(1, 10)
    .forEach(System.out::println);
```

---

### متدهای مفید Primitive Streamها

```java
OptionalDouble average = IntStream.of(1, 2, 3, 4, 5).average();
OptionalInt max = IntStream.of(1, 2, 3, 4, 5).max();
OptionalInt min = IntStream.of(1, 2, 3, 4, 5).min();

average.ifPresent(System.out::println);
max.ifPresent(System.out::println);
min.ifPresent(System.out::println);
```

---

### تبدیل بین Stream معمولی و Primitive Stream

تبدیل `Stream<Integer>` به `IntStream`:

```java
Stream<Integer> stream = Arrays.asList(1, 2, 3, 4, 5).stream();

IntStream intStream = stream.mapToInt(Integer::intValue);
```

تبدیل `IntStream` به `Stream<Integer>`:

```java
IntStream intStream = IntStream.range(1, 10);

Stream<Integer> stream = intStream.boxed();
```

---

## استریم‌های بی‌نهایت

با `Stream.generate` و `Stream.iterate` می‌توان Streamهای بی‌نهایت ایجاد کرد.

> [!danger]
> Stream بی‌نهایت باید با عملیاتی مانند `limit()` محدود شود؛ در غیر این صورت ممکن است برنامه هرگز تمام نشود.

### با `Stream.iterate`

```java
Stream<Integer> infiniteStream = Stream.iterate(0, n -> n + 2);

List<Integer> numbers = infiniteStream
    .limit(10)
    .collect(Collectors.toList());

System.out.println(numbers);
// [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

### با `Stream.generate`

```java
List<Integer> randomNumbers = Stream.generate(
        () -> (int) (Math.random() * 100)
    )
    .limit(10)
    .collect(Collectors.toList());
```

---

## پردازش موازی

با `parallelStream()` یا `parallel()` می‌توان پردازش‌ها را به‌صورت موازی انجام داد.

```java
List<Integer> numbers = Arrays.asList(
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10
);

int sum = numbers.parallelStream()
    .reduce(0, Integer::sum);

System.out.println("Sum: " + sum);
```

یا:

```java
int sum = numbers.stream()
    .parallel()
    .reduce(0, Integer::sum);
```

> [!warning] استفادهٔ محتاطانه از Parallel Stream
> پردازش موازی همیشه سریع‌تر نیست. برای داده‌های کوچک یا عملیات سبک، سربار ایجاد Thread می‌تواند عملکرد را بدتر کند.
>
> از Stream موازی زمانی استفاده کنید که:
>
> - داده‌ها نسبتاً حجیم باشند.
> - پردازش هر عنصر سنگین باشد.
> - عملیات‌ها مستقل از هم باشند.
> - نیازی به ترتیب دقیق خروجی نداشته باشید.
>
> در پردازش موازی از تغییر دادن متغیرهای مشترک اجتناب کنید.

نمونهٔ نامناسب:

```java
List<Integer> result = new ArrayList<>();

numbers.parallelStream()
    .forEach(result::add); // ممکن است مشکل Thread Safety ایجاد کند
```

نمونهٔ مناسب:

```java
List<Integer> result = numbers.parallelStream()
    .map(number -> number * 2)
    .collect(Collectors.toList());
```

---

## `Optional` در استریم‌ها

برخی از عملیات Stream، مانند `findFirst`، `max` و `reduce`، ممکن است نتیجه‌ای نداشته باشند. برای مدیریت این وضعیت از `Optional` استفاده می‌شود.

```java
Optional<String> foundName = names.stream()
    .filter(name -> name.startsWith("Ja"))
    .findFirst();

foundName.ifPresent(System.out::println);
```

دادن مقدار پیش‌فرض:

```java
String name = names.stream()
    .filter(item -> item.startsWith("Z"))
    .findFirst()
    .orElse("Not Found");
```

> [!warning]
> از `optional.get()` بدون اطمینان از وجود مقدار استفاده نکنید؛ ممکن است `NoSuchElementException` رخ دهد.

---

## Method Reference و عملگر `::`

`::` در جاوا یک **Method Reference** است، نه یک عملگر معمولی ریاضی یا منطقی.

این قابلیت زمانی کاربرد دارد که یک Lambda Expression فقط یک متد موجود را فراخوانی می‌کند.

---

### ارجاع به متد یک شیء مشخص

```java
names.forEach(System.out::println);
```

معادل Lambda:

```java
names.forEach(name -> System.out.println(name));
```

---

### ارجاع به متد استاتیک

```java
Function<Integer, String> intToString = Integer::toString;
```

معادل:

```java
Function<Integer, String> intToString =
    number -> Integer.toString(number);
```

---

### ارجاع به سازنده

```java
Supplier<List<String>> listSupplier = ArrayList::new;

List<String> list = listSupplier.get();
```

معادل:

```java
Supplier<List<String>> listSupplier = () -> new ArrayList<>();
```

---

### ارجاع به متد نمونهٔ یک نوع

```java
List<String> names = Arrays.asList("john", "jane", "jack");

names.stream()
    .map(String::toUpperCase)
    .forEach(System.out::println);
```

---

## مثال جامع

```java
import java.util.*;
import java.util.stream.Collectors;

public class StreamExample {

    public static void main(String[] args) {
        List<String> names = Arrays.asList(
            "John", "Jane", "Jack", "Doe",
            "James", "Emily", "Anna", "Bob"
        );

        Map<Integer, List<String>> result = names.stream()
            .filter(name -> name.length() > 3)
            .map(String::toUpperCase)
            .sorted()
            .collect(Collectors.groupingBy(String::length));

        result.forEach((length, nameList) ->
            System.out.println(
                "Length: " + length + " | Names: " + nameList
            )
        );
    }
}
```

### روند اجرای مثال

1. نام‌های کوتاه‌تر از ۴ حرف حذف می‌شوند.
2. نام‌های باقی‌مانده به حروف بزرگ تبدیل می‌شوند.
3. نام‌ها مرتب می‌شوند.
4. نام‌ها بر اساس طولشان گروه‌بندی می‌شوند.
5. نتیجه در قالب `Map<Integer, List<String>>` برگردانده می‌شود.

---

## نکات و اشتباهات رایج

> [!warning] تغییر منبع داده هنگام پردازش
> هنگام پردازش Stream، Collection اصلی را تغییر ندهید.

```java
// نامناسب
names.stream().forEach(name -> names.remove(name));
```

---

> [!warning] استفادهٔ مجدد از Stream
> یک Stream بعد از عملیات پایانی قابل استفاده نیست.

```java
Stream<String> stream = names.stream();

long count = stream.count();

// خطا
// stream.forEach(System.out::println);
```

---

> [!tip] ترتیب عملیات اهمیت دارد
> ابتدا داده‌ها را تا حد ممکن فیلتر کنید، سپس عملیات سنگین‌تر مانند `map` یا `sorted` را انجام دهید.

```java
// بهتر
names.stream()
    .filter(name -> name.startsWith("J"))
    .sorted()
    .toList();
```

---

> [!tip] انتخاب درست نوع Stream
> برای محاسبات عددی از `IntStream`، `LongStream` و `DoubleStream` استفاده کنید تا از Boxing و Unboxing غیرضروری جلوگیری شود.

```java
int sum = IntStream.rangeClosed(1, 100)
    .sum();
```

---

## جمع‌بندی

استریم‌ها در جاوا ابزاری قدرتمند برای پردازش داده‌ها با رویکردی تابعی هستند.

مفاهیم اصلی که باید به خاطر سپرد:

- Stream ساختار داده نیست؛ مسیر پردازش داده‌ها است.
- عملیات‌ها به دو دستهٔ **میانجی** و **پایانی** تقسیم می‌شوند.
- عملیات میانجی Lazy هستند.
- هر Stream فقط یک‌بار مصرف می‌شود.
- `filter` برای فیلتر، `map` برای تبدیل و `flatMap` برای مسطح‌سازی استفاده می‌شود.
- `Collectors` برای تبدیل نتیجه به `List`، `Set`، `Map` و موارد دیگر کاربرد دارد.
- `groupingBy` برای چند گروه و `partitioningBy` برای دو گروه است.
- Streamهای موازی باید با احتیاط و پس از ارزیابی عملکرد استفاده شوند.

> [!quote]
> Streamها کمک می‌کنند کدهایی کوتاه‌تر، خواناتر و قابل نگهداری‌تر برای پردازش مجموعه‌داده‌ها بنویسیم.