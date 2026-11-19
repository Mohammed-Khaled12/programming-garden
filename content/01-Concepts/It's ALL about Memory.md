
> [!abstract] About This Note
> This note explores the evolution of **Memory Management in Operating Systems**, starting from physical memory and early uniprogramming systems, then moving through multiprogramming, memory protection, relocation, Base & Bounds, and Segmentation, until reaching Paging and Virtual Memory.
>
> **The main goal is to understand:**
> - **WHY** each technique was introduced.
> - **HOW** each technique works.
> - **WHAT** limitations led to the next evolution.
>
> **Topics Covered:**
> 1. Physical Memory (DRAM) Fundamentals.
> 2. Uniprogramming and Multiprogramming.
> 3. Address Collision and Memory Protection.
> 4. Static Relocation.
> 5. MMU, Base & Bounds.
> 6. Segmentation and Fragmentation.
> 7. Paging, Page Tables, and Address Translation.
> 8. Virtual Memory and Memory Isolation.
>
> > [!note]
> > This is an introductory learning note focused on building conceptual understanding. Advanced Paging topics, including TLB, Multi-Level Page Tables, Demand Paging, Page Faults, and Swapping, are reserved for a follow-up study.

# الفصل الأول: إيه هي الـ RAM أصلا ؟ (Physical RAM)

الـ **Physical Memory (DRAM)** عبارة عن اراي من ال Cells كل Cell هي 1 Bit مكونة من حاجتين بس: **Transistor** (بوابة بتفتح وتقفل) و **Capacitor** (مكثف صغير جداً بيخزن شحنة كهربائية).

- المكثف مشحون كهرباء = `1`.
- المكثف فاضي = `0`.

_المكثف ده حجمه صغير لدرجة إنه بيفرغ شحنته في خلال أجزاء من الثانية! علشان كده الـ Memory Controller على البوردة "في الاجهزه الحديثه بقي جوه ال CPU" مضطر يلف على كل خلايا الـ RAM كل كام مللي ثانية ويعيد شحنها من تاني 
وده سبب تسميتها **Dynamic** RAM أو DRAM، وده برضه السبب إنك أول ما تفصل الكهرباء عن الجهاز، كل الشحنات بتتفرغ والـ RAM بتتمسح تماماً_

![[Pasted image 20261119104236.png]]

كل 8 خلايا جنب بعض متجمعين في وحدة اسمها **Byte**، وكل Byte ليه رقم  محفور في الهاردوير اسمه **Physical Address**، بيبدأ من `0x00000000` لحد آخر Byte في الـ RAM بتاعتك

الـ **CPU** والـ **RAM** مربوطين ببعض بأسلاك نحاس على البوردة اسمها **Buses**:

1. ال **Address Bus:** أسلاك بيحط عليها الـ CPU رقم الـ Byte اللي عايز يوصله (بالـ Binary).
    
2. ال **Data Bus:** أسلاك بتتنقل عليها البيانات

# الفصل الثاني: عصر ما قبل الحداثه (الخمسينات — Bare Metal & Uniprogramming)

الزمن ده كان بدايات الكومبيوتر كان الوضع عباره عن :
- ال `CPU` --> بينفذ برنامج واحد بس في المره
- ال `RAM` --> بتحمل برنامج واحد بس في المره + OS

الكلام ده في مصطلحين بيوصفوه
ال **Bare Metal:** البرنامج بيتعامل مع الهاردوير بشكل مباشر أو من خلال طبقة برمجية بسيطة جدًا، من غير نظام تشغيل حديث ومعقد يدير موارد الجهاز.
ال **Uniprogramming:** الكمبيوتر بيشغل برنامج واحد فقط في نفس الوقت.

![[Pasted image 20261119104956.png]]

شكل الـ Physical RAM وقتها كان بسيط جداً:

```Plaintext
0x00000000  +-----------------------+
            |   Operating System    |  (مثلاً أول 4 KB)
0x00001000  +-----------------------+
            |                       |
            |  Your 1 Program       |  (باقي الـ RAM كلها ملكك)
            |                       |
0x000FFFFF  +-----------------------+
```
الفكرة إن الـ OS موجود في جزء معروف من الـ RAM، والبرنامج بيتحمّل بعده.

