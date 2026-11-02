تصميم أنظمة الـ Backend بيعتمد على 5 مفاهيم أساسية، وبمجرد ما تفهمهم بعقلية مبرمج C++، هتكتشف إنهم مجرد أسماء جديدة لأفكار إنت بتستخدمها بالفعل.

**1. Client-Server Architecture (العميل والخادم)**

* **في C++:** لما بتكتب برنامج، هو بيشتغل في الميموري بتاعتك (Local) ومفيش حد بيكلمه من بره.
* **في الويب:** البرنامج بتاعك (FastAPI) بيشتغل كـ **Server**. هو عبارة عن `while(true)` loop لا نهائية (بيديرها Uvicorn)، قاعدة بتسمع على بورت معين (زي 8000) ومستنية أي جهاز تاني على الشبكة (الـ **Client** سواء كان متصفح، تطبيق موبايل، أو كود زميلك في الهاكاثون) يبعتلها رسالة.

**2. API (Application Programming Interface)**

* **في C++:** الـ API بالنسبة لك كان ملف الـ Header (`.h`)، اللي بيعرفك إيه الدوال المتاحة عشان تستخدمها من مكتبة معينة من غير ما تبص على الكود الداخلي.
* **في الويب:** الـ API هو نفس الفكرة بالظبط! هو الـ Interface اللي بيسمح للـ Frontend يكلم الـ Backend. بدل ما بيستدعي دالة من Header، بيستدعي **رابط (URL)**.

**3. HTTP Protocol & Methods (لغة التخاطب)**
عشان الـ Client يكلم الـ Server، لازم يبعتله رسالة بصيغة معينة اسمها HTTP Request. الرسالة دي لازم يكون ليها "نوع" بيحدد إحنا عايزين نعمل إيه:

* ال **`GET`:** بنستخدمه لجلب بيانات فقط. (يعادل `const member function` في C++ لأنها مش بتعدل أي داتا، مجرد `return`).
* ال **`POST`:** بنستخدمه لإرسال بيانات جديدة تتخزن في الداتا بيز. (يعادل دالة `void add_item(Data d)`).
* ال **`PUT` / `PATCH`:** بنستخدمهم لتعديل بيانات موجودة بالفعل.
* ال **`DELETE`:** بنستخدمه لمسح بيانات.

**4. Routing & Endpoints (المسارات)**
الـ Endpoint هو الرابط اللي الـ Client بيكلمك عليه (زي `[https://api.nasa.com/planets](https://api.nasa.com/planets)`).

* **تحت الهود:** لو بتكتب سيرفر C++ من الصفر، كنت هتاخد الرابط ده كنص (String)، تعمله Parsing، وتعمل `switch case` ضخمة: "لو الرابط فيه كلمة planets، استدعي دالة `get_planets()`".
* **في FastAPI:** الـ Framework بيعمل الـ Mapping ده أوتوماتيك باستخدام الـ Decorators. لما بتكتب `@app.get("/planets")` إنت بتربط الرابط ده فوراً بالدالة اللي تحته (زي فكرة الـ Function Pointers).

**5. JSON (JavaScript Object Notation)**
الـ Client والـ Server ممكن يكونوا مكتوبين بلغات مختلفة (مثلاً الـ Frontend بـ JavaScript والـ Backend بـ Python). إزاي هيفهموا المتغيرات بتاعت بعض؟

* **في C++:** نقل بيانات الـ Objects عبر الشبكة بيحتاج Serialization معقد أو نقل Bytes.
* **في الويب:** العالم اتفق على صيغة نصية موحدة اسمها **JSON**. هي عبارة عن `String` متنسق كـ Key-Value pairs.
* ال FastAPI بياخد الـ Dictionary بتاع بايثون، ويحوله أوتوماتيك لنص JSON ويبعته، وياخد نص الـ JSON من الـ Frontend ويحوله أوتوماتيك لمتغيرات بايثون تقدر تتعامل معاها.

```python
# ==========================================
# Conceptual Mapping in FastAPI
# ==========================================

# 1. This is your API Router
from fastapi import FastAPI
app = FastAPI()

# 2. Endpoint (URL Route) mapped to an HTTP Method (GET)
# This represents a Read-Only API call
@app.get("/astronauts")
def get_astronauts():
    # 3. Python Dictionary automatically converted to JSON format for the Client
    return {
        "status": "success",
        "data": ["Neil Armstrong", "Buzz Aldrin"]
    }

```
