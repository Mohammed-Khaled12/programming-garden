# General Cloud and Pre-Azure

![[Cloud Essentials.pdf]]


# AZ Regions,Zones and Sets

![[Pasted image 20260901131114.png]]

## Data Center
مبني واحد فيه سيرفرات و تبريد

## Availability Zone
مجموعه Data Centers متوصلين ببعض بكابلات بس كل مبنى (data center) جوه الـ AZ ده، لازم يكون ليه **مصدر كهرباء منفصل، ومصدر تبريد منفصل**، حتى لو المباني قريبة من بعض جغرافيًا.
الهدف: لو حصل عطل كهربائي في مبنى واحد بس، المبنى التاني جنبه يفضل شغال.

## Region

لو حصل كارثه طبيعيه مثلا و ال AZ راحت ؟
الريجون هو AZ واحد او اكتر في منطقه بعيده شويه (مسافة تكفي إنها متتأثرش بنفس الكارثة، بس قريبة كفاية إن الاتصال بينهم سريع)

- **Data Center** = مبنى واحد
- **Availability Zone** = كذا مبنى قريبين من بعض، كل واحد له كهرباء وتبريد مستقل
- **Region** = كذا Availability Zone بعيدين عن بعض جغرافيًا، لكن متصلين بشبكة سريعة

![[Pasted image 20260901132738.png]]

Region Pair:
كل ريجون هتلاقيلها نسخه احتياطيه منها في ريجون تانيه علي الكوارث الطبيعيه الضخمه
مايكروسوفت مش بتحدث الريجنين مع بعض في نفس الوقت. بتحدث واحدة، تستنى تتأكد إنها مستقرة، بعدين تحدث التانية. كده لو التحديث نفسه سبب مشكلة، مش الريجنين هيتأثروا مع بعض.
**ريجن بير مش تلقائية زي أفيلابيليتي زون**. يعني لما بتعمل موارد في أزور، هي مش بتتوزع تلقائيًا على الريجن التانية في الزوج. **إنت اللي لازم تعمل الإعداد ده بنفسك** (زي إنك تفعل خاصية زي Geo-Redundant Storage، أو تعمل Disaster Recovery setup يدوي بين الريجنين).
![[Pasted image 20260901142321.png]]


## Availability Set
بتوزع ال Vms بتاعتك في داتا سنتر واحد بس علي اجزاء مختلفه من الهاردوير يعني مش كلهم في راك سيرفر واحد 
بيحمي ال Vms علشان لو راك باظ Fault Domain او حتي لو هيحصل ابديت و صيانه فيه

![[Pasted image 20260901143103.png]]


![[Pasted image 20260901140510.png]]
# Azure Resource Groups

لما انت بتيجي تشغل تطبيق مثلا علي Azure بتحتاج Resources كتيره مثلا:
- Virtual Machine بيشغل الكود
- Storage Account بيخزن الملفات
- Database بتخزن البيانات
- Virtual Network بيربط كل حاجة ببعض
- Public IP Address عشان توصله من بره

كل واحد من دول هو **Resource منفصل تمامًا** في نظر Azure، ليه اسمه وإعداداته الخاصة

احنا بقي لو عندنا اب مثلا و حبينا نمسح او نعدل بيرميجنز كل الريسورسيز بتاعته هنقعد ندور علي كل واحد؟
لا احنا بنحطهم كلهم في جروب او صندوق واحد و نديرهم ككتله واحده 

من اكبر المزايا Cost Tracking
ازور بيديك تقرير تكلفة **لكل Resource Group لوحدها**. يعني لو عندك مشروعين مختلفين في نفس الأكونت، تقدر تعرف بالظبط كل مشروع كلفك قد إيه، من غير ما تتلخبط.

***نوت***
**كل مورد لازم يكون تابع لـ Resource Group واحدة بس، مش أكتر**. مفيش مورد ممكن يكون موجود في مجموعتين في نفس الوقت.
لكن **الموارد المختلفة جوه نفس الـ Resource Group ممكن تكون في Regions مختلفة تمامًا**. يعني مثلاً VM بتاعتك في "West Europe" وStorage Account بتاعتك في "East US"، والاتنين ممكن يكونوا تابعين لنفس الـ Resource Group من غير أي مشكلة


# Management Groups & Subscriptions

