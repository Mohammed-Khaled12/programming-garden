
# AZ-104

## Quiz 1
### Domain 1: Manage Azure Identities and Governance

**1. You are an Azure Administrator for a company. You need to ensure that a new user, User1, can manage virtual machines (VMs) in a specific resource group named `RG-Prod`. User1 must not be able to manage the virtual networks or storage accounts in `RG-Prod`. What is the best way to achieve this using the principle of least privilege?**
A) Assign the Contributor role to User1 at the subscription level.
B) Assign the Virtual Machine Contributor role to User1 at the `RG-Prod` resource group level.
C) Assign the Owner role to User1 at the VM level for each VM.
D) Assign the Virtual Machine Contributor role to User1 at the Azure tenant level.

**2. Your company has an Azure subscription. You need to ensure that all new resources created in the subscription have a tag named `CostCenter` with a value of `IT`. If a user attempts to create a resource without this tag, the deployment should automatically add the tag and the value. Which Azure Policy effect should you use?**
A) Deny
B) Append
C) Modify
D) Audit

**3. You have an Azure resource group named `RG-Finance` that contains critical database servers. You apply a `ReadOnly` lock to the resource group. Another administrator has the Owner role for `RG-Finance`. What actions can the other administrator perform on the resources within `RG-Finance`?**
A) They can start and stop the database VMs, but cannot delete them.
B) They can read the resources, but cannot modify or delete them.
C) They can delete the resources because the Owner role supersedes the resource lock.
D) They can remove the lock, and then modify or delete the resources.

---

### Domain 2: Implement and Manage Storage

**4. You need to deploy an Azure Storage account that provides the highest level of redundancy. The data must remain available even if an entire Azure region experiences an outage. Furthermore, you need read access to the data in the secondary region even when the primary region is functioning normally. Which replication strategy should you choose?**
A) Geo-redundant storage (GRS)
B) Zone-redundant storage (ZRS)
C) Read-access geo-redundant storage (RA-GRS)
D) Geo-zone-redundant storage (GZRS)

**5. You are configuring Azure File Sync to synchronize an on-premises Windows Server 2022 file server with an Azure file share. You want to ensure that frequently accessed files are cached locally on the on-premises server, while infrequently accessed files are stored only in Azure to save local disk space. Which feature should you enable?**
A) Offline Files
B) Cloud Tiering
C) Data Deduplication
D) Storage Lifecycle Management

**6. You have a Blob storage account containing thousands of video files. You need to implement a solution to automatically move files that have not been modified in 30 days to the Cool tier, and files not modified in 90 days to the Archive tier. What is the most administrative-effort-free way to achieve this?**
A) Create an Azure Logic App that runs daily to check blob modification dates and changes the tier.
B) Create an Azure Automation runbook using PowerShell to loop through the blobs and change their tiers.
C) Configure a Lifecycle Management policy on the storage account.
D) Enable Blob versioning and set the default access tier to Archive.

---

### Domain 3: Deploy and Manage Azure Compute Resources

**7. You are planning to deploy two critical web server VMs in an Azure region. You need to ensure a 99.99% Service Level Agreement (SLA) for the uptime of these VMs. How should you deploy the VMs?**
A) Deploy both VMs into the same Availability Set.
B) Deploy both VMs into different Availability Zones within the same region.
C) Deploy the VMs into different resource groups.
D) Deploy both VMs using Premium SSDs without Availability Sets or Zones.

**8. You deploy an Azure Virtual Machine Scale Set (VMSS) to host a web application. You need to configure autoscaling so that two additional VM instances are added when the average CPU utilization exceeds 75% for 10 minutes. What do you need to configure?**
A) A scale-in rule in the scale set.
B) An Azure Monitor Alert rule.
C) A scale-out rule in the scale set.
D) An Azure Application Gateway routing rule.

**9. You have an Azure App Service web app running on a Standard tier App Service plan. The application is experiencing high latency due to increased user load. You need to increase the compute resources available to the application by moving to a Premium App Service plan. Which action are you performing?**
A) Scaling out
B) Scaling up
C) Autoscaling
D) Load balancing

---

### Domain 4: Configure and Manage Virtual Networking

**10. You have two virtual networks: `VNet1` (10.1.0.0/16) and `VNet2` (10.2.0.0/16). `VNet1` has an Azure VPN Gateway connected to your on-premises network. You create a VNet peering between `VNet1` and `VNet2`. You need to ensure that resources in `VNet2` can communicate with your on-premises network using the VPN Gateway in `VNet1`. What must you configure on the peering settings?**
A) Enable "Allow gateway transit" on VNet1 and "Use remote gateways" on VNet2.
B) Enable "Use remote gateways" on VNet1 and "Allow gateway transit" on VNet2.
C) Enable "Allow forwarded traffic" on both VNets.
D) Create a Route Table in VNet2 pointing to the Internet.

**11. You are reviewing the Network Security Group (NSG) applied to a VM's network interface. There are three inbound security rules configured:**

* Rule 1: Priority 100, Allow Port 80
* Rule 2: Priority 200, Deny Port 80
* Rule 3: Priority 300, Allow Port 443
**If an inbound HTTP request (Port 80) arrives at the VM, what happens?**
A) The request is denied because Rule 2 explicitly blocks it.
B) The request is allowed because Rule 1 is processed first and has a lower priority number.
C) The request is denied due to the default implicit deny rule.
D) The request is allowed because Allow rules always take precedence over Deny rules.

