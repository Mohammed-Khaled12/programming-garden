# Azure Public and Private DNS

مفيش حد بيحفظ IP addresses. إنت بتكتب `google.com`، مش `142.250.187.78`. **DNS (Domain Name System)** هو الخدمة اللي بتترجم الاسم للعنوان الفعلي. أزور بيقدملك نوعين مختلفين تمامًا من الخدمة دي، لغرضين مختلفين تمامًا.

## Azure Public DNS - للعالم الخارجي

#### المشكلة اللي بتحلها

تخيل إنك اشتريت دومين باسم `mycompany.com` من شركة زي GoDaddy أو Namecheap. الشركة دي اسمها **Domain Registrar** (مُسجِّل الدومينات).
لكن إنت مش عايز تدير الـ DNS Records (زي الـ `A Record` أو `CNAME`) من عند GoDaddy؛ عايز تديرها من جوه Azure Portal جنب الـ Virtual Machines والـ Load Balancers بتاعتك.
#### الآلية
- بتدخل على Azure وتعمل **Azure Public DNS Zone** باسم `mycompany.com`.
    
- ا Azure بيكريتلك الـ Zone وبيديك **4 Name Servers (NS)** خاصين بـ Microsoft (مثل `ns1-01.azure-dns.com`).
    
- بترجع لـ GoDaddy وتعمل Update للـ NS Records وتحط الـ 4 Name Servers بتوع Azure.
    
- من اللحظة دي، Azure بقيت هي الـ **Authoritative DNS Host** للدومين بتاعك على مستوى كوكب الأرض. أي حد على الإنترنت يكتب `api.mycompany.com` الـ Query هتتحول لـ Azure Public DNS ويديه الـ Public IP.

#### أنواع الـ Records الأساسية

|النوع|الوظيفة|
|---|---|
|**A**|اسم → IPv4 address مباشرة|
|**AAAA**|اسم → IPv6 address|
|**CNAME**|اسم → اسم تاني (alias) — مثلاً `www` → `mohammedapp.com`|
|**MX**|تحديد سيرفرات البريد الإلكتروني بتاعت الدومين|
|**TXT**|بيانات نصية حرة — بتستخدم كتير للتحقق من ملكية الدومين (زي لما شرحنا "custom domain verification" في Entra ID)|
|**NS**|تحديد الـ Name Servers المسؤولة|

### Azure Private DNS - للعالم الداخلي بس

#### المشكلة اللي بتحلها 

جوه الـ Virtual Network (VNet) بتاعتك في Azure، عندك آلاف الـ Resources:

- `vm-app-01` (`10.0.1.4`)
    
- `vm-db-master` (`10.0.2.10`)
    
- `internal-lb` (`10.0.1.100`)
    

مش منطقي تخلي الـ Apps تكلم الـ Databases بالـ IP مباشر (لو الـ IP اتغير السيستم هيقع)، وفي نفس الوقت **ممنوع تكشف الأسماء والـ IPs الداخلية دي للإنترنت** لأسباب أمنية.

وهنا بييجي دور **Azure Private DNS Zone**.

1. ال**The Private :** بتكريت Zone باسم داخلي، زي `corp.internal` أو حتى `mycompany.com`.
    
2. ال**Virtual Network Link (VNet Link):** دي الوصلة اللي بين الـ DNS Zone وبين الـ VNet. الـ Private Zone مبتكونش شايفه أي VNet إلا لما تعمل Link بينهما.
    
3. ال**Auto-Registration:** ميزة خطيرة! لما تفعّل الـ Auto-Registration على الـ VNet Link، أي VM جيدة تكريتها جوه الـ VNet، Azure أوتوماتيك بيكريتلها `A Record` باسم الـ VM جوه الـ Private DNS Zone. ولو مسحت الـ VM، بيتمسح الـ Record أوتوماتيك.
#### الفرق الجوهري عن Public DNS

ال**Private DNS Zone مش مرئية من الإنترنت خالص** — بس الـ VNets اللي **ربطتها (Linked) بيها صراحة** تقدر تحلها. فاكر مبدأ **Deny by default** اللي شرحناه في NSG؟ نفس الفلسفة هنا.

#### Virtual Network Links - الخطوة الإلزامية

زي ما NSG لوحدها ملهاش تأثير لحد ما تربطها بـ Subnet، **Private DNS Zone لوحدها ملهاش تأثير لحد ما تربطها (Link) بـ VNet**. وفيه نوعين من الربط:

