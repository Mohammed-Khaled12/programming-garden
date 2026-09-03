# الدافع او المشكله:

كلنا عارفين ان ستيم و انت بتلعب لعبه عليه بيحسبلك الوقت اللي قضيته فيها بس المشكله ان مش كل الناس بتشتري العاب علي ستيم , الناس اللي بتلعب العاب Cracked هيتجهوا انهم ينزلوا لانشرز زي Playnite و دي مليانه Bloatware و بتستهلك موارد الجهاز 

# عايز تعمل ايه؟

انا فكرتي بناء Lightweight Desktop App لنظام Windows 11 
البرنامج عبارة عن Background Service بتشتغل في صمت جنب الساعة (System Tray) وتراقب الـ `.exe` الخاص بالألعاب عشان تحسب وتخزن وقت اللعب بدون ما تعمل أي Load على الـ CPU أو الـ RAM.
Monitoring running processes via Windows API
طبعا هيبقي فيه Features اكتر زي:
- **Smart AFK Detection (Idle Tracking):** Monitors mouse and keyboard inputs system-wide. If no activity is detected for a predefined threshold (e.g., 10 minutes), the app automatically pauses the active timer to prevent logging inflated playtime while away from the keyboard.

- **Auto-Discovery Prompt:** Instead of requiring manual `.exe` path configurations, the background service intelligently monitors running processes. Upon detecting a process with a significant spike in GPU utilization, it triggers an unobtrusive system notification asking: "Is this a new game you want to track?".

- **Performance Impact Logging:** Integrates with hardware monitoring APIs to log system resource utilization (such as VRAM allocation and GPU usage) during gameplay. This ensures the game runs optimally without frame stuttering, fully utilizing high-end hardware capabilities.

# المتطلبات:
C# .NET
JSON OR SQLite
Be Good at Operating Systems (Kernel,Processes,Threads and Memory Management)

بما إنك بتكتب C++ وغاوي Systems، فخلينا نبص للـ IP Packet من منظور الـ Memory والـ Memory Layout، مش مجرد رسمة في كتاب شبكات.

الـ **IP Packet** هي الـ PDU (Protocol Data Unit) بتاعت Layer 3. تخيلها كأنها `struct` في لغة C++، حجمها بيبدأ من 20 Bytes (ده الـ Header الأساسي) وممكن يكبر. الـ `struct` دي بتشيل جواها الـ Payload (اللي هو الـ TCP Segment أو الـ UDP Datagram اللي جاي من Layer 4).

تعالى نفتح الـ `struct` دي ونفصص أهم الـ Fields اللي جواها، وليه كل واحد فيهم اتعمل أصلاً:

### 1. الـ Source IP والـ Destination IP (العناوين)

- دول أهم 8 Bytes في الباكت (4 للـ Source و 4 للـ Dest).
    
- دول الـ GPS اللي بيخلي الراوترات على مستوى العالم تعرف توجه الباكت دي فين. وزي ما اتفقنا، **دول مبيتغيروش أبداً** طول رحلة الباكت (إلا لو في NAT، وده قصة تانية هنشرحها بعدين).
    

### 2. الـ Protocol Field (حلقة الوصل مع Layer 4)

- حجمه 1 Byte.
    
- **وظيفته الهندسية (Demultiplexing):** لما الباكت توصل لسيرفر جوجل وتطلع من Layer 3، السيرفر هيعرف منين يدي الداتا دي للـ TCP ولا للـ UDP ولا للـ ICMP؟ الـ Field ده هو اللي بيحدد.
    
- لو قيمته `6` ⬅️ الداتا اللي جوه دي TCP.
    
- لو قيمته `17` ⬅️ الداتا اللي جوه دي UDP.
    
- لو قيمته `1` ⬅️ دي رسالة ICMP (زي الـ Ping).
    

### 3. الـ TTL (Time To Live) - حلال العقد