```
Management Group (المستوى الأعلى)
    ↓
Subscription
    ↓
Resource Group
    ↓
Resources (VMs, Storage, إلخ)

```

ال Resources Group احنا كنا بندير الموارد دلوقتي هنطلع لمستوي اعلي 

## Subscription

**هو مين اللي بيدفع الفاتوره و مين اللي عنده الحد الاقصي للموارد؟**
هنا بيجي دور الـ **Subscription**. فكرها كـ **عقد فوترة (billing boundary) وحد إداري (management boundary)** في نفس الوقت.

 الوظيفتين الأساسيتين للـ Subscription

**1. حدود الفوترة (Billing Boundary)**  
كل الموارد اللي جوه Subscription معينة، تكلفتها بتتجمع في **فاتورة واحدة**. لو عندك أكتر من Subscription، كل واحدة فيهم بتاخد فاتورة منفصلة.

**2. حدود التوسع (Scale Boundary)**  
كل Subscription عندها **حدود قصوى (limits/quotas)** لعدد الموارد اللي تقدر تنشئها فيها — مثلاً عدد معين من الـ VMs أو Virtual Networks. لو شركة كبيرة محتاجة موارد أكتر من الحد المسموح في Subscription واحدة، بتعمل **أكتر من Subscription** وتوزع الموارد عليهم.

#### مثال يوضح ليه شركة بتحتاج أكتر من Subscription واحدة

تخيل شركة كبيرة عندها:

- **Subscription: "Production"** — كل الموارد اللي شغالة فعليًا للعملاء الحقيقيين
- **Subscription: "Development"** — بيئة المطورين للتجربة
- **Subscription: "Finance-Department"** — موارد قسم المحاسبة

الفصل ده مش بس تنظيمي — هو **حماية فعلية**. لو مطور غلط في بيئة الـ Development وعمل حاجة خربت موارد كتير، **الـ Production متتأثرش خالص**، لأنهم في Subscriptions منفصلة تمامًا بحدود صارمة بينهم.

## Management Group

طيب لو الشركة الكبيرة دي عندها **عشرات الـ Subscriptions** (شركة عالمية كبيرة، كل قسم أو كل دولة ليها Subscriptions خاصة بيها)... إزاي تدير سياسة أمان واحدة أو صلاحيات موحدة على **كل** الـ Subscriptions دي مرة واحدة، من غير ما تروح تظبط كل واحدة لوحدها؟

ال Management Group هي كونتينر جواه Subscriptions

 أهم ميزة: الوراثة (Inheritance)

لو طبقت **سياسة (Policy)** أو **صلاحية وصول (RBAC role)** على مستوى Management Group، بتتورث تلقائيًا لكل حاجة تحتها: كل الـ Subscriptions اللي جواها، وكل الـ Resource Groups اللي جوه الـ Subscriptions دي، وكل الموارد اللي جوه الـ Resource Groups دي.

الحد الأقصى للـ **العمق (depth)** المسموح بيه في هرم Management Groups هو **6 مستويات** (مش عدد لا نهائي)، وفيه **Root Management Group واحدة بس** لكل Azure AD Tenant (الحساب المؤسسي الأساسي)، وكل حاجة تانية بتندرج تحتها.

![[Pasted image 20260901184222.png]]
# Azure Resource Manager

احنا عندنا طرق كتيره نقدر نتعامل بيها علي Azure 
Azure Portal 
Azure CLI
PowerShell
REST API 
SDKs 
ازاي Azure بيضمن ان من اي طريقه تعامل من  دول هيتم تطبيق نفس ال Policies و الصلاحيات؟
ال ARM هو اللي بيضمن ده , هو لاير بتعدي عليه الطلبات قبل ما توصل للموارد 
مفيش أداة (Portal, CLI, PowerShell) بتتكلم مباشرة مع الموارد. **كل الأدوات دي بترسل طلباتها كـ REST API calls لـ ARM**، وARM هو اللي بيتحقق من الطلب، يتاكد من الصلاحيات والسياسات، وبعدين يبعتها لل Resource Provider