**1. Registration Virtual Network (مع Autoregistration)**  
لو فعّلت **Autoregistration** وقت الربط، **أي VM تتعمل جوه الـ VNet دي بتاخد A record تلقائيًا** في الـ Zone، من غير أي تدخل يدوي. لو الـ VM اتقفلت (Deallocated) أو اتمسحت، الـ record بيتشال تلقائيًا كمان.

**2. Resolution Virtual Network (من غير Autoregistration)**  
الـ VNet دي **تقدر تحل** أي record موجود في الـ Zone، لكن **مش بتسجل** مواردها هي فيها تلقائيًا.

**قيد مهم هتتسأل فيه**: **VNet واحدة تقدر تكون Registration Network لـ Private DNS Zone واحدة بس**. لو حاولت تفعّل Autoregistration لنفس الـ VNet في Zone تانية، هيفشل.

### نقطة عملية: On-premises مش بيشوف Private DNS تلقائيًا

لو عندك on-premises متصل بـ VPN/ExpressRoute (فاكرهم؟)، **الأجهزة on-premises مش بتقدر تحل Private DNS Zone records بشكل طبيعي مباشرة**. لازم تعمل **DNS Forwarder** (VM جوه أزور شغالة كـ DNS proxy) يستقبل الطلبات من on-premises ويوجهها لـ Private DNS Zone.

![[Pasted image 20260916020803.png]]
#### Ex on Private DNS
انا لما بربط vnet ب private dns و افعل الاوتو 
هو اوتوماتيك بياخد ips ال VMs كلها و يعمل كده عنده
VM1.DNSZoneName --->IP 
و اي VM جوه ال Vnet تكتب VM1.DNSZoneName ال DNS هيوديها ل VM1 
و ده اسهل من انك تكتب ال IP الطويل

# Public, Private and Service Endpoints

ال PaaS علي Azure زي ال **Storage Account**، أو **Azure SQL Database**، أو **App Service** بتبقي بره السبسكريبشن بتاعك و بتبقي باي ديفولت بتاخد عنوان عام تقدر توصلها من النت يعني أي حد في العالم، من أي مكان، يقدر يحاول يوصل لعنوان الخدمة دي (طبعًا هيحتاج مفتاح أو صلاحية عشان يدخل فعليًا، لكن **العنوان نفسه مكشوف ومتاح** للكل).
## Public Endpoint

الـ Public Endpoint هو أي Service ليها Public IP Address وبيتم عمل Routing للـ Traffic بتاعها عن طريق الـ Public Internet.

- ال**Accessibility:** السيرفر متاح عالمياً. أي حد معاه الـ IP Address والـ Port يقدر يعمل Initiate لـ Connection.
    
- ال **Traffic Flow:** الـ Packets بتخرج من الـ Client، بتمر عبر سلسلة من الـ Routers والـ ISPs، لحد ما توصل للـ Internet Gateway (IGW) الخاص بالـ Infrastructure بتاعتك، واللي بيمررها للسيرفر.
    
- ال**Security Posture:** لأن الـ Service بتكون Exposed للإنترنت، الـ Attack Surface بيكون كبير جداً. ده بيخليها هدف مباشر للـ Port Scanners، والـ DDoS Attacks، والـ Brute-force. التأمين هنا بيعتمد بشكل أساسي على الـ Application Layer، باستخدام Web Application Firewall (WAF)، وتشفير TLS، و Strict Rate Limiting.
    
- ال**Use Cases:** الـ Web Servers، الـ Public APIs، والـ External Load Balancers اللي بتستقبل الـ Requests من الـ End-users.

## Private Endpoint

هو **network interface خاص بيك، بعنوان Private IP من داخل الـ VNet بتاعتك نفسها**، بيوصلك مباشرة للخدمة (زي Storage Account) **من غير ما المرور يخرج للإنترنت العام خالص**.

بمعنى تاني: بدل ما تتكلم مع الخدمة عن طريق عنوانها العام، أزور بيدّيلك **عنوان خاص إضافي** (زي `10.0.1.50` مثلاً) **جوه الـ VNet بتاعتك نفسها**، وكأن الخدمة (اللي هي في الحقيقة PaaS مُدار من مايكروسوفت، مش VM بتاعتك) **بقت "عضو" داخل شبكتك الخاصة**.

 التقنية اللي بتشغّل ده: Azure Private Link

الخدمة اللي بتوفر المفهوم ده اسمها **Azure Private Link**. هي اللي بتخلي المرور بينك وبين الخدمة يعدي بالكامل على **الـ backbone الداخلي بتاعة Microsoft** (بالظبط نفس المبدأ اللي شرحناه في VNet Peering) **بدل الإنترنت العام تمامًا**.

