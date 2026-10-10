
 لما تيجي تنقل مكنة من اشتراك للتاني (`Sub1` لـ `Sub2`). أزور مش بيسمحلك تنقل المكنة حاف كده، لازم تاخد معاها كل حاجة مشبوكة فيها.  المكنة، هاردها، كارتها، وشبكتها.. باكدج واحدة بتتنقل مع بعض!

توجد نسختين من الـ Load Balancer (Basic و Standard). النسخة الـ Basic قديمة ومحدودة ولا تدعم خاصية الـ HA Ports المطلوبة لعمل أجهزة الـ NVA. لذلك، النسخة القياسية (Standard) هي الخيار الوحيد الذي يدعم هذا السيناريو.

The DSC extension for Windows requires that the target virtual machine is able to communicate with Azure.
The VM needs to be started.

ميزة التسجيل التلقائي (Auto-registration) وربط الشبكات (VNet Links) **حصرية فقط للـ Private DNS Zones**. المناطق العامة (Public DNS Zones) لا تدعم ميزة الـ Auto-registration إطلاقاً؛ لأنها موجهة للنشر العام على الإنترنت وتتطلب إضافة السجلات يدوياً أو عبر API.
إعداد الـ DNS Suffix الموجود داخل نظام التشغيل Windows Server (`Contoso.com` في `VM1` أو `None` في `VM2`) **لا يؤثر إطلاقاً** على عملية التسجيل التلقائي الخاصة بأزور على مستوى البنية التحتية (Platform-level).

- أزور يتعامل مع كارت الشبكة الخاص بالمكنة ويأخذ اسم المكنة في أزور ويضيفه فوراً للـ Private Zone.

Configuring a VNet-to-VNet connection is a good way to easily connect VNets. Connecting a virtual network to
another virtual network using the VNet-to-VNet connection type (VNet2VNet) is similar to creating a Site-to-Site
IPsec connection to an on-premises location. Both connectivity types use a VPN gateway to provide a secure
tunnel using IPsec/IKE, and both function the same way when communicating.

Your VMs should use managed disks if you want to move them to an Availability Zone by using Site Recovery.

P2S
لو الـ VPN شغال بـ **Certificate** ⬅️ الحل دايماً هو (Export the client certificate from Computer1 and install it on Computer2).لو جابلك سيرة Microsoft Entra (Azure AD) مع سيناريو Certificates ⬅️ اختار **No** فوراً.

- **ASG:** بيجمع **(المكن بتاعك إنت)** ⬅️ إنت اللي بتديره ⬅️ بيُستخدم للـ Micro-segmentation جوه شبكتك.
    
- **Service Tag:** بيجمع **(أيبيهات خدمات أزور)** ⬅️ أزور اللي بيديره ⬅️ بيُستخدم عشان تكلم خدمات PaaS أو شبكات عامة بسهولة.

عشان تضيف أي مكنة (VM) داخل Application Security Group، الربط المعماري بيتم دائماً على مستوى **كارت الشبكة (Network Interface - NIC)** الخاص بالمكنة، وليس على المكنة نفسها كـ Compute resource.

ExpressRoute + VPN Backup:

- VPN SKU: استخدم `VpnGw1` كأقل اختيار متاح هنا.
    
- Local Network Gateway: يمثل شبكة الـ On-premises.
    
- Connection: تربط الـ VPN بالـ On-premises.
    
- Gateway Subnet: موجودة بالفعل في السيناريو.


**2. ليه لازم نحذف peering1 الأول؟ (فخ الـ Disconnected State)** في معمارية أزور، الربط بين الشبكات (VNet Peering) بيتم من الاتجاهين. حالة **Disconnected** دي بتظهر في سيناريو واحد فقط: لو كان الربط شغال، وجه أدمن مسح الـ Peering من الناحية التانية (من عند `vNET1`).

- **القاعدة الهندسية:** لما الـ Peering بيقع في الـ Disconnected State، أزور بيعتبر الرابط ده "معطوب" ولا يمكن تعديل حالته أو إصلاحه مباشرة ليعود Connected.
    
- **الحل الإجباري:** يجب عليك أولاً **حذف الرابط المعطوب (delete peering1)** من جانب `vNET6`، ثم إعادة إنشاء رابط Peering جديد من الصفر بين الشبكتين.