```
(Portal/CLI/PowerShell)
    ↓ (REST API call)
Azure Resource Manager (ARM) FOR validation and routing
    ↓ (بعد التحقق من الصلاحيات والسياسات)
Resource Provider المناسب (زي Microsoft.Compute)
    ↓
تنفيذ العملية فعليًا على المورد
```
كل الأدوات دي مجرد "واجهات مختلفة" بتترجم اللي إنت طالبه لطلب REST API موحد، وARM هو نقطة الدخول الوحيدة الحقيقية.

## ايه هو ال Resource Provider?

كل نوع مورد في أزور (VMs, Storage, Networking, Databases) عنده **Resource Provider مخصص ليه**، وهو المسؤول الفعلي عن إنشاء وتعديل وحذف الموارد من النوع ده. أمثلة:
- `Microsoft.Compute` → مسؤول عن VMs
- `Microsoft.Storage` → مسؤول عن Storage Accounts
- `Microsoft.Network` → مسؤول عن Virtual Networks, NSGs, Public IPs
- `Microsoft.Sql` → مسؤول عن قواعد بيانات SQL

## هو ARM بيعمل ايه بالضبط؟

1) التحقق من الصلاحيات (Authentication & Authorization)
	قبل ما ينفذ أي حاجة، ARM بيتأكد إن الشخص أو النظام اللي بعت الطلب **معاه الصلاحية الكافية** (بناءً على RBAC roles المطبقة، واللي بتتورث من Management Groups )
	
2) تطبيق السياسات (Policy Enforcement)
	لو فيه Policy مطبقة (زي "الموارد لازم تكون في منطقة معينة بس")، ARM هو اللي بيتحقق إن الطلب الجديد متماشي مع السياسة دي **قبل** ما ينفذه، مش بعده.
	
3) تنظيم العمليات على شكل مجموعات (Consistent Deployment)
	بيسمحلك تعمل عمليات على **مجموعة موارد مع بعض في نفس اللحظة**، بدل ما تعملهم واحد واحد يدويًا. 
	 
4) إدارة الحالة (State Management)
	 بيحتفظ بمعرفة الحالة الحالية (current state) لكل الموارد بتاعتك

![[Pasted image 20260901215419.png]]

## ARM Templates

Infrastructure as a code (IaC)
ملف json بتكتب فيه وصف كامل لل Infrastructure اللي عايزها و هو بيعملهالك كذا مره اوتوماتيك حسب ما تحب 
نفس الحوار بس لغه ابسط من مايكرو اسمها Bicep

# Azure VM

الـ Hypervisor بتاع Microsoft اسمه **Microsoft Azure Hypervisor**، نسخة معدّلة من Hyper-V
لما بتعمل VM بتحدد 4 حاجات 

1) الصورة (Image):
	 نسخه ال OS اللي هتشتغل بيها (Linux Ubuntu , redhat ... windows server ) كمان ممكن تستخدم **Marketplace Images** جاهزة (زي VM فيها WordPress متثبت مسبقًا)، أو تعمل **Custom Image** بنفسك من إعداد معين عملته وعايز تكرره
	 
2) الحجم (Size)
	 أزور عندها أكتر من 50 سلسلة (series) مختلفة، لكن **4 سلاسل بس بتغطي 90% من الاستخدامات العادية**كل سلسلة اتصممت لنوع Workload معين
	 

| **Series** | **usage**          | **when to use?**                                  |
| ---------- | ------------------ | ------------------------------------------------- |
| B-series   | Burstable اقتصاديه | Light Workloads, personal projects                |
| D-series   | General Purpose    | Normal Workloads (Web apps,APIs)                  |
| E-series   | Memory-optimized   | Data Bases, high Ram usage apps                   |
| F-series   | Compute-optimized  | High CPU Usage (batch processing, gaming servers) |
| N-series   | GPU-enabled        | Training AI models                                |

![[Pasted image 20260902110948.png]]


![[Pasted image 20260902112530.png]]


# Virtual Networking, Subnets, VPN, Express Route and  Peering

## Networks Review
فاكر النيتورك؟ بالتحديد ال IP بالتحديد IPv4 
أي جهازين عايزين يتواصلوا مع بعض (سواء Physical أو Virtual) محتاجين **عنوان فريد** يعرفهم من بعض، زي رقم التليفون بالظبط. العنوان ده اسمه **IP Address**.
ال IPv4 بيتكون من **4 أرقام مفصولة بنقط**، كل رقم من 0 لـ 255. مثال: `192.168.1.10`
كل رقم من الأربعة دول فعليًا ممثل بـ **8 بت (Byte)**، يعني العنوان كله **32 بت**

