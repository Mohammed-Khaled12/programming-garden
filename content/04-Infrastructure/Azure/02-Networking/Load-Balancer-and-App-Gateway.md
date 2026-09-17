# Azure Load Balancer

عندك ويبسايت مهم، وعملتله **3 VMs** بدل واحدة (عشان لو واحدة وقعت، الباقي يكمل High Availability)
لكن السؤال: **المستخدم هيوصل لأنهي VM من الـ3 بالظبط؟** لو كل مستخدم بيتوصل بـ IP معين لـ VM بعينها، مفيش توزيع حمل حقيقي، ولو الـ VM دي وقعت، اللي متوصلين بيها بس هما اللي بيتأثروا.

ال**Load Balancer** هو **نقطة اتصال واحدة (Single point of contact)** — المستخدم بيتوصل بيها هي بس، وهي اللي بتقرر **داخليًا** تبعته لأنهي VM من الخلف.

الAzure Load Balancer هو ***Layer 4 Load Balancer***

- ال**Public Load Balancer**: عندها **Public IP**، بتستقبل مرور من **الإنترنت** وتوزعه على الـ backend
- ال**Internal Load Balancer**: عندها **Private IP بس**، بتوزع مرور **داخلي** بين موارد جوه نفس الـ VNet

### Main Components 

![[Pasted image 20260915140405.png]]

#### 1. Frontend IP Configuration

العنوان اللي المستخدم بيتوصل بيه (Public أو Private حسب النوع).

#### 2. Backend Pool

مجموعة الـ VMs (أو Virtual Machine Scale Set) اللي المرور هيتوزع عليهم.

#### 3. Health Probes 

هنا بييجي نفس العنوان اللي شفناه قبل كده في موضوع الـ Routes! فاكر `168.63.129.16` اللي قلنا إنه محجوز لبنية أزور الداخلية؟ **ده بالظبط العنوان اللي منه بتيجي طلبات الـ Health Probe** بتاعة الـ Load Balancer.

**إزاي بتشتغل**: الـ Load Balancer بيبعت طلب دوري (TCP, HTTP, أو HTTPS) لكل VM في الـ Backend Pool، عشان يتأكد **هي شغالة كويس ولا لأ**. لو VM معينة معملتش رد صح، بيتحط "Unhealthy" فورًا، والـ Load Balancer **بيوقف يبعتلها أي مرور جديد** — من غير ما يأثر على الاتصالات الموجودة بالفعل.

**نقطة عملية مهمة هتتسأل فيها**: لازم يكون عندك **NSG rule تسمح صراحة بالمرور من `168.63.129.16`** (أو استخدم الـ Service Tag `AzureLoadBalancer` اللي شرحناه قبل كده)، وإلا الـ Health Probe **هتفشل باستمرار**، والـ Load Balancer هيفتكر الـ VM "ميتة" رغم إنها شغالة فعليًا — ده أشهر سبب لمشكلة "Load Balancer مش بيوزع المرور صح".

#### 4. Load Balancing Rules

القاعدة اللي بتربط Frontend IP + Port بـ Backend Pool + Port. مثال: "أي حاجة جاية على بورت 80 من الـ Frontend، وزعها على بورت 80 في الـ Backend Pool."

#### 5. Inbound NAT Rules

مختلفة عن الـ Load Balancing Rules — دي بتوجه **بورت معين** لـ **VM واحدة بعينها**، مش لمجموعة. مثال: "أي حد جاي على بورت 5001، وديه لـ VM1 تحديدًا على بورت 22 (SSH)." مفيدة لو عايز توصل بالـ SSH لـ VM معينة من غير ما تدّيها Public IP خاصة بيها.

### Basic SKU اتقاعدت خالص

زي Public IP بالظبط، **Basic Load Balancer اتقاعدت رسميًا في 30 سبتمبر 2025**. **Standard SKU بقت الخيار الوحيد المتاح للنشر الجديد.**

 ليه Standard أفضل بكتير 

|الميزة|Basic (متقاعدة)|Standard|
|---|---|---|
|**Availability Zones**|مش مدعومة|مدعومة بالكامل — الـ Frontend IP نفسها ممكن تكون Zone-redundant|
|**SLA**|مفيش|**99.99%**|
|**الأمان**|مفتوحة افتراضيًا|**Secure by default** — لازم NSG صريحة تسمح بالمرور|
|**Global VNet Peering**|مش مدعومة|مدعومة|
|**الحد الأقصى للـ Backend Pool**|300|**حتى آلاف الـ instances**|

### الربط بموضوع Default Outbound Access اللي شرحناه

فاكر التحديث الحرج بتاع مارس 2026 (إلغاء الـ default outbound)؟ **Load Balancer نفسها واحدة من الطرق الرسمية التلاتة** اللي بتوفر Outbound connectivity صريحة (مع Public IP وNAT Gateway). لو عندك **Public Load Balancer**، تقدر تظبط **Outbound Rules** صراحة تحدد إزاي الـ VMs في الـ Backend Pool هتعمل SNAT للخروج للإنترنت — استخدام إضافي للـ Load Balancer مش بس للمرور الداخل.