لما كنت بتيجي تعمل Compile لبرنامجك و لنفترض ان البرنامج فيه سطر
```C
int x = 10;
```
البرنامج لازم يعرف مكان المتغير ده في الميموري علشان يقدر يقرأه ويكتب فيه
في النموذج القديم ده، الـ Compiler عارف إن البرنامج هيبدأ عند العنوان: `0x00001000`
و بفرض ان x علي بعد 50 بايت من البرنامج يبقي 
`Address(x) = 0x00001000 + 0x50`

 **إيه المشكلة في الطريقة دي؟**
البرنامج بقى مرتبط بمكان محدد في الـ Physical RAM.
يعني لو قررت تحمّل البرنامج في مكان تاني، العناوين اللي جواه ممكن تحتاج تتغير.
و كمان
ساعتها كان **CPU سريع و I/O بطيء!** 
![[Pasted image 20261119105716.png]]
وده معناه إننا مش مستغلين إمكانيات الـ CPU كويس فاااااا الحل نسيب ال **Uniprogramming** و ندخل في ال **Multiprogramming**

![[Pasted image 20261119110226.png]]

# الفصل الثالث: الستينات — Multiprogramming

في الفصل اللي فات، الكمبيوتر كان بيشغّل برنامج واحد، وشفنا إن الـ CPU كان بيضيع وقت كبير وهو مستني الـ I/O.
المهندسين قرروا يحلوا المشكلة دي بتشغيل أكتر من برنامج في الـ RAM في نفس الوقت.
**يعني**
بدل ما الـ CPU قاعد فاضي، ما نحمل 3 أو 4 برامج في الـ RAM في نفس الوقت! لما برنامج A يقف يستنى الهارد، نخلي الـ CPU ينط يشغل برنامج B
و من هنا تبدا المشاكل بقي

أول ما طبقوا الفكرة دي، الدنيا انفجرت في وشهم بـ 3 كوارث:

## 1) ال `Address Collision` 

تخيل إن عندنا برنامجين، A و B كل برنامج اتعمله Compile وهو متوقع إنه هيتحمّل في نفس المكان في الـ RAM

يعني مثلًا:

**Program A:**

عنده متغير اسمه `x` موجود في العنوان `0x2050`
**Program B:**

عنده متغير اسمه `y` موجود برضه في العنوان `0x2050`

ليه؟ لأن الاتنين اتبنو على افتراض إنهم هيبدأوا من نفس عنوان الذاكرة.
كده الاتنين هيكتبوا و يقروا فوق بعض _كارثه يا بيه_

![[Pasted image 20261119110816.png]]

## 2) ال `Zero Protection`

لو مبرمج في برنامج B غلط و كتب Pointer و كتب في عنوان من بتوع ال OS مفيش حاجه هتمنعه !!!!!!!!
في النموذج القديم اللي بنتكلم عنه، مفيش Hardware Memory Protection يمنع الطلب بتاع البوينتر المجنون ده
ال OS هيكراش غالبا 
و ده اسمه **Lack of Memory Protection**

**خد بالك من الفرق**
في الكارثة الأولى، برنامج B بوّظ بيانات برنامج A.
أما هنا، برنامج B بوّظ الذاكرة اللي نظام التشغيل نفسه بيعتمد عليها
ودي أخطر بكتير


## 3) ال `Security Problems`

برنامج B يقدر يقرا الرام كلها براحته ما مفيش حاجه تمنعه و تقوله لا 
```Plaintext
Read Address 0x0000
Read Address 0x0001
Read Address 0x0002
Read Address 0x0003
...
Read Address 0xFFFF
```
و بكده يقدر يسرق الداتا بتاعت برنامج A اللي مرمي في الميموري معاه

## محاوله سوفتويريه لانقاذ الميموريهيه

و هنا المهندسين حاولوا يحلوا الدنيا بالسوفت وير 
`Static Relocation`
ال OS و هو بيرفع البرنامج في الرام يقوم مغير كل العنواوين اللي فيه لمكان تاني و بكده بقي A في حته و B في حته

