# Azure App Services

فاكر لما شرحنا **IaaS مقابل PaaS**؟ لو نشرت الـ FastAPI بتاعك على **VM عادية**، إنت مسؤول عن: تثبيت Python، تحديثات الـ OS، تظبيط Nginx كـ reverse proxy، عمل الـ scaling بنفسك، إدارة الشهادات (SSL)... كل ده **قبل** حتى ما تفكر في الكود نفسه.

ال **Azure App Service** هي **أقصى درجة PaaS للتطبيقات الويب**: بترفع الكود بس، والمنصة بتتكفل بكل حاجة تانية — الـ OS، الـ Runtime، التحديثات، الـ Load Balancing الأساسي، شهادات SSL المجانية.

### App Service Plan 
#### الفكرة الأساسية اللي لازم تترسخ

ال**App Service نفسها مش "بتشتري" هاردوير مستقل**. أي App (تطبيق ويب) لازم يكون **تابع لـ App Service Plan**، والـ Plan هو فعليًا **مجموعة VMs (Workers) بيشتغل عليها تطبيقك**.

**بالتشبيه الهندسي اللي يناسبك**: فكر في الـ Plan زي **process pool** — التطبيقات المختلفة اللي بتحطها جوه نفس الـ Plan **بتشارك نفس الموارد الفيزيقية (نفس الـ VM instances)**، زي إزاي كذا thread بيشارك نفس الـ process.

**لو حطيت أكتر من App جوه نفس الـ Plan، كلهم بيشاركوا نفس الـ VM instances، وبالتالي بيأثروا في بعض.** لو App واحد استهلك كل الـ CPU، الباقي هيتبطأ. لو عايز **عزل كامل** بين تطبيقين، لازم تحطهم في **Plans منفصلة تمامًا**، مش نفس الـ Plan.

#### الـ Tiers

|Tier|استخدام|Scale-out أقصى|Deployment Slots|
|---|---|---|---|
|**Free (F1)**|تجربة وتعلم بس|مفيش scale-out خالص|مفيش|
|**Shared**|مواقع بسيطة جدًا|مفيش scale-out|مفيش|
|**Basic (B1-B3)**|تطبيقات بسيطة، بيئات تطوير|لغاية 3|مفيش|
|**Standard (S1-S3)**|تطبيقات إنتاج تجارية|لغاية 10|**لغاية 5**|
|**Premium (P1v3-P3v3 وأحدث)**|تطبيقات إنتاج عالية الأداء|لغاية 30|**لغاية 20**|
|**Isolated (App Service Environment)**|عزل شبكي كامل، تطبيقات حساسة جدًا|لغاية 100|لغاية 20|

### Scale Up VS Scale Out 
#### Scale Up (Vertical) - تكبير الآلة

بتغيّر الـ **Tier** نفسه لحاجة أقوى (زي من B1 لـ S1). **ده بياخد إعادة تشغيل (restart)** للتطبيق
#### Scale Out (Horizontal) - زيادة عدد الآلات

بتزود **عدد الـ Instances** اللي شغالة (زي من instance واحد لـ 3)، **من غير إعادة تشغيل**، والـ Load Balancing بين الـ Instances دي **بيحصل تلقائيًا من App Service نفسها** — مش محتاج تعمل Load Balancer منفصل

#### Autoscale - الأتمتة الكاملة

بدل ما تراقب وتكبر يدوي، تقدر تظبط **Autoscale Rules** زي: "لو CPU فوق 70% لمدة 10 دقايق، زود Instance واحد"، و"لو تحت 20%، قلل واحد". فاكر مبدأ **Elasticity** اللي شرحناه في AZ-900 (النمو والانكماش التلقائي)؟ ده **التطبيق العملي المباشر** ليه.

### Availability Zones For App Service 

لو الـ Plan بتاعك **Zone-redundant**، أزور بيوزع الـ Instances بتاعتك **تلقائيًا على 3 Availability Zones على الأقل** (فاكر الحد الأدنى اللي شرحناه في AZ-900؟). **الحد الأدنى المطلوب: 3 instances** عشان توزيعهم على الزونز الثلاثة بالتساوي.

