---
tags:
  - java
  - collections
  - data-structures
created: 2026-08-25
status: completed
Author: Parsa Eftekharmanesh
---

# Java Collections Framework — مرور سریع

> [!note]
> `Collection` یک اینترفیس پایه برای نگهداری گروهی از عناصر است.  
> `Map` عضو **Collections Framework** است، اما از `Collection` ارث‌بری نمی‌کند.

```text
Collection
├── List
├── Set
└── Queue
    └── Deque

Map (مستقل از Collection)
```

---

## 1. List

- مجموعه‌ای **ترتیب‌دار** و دارای **اندیس** (`0, 1, 2, ...`)
- عناصر **تکراری مجاز** هستند.
- دسترسی با اندیس معمولاً سریع است.

| پیاده‌سازی | کاربرد / ویژگی |
|---|---|
| `ArrayList` | انتخاب پیش‌فرض؛ دسترسی اندیسی سریع، افزودن در انتها مناسب |
| `LinkedList` | افزودن/حذف در ابتدا و انتها مناسب؛ دسترسی اندیسی کندتر |
| `Vector` | قدیمی (Legacy) و `synchronized`؛ معمولاً توصیه نمی‌شود |

```java
List<String> fruits = new ArrayList<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Apple"); // مجاز

String first = fruits.get(0);
```

### ArrayList vs Vector

| ویژگی | `ArrayList` | `Vector` |
|---|---|---|
| Thread-safe | خیر | بله (`synchronized`) |
| سرعت | بیشتر | کمتر |
| رشد پیش‌فرض ظرفیت | حدود ۵۰٪ | معمولاً ۲ برابر |
| استفاده امروزی | ✅ رایج و پیشنهادی | ⚠️ Legacy / کم‌استفاده |
| ذخیره‌سازی داخلی | آرایهٔ پویا | آرایهٔ پویا |

> [!tip]
> برای چندنخی بودن، معمولاً به‌جای `Vector` از ابزارهای `java.util.concurrent` یا همگام‌سازی کنترل‌شده استفاده می‌شود.

---

## 2. Set

- عناصر **یکتا** هستند؛ تکرار پذیرفته نمی‌شود.
- **اندیس ندارد**.
- نوع Set تعیین می‌کند ترتیب چگونه باشد.

| نوع | ترتیب | ساختار داخلی | کاربری |
|---|---|---|---|
| `HashSet` | نامشخص | `HashMap` | بیشترین سرعت، ترتیب مهم نیست |
| `LinkedHashSet` | ترتیب درج | Hash + Linked List | یکتا بودن + حفظ ترتیب ورود |
| `TreeSet` | مرتب‌شده | `TreeMap` / Red-Black Tree | داده‌های همیشه مرتب |

```java
Set<String> names = new HashSet<>();
names.add("Alice");
names.add("Bob");
names.add("Alice"); // اضافه نمی‌شود
```

### نکات مهم Set

- `HashSet`: عملیات `add`، `remove` و `contains` به‌طور میانگین: $O(1)$
- `TreeSet`: عملیات اصلی: $O(\log n)$
- `TreeSet` با ترتیب طبیعی معمولاً `null` را نمی‌پذیرد؛ پذیرش آن تنها با `Comparator` مناسب ممکن است.
- `HashSet` و `LinkedHashSet` حداکثر یک `null` می‌پذیرند.

---

## 3. Queue

- صف با قانون **FIFO**:  
  **First In, First Out** → اولین ورودی، اولین خروجی
- عملیات اصلی:
  - `offer()` / `add()` → افزودن
  - `poll()` / `remove()` → حذف از ابتدای صف
  - `peek()` / `element()` → مشاهدهٔ ابتدای صف

| نوع | ویژگی |
|---|---|
| `LinkedList` | پیاده‌سازی Queue و Deque |
| `ArrayDeque` | معمولاً انتخاب بهتر برای Queue/Stack |
| `PriorityQueue` | خروج بر اساس اولویت، نه الزاماً ترتیب ورود |

```java
Queue<Integer> queue = new ArrayDeque<>();

queue.offer(10);
queue.offer(20);
queue.offer(30);

System.out.println(queue.poll()); // 10
System.out.println(queue.peek()); // 20
```

> [!warning]
> تفاوت متدها:
> - `add/remove/element` در شرایط نامعتبر Exception می‌دهند.
> - `offer/poll/peek` معمولاً مقدار ویژه برمی‌گردانند (`false` یا `null`).

### صف Circular با آرایه

برای جلوگیری از هدررفت فضای ابتدای آرایه:

```java
rear = (rear + 1) % capacity;
front = (front + 1) % capacity;
```

- `enqueue`, `dequeue`, `peek`, `isEmpty`, `isFull` → همگی $O(1)$
- محدودیت: ظرفیت ثابت
- جایگزین: `LinkedList` یا `ArrayDeque`

---

## 4. Map