كلمة Relocation معناها نقل البرنامج من مكان لمكان، وتعديل العناوين اللي بيعتمد عليها.
وكلمة Static معناها إن التعديل ده بيحصل مرة واحدة أثناء تحميل البرنامج، قبل ما يبدأ التنفيذ.

**مثال:**
افترض إن برنامج B متصمم إنه يبدأ عند `0x1000`
وعنده متغير موجود عند `0x1050`
يعني المتغير موجود على بُعد `0x50` من بداية البرنامج

**قبل التحميل**
**Program B**
Expected Starting Address: 0x1000
Variable y → 0x1050

 **الـ OS قرر يحمله في مكان تاني**
لنفترض إن المنطقة اللي هنحمل فيها B بتبدأ عند `0x4000`
هنا الـ OS لازم يعدّل العنوان القديم.
كان البرنامج متوقع إنه يبدأ عند `0x1000`، لكنه اتحمّل عند `0x4000`.

الفرق بين المكانين:

Relocation Offset = 0x4000 - 0x1000= 0x3000

 **نعدّل العنوان**
العنوان القديم للمتغير `0x1050`
نضيف عليه الفرق:
New Address = 0x1050 + 0x3000 = 0x4050

![[Pasted image 20261119112300.png]]

و هنا نقف ال **Static Relocation** عالجت مشكلة **Address Collision** في الحالة اللي العناوين بتتعدل فيها بشكل صحيح
بس
حرفيا بس 
معرفتش تحل المشكلتين التانيين (تقدر تكتب فوق ال OS و تقدر تقرا بيانات غيرك)

# الفصل الرابع: التدخل السيليكوني (MMU, Base & Bounds, and Segmentation)

بعد عدم نجاح الحل السوفتويري بشكل كامل بدا تدخل الحل الهاردويري 

ريكاب علي ال 3 مشاكل بتوعنا:
1. برنامج A وبرنامج B ممكن يكتبوا في نفس العنوان.
    
2. برنامج ممكن يكتب فوق ذاكرة الـ Operating System.
    
3. برنامج ممكن يقرأ بيانات برنامج تاني.

المهندسين قالوا: **طب ما نعمل قطعة Hardware تراقب كل عملية وصول للذاكرة وتمنع أي برنامج من الخروج عن المساحة المخصصة له؟**
ومن هنا نبدأ

## المحاولة الأولى — Base & Bounds

بدل ما كل برنامج يتعامل مع عناوين Physical Memory مباشرة، خلّوا كل برنامج يتخيل إن ذاكرته بتبدأ من الصفر! 
يعني:

**Program A**
متخيل إن أول عنوان عنده: `0x0000`

**Program B**
برضه متخيل إن أول عنوان عنده: `0x0000`

لكن في الحقيقة كل واحد موجود في مكان مختلف في الـ Physical RAM.
لو برنامج A طلب ادريس معين ده هيبقي "Virtual Address" 
![[Pasted image 20261119124903.png]]

البرنامجين بيستخدموا نفس Virtual Address، لكن كل واحد بيتترجم لمكان مختلف في الـ Physical RAM مين بقي اللي بيعمل كده ؟؟؟ **MMU**

ال **Memory Management Unit** (MMU) هي قطعة سيليكون صغيرة جوه الـ CPU بينه وبين الـ Address Bus مهمتها تترجم العناوين الفيرتشوال لفيزيكال و تدير صلاحيات الوصول

ال **MMU** حواها اتنين ريجيسترز:
- ال **`Base` Register:** بداية مكان البرنامج الحقيقي في الـ RAM.
    
- ال **`Bounds` (Limit) Register:** أقصى مساحة مسموحة للبرنامج.

دلوقتي، برنامج A وبرنامج B الاتنين بيتعملهم Compile على إنهم بيبدأوا من عنوان `0x0000`! لما برنامج B يطلب عنوان `0x0050` (ده اسمه **Virtual Address**)، الـ MMU بيعترض الطلب في جزء من النانو ثانية ويعمل معادلتين:
1. يتأكد إن `0x0050 < Bounds`. (لو أكبر، الـ MMU يضرب Hardware Exception والـ CPU ينقل التحكم للـ Kernel، والـ OS يقرر يتعامل مع الخطأ إزاي. ممكن ينتهي الأمر بإنهاء الـ Process
**Segmentation Fault**!).
    
