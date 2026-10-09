
 لما تيجي تنقل مكنة من اشتراك للتاني (`Sub1` لـ `Sub2`). أزور مش بيسمحلك تنقل المكنة حاف كده، لازم تاخد معاها كل حاجة مشبوكة فيها.  المكنة، هاردها، كارتها، وشبكتها.. باكدج واحدة بتتنقل مع بعض!

توجد نسختين من الـ Load Balancer (Basic و Standard). النسخة الـ Basic قديمة ومحدودة ولا تدعم خاصية الـ HA Ports المطلوبة لعمل أجهزة الـ NVA. لذلك، النسخة القياسية (Standard) هي الخيار الوحيد الذي يدعم هذا السيناريو.

The DSC extension for Windows requires that the target virtual machine is able to communicate with Azure.
The VM needs to be started.

ميزة التسجيل التلقائي (Auto-registration) وربط الشبكات (VNet Links) **حصرية فقط للـ Private DNS Zones**. المناطق العامة (Public DNS Zones) لا تدعم ميزة الـ Auto-registration إطلاقاً؛ لأنها موجهة للنشر العام على الإنترنت وتتطلب إضافة السجلات يدوياً أو عبر API.
إعداد الـ DNS Suffix الموجود داخل نظام التشغيل Windows Server (`Contoso.com` في `VM1` أو `None` في `VM2`) **لا يؤثر إطلاقاً** على عملية التسجيل التلقائي الخاصة بأزور على مستوى البنية التحتية (Platform-level).

- أزور يتعامل مع كارت الشبكة الخاص بالمكنة ويأخذ اسم المكنة في أزور ويضيفه فوراً للـ Private Zone.

Configuring a VNet-to-VNet connection is a good way to easily connect VNets. Connecting a virtual network to
another virtual network using the VNet-to-VNet connection type (VNet2VNet) is similar to creating a Site-to-Site
IPsec connection to an on-premises location. Both connectivity types use a VPN gateway to provide a secure
tunnel using IPsec/IKE, and both function the same way when communicating.

Your VMs should use managed disks if you want to move them to an Availability Zone by using Site Recovery.

P2S
لو الـ VPN شغال بـ **Certificate** ⬅️ الحل دايماً هو (Export the client certificate from Computer1 and install it on Computer2).لو جابلك سيرة Microsoft Entra (Azure AD) مع سيناريو Certificates ⬅️ اختار **No** فوراً.