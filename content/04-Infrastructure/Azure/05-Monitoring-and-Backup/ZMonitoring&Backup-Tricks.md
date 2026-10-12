### 1. Azure Backup & Recovery (النسخ الاحتياطي والاسترداد)

- **قاعدة النطاق الجغرافي (Region Restriction):** الخزنة (Vault) لا تستطيع إجراء نسخ احتياطي أو حماية أي مورد إلا إذا كان هذا المورد يقع في نفس المنطقة الجغرافية (Region) للخزنة.
    
      
    
- **خزنة Recovery Services Vault:**
    
      
    - عندما تقوم خدمة Azure Backup بأخذ نسخة احتياطية من جهاز ظاهري (VM)، فإنها لا تضع الملفات في Storage account عادي يمكن تصفحه. بدلاً من ذلك، تقوم بتخزينها داخل "خزنة أمنية متخصصة ومقفلة" تسمى `Recovery Services vault`.
        
          
        
    - هذه الخزنة هي مساحة تخزين دائمة (Persistent Storage) تديرها Azure بالكامل لتوفير حماية ضد برامج الفدية (Ransomware) ومنع الحذف العرضي (هنا يأتي دور ميزة Resource Guard).
        
          
        
- **Backup Policy (سياسة النسخ الاحتياطي):** في Azure، يتم تجميع الجداول الزمنية (Schedule) وفترات الاحتفاظ (Retention) معاً في كائن يسمى `Backup policy`. بمجرد ربط هذه السياسة بالجهاز الظاهري، تقوم Azure بتنفيذها تلقائياً. _(ملاحظة صحيحة: الـ Recovery plan تخص خدمة Site Recovery وليست الـ Backup)._
    
      
    
- **تطابق شروط الاحتفاظ (Retention Rules Overlap):** إذا تطابقت نقطة نسخ احتياطي (Backup point) مع أكثر من شرط في سياسة الاحتفاظ في نفس الوقت (مثلاً: حققت شرط النسخ اليومي والأسبوعي والشهري معاً)، فإن Azure يحتفظ بهذه النسخة بناءً على أطول فترة زمنية متطابقة.
    
      
    
- **التشفير (Encryption) والنسخ الاحتياطي:** السياسات القياسية (Standard policies) تدعم بشكل كامل إجراء نسخ احتياطي للأجهزة الظاهرية المشفرة باستخدام Azure Disk Encryption (ADE).
    
      
    
- **Trusted Launch والنسخ الاحتياطي:** تشترط Azure هندسياً أن أي جهاز ظاهري يستخدم ميزة `Trusted launch` يجب أن يستخدم سياسة نسخ احتياطي من النوع المحسّن (`Enhanced policy`). السياسات القياسية (`Standard policy`) غير مدعومة في هذه الحالة.
    
      
    

### 2. أنواع الخزائن (Vault Types): Backup Vault vs. Recovery Services Vault

- **Azure File Shares (ملفات الشبكة):** عملية أخذ نسخة احتياطية لـ Azure File Shares هي ميزة مدمجة في الخزنة التقليدية `Recovery Services vault`.
    
      
    
- **Azure Blobs (ملفات التخزين):** النسخ الاحتياطي للـ Blobs (سواء Operational أو Vaulted) يعتمد حصرياً على النوع الأحدث من الخزائن وهو `Backup vault`. الخزنة القديمة `Recovery Services vault` لا تدعم الـ Blobs.
    
      
    
- **القاعدة الذهبية لأنواع الخزائن:**
    
      
    - **المورد:** Blobs، أو Managed Disks، أو قواعد بيانات PostgreSQL ⬅️ الإجابة: **Backup vault**.
        
          
        
    - **المورد:** أجهزة ظاهرية (VMs)، أو Azure File Shares، أو قواعد بيانات SQL على Azure VM ⬅️ الإجابة: **Recovery Services vault**.
        
          
        

### 3. استرداد الأجهزة الظاهرية (VM Restore Scenarios)

- **سيناريو تقليل وقت التوقف (Minimize Downtime) والـ Instant Snapshots:**
    
      
    - **اللقطات السريعة (Instant Snapshots):** يتم الاحتفاظ بها عادة لمدة تصل إلى 5 أيام (كإعداد افتراضي في السياسة القياسية). إذا كنت بحاجة لاسترجاع بيانات عمرها 8 أيام، فإن اللقطة السريعة قد حُذفت، والنسخة المتاحة الآن موجودة داخل الخزنة نفسها (Vault Standard tier).
        
          
        
    - الاسترجاع من الـ Vault يستغرق وقتاً أطول (لفك الضغط ونقل البيانات) مقارنة بالاسترجاع من الـ Snapshot السريع.
        
          
        
    - **تقليل وقت التوقف (Create new vs. Replace existing):**
        
          
        - **Replace existing:** سيقوم Azure بإيقاف تشغيل الجهاز الظاهري (Deallocate)، مما يعني توقف الخدمة بالكامل حتى تنتهي عملية الاسترجاع وكتابة البيانات على الأقراص القديمة.
            
              
            
        - **Create new (الخيار الأفضل لتقليل التوقف):** سيقوم Azure ببناء أجهزة أو أقراص جديدة في الخلفية باستخدام النسخة الاحتياطية، بينما يبقى الجهاز الأصلي يعمل ويخدم العملاء. بمجرد جاهزية الجهاز الجديد، يتم تحويل حركة المرور إليه (مثلاً عبر تعديل الـ Public IP).
            
              
            

