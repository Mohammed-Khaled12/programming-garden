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

## Types of Managed Disks
الـ VM نفسها ليها OS Disk وممكن تضيف Data Disks
دلوقتي التفصيل: كل ديسك من دول ليه **نوع (Tier)** بيحدد الأداء والتكلفة:


| الاستخدام                                                      | الميكانيزم      | النوع          |
| -------------------------------------------------------------- | --------------- | -------------- |
| Backups، بيانات نادرة الوصول، أرخص خيار                        | ميكانيكي تقليدي | Standard HDD   |
| تطبيقات خفيفة، بيئات تطوير                                     | SSD اقتصادي     | Standard SSD   |
| تطبيقات إنتاج، قواعد بيانات                                    | SSD عالي الأداء | Premium SSD    |
| تحكم دقيق في IOPS/Throughput منفصل عن الحجم                    | SSD أحدث        | Premium SSD v2 |
| أحمال شديدة الحساسية للـ latency (SAP HANA، قواعد بيانات ضخمة) | الأعلى أداءً    | Ultra Disk     |

## Azure Bastion

فاكر في اللاب، عملنا الـ VM **بـ Public IP خاصة بيها**، وفتحنا NSG rule تسمح بـ SSH من IP بتاعنا بس؟ ده حل شغال، لكن **فيه عيب أمني جوهري**: **الـ VM لسه ليها Public IP معروضة على الإنترنت**، حتى لو NSG بتحميها. أي Port scanner بيدور على الإنترنت هيلاقي الـ IP ده **موجود وبيرد**، حتى لو مش قادر يعدي الـ NSG.

ازور **Bastion** هي خدمة مُدارة بتديك SSH/RDP للـ VM **من غير ما الـ VM تاخد Public IP خالص**. بتتحط كـ **مورد منفصل جوه VNet بتاعتك** (في Subnet مخصصة إلزامية اسمها **`AzureBastionSubnet`**، فاكر نفس فكرة `AzureFirewallSubnet`؟)، وأنت بتتصل بيها **من خلال المتصفح مباشرة من الـ Portal نفسه**، وهي اللي بتوصلك بالـ VM عن طريق الـ **Private IP** بتاعتها.

**لو كنا مستخدمين Bastion بدل الطريقة اللي عملناها**: الـ VM كانت هتفضل **مفيهاش Public IP خالص من الأساس**، وده كان هيقلل الـ Attack Surface بشكل جذري — بدل ما تحمي VM معروضة، بتخليها **مش معروضة أصلًا**.

**القاعدة العملية للامتحان**: أي سؤال بيوصف "عايزين وصول SSH/RDP آمن من غير Public IP على الـ VM" **الإجابة Azure Bastion**، مش NSG rule ولا VPN.

## Virtual Machine Scale Sets (VMSS)
فاكر شرحنا Scale Out للـ App Service؟ [[App-Services-and-Containers#Scale Out (Horizontal) - زيادة عدد الآلات| Review from HERE]]
ال **VMSS هي نفس المبدأ، بس لـ VMs**
بدل ما تعمل VM واحدة وتكررها يدويًا، بتعرّف **إعداد واحد (Image + Size + Networking)**، وAzure بينشئ **نسخ متطابقة منه تلقائيًا**، ويزود أو يقلل العدد حسب الحمل (Autoscale)، ويوزعهم خلف **Load Balancer** (اللي شرحناه بالتفصيل هنا [[Load-Balancer-and-App-Gateway|Review from HERE]])

#### أرقام مهمة هتتسأل فيها

- من **0 لـ 1000 VM** لو مبنية على Marketplace image أو Custom image جوه Azure Compute Gallery
- **مفيش أي تكلفة إضافية للـ Scale Set نفسها** — بتدفع بس تكلفة الـ VMs والموارد الفعلية اللي شغالة، زي ما شرحنا في App Service Plan بالظبط

#### Health Probes مرة تانية

فاكر Health Probes بتاعت Load Balancer؟ [[Load-Balancer-and-App-Gateway#3. Health Probes|Review from HERE]]
ال**VMSS بتستخدم نفس المبدأ**: لو instance معينة فشلت في الـ Health Probe، Azure **بيستبدلها تلقائيًا** بـ instance جديدة سليمة — ده تطبيق مباشر لمبدأ **Self-healing**.

### Generalized VS Specialized

#### المشكلة اللي بيحلها

عملت VM، ظبطتها بالظبط زي ما عايز (تطبيقات متسطبه، إعدادات معينة)، وعايز **تستنسخها** لعمل VMs جديدة بنفس الإعداد بالضبط — بدل ما تعيد كل خطوات الإنشاء من الصفر.

#### الفرق الجوهري

ال **Specialized Image**: نسخة **كاملة بكل تفاصيلها**، بما فيها اسم الجهاز (hostname)، حسابات المستخدمين، الـ SID الخاص بيها. لو عملت VM منها، هتبقى **نسخة طبق الأصل بنفس الهوية** — مناسبة لو عايز تسترجع (restore) نفس الـ VM بالظبط.

ال**Generalized Image**: قبل ما تعمل الـ Capture، بتشغل أداة (**`waagent -deprovision` على Linux**، أو **Sysprep على Windows**) **"تنضف"** الـ VM من أي معلومات فريدة (hostname, SIDs, machine accounts) — كأنها "قالب فاضي" جاهز يتستخدم كأساس لعدد لا نهائي من VMs جديدة، كل واحدة تاخد هويتها الخاصة وقت الإنشاء.

**تحذير مهم جدًا هيتسأل فيه**: **بمجرد ما تعلّم الـ VM إنها Generalized، متقدرش تشغلها (Restart) تاني أبدًا** — العملية **لا رجعة فيها**. لازم تكون متأكد 100% قبل ما تعمل الخطوة دي.

**القاعدة العملية**: عايز تعمل نسخة "قالب" لعشرات الـ VMs الجديدة (زي أساس لـ VMSS) → **Generalized**. عايز نسخة احتياطية من نفس الـ VM المحددة بالظبط → **Specialized**.

## Managed Identity 

#### المشكلة

تخيل الـ VM بتاعتك (أو App Service) محتاجة توصل لـ **Key Vault** (مخزن أسرار) عشان تجيب connection string. الحل التقليدي: تخزن username/password جوه الكود أو environment variable — **خطر أمني واضح** (فاكر لما اتكلمنا عن App Settings في FastAPI؟).

#### الحل

ال **Managed Identity** بتدّي الـ VM **هوية جوه Entra ID تلقائيًا**، من غير أي باسورد أو مفتاح تتخزن في أي مكان. الـ VM تقدر تستخدم الهوية دي عشان تطلب **RBAC role** على أي مورد تاني (زي Key Vault أو Storage)، **بالظبط زي أي Security Principal شرحناه في RBAC**.

**نوعين**:

- ال**System-assigned**: مرتبطة بالـ VM نفسها، بتتمسح لو الـ VM اتمسحت
- ال**User-assigned**: مورد مستقل، ممكن يتشارك بين أكتر من VM

## نقل VM بين Resource Groups أو Regions

- **نقل بين Resource Groups (نفس Subscription)**: سهل نسبيًا، لكن **كل الموارد المرتبطة (NIC, Disk, Public IP) لازم تتنقل مع بعض**
- **نقل بين Regions**: **مش عملية مباشرة خالص** — أقرب طريقة هي **Azure Resource Mover** أو عمل **Snapshot/Image** ونشرها في الـ Region الجديدة من الصفر