
## First Code

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def get_root():
    return {"message": "This is Get"}


@app.post("/")
async def post_root():
    return {"message": "This is Post"}


@app.put("/", description="Put")
async def put_root():
    return {"message": "This is Put"}


@app.post("/old", deprecated=True)
async def post_old():
    return {"message": "This is deprecated Post"}
```

- ال **`from fastapi import FastAPI`**: بجيب الـ class الرئيسي من المكتبة. زي `#include` في C++، بس هنا بستورد class فيه كل منطق السيرفر.
- ال **`app = FastAPI()`**: بعمل object من الـ class ده وبحطه في `app`. ده قلب السيرفر، وعنده جدول المسارات (Routing Table) اللي بيربط كل Path بالفانكشن اللي تشتغل.
- ال `FastAPI` هو الـ class، و`app` هو الـ object، و`get` و`post` وأخواتهم هما فعلاً Methods تابعة للـ object ده.
- ال `description` بتظهر في الـ Swagger، و`deprecated=True` بتعلّم الـ endpoint إنه قديم في الـ docs، بس لسه شغال عادي.

---

## HTTP Methods

دي الأفعال اللي بتحدد الفرونت إند عايز إيه من السيرفر:

| Method   | بتعمل إيه                                                                       |
| -------- | ------------------------------------------------------------------------------- |
| `GET`    | بتجيب data بس من غير ما تعدل عليها (زي قراءات النبض عشان تتعرض على الشاشة)      |
| `POST`   | بتبعت data جديدة للسيرفر يعالجها في الـ Request Body (زي بيانات الرائد للتحليل) |
| `PUT`    | بتستبدل data موجودة بالفعل                                                      |
| `PATCH`  | بتعدل جزء بس من data موجودة                                                     |
| `DELETE` | بتمسح data                                                                      |

خد بالك: الـ Request Body مش "مخفي"، هو بس مش ظاهر في اللينك. التشفير بيجي من HTTPS مش من إنه في الـ body.

---

##  Endpoint / Path

في C++ لو عايز أشغل block معين بنده على الفانكشن باسمها، زي `calculate_hr()`. الفرونت إند ميعرفش أسماء فانكشنز بايثون اللي في السيرفر، فالطريقة الوحيدة إنه يوصلها هي **اللينك**. اللينك مجرد String بيشتغل كمفتاح بيربط الطلب اللي جاي من بره بالفانكشن اللي جوه.

لما بكتب `@app.get("/metrics")` فأنا بقول للسيرفر: "لو حد طلب `/metrics` شغّل الفانكشن اللي تحت الـ decorator وابعتله نتيجتها".

### تشريح الـ URL

`http://127.0.0.1:8000/ingest`

|الجزء|اسمه|دوره|
|---|---|---|
|`http://`|Protocol|لغة التخاطب (`https` يعني مشفر)|
|`127.0.0.1`|IP Address|عنوان الجهاز أو الـ Cloud Server (المبنى)|
|`:8000`|Port|الباب اللي Uvicorn سامع عليه، لأن الجهاز ممكن يكون عليه كذا سيرفر|
|`/ingest`|**Path**|المكتب الداخلي جوه السيرفر اللي الفانكشن قاعدة فيه|

ليه بكتب الـ **Path** بس في الـ decorator؟ لأن الدومين ممكن يتغير بكرة ويبقى `api.nasa.com` بدل `127.0.0.1`، ولو كتبت اللينك كامل الكود هيضرب لما أرفعه على Azure. الـ Path هو الجزء الثابت اللي بينتمي للتطبيق نفسه.

وللدقة: السيرفر مش بيستلم اللينك كامل كـ String ويقص منه. الـ client بيستخدم الـ IP والـ Port عشان يفتح الاتصال، وبعدها بيبعت الطلب بالشكل ده `GET /ingest` ومعاه الـ Host في الـ headers. فالـ Path هو اللي بيوصل لكود التطبيق فعلاً.

لو الفرونت إند راح لـ `http://127.0.0.1:8000` من غير path، هيروح للـ `/` يعني الـ Root.

### السيرفر مش بيبعت الفانكشن للفرونت إند