### Deployment Slots - أهم ميزة عملية في الموضوع كله

#### المشكلة الأساسية

تخيل عندك تطبيق شغال Production، وعايز تنشر **نسخة جديدة**. لو نشرتها مباشرة على الـ Production، ولو فيها باج، **كل المستخدمين هيتأثروا فورًا**.

#### الحل: Deployment Slots

بتعمل **Slot إضافي** (زي `staging`) — نسخة **منفصلة تمامًا** من التطبيق، بنفس الـ App Service Plan، بس بعنوان مختلف (`myapp-staging.azurewebsites.net`). بترفع الكود الجديد على الـ `staging` **الأول**، تختبره بأمان، وبعد ما تتأكد إنه شغال كويس، تعمل **Swap**.

#### Swap 

لما تعمل **Swap بين staging وproduction**، Azure **مش بينقل الملفات فعليًا** — هو بيبدّل **الـ Routing** بس (مين اللي بيستقبل الترافيك تحت اسم Production). العملية دي **آنية تقريبًا (near-instant)**، **من غير Downtime خالص**، لأن الـ instances التانية **كانت شغالة بالفعل** قبل الـ Swap.

**بالتشبيه الهندسي اللي يناسبك**: فكرها زي **Blue-Green Deployment** الكلاسيكي في هندسة البرمجيات — عندك نسختين شغالتين، وبتبدّل مين "الحية" فورًا، مش بتوقف وتشغل من جديد.

#### نقطة تقنية دقيقة: مفيش تكلفة إضافية للـ Slots نفسها

ال**Deployment Slots مجانية تمامًا** كميزة — التكلفة الوحيدة هي إنها **بتستهلك من نفس موارد الـ Plan** اللي إنت أصلاً بتدفع فيها (لأنها زي ما قلنا فاكرة نفس الـ VM instances). 


### Deployment Methods

#### 1. Local Git

ال App Service بتديك **Git remote خاص بيها**. تعمل `git push azure main`، والكود بيتنشر تلقائيًا. بسيطة، لكن مناسبة أكتر للتجربة، مش لفريق شغال مع بعض.

#### 2. GitHub Actions / Azure DevOps (CI/CD) 

بتربط الـ App Service مباشرة بريبو GitHub بتاعك. أي `push` على branch معينة (زي `main`) **بيشغل pipeline تلقائي** يبني الكود وينشره. ده الأقرب لعمل فريق حقيقي، وده هيبقى الطريقة اللي هتستخدمها فعليًا لما تشتغل بمشروع حقيقي.

#### 3. ZIP Deploy

بترفع ملف مضغوط فيه الكود كله مباشرة (عن طريق CLI أو الـ Portal). سريعة لسكريبتات الأتمتة، لكن مفيهاش تاريخ نسخ (version history) زي Git.

#### 4. Container Deployment

بدل ما ترفع كود خام، بترفع **Docker image** كامل (من Docker Hub أو Azure Container Registry). **ده الأنسب ليك تحديدًا** لو الـ FastAPI بتاعك أصلاً معلّب في Docker — بترفع الـ image، وApp Service بتشغله زي ما هو من غير ما تحتاج تظبط Runtime يدوي.

### App Settings VS Connection Strings

#### App Settings

دول **Environment Variables** بتتحقن (injected) وقت الـ Runtime. لو عندك في FastAPI حاجة زي:

```python
API_KEY = os.environ.get("API_KEY")
```

بتحطها في **Configuration → Application settings**، ومش محتاج تكتبها في الكود أو في ملف `.env` مرفوع على الريبو

#### Connection Strings

شكل خاص من الإعدادات، مخصص **لقواعد البيانات تحديدًا** (زي connection string لـ PostgreSQL). بتتخزن في تصنيف منفصل، وبعض الـ Frameworks (زي .NET) بتقراها تلقائيًا بطريقة خاصة.