- حجمه 1 Byte (يعني أقصى قيمة ليه 255).
    
- **المشكلة اللي بيحلها:** تخيل راوتر (A) متوصل براوتر (B)، وحصلت مشكلة في الـ Routing Table خليتهم يرموا الباكت لبعض في "حلقة مفرغة" (Routing Loop). لو مفيش TTL، الباكت دي هتفضل تلف في الشبكة للأبد، ولو حصل كده في ملايين الباكتس، الإنترنت كله هيقع (Network Meltdown).
    
- **إزاي بيشتغل؟** الـ OS بتاعك بيحط رقم مبدئي في الـ TTL (مثلاً 64 في لينكس، أو 128 في ويندوز). **كل راوتر الباكت تعدي عليه (Hop)، لازم ينقص من الـ TTL واحد.**
    
- **إجابة سؤالك (لو الـ TTL وصل لـ صفر):** لو الباكت لفت كتير والـ TTL بتاعها وصل لـ `0` قبل ما توصل للهدف، الراوتر اللي في إيده الباكت دي **بيقتلها (Drop)** فوراً، ويبعت رسالة إعتذار للـ Source IP بتاعك يقولك فيها "Time to live exceeded". (الرسالة دي بتتبعت ببروتوكول الـ ICMP، وده الأساس اللي مبني عليه أمر `traceroute`!).
    

### 4. الـ Fragmentation Fields (أسوأ كابوس للـ Performance)

دول 3 حقول (Identification, Flags, Fragment Offset).

- **القصة:** كل سلك شبكة أو راوتر ليه حد أقصى لحجم الباكت اللي يقدر يشيلها، اسمه **MTU (Maximum Transmission Unit)**، وغالباً بيكون 1500 Bytes.
    
- **المشكلة:** لو جهازك بعت IP Packet حجمها 4000 Bytes، ووصلت لراوتر الـ MTU بتاعه 1500، الراوتر هيضطر "يُكسّر" (Fragment) الباكت دي لـ 3 باكتس صغيرين عشان يقدر يعديهم.
    
- **ليه ده كابوس كـ Backend Engineer؟**
    
    1. الراوتر بيستهلك CPU عالي عشان يكسر الباكت.
        
    2. السيرفر الناحية التانية هيضطر يستنى الـ 3 باكتس يوصلوا ويستهلك CPU و Memory Buffer عشان يجمعهم تاني (Reassembly).
        
    3. لو "باكت واحدة" صغيرة من الـ 3 ضاعت في السكة، السيرفر هيرمي الباقي كله وهتضطر تبعت الـ 4000 Bytes كلهم من الأول!
        
- عشان كده، كمهندس، إحنا دايماً بنحاول نظبط الـ TCP بتاعنا بحيث إنه يبعت Segments حجمها أصغر من الـ MTU عشان **نتجنب الـ Fragmentation تماماً** في Layer 3.
    

### 5. الـ Header Checksum

- ده حقل بيعمل عملية حسابية سريعة على الـ Header بس (مش الداتا كلها) عشان يتأكد إن مفيش ولا Bit اتغير بسبب تشويش كهربائي في السلك. الراوتر لو لقى الـ Checksum غلط، بيرمي الباكت في الزبالة ومبيقولكش حتى إنه رماها!
    

**الخلاصة للـ Backend:**

الـ IP Packet هي مجرد غلاف بيشيل الداتا، بس الغلاف ده مليان تفاصيل بتأثر على الـ Performance بتاع الأبلكيشن بتاعك بشكل مرعب، خصوصاً موضوع الـ Fragmentation اللي لو مفهمتوش، هتلاقي السيرفر بتاعك بيهنج ومش عارف السبب، رغم إن الكود بتاعك زي الفل.


يا عيني عليك! إنت كده بتنخور في الـ IP Header بجد ومبتفوتش تفصيلة.

