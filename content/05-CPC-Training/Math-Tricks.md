---
title: Math Tricks for CPC 🧮
tags: #cpc #math #problem-solving #cpp
---

> [!WARNING] 🚧 Work In Progress
> This note is a collection of raw problem-solving ideas and tricks. It's constantly growing! 🌱

---

## 1. Summation Formulas (O(1) Magic) ✨

### Basic Sum (1 to N)
لما نحتاج نجمع أرقام من 1 لـ N، بدل الـ Loop اللي بتاخد `O(N)`، نستخدم معادلة `O(1)`:

$$Sum = \frac{n(n+1)}{2}$$

```cpp
// ⚡ Best Practice: Use long long to prevent overflow
long long sum = (n * (n + 1)) / 2;
```


### Sum from L to R (Range Sum)

لما يطلب مجموع الأرقام في فترة معينة من $L$ لحد $R$، بدل ما تلف بـ Loop وتاخد `O(N)`، بنستخدم قانون "المتتابعة الحسابية" عشان نحلها في خطوة واحدة `O(1)`:

$$Sum = \frac{Count \times (First + Last)}{2}$$

- **$Count$ (عدد الحدود):** $R - L + 1$
    
- **$First$ (أول رقم):** $L$
    
- **$Last$ (آخر رقم):** $R$

```C++
// ⚡ Best Practice: Use long long to prevent overflow
// لأن حاصل ضرب العدد في المجموع ممكن يعدي مساحة الـ int بكتير
long long count = (R - L + 1);
long long sum = count * (L + R) / 2;

```

### Sum of Squares ($1^2 + 2^2 + ... + n^2$)
$$Sum = \frac{n(n+1)(2n+1)}{6}$$

### Alternating Sums (Positive/Negative)
لو المسألة فيها جمع وطرح بالتبادل (زي: $1 - 2 + 3 - 4 ...$).

#### A) Pattern: Starts Positive ($1 - 2 + 3 - 4 ...$)
* **If N is Even:**
$$Sum = \frac{-n}{2}$$
* **If N is Odd:**
$$Sum = \frac{n+1}{2}$$

#### B) Pattern: Starts Negative ($-1 + 2 - 3 + 4 ...$)
* **If N is Even:**
$$Sum = \frac{n}{2}$$
* **If N is Odd:**
$$Sum = \frac{-(n+1)}{2}$$

---

## 2. Check if Prime (الجذر التربيعي) 🔍

عشان تعرف الرقم أولي ولا لأ، **أوعى** تمشي لحد الرقم نفسه `N` (Time Limit Exceeded).
* **الحل:** كفاية تمشي لحد الجذر بتاعه $\sqrt{N}$.
* **السبب:** لو الرقم ملوش قواسم قبل الجذر، مش هيكون ليه بعده.

```cpp
bool isPrime(long long n) {
    if (n <= 1) return false;
    // Check from 2 up to sqrt(n)
    for (long long i = 2; i * i <= n; i++) { 
        if (n % i == 0) return false;
    }
    return true;
}
```

---

## 3. Geometry: Circle vs. Square 📐

لما تقارن أشكال ببعض عشان تشوف مين جوه مين، قارن "الأبعاد الحاسمة":

* **Square inside Circle?** $\rightarrow$ قارن **قطر المربع** ($Diagonal$) مع **قطر الدائرة** ($2R$).
    * القانون: $S \sqrt{2} \le 2R$
* **Circle inside Square?** $\rightarrow$ قارن **قطر الدائرة** ($2R$) مع **ضلع المربع** ($S$).
    * القانون: $2R \le S$

```cpp
#include <iostream>
#include <cmath>
using namespace std;

void solve() {
    double R, S; // R = Radius, S = Square Side
    cin >> R >> S;

    // Check if Square fits inside Circle (Compare Diagonals)
    if (S * sqrt(2) <= 2 * R) {
        cout << "Square fits in Circle";
    }
    // Check if Circle fits inside Square (Compare Diameter vs Side)
    else if (2 * R <= S) {
        cout << "Circle fits in Square";
    }
    else {
        cout << "Complex Intersection";
    }
}
```

---

## 4. Degrees of Freedom (Equation Solutions) 🎲

When asked for the number of solutions to an equation like $a+b+c+d=n$, where **one variable has no constraints** (can be any integer):

> [!TIP] The Trick
> The unconstrained variable acts as a "Balancer". Whatever values you pick for the others, the last variable will adjust to make the equation true.

* **Formula:** The answer is simply the product of the ranges of the **constrained** variables.
* **Range Calculation:** $Count = (Max - Min + 1)$.

### Example:
Solve for $a+b+c+d = N$ where:
* $|a| \le n \rightarrow Range: [-n, n] \rightarrow (2n+1)$ possibilities.
* $|b| \le n \rightarrow Range: [-n, n] \rightarrow (2n+1)$ possibilities.
* $|c| \le n \rightarrow Range: [-n, n] \rightarrow (2n+1)$ possibilities.
* $d$ is free (no constraints).

$$\text{Total Solutions} = (2n + 1)^3$$