### Sticky Settings

فاكر شرحنا **Swap** في الجزء الأول؟ السؤال المنطقي: **لما تعمل Swap بين staging وproduction، الإعدادات (App Settings) بتتبدل معاها ولا لأ؟**

**الإجابة: بتتوقف على تفعيلك لخاصية "Deployment slot setting" (الاسم الشائع ليها: Sticky Setting).**

#### Default mode

أي App Setting أو Connection String، **بتتبدل مع الـ Swap** — يعني لو `staging` كان عنده `DATABASE_URL` تاني عن `production`، بعد الـ Swap **الإعداد هيتحرك مع الكود**.

#### Deployment slot setting enabled 

الإعداد **بيفضل ملتصق بالـ Slot نفسه، مش بيتحرك مع الـ Swap خالص**.

#### Example

تخيل عندك `DATABASE_URL`:

- في `production`: بتشاور على قاعدة البيانات الحقيقية
- في `staging`: بتشاور على قاعدة بيانات تجريبية

**لو الإعداد ده مش Sticky**، بعد أول Swap، **الـ `staging` القديم (اللي بقى production دلوقتي) هيفضل شاير على الداتابيز التجريبية**، مش الحقيقية — **كارثة إنتاجية محتملة**.

**الحل**: تعمل الإعداد ده **Sticky على الـ Slot، مش على القيمة**، بحيث كل Slot يفضل شاير على القاعدة المناسبة له دايمًا، بغض النظر عن أنهي كود شغال فيه.

#### إعدادات تانية "لاصقة" تلقائيًا (مش محتاجة تفعيل يدوي)

- **SSL Certificates وCustom Domains**: دايمًا خاصة بالـ Slot، مش بتتبدل خالص
- **Scale settings**: خاصة بالـ Slot
- **Publishing endpoints**: خاصة بالـ Slot

#### إعدادات بتتبدل تلقائيًا (Swappable)

- **Language framework version** (زي نسخة Python)
- **App Settings/Connection Strings العادية** (لو مش عملتلهم Sticky)

### Custom Domains + SSL

فاكر Public DNS اللي شرحناه؟ **ده التطبيق المباشر ليه**:

1. تضيف **CNAME record** في Public DNS Zone بتاعتك، يشاور على `yourapp.azurewebsites.net`
2. تروح لـ App Service → **Custom domains** → تضيف الدومين، وأزور بيتحقق من الـ DNS record
3. تفعّل **App Service Managed Certificate** — **شهادة SSL مجانية بالكامل**، Azure بيديرها ويجددها تلقائيًا، من غير ما تدفع أو تعمل حاجة يدوي

### VNet Integration VS Private Endpoint 

دي نقطة هتلخبطك لو محدش وضحهالك، لأن الاتنين "شبكة" لكن **في اتجاه معاكس تمامًا**:


|           | Private Endpoint                                                  | VNet Integration                                                                                    |
| --------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Direction | **مرور داخل (Inbound)** — حد من برة يوصل للـ App Service بأمان    | **مرور خارج (Outbound)** — الـ App Service توصل لموارد جوه VNet بأمان                               |
| Ex        | تقفل الوصول العام للـ App Service، وبس اللي جوه VNet معينة يوصلها | الـ FastAPI بتاعتك عايزة توصل لـ PostgreSQL على Private Endpoint، من غير ما تعدي على الإنترنت العام |

**بمعنى تاني**: Private Endpoint = "مين يقدر **يدخلي**". VNet Integration = "أنا **أخرج** لمين بأمان". غالبًا بتستخدمهم **مع بعض** في تطبيق واحد.

### Always On 

ال App Service الافتراضي بيقفل التطبيق (Idle) لو معملوش حد request لفترة، وأول request جاي بعد كده بياخد وقت أطول (Cold start). **Always On** بتخلي التطبيق **شغال باستمرار**، مفيدة جدًا لو عندك Background jobs أو API لازم يرد فورًا كل مرة.