### 4. Azure Monitor (المراقبة، السجلات، المقاييس والتنبيهات)

- **المقاييس (Metrics) مقابل السجلات (Logs):**
    
      
    - **المقاييس (Metrics):** إذا كان الهدف مراقبة استهلاك وحدة المعالجة المركزية (CPU > 80%)، فإن المورد الهدف (Target resource) هو الـ `Virtual machine` نفسها، لأن المقاييس أرقام تُبث حية من الجهاز الظاهري.
        
          
        
    - **السجلات (Logs):** إذا كان الهدف مراقبة حدث أو خطأ في ملفات سجلات النظام (System event log)، فلا يمكن إعداد تنبيه يعتمد على البحث النصي مباشرة على الـ VM. السجلات يجب أن تُرسل أولاً إلى وجهة مثل `Log Analytics workspace`، ثم يتم إنشاء التنبيه بناءً على استعلام السجلات (Log query).
        
          
        
- **Data Collection Rules (DCR) و Diagnostic Settings:**
    
      
    - **Data Collection Rules (DCR):** تستخدم كـ "تعليمات" لـ `Azure Monitor Agent (AMA)` المثبت داخل الأجهزة الظاهرية (VMs) لجمع سجلات النظام (مثل Event logs أو Syslog). الوجهة القياسية للـ DCR هي `Log Analytics workspace`. _(ملاحظة: يمكنك توجيهها إلى Storage account عبر Diagnostic settings، ولكن DCR مع الـ AMA تتجه أساساً إلى الـ Workspace للتحليل)._
        
          
        
    - **Diagnostic Settings:** تستخدم للموارد التي تُدار كخدمات منصة (PaaS) مثل Storage accounts أو قواعد البيانات (DBs). لا تتطلب وكيل (Agent) وتُرسل السجلات إلى Log Analytics workspace، أو Storage account، أو Event Hub.
        
          
        
- **XPath و Data Collection Rule (DCR):**
    
      
    - **XPath:** لغة استعلام لفلترة بيانات XML. وبما أن سجلات أحداث Windows (Windows Event Logs) تُحفظ وتُعالج بصيغة XML، فإن Azure يستخدم استعلامات XPath داخل الـ DCR.
        
          
        
    - **الهدف:** بدلاً من إرسال كل السجلات وتحمل تكاليف عالية، يستخدم `Azure Monitor Agent (AMA)` استعلامات XPath (Source Filtering) المحددة في الـ DCR لتصفية السجلات داخلياً قبل إرسالها إلى الـ Log Analytics Workspace.
        
          
        
- **Azure Application Insights Profiler:**
    
      
    - وظيفة الـ Profiler: يجمع "مسارات الأداء" (`Performance Traces`) لاكتشاف "عنق الزجاجة" (Bottlenecks) في التعليمات البرمجية داخل خدمات مثل App Service.
        
          
        
    - يحلل الكود العميق ليوضح الدوال (Functions) أو الاستعلامات (Queries) التي تتسبب في بطء التطبيق، ويعمل في الخلفية بتأثير منخفض (Low Overhead).
        
          
        
    - **قاعدة الامتحان:** جمل "collect performance traces" أو "identify code-level performance bottlenecks" لـ App Service ⬅️ تعني **Application Insights Profiler**.
        
          
        
- **Alert Processing Rules (Action Groups):**
    
      
    - **توارث النطاق (Scope Inheritance):** التنبيه المُعد على مستوى مجموعة الموارد (Resource Group) يراقب تلقائياً جميع الموارد بداخلها. التنبيه المُعد على مورد معين يراقب هذا المورد فقط.
        
          
        
    - **قاعدة الـ Suppress:** الإجراء `Suppress` في (Alert Processing Rules) يقوم بوقف "رد الفعل" أو الإجراء (Action) (مثل إرسال البريد الإلكتروني)، ولكنه **لا يمنع** التنبيه نفسه من التولد والظهور في واجهة Azure Portal.
        
          
        

### 5. الشبكات والتطبيقات (Networking & App Service)

- **Service Endpoints:** يتم تفعيل الـ Service Endpoint على الشبكة الفرعية (Subnet) لـ "نوع الخدمة" بالكامل (مثلاً `Microsoft.Storage`)، وليس لكل مورد فردي (كحساب تخزين معين).
    
      
    
- **Custom Domain و Web App:**
    
      
    - **قاعدة الامتحان:** لربط نطاق مخصص (Custom Domain) بتطبيق ويب، الخطوة الأولى عادةً تتضمن التحقق من الملكية باستخدام **TXT record** يحتوي على **asuid** أو **Verification ID**.
        
          
        
- **Network Security Group (NSG) Flow Logs:**
    
      
    - **قاعدة الامتحان:** المصطلحات "Suspicious traffic" أو "Identify malicious IP addresses" أو "Traffic Analytics" ⬅️ الإجابة تتضمن تفعيل **NSG Flow Logs**.
        
          
        
    - **وجهة البيانات:** تحتاج الـ NSG Flow Logs إلى وجهة لتخزين البيانات الخام، والمكان الوحيد المدعوم لها هو حساب التخزين (**Storage account**).
        