الـ **ECN (Explicit Congestion Notification)** ده اختراع عبقري بيوريك إزاي Layer 3 (الراوترات) و Layer 4 (الـ TCP) بيكلموا بعض عشان ينقذوا الشبكة قبل ما تقع، من غير ما نضحي بالـ Performance.

الـ ECN هما **2 Bits** مستخبيين جوه حقل في الـ IP Header اسمه (Type of Service أو DSCP). عشان تفهم العبقرية بتاعتهم، لازم نشوف المشكلة اللي كانوا بيحلوها:

### المشكلة: سياسة "الرمي" (Packet Drop)

في العادي، لما راوتر في النص بيجيله ترافيك عالي جداً، الـ Buffer (الذاكرة المؤقتة) بتاعته بتتملي. الراوتر الغلبان ده معندوش طريقة يقول بيها للـ Server "أنا اتخنقت بطأ السرعة شوية". فبيعمل إيه؟

بيبدأ **يرمي الباكتس في الزبالة (Drop)**.

الـ TCP عند السيرفر بيلاحظ إن الباكتس مبتردش، فيستنتج إن الشبكة زحمة، فيقوم مقلل السرعة.

- **المصيبة هنا كـ Backend:** رمي الباكتس بيعمل Latency عالي جداً، لأن السيرفر هيضطر يستنى الـ Timeout ويعمل Retransmit (إعادة إرسال) لنفس الباكتس تاني. وده بيدمر الأداء في حاجات زي الـ Real-time APIs أو ألعاب الأونلاين.
    

### الحل: الـ ECN (التحذير المُسبق بدل الرمي)

الـ ECN فكرته ببساطة: "ليه نرمي الباكت لما ممكن الراوتر يحط عليها علامة حمراء تحذر السيرفر إنه يهدي السرعة؟"

**السيناريو بيحصل كده بالترتيب (تكامل مبهر بين IP و TCP):**

1. **الاتفاق (L4):** جهازك وسيرفر جوجل بيتفقوا في البداية (في الـ TCP Handshake) إنهم الاتنين بيفهموا ويدعموا الـ ECN.
    
2. **الإرسال (L3):** السيرفر وهو بيبعت الـ IP Packet، بيحط قيمة في الـ 2 bits بتوع الـ ECN اسمهم **ECT** (ECN-Capable Transport). كأنه بيلزق ورقة على الباكت بيقول للراوتر: "لو سمحت لو إنت زحمة، حطلي علامة هنا بدل ما ترمي الباكت".
    
3. **الزحمة في النص (L3):** الباكت وصلت لراوتر في النص الـ Buffer بتاعه بيطلع في الروح. الراوتر بيشوف ورقة الـ ECT، فيقوم ماسحها وكاتب مكانها **CE** (Congestion Experienced - تم مواجهة اختناق)، ويمرر الباكت عادي جداً لجهازك من غير ما يرميها!
    
4. **التبليغ (L4):** جهازك بيستلم الباكت كاملة، بس بيقرا الـ IP Header يلاقي علامة الـ CE. جهازك يقوم باعت TCP ACK للسيرفر، ويفعل فيه Flag اسمه **ECE** (ECN-Echo) في الـ TCP Header، كأنه بيقول للسيرفر: "الداتا وصلتني يامعلم بس الراوتر اللي في النص بيصرخ من الزحمة".
    
5. **الاستجابة (L4):** السيرفر يقلل سرعة الإرسال فوراً (Congestion Window)، ويبعت لجهازك باكت فيها Flag اسمه **CWR** (Congestion Window Reduced) عشان يقولك "وصلت الرسالة وهديت اللعب".
    

**الخلاصة الهندسية:**

الـ ECN بيخلينا نعمل **Congestion Control بدون ما نخسر ولا Packet واحدة (Zero Packet Loss)**. ده بيقلل الـ Retransmissions، وبيحافظ على الـ Latency واطي وثابت، وده الهدف الأسمى لأي سيستم High Performance.