2. يجمع العنوان على البايز علشان يعرف العنوان الفيزيكال `Physical Address = Base + 0x0050`

![[Pasted image 20261119125511.png]]

طب خلاص نعتمد بقي ال Base & Bounds ؟
انسي قابل المشكله بقي 
ال **Internal Fragmentation**
البرنامج مش كتلة واحدة من التعليمات والمتغيرات.

عنده مناطق مختلفة، أشهرها:

- ال Code: التعليمات اللي البرنامج بينفذها.
    
- ال Data: البيانات الثابتة أو العامة.
    
- ال Heap: مساحة للذاكرة اللي البرنامج بيحجزها أثناء التشغيل.
    
- ال Stack: مساحة لاستدعاءات الدوال والمتغيرات المحلية.

خلينا نشوف رسمة مبسطة:
![[Pasted image 20261119125648.png]]

لاحظ إن الـ Heap والـ Stack ممكن يكبروا مع الوقت.

المشكلة إن Base & Bounds بتتعامل مع البرنامج كأنه **بلوك واحد متصل في الـ Physical RAM**.

يعني لو البرنامج محتاج 50 KB دلوقتي، لكن متوقع إنه يكبر، ممكن الـ OS يحجزله مساحة 16 MB.
تعالى نشوف النتيجة.

**مثال: برنامج محتاج 50 KB لكن اتحجزله 16 MB**
![[Pasted image 20261119125735.png]]

وده اسمه **Internal Fragmentation**.
يعني فيه هدر في مساحة الذاكرة الموجودة داخل البلوك المحجوز للبرنامج نفسه.
خد بالك من كلمة Internal:
الفراغ موجود _داخل المساحة المحجوزة_.
وده كان عيب في طريقة تخصيص بلوك كبير متصل لكل برنامج.

![[Pasted image 20261119125901.png]]

**هدر جوه البلوك**:المساحة اتحجزت، لكن جزء منها مش مستخدم.
يبقي مشكله ال **Base & Bounds** ان كل برنامج بياخد بلوك واحد كبير متصل وممكن جزء منه يفضل فاضي ومحجوز Internal Fragmentation

## المحاولة التانية — Segmentation

> طب ليه نحجز البرنامج كله في بلوك واحد؟ ما نفصل مكوناته، وكل مكون ياخد مساحته لوحده!

وهنا ظهرت فكرة الـ **Segmentation**.

في Base & Bounds كان عندنا:

- Base واحد.

- Bounds واحد.


في Segmentation بقينا بنتعامل مع عدة Segments، وكل Segment ليه Base وBounds خاصين بيه.

مثلًا:

| Segment | محتواه              |
| ------- | ------------------- |
| Code    | تعليمات البرنامج    |
| Data    | البيانات            |
| Heap    | الذاكرة الديناميكية |
| Stack   | استدعاءات الدوال    |

يعني بدل ما البرنامج كله ياخد مساحة واحدة ضخمة، كل جزء ياخد مساحة مستقلة.

![[Pasted image 20261119130419.png]]
يعني كل جزء من البروجرام بقي ليه Base & Bound بتاعه لوحده 
كل Segment موجود في مكان مختلف في الـ Physical RAM، وليه حجم وصلاحيات خاصة بيه.
يعني نفس مبدأ Base & Bounds بالضبط، لكن بدل Register واحد للبرنامج كله، بقينا نستخدم Base وBounds لكل Segment.

**طيب كده حلينا مشكلة الـ Internal Fragmentation؟**
إلى حد كبير، لأننا مبقيناش مضطرين نحجز الفراغ الكبير اللي بين الـ Heap والـ Stack ضمن بلوك واحد متصل.
لكن ظهرت مشكلة مختلفة تمامًا.

ال **External Fragmentation** نياهعاعاعاعاعاعاعاعاعاعاعاعاعاعاع 

