# Azure VM

الـ Hypervisor بتاع Microsoft اسمه **Microsoft Azure Hypervisor**، نسخة معدّلة من Hyper-V
لما بتعمل VM بتحدد 4 حاجات 

1) الصورة (Image):
	 نسخه ال OS اللي هتشتغل بيها (Linux Ubuntu , redhat ... windows server ) كمان ممكن تستخدم **Marketplace Images** جاهزة (زي VM فيها WordPress متثبت مسبقًا)، أو تعمل **Custom Image** بنفسك من إعداد معين عملته وعايز تكرره
	 
2) الحجم (Size)
	 أزور عندها أكتر من 50 سلسلة (series) مختلفة، لكن **4 سلاسل بس بتغطي 90% من الاستخدامات العادية**كل سلسلة اتصممت لنوع Workload معين
	 

| **Series** | **usage**          | **when to use?**                                  |
| ---------- | ------------------ | ------------------------------------------------- |
| B-series   | Burstable اقتصاديه | Light Workloads, personal projects                |
| D-series   | General Purpose    | Normal Workloads (Web apps,APIs)                  |
| E-series   | Memory-optimized   | Data Bases, high Ram usage apps                   |
| F-series   | Compute-optimized  | High CPU Usage (batch processing, gaming servers) |
| N-series   | GPU-enabled        | Training AI models                                |

![[Pasted image 20260902110948.png]]


![[Pasted image 20260902112530.png]]


[[VNets-Subnets-and-Peering]]



[[DNS-and-Endpoints]]