الفانكشن (كود بايثون) بتفضل جوه السيرفر وبتتنفذ على الـ CPU والـ RAM بتوعه. اللي بيرجع للفرونت إند هو **النتيجة** بس (الـ JSON).

زي مطعم:

|في المطعم|في الباك إند|
|---|---|
|الزبون بيقرا المنيو ويقرر|الفرونت إند|
|الزبون بيطلب `GET /pizza`|اللينك / Endpoint|
|المطبخ بيستقبل الطلب ويوجهه للقسم المختص|السيرفر|
|وصفة الطبخ (الزبون مبيشوفهاش ومبياخدش الشيف معاه)|الفانكشن|
|البيتزا اللي طلعت للزبون|الـ JSON Response|

---

##  Decorator

ال `@app.get("/")` ده decorator وظيفته يسجل الفانكشن اللي تحته جوه `app`:

- `@`: علامة الـ decorator.
- `app`: كائن السيرفر.
- `get`: نوع الـ HTTP Method.
- `"/"`: الـ Path.

يعني: "يا سيرفر، لما يجيلك `GET` على الـ `/` شغّل الفانكشن دي".

### إزاي بيعرف إن الفانكشن مربوطة بالـ Path؟

ده بيحصل وقت الإقلاع (Startup) قبل ما السيرفر يستقبل أي طلب:

1. لما بشغل السيرفر، بايثون بتقرا `main.py` من فوق لتحت.
2. لما توصل لـ `@app.get("/metrics")` هي **مش بتنفذ الفانكشن**، هي بتبعتها لـ `app` كـ object. في بايثون الفانكشن First-Class Object، يعني بتتمرر زي أي متغير، زي الـ Function Pointer في C++.
3. `app` بيسجل: الـ Path ونوع الطلب والفانكشن، جوه الـ Routing Table.
4. لما يجي طلب فعلي، السيرفر بيدور في الجدول ده ويشغّل الفانكشن المناسبة.

يعني الـ decorator بيشتغل **مرة واحدة** وقت الإقلاع بس.

### ملحوظة: الـ decorator هنا مش بيعمل wrapper

في الـ Decorators اللي اتعلمتها قبل كده، الـ decorator كان بياخد الفانكشن ويرجع `wrapper` جديد. هنا الوضع مختلف: `@app.get` بيسجل الفانكشن في الـ Routing Table وبعدين **بيرجع نفس الفانكشن الأصلية زي ما هي**:

```python
def read_root():
    return {"ok": True}


registered = app.get("/")(read_root)
print(registered is read_root)   # True
```

يعني `@app.get("/")` فوق الفانكشن هو بالظبط نفس `read_root = app.get("/")(read_root)`. جربتها وطلعت `True`.

يبقى الـ validation وتحويل الـ dictionary لـ JSON مش بيحصلوا لأن الـ decorator لف الفانكشن. دول بيحصلوا وقت الطلب في الـ handler اللي FastAPI بيبنيه حوالين الفانكشن. وكمان الـ auth والـ API Keys مش أوتوماتيك، دي بتضيفها بنفسك بالـ dependencies.

### الـ Routing Table مش Hash Map

كنت متخيلها `std::unordered_map` بتعمل look-up على المفتاح، بس الحقيقة إنها **List من الـ Routes بالترتيب اللي اتسجلوا بيه**. كل route فيه Path pattern ونوع الطلب. لما يجي طلب، FastAPI بيمشي على الـ list من فوق لتحت وبيشغّل **أول route يطابق**. وده بالظبط السبب إن **الترتيب مهم** (هتشوفه في الـ Path Parameters).

### دورة الطلب والرد (Request-Response Cycle)

1. الفرونت إند يبعت HTTP Request للينك، مثلاً `GET /metrics`.
2. ال Uvicorn يستقبل الطلب ويفهمه ويسلمه للـ app.
3. ال FastAPI يمشي على الـ Routes بالترتيب ويلاقي أول واحد يطابق الـ Path والـ Method.
4. ال FastAPI يتأكد إن المدخلات (path / query / body) صح، ولو لأ يرد بـ `422` من غير ما يشغل الفانكشن.
5. يشغّل الفانكشن عنده (مثلاً تقرا حالة الرائد من الـ `deque` في الميموري).
6. الفانكشن تعمل `return` لـ Dictionary.
7. ال FastAPI يحول الـ Dictionary لـ JSON (Serialization أوتوماتيك).
8. السيرفر يبعت الـ JSON بس كـ HTTP Response.
9. الفرونت إند يستلمه ويعرض الأرقام كرسومات.