و كان ال IPv4 بيتقسم حاجتين:
Network Portion --> بيعرفك احنا في انهي نيتورك  
Host Portion --> بيعرفك انت انهي جهاز في النيتورك 

و كان عندنا ال Subnet mask بيقول ال IP ده معانا في نفس النيتورك ولا لا
و كان عندنا ال CIDR Notation و ده بيقولك في كام بت للنيتورك بورشن
`10.0.0.0/8` معناها:
- أول **8 بت** (يعني أول رقم: `10.`) **ثابت** — دول بيعرّفوا "الشبكة" نفسها
- آخر **24 بت** (يعني آخر 3 ارقام) **متاحين للتوزيع** على أي جهاز جوه الشبكة دي بمجموع 2 اس 24 عنوان مميز

## VNet
نيتورك بتعملها جوه Azure بتحط جواه الريسورسيز علشان يكلموا بعض
بتبقي معزوله عن اي نيتورك تانيه ليك او لاي عميل في الداتا سنتر و حتي معزوله عن الانترنت الا لو انت سمحت 
نوت --> خلي بالك Azure مبتدعمش ال OverLap يعني لو اديت ل VNet رينج Ip معين و اديت نفس الرينج ل Vnet تانيه او On prem هيبقي كله تمام لكن مش هتعرف تكونكت الاتنين مع بعض 
لما بتعمل VNet، أول حاجة بتحددها هي **Address Space** بتاعها بصيغة CIDR، مثلاً: `10.0.0.0/16`. ده معناه إن كل الموارد اللي هتحطها جوه الشبكة دي هتاخد عناوين من الرينج ده.

## Subnet

لو حطيت كل الرسيورسيز بتاعتك في نفس المساحه الكبيره بتاعت ال Address Space كل حاجه هتقدر توصل للتانيه بسهوله و مش هيبقي فيه تنظيم 
ال Subnet --> هو تقسيم ال VNet لحتت اصغر و كل حته يبقيليها غرض معين جوه ال VNet 

![[Pasted image 20260902165003.png]]

## VNet Peering
طيب احنا دلوقتي عملنا نيتورك ازاي بقي اوصلها من بره Azure ؟ 
ب 3 طرق ---> **Peering**,VPN,ExpressRoute
تعالي نشوف ال Peering

ال Peering بيخليني اقدر اربط 2 VNet مع بعض بحيث ال Resources في الاتنين VNet يقدروا يكلموا بعض كانهم في شبكه واحده

الترافيك بين الاتنين VNet مش هيطلع علي الانترنت بيمشي في الانفراستراكتشر الداخليه بتاعت مايكروسوفت (Microsoft backbone network) و ده بيخلي اللايتينسي قليله جدا و مستوي امان كويس لانك مش هتطلع علي العامه 
*Notes:*
- الـ **Address Spaces** بتاعة الـ VNets لازم تكون **مختلفة ومش متداخلة (non-overlapping)** — يعني لو VNet الأولى `10.0.0.0/16` والتانية `10.0.0.0/16` نفسها، الـ Peering مش هيشتغل، لازم تكون مثلاً `10.1.0.0/16`
- الـ Peering **مش transitive** — يعني لو VNet A متوصلة بـ Peering مع VNet B، وVNet B متوصلة بـ Peering مع VNet C، ده **معناهوش** إن A تقدر توصل لـ C تلقائيًا. لازم تعمل Peering منفصل بين A وC صراحة لو محتاج الاتصال ده

## VPN Gateway

عباره عن Site-to-Site VPN بيوصل ال on-Prem بال VNet عن طريق بروتوكول اسمه IPsec بيعدي في الانترنت بس البيانات بتبقي مشفره Encrypted 
بنربط شبكه الشركه ب ال VNet 
#### إمتى تستخدمها

- ميزانيتك محدودة (VPN Gateway أرخص بكتير من ExpressRoute)
- الاتصال مش محتاج يكون **مضمون الأداء (guaranteed bandwidth)** بشكل صارم — ممكن يتأثر بازدحام الإنترنت العام
- سيناريوهات الـ backup أو الـ DR اللي مش critical للحظة بلحظة