معلم انت فتت الرام لحتت صغيره متنطوره طب لو جيه برنامج محتاج مساحه متصله؟ لو جالك سيجمينت كبير هتحطه فين ما ده مش هيتقسم تاني 
ال **Segmentation:** أنا بقسم البرنامج حسب مكوناته المنطقية، مثل Code وHeap وStack
![[Pasted image 20261119130701.png]]

![[Pasted image 20261119130804.png]]

كل Segment بياخد مساحة متصلة مستقلة، لكن الأحجام المختلفة بتسيب فتحات متفرقة في الـ Physical RAM.


# الفصل الخامس: اديني في التطور — Paging & Virtual Memory

![[Pasted image 20261119132007.png]]

أكبر وأعظم "خدعة بصرية" في تاريخ هندسة الكمبيوتر. البرنامج بتاعك بيسأل نظام التشغيل بكل براءة: _"ممكن حتة ميموري؟"_ (`i can haz memory?`)، ونظام التشغيل بيرد عليه بكل ثقة: _"عيش حياتك"_ (`knock yourself out`) وبيديله مساحة **Virtual Memory** نظيفة ومرتبة، بينما في الخلفية الـ OS بيلعب لعبة شطرنج معقدة عشان يربط الوهم ده بـ **Physical Memory** الحقيقية.

الحقيقه بعد عدم جدوي الحلول اللي فاتت فااا معانا حل جديد حل متطور يلا نشوفه

احنا هنبطل نقسم الرام ل Blocks باحجام مختلفه
- هنمسك الـ **Physical RAM** كلها، ونقطعها بالمسطرة لبلاطات ثابتة الحجم تماماً اسمها **Page Frames**، حجم كل بلاطة **4 KB** (4096 Bytes).
    
- وهنمسك الـ **Virtual Address Space** بتاع كل برنامج، ونقطعه برضه لبلاطات بنفس الحجم بالظبط (**4 KB**) اسمها **Virtual Pages**.

![[Pasted image 20261119132520.png]]

دلوقتي لو برنامج A طلب Memory من ال OS
ال OS هيقوله اتفضل انت معاك Virtual Address Space مرصوص ورا بعضه و مترتب ليك لوحدك
لكن في الحقيقه 
الـ OS بيرمي الـ **Virtual Pages** بتاعة برنامجك في أي **Page Frames** فاضية في الـ Physical RAM—حتى لو واحدة في أول الـ RAM، والتانية في آخرها، والتالتة في النص!

**مثال**
افترض إن عندنا برنامج A محتاج 3 Pages.
الـ OS مش مضطر يحطهم جنب بعض في الـ Physical RAM.
ممكن يعمل الآتي:

| Virtual Page | Physical Frame |
| ------------ | -------------- |
| Page 0       | Frame 5        |
| Page 1       | Frame 2        |
| Page 2       | Frame 8        |

يعني:
- Page 0 اتحطت في Frame 5.
- Page 1 اتحطت في Frame 2.
- Page 2 اتحطت في Frame 8.

البرنامج في عالمه الافتراضي شايف صفحات مرتبة:
`Page 0 → Page 1 → Page 2`

لكن في الـ Physical RAM الصفحات موجودة في أماكن متفرقة!

**وبكده حلّينا المشكلة الأساسية اللي كانت بتواجه الـ Segmentation!**
مش محتاجين 12 KB متصلة مثلًا علشان نحط 3 Pages.
محتاجين بس 3 Frames فاضية، أيًا كان مكانهم.

***بس فيه سؤال مهم:***
البرنامج هيعرف إزاي إن Page 0 موجودة في Frame 5، وPage 1 موجودة في Frame 2؟ **Page Table**

الـ **Page Table** عبارة عن جدول بيحتفظ بمعلومات عن ترجمة الـ Virtual Pages إلى Physical Frames.
يعني هو أشبه بدليل عناوين.
في مثالنا:

| Virtual Page Number (VPN) | Physical Frame Number (PFN) |
| ------------------------- | --------------------------- |
| 0                         | 5                           |
| 1                         | 2                           |
| 2                         | 8                           |

لما البرنامج يطلب Page رقم 1، الـ MMU تقدر تعرف من الـ Page Table إنها موجودة في Frame رقم 2.

