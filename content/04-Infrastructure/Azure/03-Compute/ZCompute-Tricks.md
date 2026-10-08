### 1️⃣ الأجهزة الوهمية (Virtual Machines)

- **حالة الـ (Stopped - Deallocated):** تحرر الموارد ولا تستهلك أي رصيد من حصة الـ vCPU الخاصة بالاشتراك، ولن تدفع تكلفة الحوسبة. بينما الـ (Running) أو (Stopped - Allocated) تستهلك رصيداً وتُحسب تكلفتها.
    
- **تغيير الحجم (Resizing):** إذا كان الحجم الجديد غير متوفر على نفس العنقود (Cluster)، يجب إيقاف المكنة أولاً لتصبح (Stopped - Deallocated).
    
- **الشبكات (VNet & NIC):** القاعدة الحديدية: كارت الشبكة (NIC) والشبكة الافتراضية (VNet) التي سيتم ربطه بها، **يجب** أن يكونا في نفس المنطقة الجغرافية (Region).
    
- **التوافرية (Availability Sets):** للحصول على أقصى توافر، اختر الحد الأقصى: **3 Fault Domains** (للأعطال المفاجئة) و **20 Update Domains** (للصيانة المجدولة).
    
- **إضافات المكن (Extensions):**
    
    - **LAD (Linux Diagnostic Extension):** يُستخدم لجمع بيانات التشخيص (Telemetry) من خوادم لينكس.
        
    - **DSC (Desired State Configuration):** لتعديل إعدادات نظام التشغيل الداخلي (برامج، ريجستري)، وليس لها علاقة بالهاردوير أو حجم المكنة.
        
    - احذر فخ الامتحان: اختر دائماً الـ (VM extension) الخاص بـ Microsoft Monitoring Agent وليس تسطيب الـ Agent العادي يدوياً للـ Automation.
        
- **الأقراص (Disks):** أزور **لا يدعم** صيغة `.vhdx`. لرفع قرص محلي كـ Template، يجب تحويله إجبارياً إلى `.vhd` (ونوعه Fixed Size) قبل الرفع.
    

### 2️⃣ الحاويات وكوبرنيتيز (Containers, ACR & AKS)

- **الوصول الخارجي لـ AKS:** يجب توجيه سجلات الـ DNS العامة إلى (Load Balancer front end) أو (Ingress Controller)، **ولا يتم أبداً** توجيهها لعناوين داخلية مثل الـ Cluster nodes.
    
- **توسيع AKS (Autoscaling):**
    
    - لزيادة الـ Nodes (Cluster Autoscaler) ⬅️ نستخدم Azure Portal أو CLI.
        
    - لزيادة الـ Pods (HPA) ⬅️ نستخدم أوامر `kubectl`.
        
- **أوامر Kubectl:** `kubectl apply -f <file_name>.yaml` لنشر ملفات YAML. وهو يستخدم لإدارة الكائنات الداخلية (Pods, HPA, Logs).
    
- **صيانة AKS (Upgrade):** خاصية `Max surge` تتحكم في عدد الأجهزة (Nodes) الإضافية التي يقوم أزور بإنشائها مؤقتاً أثناء التحديث لضمان عدم توقف الخدمة.
    
- **نظام التشغيل (Windows Node Pools):** لا تدعم `Kubenet`، وتتطلب إجبارياً استخدام `Azure CNI`.
    
- **أنظمة الحاويات (Container Services):**
    
    - **Azure Container Instances (ACI):** تدعم Windows و Linux. (لكن المجموعات متعددة الحاويات Multi-container groups تدعم Linux فقط).
        
    - **Azure App Service (Web App for Containers):** تدعم Windows و Linux.
        
    - **Azure Container Apps:** الفخ.. تدعم **Linux فقط**.
        
- **Azure Container Registry (ACR):**
    
    - اسم المستخدم لحساب الـ Admin هو **نفس اسم الـ Registry** تماماً.
        
    - خاصية الـ ACR Tasks مدعومة في **كل الخطط** (Basic, Standard, Premium).
        
    - خصائص الـ Private Endpoints والـ Dedicated data endpoints حصرية لخطة **Premium** فقط.
        
    - لربط ACR بـ AKS بأمان وسهولة، نغير الـ Authentication method لنستخدم (Managed Identity).
        

### 3️⃣ تطبيقات الويب (Azure App Service)