- نگهداری داده به‌شکل **کلید–مقدار** (`key-value`)
- کلیدها **یکتا** هستند؛ مقدارها می‌توانند تکراری باشند.
- دسترسی معمولاً با کلید انجام می‌شود.

| نوع | ترتیب کلیدها | کاربری |
|---|---|---|
| `HashMap` | نامشخص | انتخاب پیش‌فرض و سریع |
| `LinkedHashMap` | ترتیب درج (یا access order در پیکربندی خاص) | حفظ ترتیب |
| `TreeMap` | مرتب‌شده بر اساس کلید | کلیدهای مرتب |

```java
Map<String, Integer> ages = new HashMap<>();

ages.put("Alice", 30);
ages.put("Bob", 25);

System.out.println(ages.get("Alice")); // 30
```

### پیچیدگی

| ساختار | عملیات اصلی |
|---|---|
| `HashMap` | میانگین $O(1)$ |
| `LinkedHashMap` | میانگین $O(1)$ |
| `TreeMap` | $O(\log n)$ |

---

## 5. Stack و Deque

- پشته با قانون **LIFO**:  
  **Last In, First Out** → آخرین ورودی، اولین خروجی
- عملیات:
  - `push()` → افزودن به بالای پشته
  - `pop()` → حذف از بالا
  - `peek()` → مشاهدهٔ بالای پشته

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.pop()); // 30
```

> [!tip]
> کلاس `Stack` قدیمی است؛ برای پشته معمولاً `ArrayDeque` پیشنهاد می‌شود.

---

## 6. Array

- آرایه جزو Collections Framework نیست.
- اندازهٔ آن پس از ساخت **ثابت** است.
- می‌تواند primitiveها را مستقیم نگه دارد.

```java
int[] numbers = new int[10];
numbers[0] = 42;
```

| ویژگی | Array | ArrayList |
|---|---|---|
| اندازه | ثابت | پویا |
| نگهداری primitive | مستقیم | با Wrapper مانند `Integer` |
| دسترسی اندیسی | $O(1)$ | $O(1)$ |
| امکانات آماده | محدود | زیاد (`add`, `remove`, ...) |

---

# درخت‌ها و Heap

## Heap

### 1. Heap Memory
- بخشی از حافظهٔ JVM برای ذخیرهٔ Objectها.
- مدیریت حافظه و Garbage Collection در آن انجام می‌شود.

### 2. Heap Data Structure
- ساختاری برای دسترسی سریع به کمینه یا بیشینه.
- در جاوا، `PriorityQueue` معمولاً یک **Min-Heap** است.

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(30);
pq.offer(10);
pq.offer(20);

System.out.println(pq.poll()); // 10
```

---

## انواع درخت

| نوع | ویژگی اصلی | کاربرد / نمونه در جاوا |
|---|---|---|
| Binary Tree | هر گره حداکثر ۲ فرزند دارد | پیاده‌سازی دستی |
| BST | چپ < ریشه < راست | جست‌وجوی مرتب |
| AVL Tree | BST متوازن؛ اختلاف ارتفاع حداکثر ۱ | پیاده‌سازی دستی / کتابخانهٔ جانبی |
| Red-Black Tree | BST متوازن با قوانین رنگ | `TreeMap` و `TreeSet` |

### BST

```text
        10
       /  \
      5    15
```

- جست‌وجو، درج و حذف:
  - حالت متوازن: $O(\log n)$
  - بدترین حالت (نامتوازن): $O(n)$

### AVL Tree

- یک BST خودمتعادل است.
- شرط تعادل هر گره:

$$
|height(left) - height(right)| \leq 1
$$

- با چرخش‌های `LL`، `RR`، `LR` و `RL` متعادل می‌شود.
- عملیات اصلی: $O(\log n)$

### Red-Black Tree

- BST خودمتعادل با گره‌های قرمز و سیاه.
- نسبت به AVL سخت‌گیری تعادلی کمتری دارد.
- در Java:

```java
Map<Integer, String> treeMap = new TreeMap<>();

treeMap.put(20, "B");
treeMap.put(10, "A");
treeMap.put(30, "C");

// خروجی بر اساس ترتیب کلیدها: 10, 20, 30
System.out.println(treeMap);
```

---

# انتخاب سریع ساختار مناسب

| نیاز | انتخاب پیشنهادی |
|---|---|
| لیست عمومی و دسترسی با اندیس | `ArrayList` |
| حذف/افزودن زیاد از ابتدا و انتها | `LinkedList` یا `ArrayDeque` |
| عناصر یکتا، ترتیب مهم نیست | `HashSet` |
| عناصر یکتا با حفظ ترتیب درج | `LinkedHashSet` |
| عناصر یکتای مرتب | `TreeSet` |
| کلید–مقدار با دسترسی سریع | `HashMap` |
| کلید–مقدار مرتب | `TreeMap` |
| صف FIFO | `ArrayDeque` |
| صف بر اساس اولویت | `PriorityQueue` |
| پشته LIFO | `ArrayDeque` |