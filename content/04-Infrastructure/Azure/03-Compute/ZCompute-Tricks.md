الأجهزة الوهمية في حالة `Stopped (Deallocated)` تقوم بتحرير الموارد ولا تستهلك أي رصيد من حصة الـ vCPU Quota الخاصة بالاشتراك. الأجهزة في حالة `Running` أو `Stopped (Allocated)` فقط هي التي تستهلك الرصيد


للسماح للمستخدمين عبر الإنترنت بالوصول لتطبيقات تعمل داخل (AKS)، يجب توجيه سجلات الـ (DNS) العامة إلى عنوان الـ (Load Balancer front end) أو الـ (Ingress Controller). لا يتم أبداً توجيه الـ DNS الخارجي إلى العناوين الداخلية مثل (Cluster nodes) أو (DNS Service) أو (Bridge)


While resizing the VM it must be in a stopped state.


خدمة **Azure Budgets** هي خدمة للمراقبة وإرسال التنبيهات (Monitoring & Alerting) **فقط**. هي مابتخدش أي إجراء (Action) من نفسها عشان تقفل أو تمسح أو توقف أي مورد (VMs) مهما عديت الميزانية.

**إيه هو الـ Programmatic deployment؟ (الفخ)** دي شاشة أو إعداد في أزور بنستخدمه عشان **نوافق على الشروط والأحكام (Terms of Service)** الخاصة بالمنتجات اللي بتنزلها شركات تانية (Third-party Marketplace Images) زي فايرووول مثلاً أو صورة لينكس مخصصة من شركة معينة.


- **قاعدة أزور:** الحد الأقصى للإيميلات هو **100 إيميل في الساعة** لكل Action Group.
- **قاعدة أزور:** الحد الأقصى لرسائل الـ SMS هو **رسالة واحدة فقط كل 5 دقائق**. (يعني بحد أقصى 12 رسالة في الساعة).
- **قاعدة أزور:** الحد الأقصى للفويس كولز هو **مكالمه واحدة فقط كل 5 دقائق**. (يعني بحد أقصى 12 VC في الساعة).

لإجراء نسخ احتياطي (Backup) لجهاز وهمي (VM) داخل Recovery Services Vault، الشرط الوحيد الإلزامي هو تطابق الـ **Region**. اختلاف الـ Resource Group أو نظام التشغيل (Windows/Linux) لا يمنع عملية النسخ الاحتياطي


**💡 زتونة الـ AKS Autoscaling في الكشكول:** "لإدارة الـ **Cluster Autoscaler** (توسيع العُقد/Nodes)، نستخدم أدوات أزور الأساسية: **Azure Portal** أو **Azure CLI (`az aks`)**. أما لإدارة الـ **Horizontal Pod Autoscaler (HPA)** (توسيع الحاويات/Pods)، نستخدم أمر **`kubectl`** الخاص بكوبرنيتز."


The Linux Diagnostic Extension should be used which downloads the Diagnostic Extension (LAD) agent on Linux server.

**DSC extension:** دي أداة بتعدل في إعدادات نظام التشغيل الداخلي (زي إنها تسطب برنامج أو تظبط ريجستري)، ملهاش أي علاقة بتكبير الهاردوير بتاع المكنة من بره.

منصة Azure لا تدعم صيغة الأقراص `.vhdx` نهائياً؛ هي تدعم فقط صيغة **`.vhd`** (وأن تكون من نوع Fixed Size). لكي تتمكن من رفع هذا القرص واستخدامه كـ Template لإنشاء أجهزة وهمية جديدة في أزور، الخُطوة الإجبارية الأولى هي تعديل القرص وتحويله من VHDX إلى VHD (يتم ذلك عادةً باستخدام أمر PowerShell `Convert-VHD` أو من خلال واجهة Hyper-V Manager).


 - **File Recovery (Item-level):** يتم عن طريق تحميل سكريبت (Executable) يقوم بتركيب النسخة الاحتياطية كقرص محلي (iSCSI Mount). يمكن تشغيل هذا السكريبت على **أي جهاز متصل بالإنترنت** يحمل نظام تشغيل متوافق.
 - **Full VM Restore:** عند استرجاع آلة افتراضية بالكامل، يتيح أزور خيارين فقط: استبدال الآلة الأصلية (Replace existing VM) أو إنشاء آلة جديدة (Create new VM). لا يمكن الكتابة فوق آلة افتراضية (مثل VM2).

You plan to back up an Azure virtual machine named VM1.
You discover that the Backup Pre-Check status displays a status of Warning.
What is a possible cause of the Warning status?

The Warning state indicates one or more issues in VM’s configuration that might lead to backup failures and
provides recommended steps to ensure successful backups. Not having the latest VM Agent installed, for
example, can cause backups to fail intermittently and falls in this class of issues.

لضمان أقصى توافرية (Maximum Availability) للأجهزة الوهمية داخل Availability Set، يجب دائماً اختيار الحدود القصوى التي يدعمها أزور لتوزيع الأحمال:
**الحد الأقصى لـ Fault Domains (نطاقات الأعطال):** هو **3**.
**الحد الأقصى لـ Update Domains (نطاقات التحديث):** هو **20**.

To deploy a YAML file, the command is:
kubectl apply -f <file_name>.yaml

خلي بالك من الفرق: Microsoft Monitoring Agent on VM1, and not the Microsoft Monitoring Agent VM extension.


القاعدة الحديدية في شبكات أزور بتقول إن كارت الشبكة بتاع المكنة (NIC) والشبكة الافتراضية (VNet) اللي هيتربط بيها **لازم يكونوا في نفس المنطقة الجغرافية (Region) بالظبط**.

- المكان الوحيد المدعوم لتخزين النسخ الاحتياطية للـ App Service هو (Azure Storage Account)، ولا يتم استخدام أي نوع من الـ Vaults.
    
- لاستثناء ملفات أو مجلدات معينة من النسخة الاحتياطية، بننشئ ملف نصي باسم `_backup.filter` في مسار التطبيق.

عند استخدام صورة نظام تشغيل جاهزة من أزور (زي Windows Server أو Ubuntu)، يتم وضع معلومات الناشر (publisher) والنسخة (sku) داخل بلوك يسمى `imageReference`.



- **Windows Node Pools:** تتطلب دائماً وأبداً تغيير إعداد الشبكة إلى **Azure CNI**. (لا تدعم Kubenet).
    
- **ACR Integration:** أسهل وأأمن طريقة لربط الـ AKS بمستودع الـ ACR هي استخدام **Managed Identity**، ويتم ذلك عن طريق تغيير إعداد الـ **Authentication method**.

**"Max surge controls how many additional nodes AKS creates during an upgrade, beyond the current node count."**



كوماند **Kubectl:** يُستخدم لإدارة كائنات كوبرنيتيز الداخلية (تطبيق ونشر ملفات YAML، إنشاء الـ Pods، إعداد الـ HPA، قراءة الـ Logs).

Remove all the existing resources from RG1 before deploying the new resources.
-Mode
Specifies the deployment mode. The acceptable values for this parameter are:
* Complete: In complete mode, Resource Manager deletes resources that exist in the resource group but are
not specified in the template.
* Incremental: In incremental mode, Resource Manager leaves unchanged resources that exist in the resource
group but are not specified in the template.
Incorrect:
* All
No mode named all.


 **المجموعات متعددة الحاويات (Multi-container groups) تدعم نظام تشغيل Linux فقط.**
 