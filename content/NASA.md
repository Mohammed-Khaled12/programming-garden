# Astronaut Health Monitoring System — Full Architecture

### NASA Space Apps Challenge 2026 — Team of 6

---

## 1. End-to-End Data Flow

```
┌─────────────────────┐
│  Vitals Data Source  │  PhysioNet dataset (MIT-BIH / vitals) — replay script
│  (replay script)      │  يبعت reading كل ثانية/نص ثانية
└──────────┬───────────┘
           │ POST /ingest  (JSON: heart_rate, spo2, body_temp, resp_rate, ts)
           ▼
┌─────────────────────┐
│   Backend API        │  FastAPI
│   (Docker container) │  - Pydantic validation
│                       │  - يستقبل، يخزن، يبعت للـ AI Engine، يرجع النتيجة
└──────────┬───────────┘
           │
           ├──► [AI Engine: Isolation Forest (.pkl model, loaded once)] ──► Anomaly Score
           │
           ├──► [Azure Blob Storage] ──► تخزين كل reading + النتيجة (log دائم)
           │
           ├──► [TUI Client (Textual)] ◄── GET /metrics (polling كل 1-2 ثانية)
           │
           └──► IF anomaly detected:
                    │
                    ▼
              [Azure Logic App] ──► Email/SMS alert ("Flight Surgeon Alert")
```

---

## 2. مكونات Azure المطلوبة بالتفصيل

| المكوّن                            | الاستخدام                                             | ملاحظات                                                   |
| ---------------------------------- | ----------------------------------------------------- | --------------------------------------------------------- |
| **Azure Container Registry (ACR)** | تخزين الـ Docker image بتاع الـ Backend بعد الـ build | لازم يتعمل قبل Container Apps — الصورة بتتدفع هنا الأول   |
| **Azure Container Apps**           | تشغيل الـ FastAPI container فعليًا (Consumption plan) | `minReplicas: 0` طول الوقت، `minReplicas: 1` وقت العرض بس |
| **Azure Blob Storage**             | تخزين الـ logs وتاريخ القراءات والـ anomalies         | Container واحد اسمه مثلاً `vitals-logs`                   |
| **Azure Logic App**                | إرسال إشعار لما anomaly تتكشف                         | Trigger: HTTP request من الـ Backend → Action: Email/SMS  |


---

## 3. Git / GitHub — التنظيم الكامل

### هيكل الريبو (Monorepo واحد لسهولة الإدارة بفريق 6 أفراد):

```
sasm-space-apps/
├── .github/
│   └── workflows/
│       ├── ci.yml          # build + test عند كل push
│       └── deploy.yml      # build + push لـ ACR + deploy لـ Container Apps
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── models.py       # Pydantic schemas
│   │   └── ai_engine.py    # تحميل الموديل + inference
│   ├── Dockerfile
│   └── requirements.txt
├── ai_model/
│   ├── train.py            # تدريب Isolation Forest offline
│   └── model.pkl           # الموديل المدرّب (أو يتحمّل من Blob)
├── data_source/
│   └── replay.py           # سكريبت الـ replay من PhysioNet
├── tui/
│   ├── app.py               # Textual client
│   └── pyproject.toml       # لو هتنشروه بعدين على pip
├── automation/
│   └── logic_app_config.json
└── README.md
```

### الـ Branching Strategy:

- ال `main` — دايمًا شغال ومستقر، مش بيتعمله push مباشر.
- كل فرد بيشتغل على branch باسمه/مهمته: `feature/backend-api`, `feature/ai-model`, `feature/tui`, `feature/data-replay`, `feature/logic-app`.
- ال Pull Request لكل تغيير → مراجعة من محمد (Tech Lead) → merge لـ `main`.

### GitHub Actions (CI/CD):

1. ال**ci.yml**: عند كل push/PR → تشغيل linting بسيط + تأكد إن الـ imports شغالة (اختياري: pytest لو فيه وقت).
2. ال**deploy.yml**: عند push على `main` فقط →
   - `docker build` لصورة الـ backend
   - `docker push` لـ Azure Container Registry
   - أمر تحديث لـ Azure Container Apps بالصورة الجديدة (`az containerapp update`)
   - الـ secrets (Azure credentials) بتتخزن في **GitHub Secrets**، مش في الكود أبدًا.

---

## 4. من هيعمل إيه — بالتفصيل الكامل