في أزور، الـ Basic Load Balancer له قيود قديمة ومشددة. لا يمكنك وضع أجهزة وهمية (VMs) داخل الـ Backend Pool الخاص به إلا إذا كانت هذه الأجهزة **تقع داخل نفس الـ Availability Set** (أو نفس الـ Scale Set).#### العبارة الثانية: (لو ملف Probe1.htm موجود، هل الـ LB هيوزع ترافيك بورت 80؟)

- **تحليل الإعدادات:** الصورة بتوضح إن `Rule1` مضبوطة عشان توزع ترافيك TCP على بورت 80، ومربوطة بـ Health Probe اسمه `Probe1`. الـ Probe ده وظيفته يخبط على ملف اسمه `Probe1.htm` عبر HTTP.
    
- **المنطق:** لو الملف ده فعلاً موجود على `VM1` و `VM2`، السيرفرات هترد بـ (200 OK). الـ Load Balancer هيفهم إن المكنتين "بصحة جيدة" (Healthy)، وبالتالي هيشغل `Rule1` ويبدأ يوزع الترافيك بينهم بشكل طبيعي.
    
- **النتيجة:** **Yes**.

الـ Load Balancer في أزور بيشتغل بمبدأ (Explicit Routing)، يعني مبيعملش أي حاجة إلا لو إنت كاتبله قاعدة واضحة (Rule) يعملها. لو حذفت `Rule1`، الترافيك كله هيقع (Drop) لأن مفيش أي قاعدة بتقوله يوجه الترافيك فين.

| **الميزة (Feature)**                 | **Basic Load Balancer (القديم والمقيد)**                                                                               | **Standard Load Balancer (الموصى به لبيئة الإنتاج)**                                                                                |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| **قيود الـ Backend Pool**            | **مقيد جداً:** يجب أن تكون جميع الأجهزة (VMs) داخل نفس الـ _Availability Set_ أو نفس الـ _Virtual Machine Scale Set_.  | **مرن:** يقبل أي أجهزة (VMs) أو Scale Sets طالما أنها تقع داخل نفس الـ _Virtual Network (VNet)_، بغض النظر عن الـ Availability Set. |
| **توافق الـ Public IP**              | يتطلب IP من فئة **Basic** (يمكن أن يكون متغيراً Dynamic أو ثابتاً Static).                                             | يتطلب IP من فئة **Standard** (ويجب أن يكون **Static** فقط). ممنوع استخدام IP متغير.                                                 |
| **الأمان الافتراضي (Security)**      | **مفتوح (Open by default):** لا يشترط وجود NSG على الماكينات لمرور الترافيك، سيمر الترافيك حتى لو لم تقم بإعداد حماية. | **مغلق (Secure by default):** لن يمر أي ترافيك (Zero traffic) إلا إذا قمت بربط وإعداد **NSG** يسمح بمرور هذا الترافيك صراحةً.       |
| **فحوصات الصحة (Probes)**            | يدعم TCP و HTTP فقط.                                                                                                   | يدعم TCP و HTTP و **HTTPS**.                                                                                                        |
| **توجيه المنافذ العالية (HA Ports)** | غير مدعوم.                                                                                                             | **مدعوم:** ضروري جداً لتوجيه الترافيك على جميع المنافذ لأجهزة الـ NVAs (مثل Firewalls).                                             |
| **ضمان الخدمة (SLA)**                | لا يوجد.                                                                                                               | **99.99%** (عند استخدام مكنتين أو أكثر في الـ Backend).                                                                             |
| **قواعد الخروج (Outbound Rules)**    | غير مدعومة بشكل مخصص (يعتمد على الـ SNAT الافتراضي العشوائي).                                                          | **مدعومة بقوة:** تتيح لك التحكم الدقيق في كيفية خروج الماكينات الداخلية إلى الإنترنت باستخدام الـ Frontend IP.                      |
 **3. الـ Gateway Load Balancer (حالة خاصة)**
هذا الـ SKU مخصص لسيناريو معماري متقدم وهو حماية الترافيك عبر إدخال أجهزة شبكات وهمية (NVAs) شفافة عالية الأداء (Transparent Firewalls أو Analytics) في مسار الترافيك قبل أن يصل إلى شبكتك. لن تتعمق فيه أسئلة الـ AZ-104 كثيراً، ولكن يجب أن تعرف أنه مخصص لـ "Bump-in-the-wire" أو مسارات الشبكات الشفافة.

