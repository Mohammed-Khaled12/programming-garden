> [!WARNING] 🚧 Work In Progress
> This note is a collection of raw problem-solving ideas and tricks. It's constantly growing! 🌱

tags: #cpc #math #problem-solving

---

# 💡 Key Ideas

### 1. Sum of N numbers (Gauss Formula)
لما نحتاج نجمع أرقام من 1 لـ N في مسألة، بدل الـ Loop:

$$Sum = \frac{n(n+1)}{2}$$
```cpp
// ⚡ Best Practice: Use long long to avoid overflow
long long sum = (n * (n + 1)) / 2;
```

* **Time Complexity:** `O(1)`instead of O(N).

### 2. Check if Prime (الجذر التربيعي)

عشان تعرف الرقم أولي ولا لأ، مش لازم تمشي لحد الرقم نفسه، كفاية تمشي لحد الجذر بتاعه `sqrt(n)` او حتي نصه.

### 3. Geometry: Circle vs. Square (Comparison Trick) 📐

لما تقارن أشكال ببعض عشان تشوف مين جوه مين، لازم تقارن الأبعاد الصح:

- **Square inside Circle:** قارن **قطر المربع**  مع  قطر الدائرة
    
- **Circle inside Square:** قارن **قطر الدائرة** مع **ضلع المربع

```cpp
#include <iostream>

#include <cmath>

using namespace std;

int main()

{

double R, S;

cin >> R >> S;

if (2 * R >= (S * sqrt(2)))

{

cout << "Circle";

}

else if (2 * R <= S)

{

cout << "Square";

}

else

{

cout << "Complex";

}

}
```