#### نوع تاني مهم: Point-to-Site VPN

ده مختلف عن Site-to-Site — هنا **جهاز واحد بس (زي لابتوبك إنت شخصيًا)** بيعمل اتصال VPN مباشر بالـ VNet، من غير ما تحتاج جهاز VPN فيزيكال في مكتب كامل. مفيد جدًا للموظفين اللي شغالين من البيت وعايزين يوصلوا لموارد الشركة الداخلية في أزور.

## ExpressRoute

المشكله في ال VPN ان اداءه مش مضمون علي طول معتمد علي الزحمه بتاعت الانترنت 
و برده مش اامن حاجه

ال ExpressRoute بيوفرلك اتصال خاص private connection بين الشبكه بتاعتك و الداتا سنترز بتاعت مايكروسوفت من غير ما يطلع علي الانترنت 
الموضوع بيتم بخط فيزيائي عن طريق connectivity provider

#### الفوائد الأساسية

- **أداء ثابت ومضمون (predictable performance)**: لأنه مش متأثر بازدحام الإنترنت العام
- ال**Bandwidth أعلى بكتير**: بيوصل من 50 Mbps لحد 100 Gbps حسب الباقة
- ال**latency منخفض ومستقر**: أساسي لتطبيقات حساسة جدًا زي أنظمة مالية أو معالجة بيانات ضخمة
- **أمان أعلى**: البيانات مبتعديش على الإنترنت العام أصلاً، فمش معرّضة لنفس المخاطر
#### العيب الواضح

**التكلفة أعلى بكتير**، ومحتاج ترتيبات مع مزود اتصال متخصص (زي شركة اتصالات معينة عندها الخط الفيزيائي ده)، مش حاجة تفعّلها بضغطة زرار زي VPN

![[Pasted image 20260902193317.png]]
في الشركات الكبيرة، شائع جدًا إنك تستخدم **ExpressRoute كاتصال أساسي**، و**VPN Gateway كـ backup احتياطي** — لو خط ExpressRoute المخصص وقع لأي سبب (صيانة أو عطل في مزود الاتصال)، الاتصال بيتحول تلقائيًا لـ VPN عبر الإنترنت العام، لحد ما ExpressRoute يرجع شغال

كل الحلول التلاتة دي بتحتاج **Gateway Subnet مخصصة** جوه الـ VNet

![[Pasted image 20260902193628.png]]
# Public and Private Endpoints

ال PaaS علي Azure زي ال **Storage Account**، أو **Azure SQL Database**، أو **App Service** بتبقي بره السبسكريبشن بتاعك و بتبقي باي ديفولت بتاخد عنوان عام تقدر توصلها من النت يعني أي حد في العالم، من أي مكان، يقدر يحاول يوصل لعنوان الخدمة دي (طبعًا هيحتاج مفتاح أو صلاحية عشان يدخل فعليًا، لكن **العنوان نفسه مكشوف ومتاح** للكل).
## Public Endpoint

الـ Public Endpoint هو أي Service ليها Public IP Address وبيتم عمل Routing للـ Traffic بتاعها عن طريق الـ Public Internet.

- ال**Accessibility:** السيرفر متاح عالمياً. أي حد معاه الـ IP Address والـ Port يقدر يعمل Initiate لـ Connection.
    
- ال **Traffic Flow:** الـ Packets بتخرج من الـ Client، بتمر عبر سلسلة من الـ Routers والـ ISPs، لحد ما توصل للـ Internet Gateway (IGW) الخاص بالـ Infrastructure بتاعتك، واللي بيمررها للسيرفر.
    
- ال**Security Posture:** لأن الـ Service بتكون Exposed للإنترنت، الـ Attack Surface بيكون كبير جداً. ده بيخليها هدف مباشر للـ Port Scanners، والـ DDoS Attacks، والـ Brute-force. التأمين هنا بيعتمد بشكل أساسي على الـ Application Layer، باستخدام Web Application Firewall (WAF)، وتشفير TLS، و Strict Rate Limiting.
    
- ال**Use Cases:** الـ Web Servers، الـ Public APIs، والـ External Load Balancers اللي بتستقبل الـ Requests من الـ End-users.

## Private Endpoint

هو **network interface خاص بيك، بعنوان Private IP من داخل الـ VNet بتاعتك نفسها**، بيوصلك مباشرة للخدمة (زي Storage Account) **من غير ما المرور يخرج للإنترنت العام خالص**.