### خوارزمية التوزيع - إزاي بيقرر يبعت لمين

ال Azure Load Balancer بيستخدم **tuple hash 5-** (Source IP, Source Port, Destination IP, Destination Port, Protocol) عشان يحدد أنهي VM هتاخد كل **اتصال (flow)**. **مش Round-robin بسيط** — نفس المستخدم بنفس الاتصال هيفضل يروح لنفس الـ VM طول مدة الاتصال ده، لكن اتصال جديد ممكن يروح لـ VM تانية.

### HA Ports - ميزة متقدمة (Internal Load Balancer بس)

بدل ما تحدد بورت معين لكل قاعدة، **HA Ports** بتوزع **كل البورتات (TCP/UDP) مع بعض بقاعدة واحدة**. الاستخدام الأساسي: **Network Virtual Appliances (NVA)** زي Firewalls افتراضية، عشان تضمن كل أنواع المرور بتعدي عليها من غير ما تكتب قاعدة منفصلة لكل بورت.

# Azure Application Gateway

هو Layer 7 Load Balancer بيفهم الـ HTTP والـ HTTPS. ده معناه إنه بيقدر يقرا الـ HTTP Request نفسه ويقرر هيوجه الترافيك فين بناءً على محتواه.

## Features

- ال**URL Path-Based Routing:** لو عندك موقع، تقدر تخلي الـ requests اللي رايحة لـ `/images` تروح لـ Backend Pool معين (مثلاً VMs مخصصة للصور)، والـ requests اللي رايحة لـ `/video` تروح لـ Pool تاني خالص.
    
- ال**Multiple-Site Routing:** تقدر تستضيف أكتر من Domain (مثلاً `app1.com` و `app2.com`) على نفس الـ Gateway، وهو هيوجه ترافيك كل Domain للـ Backend الصح بتاعه.
    
- ال**SSL/TLS Termination:** الـ Gateway بيستلم الترافيك المشفر (HTTPS)، يعمل له Decryption عنده، ويبعته للـ Backend Servers كـ Unencrypted HTTP. ده بيشيل عبء معالجة التشفير (CPU overhead) من على السيرفرات بتاعتك.
    
- ال**Web Application Firewall (WAF):** إضافة قوية جداً بتخلي الـ Gateway يشتغل كحائط صد (Firewall) متخصص للـ Web. بيحمي الـ Web Apps من الهجمات المشهورة زي SQL Injection و Cross-Site Scripting (XSS) بناءً على قواعد OWASP Core Rule Sets.
    
- ال**Session Affinity (Cookie-based):** بيضمن إن طلبات الـ User تفضل تروح لنفس الـ Backend Server طول فترة الـ Session. ده مهم لو الـ App بتاعك بيسجل الـ State بتاعة اليوزر محلياً على السيرفر.

## Main Components
- ال**Frontend IP:** الـ IP اللي الـ Clients بيكلموه من برة (ممكن يكون Public أو Private).
    
- ال**Listener:** بيفضل "يسمع" الترافيك اللي جاي على Port معين (زي 80 للـ HTTP أو 443 للـ HTTPS).
    
- ال**Routing Rule:** دي القاعدة اللي بتربط الـ Listener بالـ Backend Pool. هي اللي بتقول: "لو الترافيك جاي للـ Listener ده، ورايح للـ Path ده، وديه للـ Pool ده".
    
- ال**Backend Pool:** مجموعة السيرفرات أو الـ Services (زي Azure VMs أو App Services أو حتى IP addresses خارجية) اللي هترد فعلياً على الـ Requests.
## Network Traps & Exam Specifics

### 1. Dedicated Subnet Requirement

الـ Application Gateway **لازم** ينزل في Subnet مخصصة ليه لوحده جوه الـ VNet، اسمها أو بيشار إليها بـ `ApplicationGatewaySubnet`.

- **ممنوع** تحط معاه VMs أو أي Mores تانية في نفس الـ Subnet.
    
- الحجم الموصى بيه للـ Subnet هو `/26` على الأقل لتسمح للـ Scale-out الخاص بالـ Gateway.
    

### 2. NSG Management Ports Rule

عشان Azure تقدر تدير وتعمل Health Check للـ Application Gateway نفسه من الـ Control Plane، لازم الـ Network Security Group (NSG) المربوطة بالـ Subnet تسيب البورتات دي مفتوحة:

- **Ports 65200 - 65535** للـ **v2 SKU**.
    
- Source: الخدمة المحجوزة **`GatewayManager`** (Service Tag).
    

> **تحذير حرج:** لو عملت NSG وقفلت Inbound Traffic على البورتات دي، الـ Application Gateway هيدخل في حالة `Failed State` وتتوقف الإدارة تماماً، وده أشهر سبب لعدم عمل الـ Gateway بعد إنشائه.