### Mohammed Khaled (Tech Lead)

- كتابة الـ Dockerfile وبناء الصورة.
- إعداد Azure Container Registry + Container Apps من الصفر.
- كتابة `deploy.yml` (GitHub Actions) للـ CI/CD.
- مراجعة كل الـ Pull Requests قبل الـ merge.
- إدارة الـ GitHub Projects board وتوزيع الـ Issues.

### Mohammed Karim (Backend)

- بناء `models.py` (Pydantic schemas للـ vitals وللـ response).
- بناء endpoints: `POST /ingest`, `GET /metrics`, `GET /history`.
- تكامل Azure Blob Storage SDK لحفظ كل reading في log دائم.

### Youssef Hamada (AI/ML Engineer)

- تحميل dataset من PhysioNet (MIT-BIH أو مشابه).
- تدريب Isolation Forest offline (`train.py`) وحفظ الموديل (`model.pkl`).
- بناء `ai_engine.py` اللي بيحمّل الموديل ويعمل `predict()` على كل reading جديدة.
- قياس Precision/Recall باستخدام الـ labels الحقيقية الموجودة في الـ dataset.

### Youssef Emad (Data Engineer)

- كتابة `replay.py`: تحميل الـ dataset، قراءته بـ pandas/wfdb، وبعت كل صف كـ HTTP POST بفاصل زمني ثابت لمحاكاة stream حي.
- التأكد إن شكل الـ JSON المُرسَل متطابق مع الـ Pydantic schema المتفق عليه مع الـ Backend Assistant.

### Ahmed Ehab & Mina Amgad (TUI Developers)

- بناء واجهة Textual بتعمل `GET /metrics` كل 1-2 ثانية.
- شاشة رئيسية لعرض القراءات الحية + شاشة منفصلة لسجل الـ anomalies.
- ال Fallback: لو الـ API مش راد، تعرض آخر بيانات معروفة (cached) بدل ما تفضل فاضية.
- إعداد Azure Logic App: HTTP trigger من الـ Backend → Email/SMS action.
- تصوير فيديو الـ 30 ثانية وتجهيز الـ pitch deck.

---

## 5. أدوات التطوير المحلي (قبل الرفع)

- ال **Docker Compose**: لتشغيل الـ Backend محليًا قبل أي push، عشان محدش يكتشف مشكلة أول مرة على Azure.
- ال **.env file** (مش بيتعمله commit، مضاف لـ `.gitignore`): للمتغيرات الحساسة زي Azure connection strings وقت التطوير المحلي.
- ال **GitHub Secrets**: لنفس المتغيرات دي وقت الـ CI/CD، بدل الـ `.env`.

---

## 6. Showcase Page 

صفحة **static واحدة** مستضافة على **GitHub Pages** (مجانية، منفصلة تمامًا عن Azure ومن غير Docker):

```
sasm-space-apps/
└── docs/                      # GitHub Pages بيقرأ من هنا تلقائي
    ├── index.html
    ├── style.css
    └── assets/
        └── demo.cast           # ملف تسجيل asciinema (مش فيديو)
```

**محتوى الصفحة (3 عناصر بس):**

1. ال **Asciinema player** — عبر `<script src="https://cdn.jsdelivr.net/npm/asciinema-player.../asciinema-player.min.js">` وعنصر `<asciinema-player src="assets/demo.cast">` بيشغّل تسجيل حقيقي لجلسة `sasm` شغالة فعليًا.
2. **الأمرين + زرار نسخ**: `pip install sasm-monitor` ثم `sasm`.
3. وصف سطرين للمشروع + رابط الريبو الرئيسي.

**تفعيل GitHub Pages**: من إعدادات الريبو → Pages → Source: `main` branch، folder `/docs`. خطوة واحدة، مفيش build process معقد.

**تسجيل الـ demo.cast**: عن طريق أداة `asciinema rec demo.cast` وانت شغّال `sasm` فعليًا متصل بالـ API الحي على Azure — التسجيل ده بيتعمل في آخر مرحلة بعد ما كل حاجة تشتغل end-to-end، مش قبل كده.

**مهم:** الصفحة دي لا تستضيف التطبيق ولا تشغّله — هي بس عرض. التطبيق نفسه (`sasm`) لازم يتثبت ويشتغل من جهاز أي حد بيجربه.

---