- ال**Security Posture:** الـ Attack Surface من خارج الشبكة شبه معدوم. الحماية هنا بتعتمد على الـ Network Security Groups (NSGs) والـ Firewalls الداخلية لتطبيق مبدأ הـ Least Privilege وتقييد الـ Traffic بين الـ Subnets المختلفة (Micro-segmentation).
    
- ال**Use Cases:** الـ Databases، الـ Backend Microservices، والـ Caching Clusters (زي Redis و Memcached).

![[Pasted image 20260902231347.png]]


![[Pasted image 20260902232814.png]]

![[Pasted image 20260902232954.png]]

## Service Endpoint

لما شرحنا Private Endpoint: خدمة زي **Storage Account** ليها عنوان عام على الإنترنت افتراضيًا، وعايزين نأمّنها عشان بس الـ VM بتاعتك توصلها؟ **Service Endpoint** بيحل **نفس المشكلة بالظبط**، لكن بآلية مختلفة تمامًا.

ال Private Endpoint: بيعمل **NIC فعلية بعنوان Private IP** جوه الـ Subnet بتاعتك. الخدمة (Storage Account) **بقت فعليًا "عضو" في شبكتك**، وليها عنوان داخلي زي أي VM.

ال Service Endpoint: **مفيش أي NIC ولا عنوان جديد بيتعمل خالص.** الـ Storage Account **لسه محتفظة بعنوانها العام (Public IP)**
اللي بيتغير هو **مسار المرور بس** — بدل ما الطلب من الـ VM يعدي على الإنترنت العام عشان يوصل للـ Storage، بيتوجه عبر **backbone Microsoft الداخلي مباشرة** 

## Scenarios

 4 سيناريوهات، نفس السيرفر ونفس الـ Storage، طريقة وصول مختلفة كل مرة
هستخدم VM في Subnet Dev، عايز يوصل Storage Account، وهنغير بس **طريقة الاتصال** في كل سيناريو

![[Pasted image 20260914150819.png]]

### السيناريو 1: Public Endpoint (الوضع الافتراضي، زي ما هو من غير أي إعداد)

عملت Storage Account عادي، معملتش أي إعداد إضافي خالص. الـ Storage ليها عنوان زي `mystorage.blob.core.windows.net`، **متاح لأي حد في العالم** يحاول يوصله (يحتاج مفتاح وصول عشان يدخل فعليًا، لكن العنوان نفسه مكشوف).

**الـ VM بتاعتك بتوصلها إزاي؟** بتطلع على الإنترنت العام بالظبط زي أي حد تاني في العالم، تدخل بنفس الطريقة اللي أي هاكر أو بوت ممكن يحاول بيها يدخل (طبعًا هيترفض لو مالوش المفتاح، لكن **الباب نفسه مفتوح للعالم**).

**بالتشبيه**: زي محل مفتوح على الشارع العام، أي حد عدى ممكن يحاول يفتح الباب (هيحتاج مفتاح يدخل)، لكن الواجهة نفسها في الشارع العمومي.

### السيناريو 2: Service Endpoint 

نفس الـ Storage، بس دلوقتي فعّلت Service Endpoint على Subnet Dev، وقفلت الـ Firewall بتاعت الـ Storage تقبل بس من الـ Subnet ده.

**الـ VM بتاعتك بتوصلها إزاي؟** الطلب لسه رايح لنفس **العنوان العام** بتاع الـ Storage (`mystorage.blob.core.windows.net`)، لكن **المسار اللي المرور بياخده اتغير** — بدل ما يعدي على الإنترنت العام، بيعدي على شبكة Microsoft الداخلية. **وحد من برة الـ Subnet دلوقتي مش هيقدر يوصلها خالص**، حتى لو معاه المفتاح، لأن الـ Firewall بتاعت الـ Storage بترفض أي حد مش جاي من الـ Subnet ده.

**بالتشبيه**: نفس المحل، بس دلوقتي حطيت لافتة "بندخل بس أصحاب الحي ده"، والـ Firewall بتاعت المحل بتتأكد من العنوان قبل ما تسمحلك تدخل. **العنوان بتاع المحل نفسه متغيرش**، بس مين المسموحله يدخل اتغير

### السيناريو 3: Private Endpoint 

دلوقتي مش هعمل Service Endpoint، هعمل **Private Endpoint** للـ Storage، جوه Subnet Dev بالظبط.