بمعنى تاني: بدل ما تتكلم مع الخدمة عن طريق عنوانها العام، أزور بيدّيلك **عنوان خاص إضافي** (زي `10.0.1.50` مثلاً) **جوه الـ VNet بتاعتك نفسها**، وكأن الخدمة (اللي هي في الحقيقة PaaS مُدار من مايكروسوفت، مش VM بتاعتك) **بقت "عضو" داخل شبكتك الخاصة**.

 التقنية اللي بتشغّل ده: Azure Private Link

الخدمة اللي بتوفر المفهوم ده اسمها **Azure Private Link**. هي اللي بتخلي المرور بينك وبين الخدمة يعدي بالكامل على **الـ backbone الداخلي بتاعة Microsoft** (بالظبط نفس المبدأ اللي شرحناه في VNet Peering) **بدل الإنترنت العام تمامًا**.

- ال**Security Posture:** الـ Attack Surface من خارج الشبكة شبه معدوم. الحماية هنا بتعتمد على الـ Network Security Groups (NSGs) والـ Firewalls الداخلية لتطبيق مبدأ הـ Least Privilege وتقييد الـ Traffic بين الـ Subnets المختلفة (Micro-segmentation).
    
- ال**Use Cases:** الـ Databases، الـ Backend Microservices، والـ Caching Clusters (زي Redis و Memcached).

![[Pasted image 20260902231347.png]]


![[Pasted image 20260902232814.png]]

![[Pasted image 20260902232954.png]]

# Describe Azure storage services
## Storage Account
في أزور، أي نوع تخزين محتاجه (Blob, File, Queue, Table) **لازم يكون تابع لـ Storage Account**.
فعليا هي حاويه لانواع ال Storage 
اسم الـ Storage Account لازم يكون **فريد على مستوى أزور بالكامل** (مش بس في حسابك إنت)، لأنه بيتحول لجزء من الـ URL نفسه (زي `mystorageaccount.blob.core.windows.net`)، وده سبب إنك هتلاقي أسماء بسيطة زي `storage1` غالبًا مأخوذة بالفعل.

### ايه انواع ال Storage اللي جوه Storage Account ؟

#### 1) Blob Storage 

**Blob** = **Binary Large Object**.
ده تطبيق Azure ل Object Storage "راجع في اول النوت في pdf لو نسيته"

**البنية الداخلية**: Blob Storage بتتكون من **Containers** (زي "المجلدات" الأساسية)، وجوه كل Container بتحط **Blobs** (الملفات الفعلية). تفتكر لما شرحنا Object Storage وقلنا إن الوصول بيتم بـ Key مش مسار هرمي حقيقي؟ نفس المبدأ هنا — الـ "مجلدات" اللي بتشوفها في الـ Portal هي محاكاة بصرية بس، مش هيكل حقيقي زي File Storage.

**3 أنواع من الـ Blobs نفسها**:

- ال**Block Blobs**: للملفات العادية (صور، فيديوهات، مستندات) — الأكتر استخدامًا بفارق كبير
- ال**Append Blobs**: مُحسّنة للإضافة المستمرة (زي ملفات الـ logging اللي بتتكتب فيها بيانات جديدة باستمرار من غير ما تعدل القديم)
- ال**Page Blobs**: مُحسّنة للقراءة/الكتابة العشوائية، وده الأساس اللي بتشتغل عليه **Managed Disks** بتاعة الـ VMs (اللي اتكلمنا عنها قبل كده)

#### 2) Azure Files
تطبيق Azure ل File Storage 
بيديك **مشاركة ملفات مُدارة بالكامل (fully managed file share)**، وصولها بيتم عن طريق بروتوكولات معروفة زي **SMB** (بروتوكول ويندوز) و**NFS** (بروتوكول لينكس).

**الفايدة الجوهرية**: تقدر تربط (mount) نفس الـ File Share من **أكتر من جهاز في نفس الوقت** (VMs مختلفة، أو حتى من on-premises عن طريق VPN/ExpressRoute اللي شرحناهم)، وكأنه **network drive مشترك**.

