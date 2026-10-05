### 1. أدوات الإدارة والنقل (AzCopy, Storage Explorer, Import/Export)

- ال **AzCopy:**
   
    - **الوجهات المدعومة:** Blob، File Share، و ADLS Gen2.
    - **المصادقة (Authentication):** لنقل البيانات إلى Blob أو ADLS يفضل استخدام (Entra ID أو SAS Token). لنقل البيانات إلى File Share يُستخدم (SAS Token). الأداة لا تدعم استخدام الـ Access Keys مباشرة.
    - **الأوامر:** `azcopy make` لإنشاء (Container/Share) فارغ. `azcopy copy` لنقل البيانات.
    - **دعم الأنظمة:** تعمل على (Windows, Linux, macOS) **ولا تعمل على Android**.

- ال **Azure Storage Explorer:** 
- أداة لإدارة الـ Data Plane (رفع ملفات، إنشاء حاويات). **لا يمكنها** إنشاء الـ Storage Account نفسه لأن ذلك من اختصاص الـ Management Plane.

- ال **Azure Import/Export:**
- تدعم الاستيراد (Import) إلى Blob و File Share، لكنها تدعم التصدير (Export) من Blob **فقط**.

### 2. معمارية الحسابات وطبقات التخزين (Accounts, Tiers & Replication)

- **الأجيال والخدمات:** حسابات (GPv1) و (GPv2) تدعم الخدمات الأربعة (Blob, File, Queue, Table).

- **حسابات الـ Premium:** حسابات (BlockBlobStorage) و (FileStorage) مرتبطة دائماً بأداء Premium ولا يمكن إنشاؤها كـ Standard.

- **الترقية (Upgrade):** الجيل الأول (GPv1) لا يدعم تكرار (ZRS) ولا طبقات (Access Tiers). لاستخدامها يجب الترقية إلى (GPv2) أولاً.
 
- **طبقات الوصول (Access Tiers):** (Hot/Cool/Archive) مدعومة في (GPv2) و (BlobStorage). **تصحيح هام:** حسابات (BlockBlobStorage - Premium) **لا تدعم** طبقة الـ Archive. وطبعاً الـ Tiers غير مدعومة نهائياً في (FileStorage) أو (GPv1).

- **دورة الحياة (Lifecycle Management):** تعمل القواعد مع الـ Blobs فقط لنقل البيانات بين الطبقات أو حذفها أوتوماتيكياً.

- **التخزين الدائم:** خدمة (Azure Files) هي الـ (Shared Persistent Storage) لأكثر من سيرفر أو حاوية.

- ال **Object Replication:** مدعومة فقط في حسابات (GPv2) وحسابات (Premium Block Blob).

- **قيود طبقة Archive:** مدعومة فقط مع التكرار الذي لا يعتمد على الـ Zones، أي (LRS, GRS, RA-GRS) فقط.

### 3. الأمان، التشفير والمصادقة (Security, Encryption & RBAC)

- **الـ Access Keys:** هي الباسوورد الماستر للحساب وتمنحك Full Permission. لديك مفتاحان (key1, key2) للـ Rotation. لا تشاركها، واستخدمها فقط لتوليد SAS Tokens أو للربط المباشر في حالات ضيقة.

- **تشفير البيانات (Data at Rest):**
  
    - ال **MMK (Microsoft-managed keys):** الافتراضي والمجاني. مايكروسوفت تدير المفتاح وتشفره وتعمل له Rotation.

    - ال **CMK (Customer-managed keys):** العميل ينشئ المفتاح ويحفظه في Azure Key Vault. الحساب يستلف المفتاح للتشفير، وإذا تم تعطيله من الـ Vault، تتوقف قراءة البيانات.

    - **ال CPK (Customer-provided keys):** للـ Blobs فقط. العميل يحتفظ بالمفتاح On-Premises ويرسله مع كل API Request. أزور يشفر به البيانات ويمسحه من الذاكرة فوراً.
        
          
        
- ال **Azure Disk Encryption (ADE):**  الحل الهندسي الوحيد للحفاظ على التشفير إذا تم تنزيل (Download/Export) الـ VHD خارج أزور، لأنه يعتمد على (BitLocker/DM-Crypt) داخل نظام التشغيل.

- ال **Encryption Scope:** يطبق على مستوى الـ Container أو الـ Blob الفردي.

- **قيود الـ SAS Token:** للسماح للمستخدم بتنزيل ملف باسمه فقط دون استعراض الحاوية، امنحه (Read) على مستوى (Object). وللسماح له بالاستعراض والتنزيل معاً، امنحه (Read + List) على مستوى (Object + Container).

- ال **RBAC Conditions (ABAC):** إضافة شروط على الصلاحيات مدعومة حصرياً لخدمات (Containers) و (Queues) فقط.

- **حدود الحاويات:** الحد الأقصى للـ (Stored access policies) هو 5 لكل حاوية. الحد الأقصى لسياسات الـ (Immutability) هو 2 (واحدة Time-based + واحدة Legal hold).

### 4. الشبكات، المزامنة والنسخ الاحتياطي (Networking, Sync & Backup)

- **تحويل الحساب إلى ZRS:** يجب أن يكون (GPv2) و(Standard), إذا كان الحساب (RA-GRS)، يجب تحويله أولاً إلى (LRS) أو (GRS) كخطوة وسيطة قبل الـ ZRS. حسابات الـ Premium لا تدعم التغيير المباشر (Live Migration).

- ال **Azure File Sync:** الـ (Sync Group) تحتاج Cloud Endpoint واحدة، وعدد غير محدود من الـ Server Endpoints. يجب أن يكون الـ Storage Account والـ Storage Sync Service في نفس الـ Region.
    
    
- ال **Azure Backup:**
      
    - لعمل تقارير: الـ Storage Account يجب أن يكون في نفس منطقة الـ (Vault)، بينما الـ (Log Analytics Workspace) يمكن أن يكون في أي منطقة.  
        
    - لنقل مسار النسخ لـ VM إلى Vault آخر، يجب عمل (Stop backup) على القديم أولاً.

- ال **SMB Multichannel:** مدعومة فقط في حسابات (Premium File Shares) لتسريع النقل.

### 5. معلومات متفرقة وهامة للامتحان

- ال **Azure Container Registry (ACR):** ميزة الـ (Geo-replication) متاحة **فقط** في الـ Premium Tier.

- ال **Azure Container Instances (ACI):** **تصحيح هام:** عمل (Volume mount) لـ Azure Files مدعوم على حاويات **Linux و Windows** معاً (ليس Linux فقط كما ذكرت).

- ال **Deny Assignments & Blueprints:** أقفال الموارد (Resource Locks) لا تمنع الـ Owner من حذفها إذا قام بفك القفل. الطريقة الوحيدة لفرض (Deny Assignment) يمنع حتى الـ Owner من التعديل هي عبر **Azure Blueprints**، وتُطبق فقط عند إنشاء المورد الجديد (وليس الموارد الحالية).