**قيد**: متاحة بس من **Basic tier فما فوق**، مش في Free

# VMs, Containers and ACI

## VM VS Container
ده أشهر سوء فهم، ولازم نصححه من الأساس. فاكر لما شرحنا الـ Hypervisor بالتفصيل [[General Cloud#General Cloud and Pre-Azure|Review From Here]]
، وقلنا إن كل **VM** معاها **نسخة كاملة من نظام تشغيل مستقل** (Kernel خاص بيها، بتتوهم إنها شغالة على هاردوير كامل)؟

ال**Container مختلفة جوهريًا**: الـ Containers **كلها بتشارك نفس الـ Kernel بتاع نظام التشغيل المضيف (Host OS)**. الـ "عزل" اللي بتحس بيه (إن كل container عندها ملفاتها ومكتباتها الخاصة) بيتحقق عن طريق ميزات جوه الـ Linux Kernel نفسه (زي **Namespaces** و**Cgroups**)، مش عن طريق Hypervisor بيحاكي هاردوير كامل.
```
VM Stack:                          Container Stack:
Hardware                           Hardware
  → Hypervisor                       → Host OS (Kernel واحد مشترك)
    → Guest OS كامل (لكل VM)           → Container Runtime (Docker)
      → Libraries/Binaries               → Container 1 (Libraries بس)
        → App                            → Container 2 (Libraries بس)
                                          → Container 3 (Libraries بس)
```
شرحنا Hypervisor بمنطق الـ **polymorphism** (كل Guest OS فاكر إنه Kernel مستقل، لكن الـ Hypervisor بيترجم كل حاجة)؟ الـ Container **مالهاش الطبقة دي خالص** — بدل ما "توهم" الـ App إنه عنده Kernel خاص بيه، الـ App **فعليًا بيستخدم نفس الـ Kernel المشترك**، بس معزول عن باقي الـ Containers بحدود منطقية (Namespaces).

## Azure Container Instances (ACI)

#### الفكرة الأساسية

ال**ACI هي PaaS للـ Containers** — بتاخد الـ Docker image بتاعتك، وأزور بيشغلها **مباشرة**، من غير ما تحتاج تعمل VM، تنصب Docker عليها، تديرها. **"Serverless containers"** — إنت مش شايف أي سيرفر خالص، بس بتحدد الـ image وحجم الموارد.

#### Container Groups - المفهوم المحوري

الوحدة الأساسية في ACI اسمها **Container Group** — مجموعة containers **بتتشارك نفس دورة الحياة (lifecycle)، نفس الشبكة المحلية، ونفس الـ storage volumes**. لو سمعت عن **Kubernetes Pods**، المفهوم **مطابق تمامًا** — نفس الفكرة بالظبط.

**مثال عملي**: تخيل عايز تشغل الـ FastAPI بتاعك **مع** container تاني بيعمل logging جنبه — الاتنين بيتحطوا في نفس Container Group، بيشاركوا نفس الـ IP، وبيقدروا يتكلموا مع بعض عن طريق `localhost`.

#### التسعير 

ال ACI بتتحاسب **بالثانية**، حسب عدد الـ vCPUs والـ RAM اللي حددتها. **مفيش أي تكلفة "خلفية" للـ VM نفسها** — أنت بس بتدفع مدة تشغيل الـ container الفعلية. ده بيخليها أرخص بكتير من إنك تعمل VM كاملة وتنصب Docker عليها بنفسك.

#### إمتى تستخدم ACI (القاعدة العملية للامتحان)

- **مهام قصيرة العمر (Short-lived tasks)**: batch processing، CI/CD pipeline steps
- **تجارب سريعة**: عايز تشغل container وتشوف نتيجته فورًا، من غير Infrastructure setup
- **مهامّ غير مستمرة**: مش تطبيق ويب شغال 24/7

**مش مناسبة لـ**: تطبيقات ويب إنتاجية مستمرة (App Service أفضل)، أو أنظمة معقدة محتاجة orchestration بين عشرات الـ Containers (AKS أفضل).