**ميزة إضافية مهمة**: فيه خدمة اسمها **Azure File Sync**، بتخليك **تعمل cache** لنسخة من الملفات دي على سيرفر Windows محلي (on-premises)، عشان الوصول السريع محليًا، مع الاحتفاظ بالنسخة الأساسية في أزور.

#### 3) Disk Storage
ده تطبيق أزور لمفهوم **Block Storage** . دي الديسكات اللي بتتوصل بـ **Azure VMs** (OS Disk وData Disks).

**نقطة تقنية مهمة**: أزور بيقدملك **Managed Disks**، يعني **إنت مش بتدير الـ Storage Account اللي بيخزن فيه الديسك ده بنفسك** — أزور بيتكفل بده بالكامل من ورا الكواليس. ده الفرق عن الوضع القديم (**Unmanaged Disks**) اللي كنت مضطر فيه تدير Storage Account بنفسك لكل مجموعة ديسكات، وده كان بيسبب تعقيد وحدود إدارية غير ضرورية.

#### 4) Queue Storage

ده خدمة **مختلفة تمامًا** عن التلاتة اللي فاتوا — مش عن تخزين ملفات، دي عن **تخزين رسائل (messages) بسيطة** بين أجزاء مختلفة من تطبيقك، بهدف **فك الترابط (decoupling)** بينهم.

**مثال عملي يخصك كـ Backend Engineer**: تخيل عندك FastAPI endpoint بيستقبل طلب "معالجة صورة"، لكن المعالجة دي بتاخد وقت طويل. بدل ما تخلي المستخدم ينتظر، الـ endpoint بيحط **رسالة في Queue** تقول "فيه صورة محتاجة معالجة"، ويرد على المستخدم فورًا. عندك **worker process منفصل** بيقرا من الـ Queue باستمرار، ويعالج الصور واحدة واحدة في الخلفية.

**بالتشبيه الهندسي**: ده نفس مبدأ **Message Queue** أو **Producer-Consumer pattern** اللي غالبًا سمعت عنه في هندسة الأنظمة — Queue Storage هي التطبيق البسيط والمُدار بالكامل بتاعه في أزور (البديل الأكتر تقدمًا هو **Azure Service Bus**، لسيناريوهات أعقد).

#### 5) Table Storage
دي خدمة **NoSQL key-value store** بسيطة جدًا، مصممة لتخزين **بيانات منظمة لكن 
تكون non-relational** بكميات ضخمة وبتكلفة منخفضة جدًا. كل "صف" في الجدول عنده **Partition Key** و**Row Key** بيحددوا هويته بشكل فريد.

**متلخبطش بينها وبين Azure SQL Database أو Cosmos DB** — Table Storage أبسط بكتير، مفيهاش علاقات (relations) أو استعلامات معقدة (complex queries)، ومناسبة لبيانات بسيطة زي logs أو metadata خفيفة.

### Access Tiers

![[Pasted image 20260903105412.png]]

![[Pasted image 20260903105505.png]]

### Redundancy Options
#### LRS (Locally Redundant Storage)

بيحتفظ بـ **3 نسخ من بياناتك جوه نفس الـ Datacenter الواحد**. أرخص خيار، بيحميك بس من فشل هاردوير محلي (زي ديسك واحد اتعطل)، لكن **لو الداتا سنتر كله وقع، بياناتك ضاعت**.

#### ZRS (Zone-Redundant Storage)

بيوزع النسخ الثلاثة على **3 Availability Zones مختلفة** جوه نفس الـ Region
لو Zone كاملة وقعت، بياناتك لسه موجودة وآمنة.

#### GRS (Geo-Redundant Storage)

بينسخ بياناتك بالكامل لـ **Region تانية بعيدة (الـ Region Pair)**. لو الـ Region الأساسية بالكامل وقعت (كارثة إقليمية)، بياناتك لسه موجودة في الـ Region الشريكة.

**RA-GRS**
هو نفس GRS، بس بيديك **صلاحية قراءة مباشرة (Read Access)** من النسخة الاحتياطية في الـ Region التانية حتى في الحالة العادية (مش وقت الكارثة بس)، مفيد لو عايز توزع حمل القراءة جغرافيًا.

**GZRS** 
دمج بين الاتنين (ZRS في الـ Region الأساسية + نسخة في Region تانية بعيدة) — أعلى مستوى حماية متاح، وبالتبعية أغلى خيار.