الـ OS بيدير الـ Page Tables، والـ MMU بتستخدمها أثناء ترجمة عناوين الذاكرة.


لحد هنا احنا عرفنا ان:
- البرنامج بيتعامل مع Virtual Address.
    
- الـ RAM بتتعامل مع Physical Address.
    
- الـ Page Table بتربط الـ Virtual Pages بالـ Physical Frames.

لكن إزاي بنحوّل العنوان من واحد للتاني؟
هنا لازم نفهم شكل الـ Virtual Address
## شكل الـ Virtual Address

افترض إن عندنا معمارية 32-bit.
يعني كل Virtual Address مكوّن من 32 Bit
وحجم الصفحة 4 KB
إحنا عارفين إن:
4KB = 4096 = 2 ^ 12

يعني علشان نحدد أي Byte داخل صفحة حجمها 4096 Byte، هنحتاج 12 Bit.
ليه؟
لأن 12 Bit تقدر تمثل:

 2 ^ 12=4096
قيمة مختلفة، من صفر إلى 4095.
إذن آخر 12 Bit من العنوان هنستخدمهم لتحديد مكان الـ Byte جوه الصفحة.

والـ Bits المتبقية:
 32-12=20
هنستخدمهم لتحديد رقم الصفحة نفسها.
وبالتالي:
![[Pasted image 20261119133836.png]]

يعني البروجرام هيبعت لل **MMU** فيرتشوال ادريس مقسوم جزئين VPN-Offset
بيقوله فيها يا MMU انا عايز ال Byte اللي موجوده في page كذا (**VPN**)
و علي بعد كذا من بدايه البيدج (**Offset**)

إذن البرنامج بيقول للـ MMU:
أنا محتاج الـ Byte اللي موجود في الصفحة رقم 2، وعلى مسافة `0xABC` من بدايتها.
**الـ MMU تروح تدور في الـ Page Table** 
إحنا طلبنا VPN رقم 2.
الـ MMU تروح للجدول، وتلاقي:
VPN 2 -->PFN 7 
يعني:
الصفحة الافتراضية رقم 2 موجودة في Frame رقم 7 في الـ Physical RAM
هتطلع بقي الفيزيكال ادريس و تبعته

كده حلينا شويه مشاكل:
ال **External Fragmentation** اتحلت علشان مش لازم ال Frames جنب بعضها
ال **Internal Fragmentation** لسه موجود للاسف لأن آخر Page ممكن يكون فيها مساحة مش مستخدمة
ال **Memory Isolation** زي الفل فاكر مشكلة إن برنامج A ممكن يشوف بيانات برنامج B؟
دلوقتي كل Process عنده Page Table خاصة به، والـ MMU بتستخدم جدول العملية الحالية في ترجمة عناوينها.
![[Pasted image 20261119134602.png]]

**طب والحماية بقت عاملة إزاي؟**

الـ Page Table مش مجرد جدول بيقول الصفحة موجودة فين.

الـ Entries بتاعته فيها كمان Permission Bits، منها:

|Permission|معناها|
|---|---|
|Read|مسموح تقرأ|
|Write|مسموح تكتب|
|Execute|مسموح تنفذ تعليمات|
|User / Supervisor|تحديد مستوى الوصول|

مثلًا لو برنامج بيحاول يكتب في صفحة مسموح بقراءتها فقط، الـ Hardware يرفض عملية الكتابة ويولّد Exception.

وده بيحافظ على عزل البرامج عن بعضها.

![[Pasted image 20261119134754.png]]

# ختاميه و ملخص

القصه ليها تكمله و تحسينات في ال Paging و حجمه (هكملها و انا بذاكر OSTEP) و لحد هنا اكتفي بهذا القدر

شكرا لو وصلت هنا 😍😍