---

## `async def`

ال `async` معناها Asynchronous. لما الفانكشن تستنى حاجة بطيئة (قاعدة بيانات أو شبكة)، السيرفر يسيبها ويرد على ناس تانية، ولما النتيجة تجهز يرجع لها. ومن غير ما أعمل Multithreading يدوي زي C++.

بس في شرط لازم أفهمه: `async def` بتفيد **بس لو جواها `await` على حاجة non-blocking**. لو كتبت جواها كود بيبلوك، هي هتبلوك السيرفر كله:

```python
import asyncio
import time


@app.get("/slow-bad")
async def slow_bad():
    time.sleep(5)             # blocks the whole event loop, nobody else is served
    return {"done": True}


@app.get("/slow-async")
async def slow_async():
    await asyncio.sleep(5)    # non-blocking wait, other requests are served meanwhile
    return {"done": True}


@app.get("/slow-sync")
def slow_sync():
    time.sleep(5)             # plain def runs in a threadpool, so it doesn't block the loop
    return {"done": True}
```

القاعدة: لو بستخدم مكتبة بتدعم `await` → `async def`. لو بستخدم مكتبة عادية بتبلوك → `def` عادية، وFastAPI بيشغلها في thread منفصل.

ملحوظة لمشروعي: الـ `async def` كلها بتشتغل على thread واحد، بس الـ `def` العادية بتشتغل في threads، فلو في data مشتركة في الميموري (زي الـ `deque`) أخد بالي.

---

## Converting `return` to JSON

الباك إند مالوش واجهة رسومية، فلغة التواصل مع الفرونت إند هي JSON. أنا بعمل `return` لـ Dictionary، وFastAPI بيحوله لـ JSON لوحده (Serialization).

```python
@app.get("/status")
async def get_status():
    return {"status": "ok", "astronauts": 4}
```

---

##  Duplicate Operation ID

لما شغلت الكود الأصلي (4 فانكشنز اسمهم `root`) طلع التحذير:

```
Duplicate Operation ID root__post for function root
```

كنت فاكر السبب إني سميت الأربعة بنفس الاسم، وإن بايثون مفيهاش Function Overloading زي C++. **ده مش السبب.** جربت وطلع:

- الأربع فانكشنز اتسجلوا وبيشتغلوا عادي، لأن الـ decorator سجل كل فانكشن لحظة ما اتعرّفت، قبل ما الاسم `root` يتعمله rebind.
- الـ Operation ID بيتبني من **اسم الفانكشن + الـ Path + الـ Method**: `root__get` و`root__put` و`root__post`. التلاتة مختلفين.
- التحذير جه من الـ `POST` المتكرر بس: **اتنين `POST` على نفس الـ `/` وبنفس اسم الفانكشن**، فطلعوا بنفس الـ ID `root__post`.

وفي مشكلة أكبر: الـ `POST` التاني (الـ deprecated) **عمره ما هيتنفذ**، لأن الـ Routing بيمشي بالترتيب وأول واحد بيطابق هو اللي بيشتغل.

الحل: أسماء واضحة ومختلفة للفانكشنز، وكل endpoint يبقى له Path + Method مميز.

---

## Uvicorn

ال FastAPI مش سيرفر، هو **Framework** بينظم الكود. السيرفر الفعلي اللي بيفتح الـ Sockets ويسمع على الـ Port هو **Uvicorn**. في C++ هو الـ `while(true)` loop اللي بتستقبل اتصالات TCP وتمررها لكودي. وهو مبني على **ASGI** (Asynchronous Server Gateway Interface) عشان يدعم `async/await`.

```bash
pip install fastapi uvicorn
uvicorn main:app --reload
```

