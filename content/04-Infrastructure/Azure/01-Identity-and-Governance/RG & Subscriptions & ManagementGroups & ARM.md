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

عشان تقدر **تنشئ أو تدير** Management Groups، **مش كفاية إنك Subscription Owner أو حتى Contributor**. المطلوب واحد من الاتنين:

1. تكون **Global Administrator** في Entra ID، **وتفعّل "Access management for Azure resources"** بنفسك (checkbox في **Entra ID → Properties**) — ده بيدّيك **User Access Administrator role على الـ Root Management Group**
2. أو يكون **حد تاني معاه الصلاحية دي بالفعل يدّيك إياها**

### Creating Management groups

- Search bar → **Management groups**
- **+ Create**
- تحدد:
    - ال**Management group ID**: معرّف فريد (زي `mg-sales`) — **ده لازم يكون فريد على مستوى الـ Tenant كله، ومينفعش تغيره بعد الإنشاء**
    - ال**Display name**: الاسم اللي بيظهر (ده تقدر تغيره لاحقًا بسهولة)
- **Create**

عشان تنقل Subscription لجوه Management Group معينة، لازم يكون معاك **صلاحية كافية على الاتنين مع بعض**: صلاحية على الـ Subscription نفسها (زي Owner)، **وصلاحية على الـ Management Group الهدف** (زي Contributor على الأقل).

**مينفعش تمسح Management Group لو لسه فيها Subscriptions أو Management Groups فرعية جواها** — لازم تفضيها الأول (تنقل كل حاجة برة أو تمسحها)، **بعدين** تقدر تمسح الـ Management Group نفسها.

### Tenant Root Group

فيه **Root Management Group واحدة بس لكل Tenant**؟ أول مرة تفتح صفحة Management Groups، أزور بيوريك **Tenant Root Group** موجودة بالفعل تلقائيًا (اسمها نفس اسم الـ Tenant)، وأي Management Group جديدة تعملها بتتحط **تلقائيًا تحتها مباشرة** كمستوى أول.

### CLI Commands

```bash
# Creating a Management Group
az account management-group create --name "mg-sales"

# moving Subscription into it
az account management-group subscription add \
  --name "mg-sales" \
  --subscription "<subscription-id>"
```

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