**12. You need to load balance incoming internet traffic across several Azure VMs based on the URL path requested by the user (e.g., `/images` goes to VM1, `/video` goes to VM2). Which Azure service should you use?**
A) Azure Standard Load Balancer
B) Azure Application Gateway
C) Azure Traffic Manager
D) Azure Route Server

**13. You create a private DNS zone named `corp.local` in Azure. You need to ensure that VMs in a virtual network named `VNet-Core` can resolve the DNS names of other VMs registered in this zone. What must you do first?**
A) Add a virtual network link from the private DNS zone to `VNet-Core`.
B) Configure the DNS settings of the VMs to use Azure provided DNS.
C) Create a public DNS zone and create a CNAME record.
D) Deploy an Azure Firewall in `VNet-Core`.

---

### Domain 5: Monitor and Maintain Azure Resources

**14. You are configuring Azure Backup for several virtual machines. You create a Recovery Services vault. Which of the following is a strict requirement for backing up an Azure VM to a Recovery Services vault?**
A) The VM and the vault must be in the same resource group.
B) The VM and the vault must be in the same Azure region.
C) The VM must be stopped (deallocated) before the first backup.
D) The vault must be configured with Geo-redundant storage (GRS).

**15. You are using Azure Monitor and Log Analytics to query diagnostic logs from your Azure resources. Which query language must you use to write your Log Analytics queries?**
A) Transact-SQL (T-SQL)
B) PowerShell
C) Kusto Query Language (KQL)
D) Python

---

### Answer Key and Explanations

**1. Correct Answer: B**

* **Explanation:** RBAC (Role-Based Access Control) allows you to assign permissions at different scopes (Management Group, Subscription, Resource Group, or Resource). Assigning the *Virtual Machine Contributor* role limits the user to managing only VMs (not networks or storage). Assigning it at the `RG-Prod` resource group scope ensures they only have this access for resources within that specific group, strictly adhering to the principle of least privilege.

**2. Correct Answer: C**

* **Explanation:** The `Modify` effect is used to add, update, or remove properties or tags on a resource during creation or update. (Note: `Append` can also add fields, but `Modify` is the modern and recommended approach specifically designed for remediating tags on existing resources and adding them during deployment). `Deny` would block the creation entirely, and `Audit` would only log the missing tag.

**3. Correct Answer: D**

* **Explanation:** A `ReadOnly` lock prevents any user—regardless of their RBAC permissions (including Owners)—from modifying or deleting the resource. The lock must be explicitly removed before modifications can happen. Because the other administrator is an Owner, they have the permission required (`Microsoft.Authorization/locks/delete`) to remove the lock first, and then they can proceed to modify or delete the resources.

**4. Correct Answer: C**

* **Explanation:** Geo-redundant storage (GRS) replicates data to a secondary region, but the secondary region is not readable unless Microsoft initiates a failover. Read-access geo-redundant storage (RA-GRS) provides the cross-region redundancy of GRS *and* provides a secondary endpoint so you can read your data from the secondary region at all times.

**5. Correct Answer: B**

* **Explanation:** Cloud Tiering is a feature of Azure File Sync that allows you to cache frequently accessed files on your local on-premises server while tiering less frequently accessed (cool) files to the cloud (Azure file share). This leaves a "pointer" on the local server, saving local disk space.

**6. Correct Answer: C**

* **Explanation:** Lifecycle Management in Azure Storage allows you to create rules (policies) that automatically transition blobs to a cooler storage tier (Cool or Archive) or delete them based on the time since they were last modified or created. This is built-in and requires no custom scripts or Logic Apps.

**7. Correct Answer: B**

* **Explanation:** To achieve a 99.99% SLA for virtual machines in Azure, you must deploy two or more VMs across different Availability Zones in the same region. Deploying them in an Availability Set (Option A) provides a 99.95% SLA.

**8. Correct Answer: C**

* **Explanation:** To add more instances to a VMSS based on metrics like CPU utilization, you create a "scale-out" rule. A "scale-in" rule removes instances when demand drops.

**9. Correct Answer: B**

* **Explanation:** Changing the pricing tier of an App Service plan to get more CPU, memory, or disk space (e.g., moving from Standard to Premium) is known as "scaling up". Adding more instances of the same size to handle load is known as "scaling out".

**10. Correct Answer: A**

* **Explanation:** When configuring VNet peering where one VNet has a VPN Gateway and the other needs to use it, you must configure the VNet with the gateway (`VNet1`) to "Allow gateway transit". You must configure the VNet without the gateway (`VNet2`) to "Use remote gateways".

**11. Correct Answer: B**

* **Explanation:** NSG rules are processed in priority order, starting from the lowest number (100) to the highest (4096). Once a rule matches the traffic, processing stops. Because Rule 1 (Priority 100) matches Port 80 and allows it, the traffic is allowed, and Rule 2 is never evaluated.

**12. Correct Answer: B**

* **Explanation:** URL path-based routing requires a Layer 7 (application layer) load balancer. Azure Application Gateway operates at Layer 7 and supports URL path-based routing. Azure Standard Load Balancer operates at Layer 4 (transport layer - TCP/UDP) and cannot route traffic based on URL paths.

**13. Correct Answer: A**

* **Explanation:** For resources in a virtual network to resolve DNS records in a private Azure DNS zone, you must create a virtual network link between the private DNS zone and the virtual network.

**14. Correct Answer: B**

* **Explanation:** Azure Backup requires that the Recovery Services vault and the Azure virtual machine being backed up reside in the exact same Azure region. They do not need to be in the same resource group.

**15. Correct Answer: C**

* **Explanation:** Azure Monitor and Log Analytics rely entirely on the Kusto Query Language (KQL) to search, filter, and analyze massive volumes of log data.