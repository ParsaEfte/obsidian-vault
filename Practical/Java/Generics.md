---
tags:
  - java
  - generics
  - oop
  - cheat-sheet
created: 2026-08-25
status: completed
Author: Parsa Eftekharmanesh
---

# Java Generics — مرور سریع

> [!abstract] هدف اصلی
> ۱. **ایمنی نوع در زمان کامپایل (Compile-time Type Safety)**  
> ۲. **حذف تبدیل نوع دستی (Eliminating Explicit Casts)**  
> ۳. **قابلیت استفاده مجدد از کد (Code Reusability)**

---

## ۱. سینتکس و مفاهیم پایه

قرارداد نام‌گذاری پارامترهای جنریک:
- `T` / `U` / `S`: Type
- `E`: Element (مورد استفاده در Collectionها)
- `K`, `V`: Key, Value (در Mapها)
- `N`: Number

### کلاس و اینترفیس جنریک
```java
public class Box<T> {
    private T content;
    public void set(T content) { this.content = content; }
    public T get() { return content; }
}

// استفاده:
Box<String> box = new Box<>();
box.set("Java");
String val = box.get(); // بدون نیاز به Cast
```

### متد جنریک (Generic Method)
متد می‌تواند به‌تنهایی و مستقل از جنریک بودن یا نبودن کلاس، جنریک باشد (علامت `<T>` قبل از نوع بازگشتی می‌آید):

```java
public class Util {
    public static <T> void swap(T[] array, int i, int j) {
        T temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

---

## ۲. ارث‌بری در جنریک‌ها (Invariance)

> [!danger] نکته تله‌دار
> جنریک‌ها در حالت عادی **Invariant** هستند:  
> اگر `Integer` زیرکلاس `Number` باشد، `List<Integer>` زیرکلاس `List<Number>` **نیست**.

```java
List<Integer> intList = new ArrayList<>();
List<Number> numList = intList; // ❌ Compile Error
```

---

## ۳. کران‌دار کردن انواع (Bounded Type Parameters)

برای محدود کردن پارامتر نوع به یک کلاس و فرزندانش یا پیاده‌سازان یک اینترفیس:

```java
// T حتماً باید Number یا زیرکلاسی از آن باشد
public static <T extends Number> int compare(T a, T b) {
    return Double.compare(a.doubleValue(), b.doubleValue());
}
```

> [!tip] چند کرانگی (Multiple Bounds)
> در جاوا برای چند کرانگی از `&` استفاده می‌شود (نه `,`):  
> `<T extends ClassA & InterfaceB & InterfaceC>`  
> *(اگر کلاس وجود دارد، باید اول قرار بگیرد)*

---

## ۴. کار با وایلدکاردها (Wildcards `?`)

| نوع Wildcard | سینتکس | مفهوم | مورد مصرف |
|---|---|---|---|
| **Unbounded** | `<?>` | هر نوعی مجاز است | مواقعی که فقط توابع پایه `Object` مهم است |
| **Upper Bounded** | `<? extends T>` | خود `T` یا زیرکلاس‌های آن (**Covariance**) | **خواندن داده (Read-Only / Producer)** |
| **Lower Bounded** | `<? super T>` | خود `T` یا سوپرکلاس‌های آن (**Contravariance**) | **نوشتن داده (Write-Only / Consumer)** |

> [!important] قاعده طلایی: PECS
> **Producer Extends, Consumer Super**
> - اگر از ساختار داده فقط **می‌خوانید** $\rightarrow$ `<? extends T>`
> - اگر در ساختار داده **می‌نویسید (add)** $\rightarrow$ `<? super T>`

```java
// Producer: فقط خواندن (Covariant)
public void printNumbers(List<? extends Number> list) {
    for (Number n : list) System.out.println(n.doubleValue());
    // list.add(10); // ❌ خطا: جاوا نمی‌داند نوع دقیق چیست
}

// Consumer: نوشتن داده (Contravariant)
public void addNumbers(List<? super Integer> list) {
    list.add(1); // ✅ مجاز
    list.add(2);
    // Object obj = list.get(0); // خروجی فقط به شکل Object تضمین می‌شود
}
```

---

## ۵. مکانیزم Type Erasure و عوارض آن

کامپایلر جاوا بعد از بررسی نوع، در بایت‌کد تمام تایپ‌های جنریک را حذف (**Erase**) می‌کند تا با نسخه‌های قبل از Java 5 سازگار باشد:
- `T` بدون کران $\rightarrow$ به `Object` تبدیل می‌شود.
- `T extends Number` $\rightarrow$ به `Number` تبدیل می‌شود.

```java
// کد شما
Box<String> box = new Box<>();

// کد تولیدی بعد از کامپایل (در سطح بایت‌کد)
Box box = new Box();
```

---

## ۶. محدودیت‌های کلیدی Generics

1. **عدم پشتیبانی از Primitiveها:**  
   `Box<int>` ❌ $\rightarrow$ باید از Wrapper Class استفاده شود: `Box<Integer>` ✅
2. **عدم امکان `new T()`:**  
   اطلاعات `T` در زمان اجرا پاک شده است؛ بنابراین `new T()` خطاست.
3. **عدم امکان ساخت مستقیم آرایه جنریک:**  
   `new List<String>[10]` ❌ $\rightarrow$ روش جایگزین: `(List<String>[]) new List<?>[10]` ✅
4. **عدم امکان ساخت فیلد/متد static با نوع جنریک کلاس:**  
   چون فیلد استاتیک بین نمونه‌های مختلف با تایپ‌های متفاوت مشترک است.
5. **عدم امکان `instanceof` روی تایپ پارامتری:**  
   `if (obj instanceof List<String>)` ❌ (تنها `if (obj instanceof List<?>)` مجاز است).