- **قواعد أنظمة التشغيل (OS Rules):**
    
    - ASP.NET (V3.5 / V4.8) ⬅️ **Windows** فقط.
        
    - PHP (8.0 وما أحدث) / Python / Ruby ⬅️ **Linux** فقط.
        
    - PHP (7.4 وما أقدم) / .NET Core (.NET 6,7,8) / Java / Node.js ⬅️ **Cross-platform** (يعمل على الاتنين).
        
- **التوسع (Scaling):** الـ Rule-based scale out (بالـ CPU والذاكرة) متاح من خطة Standard فما فوق.
    
- **التوافر (Zone Redundancy):** مدعومة في خطط Premium v2/v3، والفخ المعماري: **لا يمكن تفعيلها إلا أثناء إنشاء الخطة فقط** (لا يمكن إضافتها لخطة موجودة بالفعل).
    
- **الشبكات:** ميزة VNet Integration تسمح للتطبيق بإرسال ترافيك (Outbound) داخل الشبكة وتدعم العبور عبر (VNet Peering).
    
- **النسخ الاحتياطي:** يخزن في (Azure Storage Account) وليس في Vault. ولاستثناء ملفات ننشئ ملف `_backup.filter`.
    

### 4️⃣ إدارة البنية التحتية (ARM Templates & Deployments)

- **صلاحيات الـ Marketplace:** لنشر صورة من شركة خارجية (Programmatic deployment)، يجب أولاً قبول الـ (Terms of Service) الخاصة بها.
    
- **الاعتمادات (Dependencies):** عند إنشاء مكنة (VM) بالـ ARM، المورد الوحيد الذي يجب إضافته في `dependsOn` هو كارت الشبكة (`NIC`) وليس الـ VNet أو الـ IP.
    
- **ترتيب النشر (Deployment Scopes):**
    
    - لإنشاء Resources (VM, VNet) ⬅️ `New-AzResourceGroupDeployment`
        
    - لإنشاء Resource Groups أو صلاحيات RBAC ⬅️ `New-AzSubscriptionDeployment`
        
    - لإنشاء Azure Policies ⬅️ `New-AzManagementGroupDeployment`
        
- **وضع النشر (Deployment Mode):**
    
    - `Complete`: يمسح أي موارد موجودة في الـ RG ومش مكتوبة في كود الـ Template.
        
    - `Incremental`: بيسيب الموارد القديمة زي ما هي ويضيف/يعدل الجديد بس.
        
- **حماية الأسرار (Secrets):** الطريقة الوحيدة المدعومة لتمرير الباسووردات في الـ ARM هي حفظها كـ Secret في `Azure Key Vault` وإعطاء صلاحية للـ Resource Manager لقرائتها.
    
- **الصور الجاهزة:** في الـ ARM Template، خصائص الـ publisher والـ sku توجد داخل بلوك `imageReference`.
    

### 5️⃣ النسخ الاحتياطي (Backup & Restore)

- **شروط الـ RSV:** لعمل Backup لمكنة في Recovery Services Vault، الشرط الوحيد الإلزامي هو **تطابق الـ Region**. (الـ Resource Group والـ OS لا يهمان).
    
- **حالة الـ Warning في الـ Pre-check:** تعني وجود مشكلة في إعدادات المكنة (مثل عدم تسطيب أحدث إصدار من الـ VM Agent) مما قد يؤدي لفشل متقطع.
    
- **الاسترجاع (Restore):**
    
    - _File-level:_ ينزل سكريبت (Executable) يركب كقرص (iSCSI Mount) ويمكن تشغيله على أي جهاز מתאים.
        
    - _Full VM:_ يتيح إما إنشاء مكنة جديدة أو استبدال المكنة الأصلية (Replace existing). **لا يمكن** الكتابة فوق مكنة وهمية أخرى (Overwrite).
        

### 6️⃣ المراقبة والتنبيهات (Monitoring & Limits)

- **Azure Budgets:** للمراقبة وإرسال التنبيهات فقط، ولا تأخذ أي Action للإيقاف أو الحذف مهما تعديت الميزانية.
    
- **التحقق من الدومين:** نستخدم دائماً سجل `TXT` للتحقق من ملكية الدومين دون إيقاف أو التأثير على الترافيك المباشر.
    
- **حدود الـ Action Groups:**
    
    - الإيميلات: بحد أقصى **100 إيميل / ساعة** لكل Action Group.
        
    - الـ SMS: بحد أقصى **1 رسالة / 5 دقائق** (12 في الساعة).
        
    - المكالمات الصوتية (Voice calls): بحد أقصى **1 مكالمة / 5 دقائق** (12 في الساعة).