- **لو جابلك سؤال مكنة مش راضية تنضاف للـ Load Balancer:** بص على الـ SKU فوراً. الـ Standard LB هيرفض المكنة لو عليها Basic/Dynamic IP، وهيرفضها لو هي في VNet تانية.
    
- **لو الترافيك بيقع (Unreachable) من خلال Standard LB:** الإجابة دايماً هتكون إنك نسيت تبرمج الـ NSG عشان تسمح بالترافيك، لأنه Drop by default.
    
- **ممنوع الخلط:** أزور بيرفض تماماً خلط الموارد. لازم المورد (Public IP) والـ Load Balancer يكونوا من نفس الـ SKU بالظبط (Standard مع Standard، و Basic مع Basic).
    
- **لو طلب منك Active-Active NVAs:** عينك تروح فوراً على اختيار **Standard Load Balancer** مع تفعيل **HA Ports**.

لا يمكن إنشاء كارت شبكة (Network Interface) في الهواء؛ يجب أن يتم ربطه بشبكة وهمية (VNet) موجودة مسبقاً. والأهم من ذلك، **يجب أن يكون كارت الشبكة والشبكة الوهمية في نفس الموقع الجغرافي (Location/Region) بالضبط**


- **ال(IP flow verify):** السؤال هنا طالب أداة تقدر تحدد "قاعدة الأمان (security rule) التي تمنع حزمة بيانات (packet) من الوصول للمكنة". أداة `IP flow verify` مصممة خصيصاً للوظيفة دي؛ إنت بتديها تفاصيل الباكت (الـ IP، البورت، والبروتوكول)، وهي بتعمل محاكاة وتقولك الباكت ده هيترفض ولا هيعدي، والأهم إنها بتجيبلك **اسم قاعدة الـ NSG تحديداً** اللي طبقت الأكشن ده.
    
- **ال (Connection troubleshoot):** هنا المطلوب هو "التحقق من الاتصال الصادر (outbound connectivity) من المكنة إلى مضيف خارجي". أداة `Connection troubleshoot` بتعمل اختبار اتصال فعلي (End-to-end) من المكنة بتاعتك لأي عنوان IP أو FQDN خارجي. الأداة بتوريك حالة الاتصال، المسار اللي مشيت فيه الباكت (Hops)، وأي عوائق في السكة.

- ال **Next hop:** بيعرفك الباكت هتروح لمين الخطوة الجاية (الراوتر، أو الـ VPN، أو الإنترنت). بنستخدمه لو شاكك إن فيه مشكلة أو تعارض في توجيه الترافيك (Routing).
    
- ال **Packet capture:** بيسجل حزم البيانات (Traffic) اللي داخله وطالعه من المكنة فعلياً لفترة معينة، عشان تنزلها وتحللها ببرامج متخصصة زي Wireshark لو فيه مشكلة معقدة في الشبكة أو الأمان.
    
- ال **Security group view:** بيعرضلك كل قواعد الـ NSG المتطبقة على كارت الشبكة (NIC) بتاع المكنة في شاشة واحدة مجمعة، عشان تشوف الصورة الكاملة للحماية المطبقة عليها.
    
- ال **NSG flow logs:** بيسجل "دفتر أحوال" لكل ترافيك بيعدي أو يترفض من الـ NSG (مين الـ IP اللي حاول يتصل، على بورت إيه، والنتيجة اتسمح له ولا اترفض).
    
- ال **Traffic Analytics:** دي أداة ذكية بتاخد السجلات الخام بتاعت (NSG flow logs) وتحللها، وتطلعلك منها داشبورد ورسومات بيانية توضحلك أكتر أيبيهات بتسحب داتا، أو لو فيه محاولات اتصال خبيثة من شبكات بر

- **NIC > VNet > Default** (الـ NIC دايماً كلمته هي اللي بتمشي).
    
- لو عدلت الـ DNS على مستوى الـ VNet أو الـ NIC، المكنة مش هتحس بالتغيير ده غير لما تعملها **Restart**. (بتيجي كتير في الأسئلة كخطوة Troubleshooting).