**اللي بيحصل فعليًا**: أزور بيروح **يعمل NIC جديدة تمامًا**، زي إزاي عملت NIC للـ VM بتاعتك، **بس المرة دي مش لـ VM، دي NIC "بتمثل" الـ Storage Account داخل الـ Subnet بتاعتك**. النتيجة: الـ Storage دلوقتي عندها **Private IP خاص بيها جوه `10.0.0.0/26`** (نفس رينج الـ Subnet بتاعتك)، زي `10.0.0.10` مثلاً.

**الـ VM بتاعتك بتوصلها إزاي؟** بتعمل طلب لنفس الاسم (`mystorage.blob.core.windows.net`)، لكن أزور (عن طريق Private DNS) بيحوّل الاسم ده لـ **الـ IP الداخلي `10.0.0.10`** بدل العنوان العام. يعني من وجهة نظر الـ VM، الـ Storage **بقت جارة ليها جوه نفس الشبكة، مش خدمة برة**. **تقدر تقفل الـ Public Access تمامًا** على الـ Storage، ومحدش من برة هيقدر يوصلها **خالص، حتى لو معاه المفتاح**.

خد بالك: الـ Storage Account **فيزيائيا لسه في بنية تحتية منفصلة تمامًا بتديرها Microsoft**، مش نقلتها فعليًا جوه شبكتك. اللي حصل هو إن أزور عمل **"بوابة" (NIC) جوه شبكتك، بتوجه أي طلب ليها لمكان الـ Storage الحقيقي من ورا الكواليس**، بس من وجهة نظرك إنت وأي مورد جوه الـ VNet، هي بتتصرف بالظبط زي أي جهاز عادي على الشبكة.

### السيناريو 4: Azure Private Link (مش سيناريو رابع منفصل — ده اسم **التقنية** اللي بتشغل Private Endpoint)

دي أهم نقطة توضيح: **Private Link مش "طريقة اتصال رابعة" زي التلاتة اللي فاتوا**. **Private Link هي الخدمة/التقنية اللي من ورا كواليس Private Endpoint**، اللي بتعمل ربط الـ IP الداخلي بالـ Storage الحقيقي.

**الفرق العملي الوحيد**: كل اللي شرحناه في السيناريو 3 كان عن **خدمات Microsoft الجاهزة** (Storage, SQL, إلخ). لو **إنت شخصيًا** عندك تطبيق (زي API بتاعك بـ FastAPI) شغال على VM أو Load Balancer بتاعتك، وعايز **زبون تاني (شركة تانية)** يوصله بنفس الطريقة الآمنة دي (Private IP جوه شبكته هو)، بتستخدم **Private Link Service** عشان **تعرض تطبيقك إنت** بنفس الآلية اللي Microsoft بتستخدمها لعرض الـ Storage بتاعها.

**بمعنى تاني**: Private Endpoint = "أنا بستهلك خدمة حد تاني (Microsoft) بأمان". Private Link Service = "أنا بعرض خدمتي إنت لحد تاني بنفس الأمان ده".

# Azure Public and Private DNS

مفيش حد بيحفظ IP addresses. إنت بتكتب `google.com`، مش `142.250.187.78`. **DNS (Domain Name System)** هو الخدمة اللي بتترجم الاسم للعنوان الفعلي. أزور بيقدملك نوعين مختلفين تمامًا من الخدمة دي، لغرضين مختلفين تمامًا.

## Azure Public DNS - للعالم الخارجي

#### المشكلة اللي بتحلها

تخيل إنك اشتريت دومين باسم `mycompany.com` من شركة زي GoDaddy أو Namecheap. الشركة دي اسمها **Domain Registrar** (مُسجِّل الدومينات).
لكن إنت مش عايز تدير الـ DNS Records (زي الـ `A Record` أو `CNAME`) من عند GoDaddy؛ عايز تديرها من جوه Azure Portal جنب الـ Virtual Machines والـ Load Balancers بتاعتك.
#### الآلية
- بتدخل على Azure وتعمل **Azure Public DNS Zone** باسم `mycompany.com`.
    
- ا Azure بيكريتلك الـ Zone وبيديك **4 Name Servers (NS)** خاصين بـ Microsoft (مثل `ns1-01.azure-dns.com`).
    
- بترجع لـ GoDaddy وتعمل Update للـ NS Records وتحط الـ 4 Name Servers بتوع Azure.
    
- من اللحظة دي، Azure بقيت هي الـ **Authoritative DNS Host** للدومين بتاعك على مستوى كوكب الأرض. أي حد على الإنترنت يكتب `api.mycompany.com` الـ Query هتتحول لـ Azure Public DNS ويديه الـ Public IP.

