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
