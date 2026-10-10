
### 1. الأجهزة الوهمية (VMs) والـ Compute

- **نقل الـ VMs:** مينفعش تنقل المكنة لوحدها. لازم تنقل معاها الموارد المرتبطة بيها (الـ Disks والـ NIC). وبالنسبة للشبكة (VNet)، لازم تكون هي كمان في نفس الاشتراك الوجهة أو تنقلها معاهم.
    
      
    
- **نقل المكن لـ Availability Zone:** لو هتستخدم Azure Site Recovery (ASR) لنقل المكن لـ Zone تانية، لازم وحتماً المكن ده يكون شغال بـ **Managed Disks**.
    
      
    
- **أداة DSC Extension (للويندوز):** عشان تشتغل صح، لازم المكنة تكون (Started) وشغالة، ولازم يكون عندها اتصال بأزور (تقدر تطلع إنترنت أو تكلم خدمات أزور).
    
      
    
- **كارت الشبكة (NIC):** مستحيل يتكريت في الهواء؛ لازم يتربط بـ VNet موجودة بالفعل، ولازم يكونوا هم الاتنين في **نفس الـ Region**. (الـ Public IP والـ Route Table اختياريين للـ NIC).
    
      
    

### 2. موازنة الأحمال (Load Balancers - LB)

- **الفرق الجوهري (Basic vs Standard):**
    
      
    - **Basic:** قديم، مقيد (المكن لازم يكون في نفس الـ Availability Set/VMSS)، الأمان فيه مفتوح (مش محتاج NSG)، بيدعم TCP/HTTP بس، ومبيدعمش الـ HA Ports.
        
          
        
    - **Standard (الموصى به):** مرن (أي مكن جوه نفس الـ VNet)، مقفول أمنياً (Secure by Default - إلزامي تستخدم NSG وإلا الترافيك هيقع)، بيدعم TCP/HTTP/HTTPS، وبيدعم **HA Ports** (أساسي لعمل أجهزة الفايروول/NVAsแบบ Active-Active).
        
          
        
- **توافق الـ Public IP:** أزور بيرفض خلط الفئات. Standard LB يطلب Standard Public IP (ولازم يكون Static). و Basic LB يطلب Basic IP.
    
      
    
- **قاعدة التوجيه (Explicit Routing):** الـ LB مش بيشتغل بالنيابة عنك. لو حذفت قاعدة التوجيه (Rule)، الترافيك هيقع (Drop).
    
      
    
- **Health Probes:** لو الـ LB مضبوط يعمل Probe لملف `Probe1.htm` على بورت 80، والملف موجود وبيرد بـ 200 OK، الـ LB هيعتبر المكنة Healthy ويوزع عليها الترافيك فوراً.
    
      
    
- **Gateway LB:** ده فئة تالتة مخصصة فقط لسيناريو الـ (Transparent NVAs / Bump-in-the-wire).
    
      
    

### 3. الشبكات (Networking) والتوجيه (Routing)

- **حالة الـ Disconnected في الـ Peering:** لو مسحت الـ Peering من طرف واحد، الطرف التاني بيقع في حالة Disconnected. **الحل الإجباري:** لازم تحذف الرابط المعطوب ده، وتكريت Peering جديد من الصفر بين الطرفين.
    
      
    
- **الـ Global VNet Peering:** تقدر تربط شبكات عبر مناطق جغرافية (Regions) أو اشتراكات (Subscriptions) مختلفة. لكن **ممنوع** الربط بين السحابة العامة (Azure Public) والسحابة الحكومية (Azure Government).
    
      
    
- **الـ Route Tables (UDRs):** جداول التوجيه بتتربط **بالـ Subnet فقط**. مستحيل تربط Route Table بـ VNet كاملة أو بـ NIC بشكل مباشر.
    
      
    
- **تغيير الـ DNS:** لو عدلت إعدادات الـ DNS على مستوى الـ VNet أو الـ NIC، المكن مش هيحس بالتغيير إلا لما تعمله **Restart**.
    
      
    

### 4. الـ DNS

- **التسجيل التلقائي (Auto-registration):** حصري للـ **Private DNS Zones**. الـ Public DNS بيحتاج تسجيل يدوي أو API.
    
      
    
- **قاعدة الـ DNS Suffix:** أزور ملوش دعوة بإعدادات الـ DNS Suffix اللي جوه نظام التشغيل (زي Contoso أو None). أزور بياخد اسم المكنة من إعدادات الـ NIC على المنصة (Platform-level) ويسجله فوراً في الـ Private Zone.
    
      
    
- **Registration vs Resolution:** الشبكة الوهمية مسموح ليها تكون (Registration VNet) لمنطقة DNS واحدة فقط. لكن تقدر تكون (Resolution VNet) وتستعلم من مناطق كتير جداً.
    
      
    
- **PTR Record:** مخصص للـ Reverse Lookup (تحويل الـ IP لاسم)، ولازم يتحط في Reverse Lookup Zone مش Forward Zone.
    
      
    

### 5. بوابات الاتصال (VPN & ExpressRoute) و Bastion

- **الـ GatewaySubnet:** إلزامي لخدمات (Site-to-Site, Point-to-Site, ExpressRoute, VNet-to-VNet). البوابة بتاخد الآيبيهات بتاعتها من الـ Subnet دي. **مبيستخدمش نهائياً** في الـ VNet Peering أو الـ Service/Private Endpoints.
    
      
    
