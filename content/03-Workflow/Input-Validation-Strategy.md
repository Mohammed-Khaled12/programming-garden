
المشكله بتبدا لما المستخدم بيدخل سترينج او اي نوع تاني و انت مستني منه int مثلا فااا البرنامج بتاعك يضرب.

عشان نحل المشكلة لازم نعمل 3 خطوات بالترتيب:

1. نتأكد هل حصل خطأ؟ (`cin.fail()`)
    
2. لو حصل، نصلح الـ `cin` نفسها (`cin.clear()`).
    
3. ننظف الماسورة من الزبالة اللي فيها (`cin.ignore()`).

```cpp
#include <iostream>
#include <limits> // عشان نستخدم numeric_limits

using namespace std;

int readNumber() {
    int num;
    cout << "Please enter a number: ";
    cin >> num;

    // طول ما فيه خطأ، خليك جوه اللوب
    while (cin.fail()) {
        // 1. صلح الـ cin (Reset Error Flag)
        cin.clear(); 

        // 2. نظف البافر (امسح الكلام الغلط لحد ما تلاقي Enter)
        cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n'); 

        // 3. اسأل المستخدم تاني
        cout << "Invalid input! Please enter a numeric value: ";
        cin >> num;
    }
    
    // لو وصلنا هنا يبقى الرقم سليم
    return num; 
}

int main() {
    int myAge = readNumber();
    cout << "Your age is: " << myAge << endl;
    return 0;
}
```

#### 1. `cin.fail()`

- بترجع `true` لو آخر عملية إدخال فشلت (مثلاً حاولت تدخل حروف في متغير `int`).

#### 2. `cin.clear()`

- **مهم جداً تفهم دي:** هي **مش بتمسح الكلام الغلط**.
    
- هي بس بتعمل "Reset" للـ `cin` عشان ترجع تشتغل تاني. زي لما ترفع مفتاح الكهرباء (Circuit Breaker) لما ينزل. من غير السطر ده، الـ `cin` هتفضل "ميتة".

#### 3. `cin.ignore(...)`

- دي "المقشة" اللي بتمسح الزبالة من البافر.
    
- الكود المعقد `numeric_limits<streamsize>::max()` معناه: "امسح **أقصى عدد ممكن من الحروف** لحد ما تقابل زرار الإنتر `\n`".
    
- ممكن تستبدلها بـ `cin.ignore(10000, '\n');` للتبسيط، بس الطريقة الأولى أضمن هندسياً.

---

💡 Advanced Tip: Instead of just printing errors, we can [[CPP Exception Handling]] to handle them logically.