#### أنواع الـ Records الأساسية

|النوع|الوظيفة|
|---|---|
|**A**|اسم → IPv4 address مباشرة|
|**AAAA**|اسم → IPv6 address|
|**CNAME**|اسم → اسم تاني (alias) — مثلاً `www` → `mohammedapp.com`|
|**MX**|تحديد سيرفرات البريد الإلكتروني بتاعت الدومين|
|**TXT**|بيانات نصية حرة — بتستخدم كتير للتحقق من ملكية الدومين (زي لما شرحنا "custom domain verification" في Entra ID)|
|**NS**|تحديد الـ Name Servers المسؤولة|

### Azure Private DNS - للعالم الداخلي بس

#### المشكلة اللي بتحلها 

جوه الـ Virtual Network (VNet) بتاعتك في Azure، عندك آلاف الـ Resources:

- `vm-app-01` (`10.0.1.4`)
    
- `vm-db-master` (`10.0.2.10`)
    
- `internal-lb` (`10.0.1.100`)
    

مش منطقي تخلي الـ Apps تكلم الـ Databases بالـ IP مباشر (لو الـ IP اتغير السيستم هيقع)، وفي نفس الوقت **ممنوع تكشف الأسماء والـ IPs الداخلية دي للإنترنت** لأسباب أمنية.

وهنا بييجي دور **Azure Private DNS Zone**.

1. ال**The Private :** بتكريت Zone باسم داخلي، زي `corp.internal` أو حتى `mycompany.com`.
    
2. ال**Virtual Network Link (VNet Link):** دي الوصلة اللي بين الـ DNS Zone وبين الـ VNet. الـ Private Zone مبتكونش شايفه أي VNet إلا لما تعمل Link بينهما.
    
3. ال**Auto-Registration:** ميزة خطيرة! لما تفعّل الـ Auto-Registration على الـ VNet Link، أي VM جيدة تكريتها جوه الـ VNet، Azure أوتوماتيك بيكريتلها `A Record` باسم الـ VM جوه الـ Private DNS Zone. ولو مسحت الـ VM، بيتمسح الـ Record أوتوماتيك.
#### الفرق الجوهري عن Public DNS

ال**Private DNS Zone مش مرئية من الإنترنت خالص** — بس الـ VNets اللي **ربطتها (Linked) بيها صراحة** تقدر تحلها. فاكر مبدأ **Deny by default** اللي شرحناه في NSG؟ نفس الفلسفة هنا.

#### Virtual Network Links - الخطوة الإلزامية

زي ما NSG لوحدها ملهاش تأثير لحد ما تربطها بـ Subnet، **Private DNS Zone لوحدها ملهاش تأثير لحد ما تربطها (Link) بـ VNet**. وفيه نوعين من الربط:

**1. Registration Virtual Network (مع Autoregistration)**  
لو فعّلت **Autoregistration** وقت الربط، **أي VM تتعمل جوه الـ VNet دي بتاخد A record تلقائيًا** في الـ Zone، من غير أي تدخل يدوي. لو الـ VM اتقفلت (Deallocated) أو اتمسحت، الـ record بيتشال تلقائيًا كمان.

**2. Resolution Virtual Network (من غير Autoregistration)**  
الـ VNet دي **تقدر تحل** أي record موجود في الـ Zone، لكن **مش بتسجل** مواردها هي فيها تلقائيًا.

**قيد مهم هتتسأل فيه**: **VNet واحدة تقدر تكون Registration Network لـ Private DNS Zone واحدة بس**. لو حاولت تفعّل Autoregistration لنفس الـ VNet في Zone تانية، هيفشل.

### نقطة عملية: On-premises مش بيشوف Private DNS تلقائيًا

لو عندك on-premises متصل بـ VPN/ExpressRoute (فاكرهم؟)، **الأجهزة on-premises مش بتقدر تحل Private DNS Zone records بشكل طبيعي مباشرة**. لازم تعمل **DNS Forwarder** (VM جوه أزور شغالة كـ DNS proxy) يستقبل الطلبات من on-premises ويوجهها لـ Private DNS Zone.

![[Pasted image 20260916020803.png]]
#### Ex on Private DNS
انا لما بربط vnet ب private dns و افعل الاوتو 
هو اوتوماتيك بياخد ips ال VMs كلها و يعمل كده عنده
VM1.DNSZoneName --->IP 
و اي VM جوه ال Vnet تكتب VM1.DNSZoneName ال DNS هيوديها ل VM1 
و ده اسهل من انك تكتب ال IP الطويل