- **Route-based vs Policy-based VPN:**
    
      
    - **Route-based:** هو الأساس (بيدعم P2S، التعايش Coexistence مع ExpressRoute، التوجيه BGP، الـ Active-Active، وأكتر من فرع Multi-site).
        
          
        
    - **Policy-based:** تكنولوجيا قديمة، بتدعم IKEv1، وآخرها اتصال Site-to-Site **واحد فقط**.
        
          
        
- **الـ Point-to-Site (P2S):** لو شغال بـ Certificate، الحل إنك تعمل Export من جهاز وتنزلها (Install) على الجهاز التاني. لو السيناريو فيه Azure AD مع Certificates، اختار **No** (الـ Entra ID مبيستخدمش شهادات للـ P2S).
    
      
    
- **Azure Bastion:**
    
      
    - الـ Subnet لازم يكون اسمها الإجباري: `AzureBastionSubnet`.
        
          
        
    - حجمها: الحد الأدنى حالياً `/26` (ولو السؤال قديم وملقيتش غير `/27` اختارها كحد أدنى).
        
          
        
    - الـ IP: لازم يكون Standard, Regional, Static.
        
          
        
    - الـ Peering: لو عندك 10 شبكات مربوطين ببعض (Peered)، هتحتاج **Bastion واحد فقط** لخدمتهم كلهم.
        
          
        

### 6. الأمان (Security & NSG)

- **الـ NSG (Subnet vs NIC) ⚠️ (تصحيح مهم):** المقولة بتاعت "الـ NIC دايماً كلمته بتمشي" مش دقيقة هندسياً. التقييم بيتم بالتسلسل؛ لو الـ Subnet عملت Allow والـ NIC عملت Deny، الترافيك هيقع. ولو الـ NIC عملت Allow والـ Subnet عملت Deny، الترافيك برضه هيقع. **القاعدة:** أي تقاطع يؤدي لـ Deny بيكسب.
    
      
    
- **إضافة مكنة لـ ASG:** بيتم دايماً من خلال ربط كارت الشبكة (NIC) بالـ ASG، وليس الـ VM كـ Compute.
    
      
    
- **ASG vs Service Tag:** الـ ASG بتديره إنت عشان تجمع مكنك (Micro-segmentation). الـ Service Tag بيديره أزور عشان يجمع أيبيهات خدماته (زي الـ Storage أو SQL).
    
      
    
- **Service Endpoints vs Policies:** الـ Endpoint بيفتح طريق آمن للخدمة. الـ Policy بتقيد الوصول لحسابات Storage معينة ولازم تتطبق في **نفس الـ Region** بتاعة الشبكة.
    
      
    
- **غلق بورت وقت الإنشاء أوتوماتيك:** استخدام Custom Azure Policy بتأثير **Append** هو الحل الوحيد لإجبار إضافة Deny Rule جوه أي NSG جديد.
    
      
    

### 7. أدوات المراقبة (Network Watcher)

- **IP Flow Verify:** بتأكد "إيه قاعدة الـ NSG اللي بتمنع أو بتسمح لباكت معينة إنها توصل؟".
    
      
    
- **Connection Monitor:** لمراقبة جودة الاتصال باستمرار (قياس RTT، نسبة الـ Packet Loss). الأداة دي **Region-specific**، يعني بتحتاج Connection monitor لكل Region.
    
      
    
- **Connection Troubleshoot:** فحص الاتصال الفعلي End-to-End من مكنة لهدف خارجي.
    
      
    
- **Packet Capture:** فحص "محتوى" الترافيك الفعلي اللي داخل وطالع (تحليل عميق).
    
      
    
- **Next Hop:** لتتبع مشكلة في التوجيه ومعرفة الترافيك رايح لمين الخطوة الجاية (Router, VPN, Internet).
    
      
    
- **NSG Flow Logs & Traffic Analytics:** الـ Flow logs بتسجل الريكوردات خام، والـ Traffic Analytics بتاخدها تحللها وتطلع رسومات بيانية ذكية توضح استهلاك الأيبيهات والتهديدات.
    
      
    

### 8. التعافي من الكوارث (ASR) والتخزين (Storage & Backup)

- **ASR Failover Subnet:** أزور بيدور على Subnet في الشبكة الوجهة ليها **نفس الاسم بالضبط**. لو ملقاش، بيوصل المكنة بـ **أول Subnet متاح** يقابله في الترتيب (مش بيبص على تطابق الآيبيهات).
    
      
    
- **Multi-user Authorization (MUA):** لحماية الـ Recovery Services Vault، لازم تكريت مورد اسمه **Resource Guard** كأول خطوة.
    
      
    

### 9. الحاويات (Containers) و AKS

- **Azure Container Instances (ACI):** ميزة الـ Private Networking (نشر الحاوية جوه VNet) مدعومة حاويات **Linux فقط**. في الـ Windows بتكون مخفية أو غير مدعومة.
    
      
    
- **Azure Container Apps (ACA):** بتشترط وجود Subnet مخصصة ليها حجمها كحد أدنى **`/23`**.
    
      
    
- **AKS Network Policies:** سياسات الـ Azure Network Policy بتشتغل مع Azure CNI فقط. أما الـ Calico بيشتغل مع Azure CNI و kubenet.