|الجزء|معناه|
|---|---|
|`main`|اسم الملف `main.py` من غير الامتداد|
|`:`|فاصل بين اسم الملف واسم المتغير|
|`app`|الـ object اللي عملته بـ `app = FastAPI()`|
|`--reload`|للـ development بس: بيراقب الملفات ويعيد تشغيل السيرفر أول ما أحفظ تعديل|
|`--host 0.0.0.0`|الديفولت `127.0.0.1` يعني جهازي بس. عشان زمايلي على الشبكة أو الـ Docker يوصلوا لازم `0.0.0.0`|
|`--port 8080`|لتغيير الـ Port لو 8000 مشغول|
|`--workers N`|عدد الـ Processes. الديفولت **1**|

في الإصدارات الحديثة في كمان `fastapi dev main.py`، وبيشغل Uvicorn تحت الغطا.

### `--workers` في مشروع ناسا

الـ Workers بيعمل نسخ **منفصلة** من الـ Process، وكل نسخة ليها ميموري لوحدها. بما إني بحفظ قراءات الرواد في `deque` في الميموري، لو شغّلت أكتر من worker هيبقى عندي أكتر من `deque` والـ TUI هيقرا data متضاربة. الديفولت أصلاً 1 فمش لازم أكتبه، بس المهم **ما أزودوش**.

نفس المشكلة لو الـ container اتعملّه scale لأكتر من replica في Azure، فلازم أحدد replica واحدة طول ما الداتا في الميموري.

---

##  Docs (Swagger)

من غير ما أكتب حاجة، FastAPI بيولد documentation لوحده:

| اللينك                               | الوصف                                     |
| ------------------------------------ | ----------------------------------------- |
| `http://127.0.0.1:8000/docs`         | Swagger UI، أقدر أجرب منه الـ endpoints   |
| `http://127.0.0.1:8000/redoc`        | ReDoc، شكل تاني للـ docs                  |
| `http://127.0.0.1:8000/openapi.json` | الـ schema الخام اللي الاتنين مبنيين عليه |

---

## Path Parameters

### Static Path

لو فتحت `http://127.0.0.1:8000/hello` والكود مفيهوش `/hello` هيطلع `{"detail":"Not Found"}`. عشان أضيف Path أعرّفه:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/hello")
async def hello_world():
    return "This message From Get"
```

### Dynamic Path

الأقواس `{}` معناها إن الجزء ده متغير مش ثابت:

```python
@app.get("/users/{user_id}")
async def get_user(user_id):
    return f"Hello User: {user_id}"
```

لو نسيت الـ `f` قبل الـ String هيرجع `Hello User: {user_id}` حرفياً بالأقواس.

مثال من مشروع ناسا: `@app.get("/astronauts/{astronaut_id}")` عشان أجيب قراءات رائد معين.

### أحدد نوع البيانات

```python
@app.get("/users/{user_id}")
async def get_user(user_id: int):
    return f"Hello User: {user_id}"
```

كده FastAPI بيعمل حاجتين: يحول القيمة لـ `int` فعلاً (من غير الـ type hint هتفضل `str`)، ويرفض أي حاجة مش رقم. لو فتحت `/users/abc` هيرجع `422` من غير ما يشغل الفانكشن:

```
{"detail": [{"type": "int_parsing", "loc": ["path", "user_id"], "msg": "Input should be a valid integer, unable to parse string as an integer", "input": "abc"}]}
```

ودي نقطة مهمة: الـ type hints في FastAPI **بتتنفذ فعلاً**، مش مجرد ملاحظات زي ما اتعلمنا قبل كده.

### الترتيب مهم

الـ Framework بيمشي على الـ Routes بالترتيب، وأول واحد يطابق هو اللي بيشتغل. اتوقع الـ output لو فتحت `/users/1`:

```python
@app.get("/users/{user_id}")
async def get_user(user_id: int):
    return f"Hello User: {user_id}"


@app.get("/users/1")
async def get_admin():
    return "Hello User: Admin"
