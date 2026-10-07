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