
# Lab 1 CairoStore

تقدر تحله بعد الفيديو رقم 22 في AZ-104


You are an Azure Administrator at a company named **CairoStore**. You have an application consisting of:

* Web server
* Application server
* Database
* Storage Account for images and files

The objective is to design a secure network using the concepts you have studied up to **Service Endpoints**.

## Constraints

* No server is allowed to accept direct access from the Internet except the Web server.
* The Application server must not receive Internet traffic.
* The Database must only receive traffic from the Application subnet.
* The Storage Account must not be publicly accessible.
* A company branch needs to connect to the main network.
* Use the minimum possible number of resources to reduce costs.
* Do not use Private Endpoints or Load Balancers; you haven't covered them yet.

## Your Task

Create the following design, choosing the exact details yourself:

### 1. Address Planning

Design an Address Space for:

* Main Network
* Branch Network
* Web subnet
* App subnet
* DB subnet

**Condition:** Both networks will need to connect later, so there must be no overlap between them.

Fill out a table like:

| Network | Address Range |
| --- | --- |
| Main VNet | ? |
| Branch VNet | ? |
| Web Subnet | ? |
| App Subnet | ? |
| DB Subnet | ? |

### 2. Network Security

Create NSG rules to fulfill the following scenario:

* Internet users access the Web server over HTTP.
* Web server connects to the App server on a port of your choice.
* App server connects to the Database on a port of your choice.
* No direct access from the Internet to the App server or Database.

Draft the rules before applying them:

| Priority | Source | Destination | Port | Action |
| --- | --- | --- | --- | --- |
| ? | ? | ? | ? | ? |

**Think:** Do you associate the NSG with the NIC or the Subnet? Which approach minimizes repetition?

### 3. Storage Using Service Endpoints

The company wants the Storage Account to allow access **only from the App subnet**.

Design the solution so that:

* The appropriate Service Endpoint is enabled on the correct subnet.
* The Storage Account restricts access to the designated network only.
* Do not select "Allow access from all networks."

**Key Question:**

Why must you enable the Service Endpoint on the subnet before referencing it in the Storage Account settings?

### 4. VNet Peering

Peer the Main VNet with the Branch VNet.

Before implementing, answer:

1. Do you need one-way or two-way peering?
2. What happens if the Main VNet is `10.10.0.0/16` and the Branch VNet is also `10.10.0.0/16`?
3. If you add a third VNet connected only to the Branch VNet, will it automatically reach the Main VNet? Why?

### 5. Route Table

Assume the company wants to route App subnet traffic to a specific path within the network.

Create a Route Table, but first determine:

* What is the Destination Address Prefix?
* What is the appropriate Next Hop type?
* Which subnet requires the Route Table?
* Does a Route Table allow/deny traffic, or does it only determine the path?

Do not add arbitrary routes just to complete the task. State the reasoning behind every route you add.

## Checkpoints

After each section, stop and reflect on the following:

### Checkpoint 1

If you place the Web and Database servers in the same subnet:

* Will the application work?
* What is the security concern?
* Can an NSG alone compensate for poor network segmentation?

### Checkpoint 2

If the Storage Account allows access from the VNet, but the Service Endpoint is not enabled:

* What missing link is preventing access?
* Is an NSG responsible for controlling this type of access?

### Checkpoint 3

If you delete an Allow rule from an NSG:

* Will the Route Table still allow the traffic?
* Which evaluation fails first: routing or security permissions?

## Hints

Use these only if you get stuck:

**Hint 1:** Start with address space planning before creating any resources.

**Hint 2:** The Web subnet is the only subnet that requires a rule allowing inbound traffic from the Internet.

**Hint 3:** Service Endpoints consist of two parts: subnet configuration and service-side restriction.

**Hint 4:** VNet Peering does not provide transitive connectivity.

**Hint 5:** An NSG decides whether traffic is allowed, whereas a Route Table decides where traffic goes.

## Final Deliverable

At the end of the lab, compile a document or set of notes containing:

1. A simple diagram of the architecture.
2. Address Space table.
3. NSG Rules table.
4. Rationale behind each subnet choice.
5. Reason for choosing Service Endpoints.
6. Answers to all Checkpoint questions.
7. Screenshots or descriptions confirming:
* Peering status = Connected
* NSG associated with the correct scope
* Service Endpoint enabled
* Storage Account restricted to the target network



## Grading & Self-Assessment

Score yourself out of 10:

* 2 points: Address space planning without overlap
* 2 points: Logical subnet architecture
* 2 points: Secure NSG configuration
* 2 points: Properly configured Service Endpoint
* 1 point: Correct VNet Peering set up
* 1 point: Clear understanding of the difference between Route Tables and NSGs

If you score below 7, do not redo the entire lab; review and fix only the section where you lost points. Afterwards, send me **your design and answers before deployment**, and I will review them like an exam review without giving away direct solutions.