```

الـ output هيبقى `"Hello User: 1"` لأن الـ route الديناميك فوق بيطابق الأول، والـ route التاني عمره ما هيتنفذ. جربتها وطلعت كده فعلاً.

**الحل:** الـ Static Paths فوق والـ Dynamic تحتهم.

### أخبّي endpoint من الـ docs

```python
@app.get("/users/{user_id}", include_in_schema=False)
async def get_user(user_id: int):
    return f"Hello User: {user_id}"
```

بيختفي من `/docs` بس لسه شغال لو نديت عليه.

### Enum عشان أحدد قيم مسموحة

أعرف نوع بيانات جديد بيورث من `str` ومن `Enum`، وبكده الـ Client مينفعش يبعت غير القيم دي:

```python
from enum import Enum

from fastapi import FastAPI

app = FastAPI()


class UserType(str, Enum):
    admin = "admin"
    manager = "manager"
    user = "user"


@app.get("/{user_type}/{user_id}")
async def get_user_type(user_type: UserType, user_id: int):
    return {"user": {"type": user_type, "id": user_id}}
```

لو الفرونت إند طلب `/admin/45`: FastAPI بياخد `admin` في `user_type` و`45` في `user_id` (كـ `int`). والـ output:

```
{"user": {"type": "admin", "id": 45}}
```

**غلطتين كانوا في نسختي الأولى:**

1. كنت كاتب القيم أرقام (`admin = 1`) رغم إن الـ class بيورث من `str`. اللي حصل إن القيم بقت `'1'` و`'2'` و`'3'` كـ Strings، فالـ URL `/admin/45` **كان بيرجع 422**، والمسموح كان `/1/45`. القيم لازم تكون نصوص بنفس الأسماء: `admin = "admin"`.
2. كنت كاتب `manger` بدل `manager`.

> ⚠️ **فخ:** الـ route ده `/{user_type}/{user_id}` بيطابق **أي** path من جزئين. لو حطيته فوق `/users/{user_id}`، الطلب `/users/5` هيطابقه الأول، وبعد كده الـ Enum هيرفض `users` ويرجع `422`، وهو مش بيكمل للـ route اللي تحته. ده نفس درس الترتيب: الـ routes العامة تتحط **آخر** حاجة.

---

## Query Parameters

دي الطريقة اللي الفرونت إند بيبعت بيها إعدادات إضافية أو فلاتر مع الطلب من غير ما يغير الـ Path الأساسي.

`http://127.0.0.1:8000/alerts?limit=50&sort=desc`

- قبل علامة `?` ده الـ **Path** (`/alerts`)، وهو اللي بيحدد أنهي فانكشن تشتغل.
- بعد `?` ده الـ **Query String**: كل متغير وقيمته بعلامة =، وبين المتغيرات علامة `&`

**القاعدة في FastAPI:** أي parameter في الفانكشن مش موجود في الـ Path بتاع الـ decorator بيتعامل معاه كـ Query Parameter أوتوماتيك.

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/alerts")
async def get_alerts(limit: int = 50, sort: str = "desc"):
    # Simulating the alerts history stored in memory
    all_alerts = [
        {"id": 1, "astronaut": "CDR", "type": "HR_HIGH"},
        {"id": 2, "astronaut": "PLT", "type": "SPO2_DROP"},
    ]

    ordered = sorted(all_alerts, key=lambda a: a["id"], reverse=(sort == "desc"))

    return {"alerts": ordered[:limit], "sorted_by": sort}
```

- `/alerts` من غير حاجة → `limit=50` و`sort="desc"` (الـ defaults).
- `/alerts?limit=1&sort=asc` → أول alert بس بترتيب تصاعدي.
- parameter من غير default (زي `limit: int`) يبقى **إجباري**، ولو مبعتهوش FastAPI بيرجع `422`.
- لو كتبت `q: str | None = None` يبقى اختياري ومالوش قيمة افتراضية.

> الكود الأصلي في ملاحظاتي كان بيرجع `sorted_by: sort` من غير ما يرتب فعلاً. الكود اللي فوق بيرتب بجد.

### أحط حدود للقيم بـ `Query`

ال `limit: int` لوحدها بتقبل أي رقم، حتى السالب. جربت `?limit=-1` وراجع كل الـ alerts **ما عدا الأخير**، لأن `[:-1]` في بايثون معناها كده. ده bug بيعدي من غير error. الحل:

```python
from typing import Annotated

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/alerts")
async def get_alerts(limit: Annotated[int, Query(ge=1, le=500)] = 50, sort: str = "desc"):
    return {"limit": limit, "sort": sort}