```Plaintext
              MEMORY MANAGEMENT EVOLUTION
                          |
                          ▼
              ┌──────────────────────┐
              │ 1. UNIPROGRAMMING    │
              │                      │
              │ One program in RAM   │
              │ at a time            │
              └──────────┬───────────┘
                         │
                         ▼
               CPU wastes time waiting
                     for I/O
                         │
                         ▼
              ┌──────────────────────┐
              │ 2. MULTIPROGRAMMING  │
              │                      │
              │ Multiple programs    │
              │ loaded into RAM      │
              └──────────┬───────────┘
                         │
                         ▼
                 THREE MAJOR PROBLEMS
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
    Address Collision  No Memory     Security Risks
                       Protection
          │              │              │
          └──────────────┼──────────────┘
                         │
                         ▼
              ┌──────────────────────┐
              │ 3. STATIC            │
              │    RELOCATION        │
              │                      │
              │ Adjust addresses at  │
              │ load time            │
              └──────────┬───────────┘
                         │
                         ▼
                Solves Address Collision
                but provides no real
                memory protection
                         │
                         ▼
              ┌──────────────────────┐
              │ 4. BASE & BOUNDS     │
              │       (MMU)          │
              └──────────┬───────────┘
                         │
               ┌─────────┴─────────┐
               ▼                   ▼
         BASE REGISTER        BOUNDS REGISTER
         Program's start      Allowed memory
         in physical RAM      size / limit
               │                   │
               └─────────┬─────────┘
                         │
                         ▼
              Bounds Check + Address
                    Translation
                         │
                         ▼
                 MEMORY PROTECTION
                         │
                         ▼
               INTERNAL FRAGMENTATION
               (Wasted space inside
                allocated memory)
                         │
                         ▼
              ┌──────────────────────┐
              │ 5. SEGMENTATION      │
              │                      │
              │ Code / Data / Heap   │
              │       / Stack        │
              └──────────┬───────────┘
                         │
                         ▼
              Each segment has its own
                  Base and Bounds
                         │
                         ▼
               Reduces internal waste
                         │
                         ▼
               EXTERNAL FRAGMENTATION
               (Scattered free spaces)
                         │
                         ▼
              ┌──────────────────────┐
              │ 6. PAGING            │
              │                      │
              │ The major solution   │
              └──────────┬───────────┘
                         │
                         ▼
               DIVIDE MEMORY INTO
                 FIXED-SIZE BLOCKS
                         │
               ┌─────────┴─────────┐
               ▼                   ▼
         VIRTUAL MEMORY       PHYSICAL RAM
         Virtual Pages        Page Frames
               │                   │
               └─────────┬─────────┘
                         │
                         ▼
                     PAGE TABLE
                     VPN → PFN
                         │
                         ▼
                        MMU
                Address Translation
                         │
                         ▼
                 PHYSICAL ADDRESS
                         │
                         ▼
                   PHYSICAL RAM
```

```Plaintext
                  FRAGMENTATION
                        |
             ┌──────────┴──────────┐
             ▼                     ▼
       INTERNAL               EXTERNAL
     FRAGMENTATION           FRAGMENTATION
             |                     |
             ▼                     ▼
    Waste INSIDE an          Free spaces scattered
    allocated memory         BETWEEN allocated
    block                    memory blocks
             |                     |
             ▼                     ▼
    Allocated memory         Total free memory may
    contains unused          be sufficient, but
    space                    no large contiguous
                             block is available
```

```Plaintext
                USER PROGRAM
                      |
                      |
                      ▼
              VIRTUAL ADDRESS
                  (32-bit)
                      |
               ┌──────┴──────┐
               ▼             ▼
             VPN           OFFSET
           (20 bits)      (12 bits)
               |             |
               |             |
               ▼             |
             PAGE TABLE      |
               |             |
               ▼             |
          VPN → PFN           |
               |             |
               └──────┬──────┘
                      |
                      ▼
                     MMU
                      |
                      |
             PFN + SAME OFFSET
                      |
                      ▼
             PHYSICAL ADDRESS
                      |
                      ▼
                PHYSICAL RAM
```

```Plaintext
          PROGRAM
       (Virtual Address)
                |
                ▼
        ┌───────────────┐
        │      MMU      │
        │               │
        │  Page Table   │
        │  Translation  │
        │  Protection   │
        └───────┬───────┘
                |
                ▼
        PHYSICAL ADDRESS
                |
                ▼
          PHYSICAL RAM
          (Page Frames)
```

## Hand Written

![[MEMORY.pdf]]

تم بحمد الله
**محمد خالد 2026**