```

دلوقتي `?limit=0` بيرجع `422` برسالة `Input should be greater than or equal to 1`.

### Path VS Query ? 

| Path Parameter                            | Query Parameter                        |            |
| ----------------------------------------- | -------------------------------------- | ---------- |
| /astronauts/5                             | /alerts?limit=50                       | مثال       |
| Resource محدد وأساسي (هاتلي الرائد رقم 5) | فلترة / ترتيب / تحديد عدد (Pagination) | بستخدمه لـ |
| الطلب ملوش معنى                           | الطلب لسه ليه معنى بالقيمة الافتراضية  | لو مبعتوش  |



---

## Some Errors

|الكود|معناه|سببه|
|---|---|---|
|`404 Not Found`|مفيش Route بالـ Path ده|Path غلط أو مش متعرّف|
|`405 Method Not Allowed`|الـ Path موجود بس الـ Method مش متسجلة عليه|بعمت `POST` على endpoint متعرّف `GET` بس|
|`422 Unprocessable Entity`|الـ Path صح بس البيانات مش مطابقة للنوع أو القيود|`/users/abc` مع `user_id: int`|

---

## Recap

- الـ Backend برنامج شغال في الخلفية، بيستقبل HTTP Requests ويرد بـ data مهيكلة (JSON).
- الـ Decorator (`@app.get("/path")`) بيسجل الفانكشن في الـ Routing Table وقت الإقلاع **ويرجع نفس الفانكشن**. الـ Routing Table هي list بالترتيب، فالـ **ترتيب الـ routes مهم** والأول المطابق هو اللي بيشتغل.
- `GET` للقراءة، و`POST` لإرسال data جديدة في الـ body.
- الـ Path بيحدد الفانكشن، والـ Path Parameter بيحدد Resource، والـ Query Parameter بيفلتر.
- FastAPI بيستخدم الـ type hints فعلاً للـ validation والتحويل، وبيرجع `422` لو القيمة غلط.
- `async def` مفيدة بس مع `await` على حاجة non-blocking.
- FastAPI هو الـ Framework وUvicorn هو السيرفر. وطول ما الداتا في الميموري أخلي الـ `--workers` على 1 (وحتى الـ replicas في Azure).

## Request Body

الـ **Request Body** هو الحاوية الرئيسية (Payload) اللي الفرونت إند بيبعتها للسيرفر لما يكون عايز ينقل بيانات ضخمة، معقدة، أو سرية.

في عالم C++ و Java، الـ Path والـ Query Parameters يعادلوا تمرير متغيرات بسيطة لدالة (زي `int` أو `string`). أما الـ Request Body فيعادل تمرير `Object` كامل أو `Struct` معقد كـ Reference لدالة (مثلاً: `void ingest_data(const Vitals& data)`).

### ليه بنحتاج الـ Request Body؟ (القصور المعماري في الـ Links)

1. **التعقيد (Complexity):** لو عايز تبعت `List of Dictionaries` (مثلاً مجموعة قراءات حيوية لرواد الفضاء)، مستحيل تكتب ده جوه اللينك. الـ Body بيسمحلك تبعت JSON معقد جداً (شجرة من البيانات).
    
2. **الأمان (Security):** أي حاجة بتتكتب في الـ URL (سواء Path أو Query) بتتسجل في ملفات الـ Logs بتاعة السيرفر أو الراوتر. لو بتبعت باسورد أو داتا سرية في اللينك، أي حد معاه أكسس للـ Logs هيشوفها. الـ Body بيكون مخفي ومحمي بتشفير HTTPS.
    
3. **الحجم (Size):** المتصفحات والسيرفرات بتحط حد أقصى لطول اللينك (غالباً 2048 حرف). الـ Request Body ملوش حد أقصى نظرياً (ممكن ترفع ملف جيجابايت من خلاله).

