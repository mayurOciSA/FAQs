# FAQs

General questions
What is a virtual cloud network (VCN)?
A VCN is a customizable private network in Oracle Cloud Infrastructure. Just like a traditional data center network, a VCN provides you with complete control over your network environment. This includes assigning your own private IP address space, creating subnets, creating route tables, and configuring stateful firewalls. A single tenancy (an Oracle Cloud Infrastructure account) can have multiple VCNs, thereby providing grouping and isolation of related resources. For example, you might use multiple VCNs to separate the resources in different departments in your company.

What are the core components of a VCN?
For a complete list of components, see Overview of Networking.

How do I get started with VCN?
For a quick tutorial on how to launch an instance inside a VCN in Oracle Cloud Infrastructure, see the Getting Started Guide.

Also see these topics:

Overview of Networking
VCNs and Subnets
What IP addresses can I use inside my VCN?
When you create your VCN, you assign a contiguous IPv4 CIDR block of your choice. VCN sizes ranging from /16 (65,533 IP addresses) to /30 (1 IP address) are allowed. Example: 10.0.0.0/16, 192.168.0.0/24.

We recommend using a CIDR block from the private address ranges specified by RFC1918. If you use a non-RFC1918 CIDR block, note that it is still treated as a private IP address range and is not routable from the internet via Oracle's internet gateway.

You create subnets by subdividing the VCN's address range into contiguous IPv4 CIDR blocks. A subnet's CIDR block must fall within the VCN's CIDR block. When you launch an instance into a subnet, the instance's private IP address is allocated from the subnet's CIDR block.

Can I mark a subnet as private?
Yes. When you create a subnet, you can specify the access type: either private or public. A subnet is created with public access by default, in which case the instances in the subnet can be allocated a public IP address. Instances launched in a subnet with private access are prohibited from having public IP addresses, which ensures these instances have no direct internet access.

Can a VCN span multiple availability domains?
Yes.

Can a subnet span multiple availability domains or multiple VCNs?
Subnets can span multiple availability domains, but not multiple VCNs. If you create a regional subnet, the subnet's resources can reside in any availability domain (AD) in the region. However, if you create an AD-specific subnet, the subnet's resources must reside in the subnet's particular availability domain.

Can I create VCNs with overlapping IP addresses?
Yes. However, if you intend to connect a VCN to your on-premise network or another VCN, we recommend you ensure that the IP address ranges don’t overlap.

How many VCNs, subnets, and other networking resources can I create?
For current limits for all services and instructions for requesting a service limit increase, see the Service Limits Help Documentation.

Can I modify my subnet after I create it?
Yes. You can modify the subnet's name and change which route table, security lists, and set of DHCP options are associated with it. However, you cannot change the subnet's CIDR block.

Dynamic Routing Gateway (DRG)
In which realms is the new DRG functionality available?
The new functionality is available in the commercial realm. It will be made available in other realms in the future.

In which regions is the new DRG functionality available?
The new functionality is available in all commercial Oracle Cloud Infrastructure (OCI) regions.

Is the new functionality available on my existing DRGs (created prior to April 15, 2021)?
Yes. However, you must update the DRG using the update process specified in the documentation.

How do I control which virtual cloud networks (VCNs) can communicate with each other?
Communications between DRG attachments (including VCNs) is controlled by route tables and their associated import policies. The default VCN attachment route table enables all attached VCNs to communicate with one another. You may change the associated import policy to achieve isolation of VCNs as show here.

Can I attach a VCN from another tenancy to my DRG?
Yes. However, this requires specific IAM policies to be configured.

What is the default routing configuration in the DRG?
The DRG supports dynamic and static routing between attached networks. The DRG has two default route tables. One for your FastConnect, IPsec VPN, and RPC peering connection attachments and one for VCN attachments. You can create additional route tables for more granular control of traffic flow between attachments. The routes decide the next hop attachment depending on the destination IP address of the packet.

How does the DRG resolve route conflicts?
Static routes have higher preference than dynamic routes. You cannot create multiple static routes for the same classless inter-domain routing (CIDR). For dynamic routes, conflicts are resolved as follows:

For routes destined to VCN attachments: If identical CIDRs are imported from two VCN attachments, a deterministic and consistent forwarding decision is made about route preference. This preference order is not customer controllable.
For routes destined to FastConnect Virtual Circuits or IPsec VPN: If multiple routes with the same CIDR and as-path length are imported into a DRG route table and Equal Cost Multi-Path (ECMP) routing is disabled, the route with the lowest next-hop IP address is selected. If ECMP is enabled, both routes will be used and traffic destined to the CIDR will be ECMP routed. If the routes have identical CIDRs but different as-path lengths, only the route with the shortest as-path length will be utilized.
For routes destined to RPC/Peering attachments: If identical CIDRs are imported from two RPC/Peering attachments, the one with the shortest as-path length is used. If the two routes have the same as-path length, the route with the lowest network distance is selected. Network distance refers to the number of DRGs that the route will traverse. If two routes have identical network distances, selection is based on the following ordering: {Static, VCN, VC, IPSec}). If there is still a conflict, Oracle will route traffic via the most efficient path in its internal network.
How do routes get propagated into the DRG?
You can specify how routes are imported and exported on a per route table basis by modifying the associated import and export policies. Routes are propagated as follows:

Routes propagated to/from on-premises networks: When you connect IPsec VPNs or FastConnect virtual circuits to a DRG, routes are propagated between the DRG and your on-premises router using Border Gateway Protocol (BGP). IPsec VPNs also support static routing, but to ensure optimal automatic failover for redundancy the best practice is to use BGP.
Routes Propagated from VCNs: When you attach a VCN to a DRG, the attachment’s route table will automatically be updated to include routes to all subnets in the VCN and all CIDRs in the VCN ingress route table associated with the VCN attachment.
Routes Propagated to VCNs: DRG routes are not automatically propagated to your VCN. You must create static routes in your VCN route tables for traffic destined to on-premises networks or other OCI VCNs. These route tables are managed as a part of the VCN configuration.
Can I connect OCI VCNs with identical subnet CIDRs to the same DRG?
You can connect two OCI VCNs with overlapping CIDRs to the same DRG. The DRG’s route table makes a deterministic and consistent forwarding decision to determine which VCN attachment is the next hop for the conflicting subnet CIDRs. This preference order is not customer controllable. Due to the complexity in controlling this behavior, overlapping CIDRs are not recommended.

Can I use a single FastConnect Virtual Circuit to connect my on-premises network to all OCI VCNs regardless of the region?
Yes, the DRG now allows you to use a FastConnect in one region to communicate with resources in a VCN in another region.

Performance and limits: What are the default limits or quotas for the DRG?
Details on limits and quotas can be found in our documentation.
Should you need to exceed these limits, please create a support case.

Feature interoperability: Does the DRG support IPv6?
Yes, the DRG supports attaching VCNs with IPv6 CIDRs.

What if I want to continue to use my DRG as-is and do not need the enhanced capabilities?
The default behavior of the DRG has not changed. You need to explicitly enable the new features.

What are the contents of the default route tables associated with the DRG?
The DRG has two default route tables. One for your FastConnect, IPsec VPN, and RPC peering connection attachments and one for VCN attachments. These two default route tables implement the existing DRG behaviour.

Should I use the local peering gateway or the DRG to enable communication between VCNs in the same region?
Each VCN can have up to 10 local peering gateways and one DRG. A single DRG supports up to 300 VCN attachments. We recommend using the DRG if you need to peer with a large number of VCNs. In addition, If you want extremely high bandwidth and super low-latency traffic between two VCNs in the same region, use the scenario described in Local VCN Peering using Local Peering Gateways. Peering two VCNs in the same region through a DRG gives you more flexibility in your routing, but comes at the cost of higher latency and potentially lower bandwidth.

What is the maximum number of supported ECMP (Equal-cost multipath routing) paths?
The current limit is 8 paths

How do I upgrade my DRG so that I can use the new functionality?
Please see the ‘Upgrading a DRG’ section here.

Virtual Network Interface Cards (VNICs)
What is a virtual network interface card (VNIC)?
The servers in Oracle Cloud Infrastructure data centers have physical network interface cards (NICs). When you launch an instance on one of these servers, the instance communicates using the networking service's virtual NICs (VNICs) associated with the physical NICs. A VNIC enables a compute instance to be connected to a VCN and determines how the instance communicates with endpoints inside and outside the VCN.

Each VNIC resides in a subnet and has the following configuration:

One primary private IPv4 address from the subnet the VNIC is in, assigned by either you or Oracle
Up to 31 secondary private IPv4 addresses from the subnet the VNIC is in, assigned by either you or Oracle
Optional public IPv4 address for each private IP address
Optional hostname for DNS for each private IP address (see DNS in Your Virtual Cloud Network)
MAC address
VLAN tag assigned by Oracle and available when attachment of the VNIC to the instance is complete (relevant only for bare metal instances)
For more information, see Virtual Network Interface Cards (VNICs).

What is the primary VNIC of an instance?
Every instance in your VCN is created with a VNIC, which has a private IP address (assigned by you or Oracle) from the subnet provided at instance creation, and a corresponding public IP address. This VNIC is referred to as the primary VNIC, and its private IP address as the primary private IP address.

The primary VNIC cannot be detached from the instance. It gets automatically deleted when the instance is terminated.

What are secondary VNICs on an instance?
Every instance in your VCN has at least one VNIC, which is its primary VNIC. You can attach additional VNICs to an instance which are referred to as secondary VNICs. The secondary VNICs can belong to different VCNs or subnets.

What is the maximum number of VNICs supported on an instance?
The limit to how many VNICs can be attached to an instance varies by shape. For those limits, see Compute Shapes Support Documentation.

Can I find VNIC information from within the instance?
Yes. Query the instance metadata service available at http://169.254.169.254/opc/v1/vnics/.

Can I assign a specific private IP address to a VNIC?
Yes. In case of the primary VNIC, you can specify the private IP address at instance launch. In case of secondary VNICs, you can specify a private IP address when you attach the VNIC to an instance. The specified private IP address should belong to the same subnet the VNIC belongs to, and should not be in use.

Can I move a VNIC from one instance to another?
No. Currently, VNICs are always bound to the instance and do not exist independently. The primary VNIC is created and destroyed with the instance. All secondary VNICs are created and destroyed when they are attached and detached respectively.

Can I attach two VNICs from the same subnet to an instance?
Yes. However, attaching multiple VNICs from the same subnet CIDR block to an instance can introduce asymmetric routing, especially on instances using a variant of Linux. If you need this type of configuration, Oracle recommends assigning multiple private IP addresses to one VNIC, or using policy-based routing. For an example, see the script in the Linux: Configuring the OS for Secondary VNICs.

Can the VNICs attached to an instance belong to subnets in different availability domains (AD)?
No. All VNICs must belong to subnets in the same AD as the instance. When using regional subnets, the VNICs must be created in the same AD as the instance.

Can the VNICs attached to an instance belong to subnets in different VCNs?
Yes. You can attach secondary VNICs that belong to a subnet of a VCN that is different from the VCN of the primary VNIC.

IP Addressing
Can I assign one or more private IP addresses of my choice to my compute instance?
Every compute instance in your VCN is created with a virtual network interface card (VNIC) and is assigned a private IP address from the subnet provided at instance launch. These are the primary VNIC and its primary private IP address, respectively. You can also attach additional VNICs to an instance, referred to as secondary VNICs, which also have a primary private IP address.

You can let Oracle choose the private IP address, or you can choose it from the subnet's available pool. If the address you specify is already in use, the launch request will fail.

Additionally, you can assign secondary private IP addresses to a VNIC. Similar to primary private IP addresses, a secondary private IP address provides connectivity to destinations within your VCN and/or on premise (when there is connectivity through VPN or Oracle Cloud Infrastructure FastConnect).

Can I move a secondary private IP address from one instance's VNIC to another?
Yes. You can move a secondary private IP address from a VNIC on one instance to a VNIC on another instance, provided that both VNICs belong to the same subnet and authorization allows the operation. When using regional subnets, the secondary private IP can be moved to a VNIC in a different AD as well.

How many secondary private IP addresses can I assign to a VNIC of an instance?
Currently you can assign up to 31 secondary private IP addresses to a VNIC.

Can the instance OS discover and configure the secondary private IP address automatically (using DHCP)?
No. The OS cannot discover the secondary private IP address using mechanisms like DHCP. You need to configure the secondary private IP addresses using an OS-specific procedure. For more information, see the scripts provided in Virtual Network Interface Cards (VNICs).

What is a public IP address and how is it different from a private IP address?
A public IP address is an IPv4 address that is reachable from the internet (an internet-routable IP address). An instance in your VCN communicates with hosts on the internet via a public IP address. A private IP address is not internet routable. Instances inside the VCN communicate with each other using private IP addresses.

You can assign a public IP address to a private IP address of a compute instance, or to a load balancer instance, and enable them to communicate with the internet. For a public IP address to be reachable over the internet, the VCN it's in must have an internet gateway, and the public subnet must have route tables and security lists configured accordingly.

What are the types of public IP addresses?
There are two types of public IP addresses:

Ephemeral public IP addresses: Think of them as temporary and existing for the lifetime of the instance. At your request, Oracle will assign one from Oracle's available pool of public IP addresses. This public IP address is bound to the lifecycle of the private IP address. If you unassign the public IP address explicitly, or unassign the private IP address from the VNIC, or terminate the corresponding instance, this public IP address is released to the available pool. If you later request to assign a public IP address again, it may be a different address than before.
Reserved public IP addresses: Think of them as floating public IP addresses that reside in a compartment of your choice. They are persistent and exist beyond the lifetime of the instances they're assigned to. They belong to a specific region. You can keep a reserved public IP address unassigned within your compartment, or assign it to a private IP address of an instance or a load balancer within the same region as it was created in. You can also move it to any another private IP address within the same region.
For more details and a table comparing the two types, see Public IP Addresses Help Documentation.

Why do I need reserved public IP addresses?
A public IP address becomes the identity of your service for clients that cannot use the DNS FQDN. A reserved public IP address allows you to keep this identity regardless of any changes to the underlying resources. Here are a couple of specific scenarios that can benefit from using a reserved public IP address:

Insulate your clients from any instance-specific failures: You can assign a reserved public IP address to your instance and seamlessly move it to another instance in case of a failure. Your clients are insulated from this change as they continue to connect to the same public IP address.
Optimize usage of compute resources with no impact to users: Whether you want to change the size of an instance, or terminate your instances based on the usage patterns to save costs, a reserved public IP address enables you to expose the same public IP address to your clients.
How many reserved public IP addresses can I assign to an instance?
You can assign only one reserved public IP address to any (primary or secondary) private IP address. However, you can assign multiple private IP addresses to each VNIC attached to your instance. You can then assign a reserved public IP address to each of these private IP addresses.

There is a limit on the maximum number of reserved public IP addresses you can create in your tenancy. See the Service Limits Help Documentation.

How many ephemeral public IP addresses can I assign to an instance?
You can assign only one ephemeral public IP address to any primary private IP address of the VNIC. However, you can create and attach multiple VNICs to your instance. You can then assign an ephemeral private IP address to each of the primary IP address of each VNIC.

There is a limit on the maximum number of ephemeral public IP addresses that can be assigned to an instance. See the Service Limits Help Documentation.

Can I move an ephemeral public IP address from one VNIC/instance to another?
Yes, but only if it's assigned to a secondary private IP on a VNIC. If you move that secondary private IP to a different VNIC (which must be in the same subnet), the ephemeral public IP goes with it.

Can I move a reserved public IP address from one VNIC/instance to another?
Yes, and you can move it from one availability domain or VCN to another. The VCNs must be in the same region.

There are two ways to move a reserved public IP:

Unassign the reserved public IP and then reassign it to another private IP. The private IP can be on a VNIC in a different availability domain or VCN than the original VNIC.
If the reserved public IP is assigned to a secondary private IP, you can move the private IP to a different VNIC (which must be in the same subnet) and the reserved public IP goes with it.
When is an ephemeral public IP address released?
When you delete a private IP address, its corresponding ephemeral public IP address is released.
When you detach a secondary VNIC, any corresponding ephemeral public IP addresses are released.
When you terminate the instance, the corresponding ephemeral IP addresses are released.
Note that when you reboot the instance, there is no impact to the corresponding ephemeral public IP addresses.

What IP addresses do I see when I log on to my compute instance?
You see only the private IP address of your compute instance. If the instance is assigned a public IP address, the networking service provides a one-to-one NAT (static NAT) between the private and public IP addresses when the instance tries to communicate to a destination on the internet (through the internet gateway).

How does the traffic for a public IP address show up on the instance?
At the instance OS level, you see only the private IP address of the VNIC attached to the instance. When traffic sent to the public IP address is received, the networking service does a network address translation (NAT) from the public IP address to the corresponding private IP address. The traffic shows up inside your instance with the destination IP address set to the private IP address.

Can I assign a MAC address to my compute instance?
No. The networking service assigns the MAC address.

Is IPv6 supported?
Yes. IPv6 is supported. For more information, see IPv6 Addresses.

Do you support IP multicast or broadcast within the VCN?
No, not currently.

Does VCN support transparent IP takeover using gratuitous ARPs (GARP)?
No, not currently.

Bring Your Own IP Address (BYOIP)
What is Bring Your Own IP Address (BYOIP)?
Bring your own IP (BYOIP) allows you to import publicly routable IPv4 CIDR blocks to Oracle Cloud Infrastructure so that your resources can use them.

What are the advantages of BYOIP?
IP addresses are assets that are carefully managed and controlled by an organization. Some applications require strong IP Reputation for sending mail, other applications have established accessibility policies in global deployments and some applications have architectural decencies on their IP addresses. The migration of an IP prefix from an on-premises infrastructure to OCI allows you to minimize the impact to your customers and applications while taking advantage of all the benefits of Oracle Cloud Infrastructure. BYOIP in OCI will allow you to minimize downtime during migration by simultaneously advertising your IP address prefix from OCI and withdrawing it from the on-premises environment.

How do I get started with BYOIP?
The process to move an IP prefix for use in OCI starts in the portal under Networking>IP Management or can be initiated via the API. There are just a few easy steps.

  1 - initiate the request to bring an IP CIDR to OCI (the IP CIDR must be a /24 or larger that is owned by your organization).
  2 - Register a validation token generated from the request with the regional Internet registry (RIR) service (ARIN, RIPE, or APNIC). Follow the steps in the documentation.
  3 - After you register your token, you return to the Console and click "Validate CIDR block" so Oracle can complete the validation process. Oracle validates your CIDR block has been properly registered for the transfer and provisions your BYOIP. This step can take up to 10 business days. You are notified by email when the process is complete. You can also check the progress of this step in your work requests.

What to do with the validation token issued by Oracle?
What to do with the validation token issued by Oracle? As part of importing a BYOIP CIDR block, Oracle issues a validation token. Once you have your token, you will need to modify it slightly, adding the information shown below. You can use any text editor.

OCITOKEN:: <CIDRblock> : <validation_token>

To Submit the validation token to your RIR (Regional Internet Registry).

ARIN: Add the modified token string in the "Public Comments" section for your address range. Do not add it to the comments section for your organization.
RIPE: Add the modified token string as a new "descr" field for your address range. Do not add it to the comments section for your organization.
APNIC: Add it to the "remarks" field for your address range by emailing the modified token string to helpdesk@apnic.net. Send the email using the APNIC authorized contact for the IP addresses.

How do I use BYOIP addresses with OCI resources?
After the IP CIDR is validated you have full control of the IP CIDR. Manage the prefix by breaking it into smaller IP Pools and create reserved IP addresses for use with your resources.

What OCI resources can be used with BYOIP CIDR ?
You can assign BYOIP addresses to compute, NAT Gateway, and LBaaS instances. You can manage IP space through IP Pools and create reserved IP addresses.

Do I control the advertisement of the BYOIP prefix after it is onboarded?
Once the IP prefix is onboarded to OCI you control the advertisement and withdrawal of the prefix.

How long does BYOIP validation and onboarding take?
BYOIP validation and provisioning can take up to 10 business days. You will be notified by email when the process is complete.

Can a BYOIP prefix be moved between OCI regions?
No. The BYOIP prefix is assigned to a specific OCI region and will only be advertised in the region it is onboarded.

What are the minimum and maximum size prefixes I can use for BYOIP?
The minimum prefix for BYOIP is a /24 and the maximum prefix is a /8. You do not have to bring all of your IP space to OCI. If you own a larger IP block, you can choose which prefixes to bring to OCI.

How do I distribute my BYOIP prefix into IP Pools?
After the prefix is onboarded, you control the distribution of addresses and policy within your OCI tenant. You can keep the prefix in one IP pool or distribute the prefix down to /28's for use with your OCI resources.

Can I create reserved IP addresses from my BYOIP prefix?
Yes. You can create reserved IP addresses from a BYOIP prefix. See more information under "IP Addressing" here: https://www.oracle.com/cloud/networking/virtual-cloud-network-faq.html

Can I bring my own IPv6 prefix to OCI?
The BYOIP feature supports IPv4 prefixes only.

Can I still use Oracle owned ephemeral and reserved IP addresses if I bring my own prefix to OCI?
Yes. You can still use Oracle owned ephemeral and reserved IP addresses along with your own IP addresses. Standard limits apply to Oracle addresses.

Connectivity
What connectivity options are available for instances running in my VCN?
The instances can connect to:

The internet (via an internet gateway)
Your on-premise data center using an IPSec VPN connection or FastConnect (via a dynamic routing gateway)
Instances in peered VCNs (in the same region or another region)
Oracle Cloud Infrastructure services such as Object Storage, ADW (via a service gateway)
What is an internet gateway?
An internet gateway is a software-defined, highly available, fault-tolerant router providing public internet connectivity for resources inside your VCN. Using an internet gateway, a compute instance with a public IP address assigned to it can communicate with hosts and services on the internet.

In lieu of using an internet gateway, you can connect your VCN to your on-premise data center, from which you can route traffic to the internet via your existing network egress points.

What is a NAT gateway?
A NAT gateway is a reliable and highly available router that provides outbound-only internet connectivity for resources inside your VCN. With a NAT gateway, private instances (with only a private IP address) can initiate connections to hosts and services on the internet, but not receive inbound connections initiated from the internet.

Can I have more than one NAT gateway per VCN?
No. The default limit is one NAT gateway per VCN. We expect this to be sufficient for the vast majority of applications.

If you would like to allocate more than one NAT gateway in a specific VCN, request a limit increase. For instructions on how to request an increase in limits, see Service Limits.

Are there any new throughput limits when using a NAT gateway?
Instances get the same throughput with the NAT gateway as they do when the traffic is routed through an internet gateway. In addition, a single traffic flow through the NAT gateway is limited to 1Gbps (or less for small instance shapes).

Is there a concurrent connection limit when using a NAT gateway?
Yes. There is a limit of ~20,000 concurrent connections to a single destination IP address and port. This limit is aggregate of all connections initiated by instances across the VCN that are using the NAT gateway.

What is a dynamic routing gateway (DRG)?
A dynamic routing gateway is a software-defined, highly available, fault-tolerant router that you can add to a VCN. It provides a private path for traffic between the VCN and other networks outside the VCN's region, such as your on-premise data center or a peered VCN in another region. To connect your VCN with your on-premise data center, you can set up an IPSec VPN or FastConnect to the VCN's DRG. The connection enables your on-premise hosts and instances to communicate securely.

What is a customer premise equipment (CPE) object and why do I need it?
You use this object if you set up an IPSec VPN. It's a virtual representation of the actual router that is on-premises at your site, at your end of the VPN. When you create this object as part of setting up an IPSec VPN, you specify the public IP address of your on-premise router.

Do I need an internet gateway to establish an IPSec VPN to my on-premise data center?
No. You just need to provision a DRG, attach it to your VCN, configure the CPE object and IPSec connection, and configure the route tables.

Which customer premise equipment routers or gateways have you tested with Oracle Cloud Infrastructure IPSec VPN?
See the list of tested device configurations.

I have an IPSec VPN router that is not on the above list of tested equipment. Can I use it to connect to my VCN?
Yes. If you configure it according to Generic CPE Configuration Information. We support multiple configuration options to maximize interoperability with different VPN devices.

How do I ensure availability of my IPSec VPN connection between Oracle Cloud Infrastructure and my on-premise data center?
Oracle provisions two VPN tunnels as part of the IPSec connection. Make sure to configure both tunnels on your CPE for redundancy.

Additionally, you can deploy two CPEs routers in your on-premise data center with each configured for both tunnels.

Can I use a software VPN to connect to my VCN?
IPSec VPN is an open standard and software IPSec VPNs can interoperate with Oracle Cloud Infrastructure. You need to verify that your software IPSec VPN supports at least one supported Oracle IPSec parameter in each configuration group according to Generic CPE Configuration Information.

Does traffic between two OCI public IP addresses stay within OCI?
Yes. Traffic between two OCI public IP addresses in the same region stays within the OCI region. Traffic between OCI public IP addresses in different regions travels over the private OCI backbone. In either case, traffic does not traverse the Internet. The complete list of OCI public IP addresses can be found here: https://docs.cloud.oracle.com/en-us/iaas/tools/public_ip_ranges.json.

Service Gateway
What is the Oracle Services Network?
The Oracle Services Network is a conceptual network in Oracle Cloud Infrastructure that is reserved for Oracle services. The network comprises a list of regional CIDR blocks. Every service in the Oracle Services Network exposes a service endpoint that uses public IP addresses from the network. A large number of Oracle services are currently available in this network (see the complete list), and more services will be added in the future as they get deployed on Oracle Cloud Infrastructure.

What is a service gateway?
A service gateway lets resources in your VCN privately and securely access Oracle services in the Oracle Services Network, such as Oracle Cloud Infrastructure Object Storage, ADW, and ATP. Traffic between an instance in the VCN and a supported Oracle service uses the instance's private IP address for routing, travels over the Oracle Cloud Infrastructure fabric, and never traverses the internet. Much like the internet gateway or NAT gateway, the service gateway is a virtual device that is highly available and dynamically scales to support the network bandwidth of your VCN.

What Oracle Cloud Infrastructure services can I access through a service gateway?
Currently, you can configure the service gateway to access Oracle services in the Oracle Services Network. A large number of Oracle services are currently available in this network (see the complete list), and more services will be added in the future as they get deployed on Oracle Cloud Infrastructure.

I am currently using an internet gateway or NAT gateway to access an Oracle service such as ADW. How do I use the service gateway to access the same Oracle service endpoint?
Create a service gateway for the VCN.
Update the VCN routing to forward all traffic for Oracle services in the Oracle Services Network using the service gateway instead of using the internet gateway or NAT gateway.
For instructions, see the Access to Object Storage: Service Gateway. Please note that the service gateway allows access to Oracle services within the region to protect your data from the internet. Your applications may require access to public endpoints or services not supported by the service gateway, for example, for updates or patches. Ensure you have a NAT gateway or other access to the internet if necessary.

What is a service CIDR label?
The service gateway uses the concept of a service CIDR label, which is a string that represents all the regional public IP address ranges for the service or a group of services (for example, OCI IAD Services in Oracle Services Network is the label that maps to the regional CIDR blocks in the Oracle Services Network in us-ashburn-1). You use the service CIDR label when you configure the service gateway and route/security rules. For instructions, see the Access to Oracle Services: Service Gateway.

Can I configure the service gateway to access services running in a different region?
No. The service gateway is regional and can access only services running in the same region.

Can I allow access to an Object Storage bucket from only specific VCNs or subnets?
Yes. If you're using a service gateway, you can define an IAM policy that allows access to a bucket only if the requests come from a specific VCN or CIDR range. The IAM policy works only for traffic routed through the service gateway. Access is blocked if the IAM policy is in place and the traffic instead goes through an internet gateway. Also, be aware that the IAM policy prevents you from accessing the bucket through the console. Access is allowed only programmatically from resources in the VCN.

For an example IAM policy, see the Access to Object Storage: Service Gateway.

Can I have multiple service gateways within my VCN?
No. A VCN can have only one service gateway at this time.

Can I use a service gateway with VCN peering?
No. A VCN that is peered with another VCN that has a service gateway cannot use that service gateway to access Oracle services.

Can I leverage a service gateway to establish connectivity (through FastConnect) from my on-premise network to my VCN?
No. However, you can use FastConnect public peering to do this (without going through internet).

Are there any new throughput limits when using a service gateway?
No. Instances get the same throughput with the service gateway as they do when the traffic is routed through an internet gateway.

How much does the service gateway cost?
The service gateway is free for all Oracle Cloud Infrastructure customers.

VCN Security
What are security lists and why do I need them?
A security list provides a virtual firewall for an instance, with ingress and egress rules that specify the types of traffic allowed in and out of the instance. You can secure your compute instance by using security lists. You configure your security lists at the subnet level, which means all the instances in the subnet are subject to the same set of security list rules. The rules are enforced at the instance level and control traffic at the packet level.

What security lists are applicable to a given instance? How is the VCN's default security list involved?
A given VNIC on an instance is subject to the security lists associated with the VNIC's subnet. When you create a subnet, you specify one or more security lists to associate with it, and that can include the VCN's default security list. If you don't specify at least one security list during subnet creation, the VCN's default security list is associated with the subnet. The security lists are associated at the subnet level, but the rules apply to the VNIC's traffic at the packet level.

Can I change the security lists used by my subnet after I create the subnet?
Yes. You can edit subnet properties to add or remove security lists. You can also edit the individual rules in a security list.

How many security lists and rules can I configure?
There's a limit to the number of security lists you can create, the number of lists you can associate with a subnet, and the number of rules you can add to a given list. For current service limits and instructions on how to request an increase in limits, see the Service Limits Help Documentation.

Can I use "deny" rules within the security lists?
No. Security lists use only "allow" rules. All traffic is denied by default and only network traffic matching the attributes specified in the rules is permitted.

What type of rules are supported in the security lists?
Each rule is either stateful or stateless, and either an ingress rule or an egress rule.

With stateful rules, once a network packet matching the rule is allowed, connection tracking is used and all further network packets belonging to this connection are automatically allowed. So if you create a stateful ingress rule, both incoming traffic matching the rule and the corresponding outgoing (response) traffic are allowed.

With stateless rules, only the network packets matching the rule are allowed. So, if you create a stateless ingress rule, only the incoming traffic is allowed. You need to create a corresponding stateless egress rule to match the corresponding outgoing (response) traffic.

For more information, see the Security Lists Support Documentation.

What are network security groups and how are they different from security lists?
Network security groups and security lists are two different ways to implement security rules, which are rules that control allowed ingress and egress traffic to and from VNICs.

Security lists let you defined a set of security rules that apply to all the VNICs in a given subnet. Network security groups (NSGs) let you define a set of security rules that apply to a group of VNICs of your choice. For example: a group of Compute instances that have the same security posture.

For more information, see:

Security Rules
Network Security Groups
Is there an ordering or priority of security rules in NSGs versus security lists?
No. By default, all traffic is denied. Security rules only allow traffic. The set of rules that applies to a given VNIC is the union of these items:

The security rules in the security lists associated with the VNIC's subnet
The security rules in all NSGs that the VNIC is in
Which Oracle services support the use of NSGs?
Compute, load balancing, and database services. This means that when you create a compute instance, a load balancer, or a database system, you can specify one or more network security groups to control traffic for those resources.

With the introduction of NSG feature, do we need a security list?
With the introduction of NSGs, there's no change to security list behavior. Your VCN still has a default security list that you may optionally use with your VCN's subnets.

Can we define NSG as a source or destination for security rules?
When writing rules for an NSG, you have the option to specify an NSG as the source of traffic (for ingress rules) or the traffic's destination (for egress rules). The ability to specify an NSG means that you can easily write rules to control traffic between two different NSGs. The NSGs must be in the same VCN.

Can I write security rules that control traffic explicitly between NSGs in different VCNs?
No. When you write an NSG security rule that specifies another NSG as the source or destination, that NSG must be in the same VCN. This is true even if the other NSG is in a peered VCN.'s different from security list.

Security lists let you define a set of security rules that apply to all the VNICs in an entire subnet while network security groups (NSGs) let you define a set of security rules that apply to a group of VNICs of your choice (including the VNIC's of load balancers or database systems) within a VCN.

VCN Routing
What is a VCN route table?
A VCN route table contains rules to route traffic that's ultimately destined for locations outside the VCN.

Each rule in a route table has a destination CIDR block and a route target. When the subnet's outgoing traffic matches the destination CIDR block of the route rule, traffic is routed to the route target. Examples of common route targets include: an internet gateway or a dynamic routing gateway.

For more information, see Route Tables.

What route tables are applicable to a given instance? How is the VCN's default route table involved?
A given VNIC on an instance is subject to the route table associated with the VNIC's subnet. When you create a subnet, you specify one route table to associate with it, and that can be the VCN's default route table or another you've already created. If you don't specify a route table during subnet creation, the VCN's default route table is associated with the subnet. The route table is associated at the subnet level, but the rules apply to the VNIC's traffic at the packet level.

Can I create a route rule for any destination CIDR block?
No. Currently, you can add a route rule only for a CIDR block that doesn't overlap with the VCN's address space.

Can I change the route table used by my subnet after I create the subnet?
Yes. You can edit subnet properties to change the route table. You can also edit the individual rules in a route table.

Does VCN support source-based routing?
No. Not currently.

How many route rules can I create in a single route table?
There's a limit to the number of rules in a route table. See the Service Limits Help Documentation.

Can I use a private IP as the route target in the VCN route rule?
Yes. You can use a private IP as the target of a route rule in situations where you want to route a subnet's traffic to another instance in the same VCN. For requirements and other details, see Using a Private IP as a Route Target.

VCN Peering
What is VCN peering?
VCN peering is a process of connecting two VCNs to enable private connectivity and traffic flow between them. There are two general types of peering:

Local VCN peering (or intraregion peering): The two VCNs are in the same region. They can be in the same tenancy (in the same or different compartments), or different tenancies.
Remote VCN peering (or interregion VCN peering): The two VCNs are in different regions.
For more information, see Access to Other VCNs: Peering.

Is VCN peering supported in all regions?
Local VCN peering (or intraregion peering) is supported in all regions.
Remote VCN peering (or interregion peering) is currently supported. The list of supported regions can be found in product documentation.
Why do I need VCN peering?
With local VCN peering, you get flexibility to organize your resources in to separate VCNs and meet requirements for governance and regional presence, while enabling private connectivity across these VCNs. With cross-tenancy local VCN peering, you get flexibility to organize your resources into separate VCNs in different tenancies, while enabling private connectivity across these VCNs. You can also enable a service provider model by providing private access to your services for multiple consumer VCNs (in different tenancies) located in the same region.
With remote VCN peering, you get flexibility to organize your resources in to separate VCNs and meet your requirements for governance, multiregion presence, and DR, while enabling private connectivity across these VCNs in different regions.
What are the benefits of local VCN peering?
A no-cost, reliable alternative to connectivity models such as VPN by eliminating internet gateways, public IPs for instances, encryption, and performance bottlenecks.
Ease of peering enablement between VCNs with no scheduled downtime.
Private connectivity for resources in peered VCNs using the Oracle Cloud Infrastructure fabric’s highly redundant links with predictable bandwidth and latency.
How do I establish a local VCN peering between two VCNs?
For instructions, see Local VCN Peering.

Can I establish local peering between two VCNs with overlapping address ranges?
No. The two VCNs in a local peering relationship cannot have overlapping CIDRs.

Can I establish local peering from my VCN to two other VCNs that have overlapping IP address ranges?
Yes. If VCN-1 is peered with two other VCNs (say VCN-2 and VCN-3), those two VCNs (VCN-2 and VCN-3) can have overlapping CIDRs.

Can I establish a local peering connection to a VCN that belongs to another account?
Yes.

How many local peerings can I establish per VCN?
A given VCN can have a maximum of ten local peerings at a time.

What are the benefits of remote VCN peering?
A low-cost, reliable alternative to connectivity models such as VPN by eliminating internet gateways, public IPs for instances, encryption, and performance bottlenecks.
Ease of peering enablement between VCNs with no scheduled downtime.
Private connectivity for resources in peered VCNs using Oracle Cloud Infrastructure's highly redundant backbone links with predictable bandwidth and latency.
Do I need an internet gateway to create a remote peering connection?
No. You establish a remote peering connection using a dynamic routing gateway (DRG).

How do I establish a remote VCN peering between two VCNs?
For instructions, see Remote VCN Peering.

Can I establish remote peering between two VCNs with overlapping address ranges?
No. The two VCNs in a remote peering relationship cannot have overlapping CIDRs.

Can I establish remote peering from my VCN to two other VCNs that have overlapping IP address ranges?
No. If VCN-1 is remotely peered with two other VCNs (VCN-2 and VCN-3), those two VCNs (VCN-2 and VCN-3) cannot have overlapping CIDRs.

Can I establish a remote peering connection to a VCN that belongs to another account?
No.

Is my remote VCN peering traffic encrypted?
Yes. Your remote VCN peering traffic is encrypted using industry standard link encryption.

How many remote peerings can I establish per VCN?
A given VCN can have a maximum of ten remote peerings at a time.

As administrator of VCN-A, can I control connectivity to only a specific subnet on the peered VCN-B?
Yes. You can use VCN-A's route tables and security lists to control connectivity to the peered VCN-B. You can allow connectivity to the full address range of VCN-B or limit it to one or more subnets.

As administrator of VCN-A, can I control which subnets of VCN-A are accessible from the peered VCN-B?
Yes. After the local or remote peering is established, the instances in VCN-B can send traffic to the full address range of VCN-A. However, you can limit access from instances in VCN-B to a specific subnet in VCN-A by using appropriate ingress rules in the subnet's security lists.

Is there a performance impact based on throughput and latency over the established local peering between two VCNs?
No. Throughput and latency should be close to intra-VCN connections. Traffic over the local peering has similar availability and bandwidth constraints as the traffic between instances in a VCN.

Is there a performance impact based on throughput and latency over the established remote peering between two VCNs?
Remote VCN peering uses the Oracle Cloud Infrastructure inter-region backbone, which is designed to deliver superior performance and availability characteristics, and a 99.5% availability SLA for inter-region connectivity. As a guideline, Oracle provides more than 75Mbps throughput and latency less than 60ms between U.S. regions, 80ms between the EU and the U.S., 175ms between U.S. and APAC, and 275ms between EU and APAC.

What is the price for VCN peering?
Local peering (intraregion): No charge.
Remote peering (inter-region): Pricing is based on outbound data transfer. Refer to the latest published pricing for “Outbound Data Transfer”.
VCN Transit Routing
What is VCN transit routing (VTR)?
The VCN transit routing (VTR) solution is based on a hub-and-spoke topology and enables the hub VCN to provide transit connectivity between multiple spoke VCNs (within the region) and on-premise networks. Only a single FastConnect or IPSec VPN (connected to the hub VCN) is required for the on-premise network to communicate with all the spoke VCNs.

How do I get started with VCN transit routing (VTR)?
See the instructions in Setting Up VCN Transit Routing in the Console.

What kinds of remote networks can the spoke VCNs access using the hub VCN?
Currently, the spoke VCNs can access your on-premise networks using the hub VCN.

Can I configure the hub VCN to provide connectivity to spoke VCNs in remote Oracle Cloud Infrastructure regions?
No. The VCN transit routing solution only supports consolidated connectivity between VCNs in the same region.

Can I configure the hub VCN so a spoke VCN can access only specific subnets in the on-premise network?
Yes. You control this with the route table associated with the LPG on the hub VCN. You can configure restrictive route rules that specify only the on-premise subnets that you want to make available to the spoke VCN. The routes advertised to the spoke VCN are those in that route table and the hub VCN's CIDR.

Can I configure the hub VCN so the on-premise network can access only specific subnets in the spoke VCN?
Yes. You control this with the route table associated with the DRG on the hub VCN. You can configure restrictive route rules that specify only the spoke VCN subnets that you want to make available to the on-premise network. The routes advertised to the on-premise network are those in that route table and the hub VCN's CIDR.

Is there a limit to the number of spoke VCNs that can peer with the hub VCN?
Yes. The hub VCN is limited to a maximum of 10 local peerings with spoke VCNs.

Can I configure the hub VCN so the on-premise network can access Oracle services?
Yes. You can add a service gateway to a VCN that is connected to your on-premise network by way of FastConnect or Site-to-Site VPN. You can then configure route rules on the route tables associated with VCN's DRG and service gateway to direct on-premises traffic through the VCN to the Oracle services of interest. You on-premises hosts can use their private IPs when communicating with the Oracle services, and the traffic does not go over the internet.

For more information, see Transit Routing: Private Access to Oracle Services.

Can I enable routing via a network virtual appliance (such as a firewall instance) in the hub VCN?
Yes. You can set up transit routing through a private IP in the hub VCN. In this case, you route the traffic to a private IP on the firewall instance in the hub VCN. The firewall instance can inspect all traffic between you on-premise network and spoke VCNs.

Are there any performance limits related to VCN transit routing?
If you are routing via a firewall instance (or any other network virtual appliance) in the hub VCN, the performance limits are based on the I/O characteristics of the network virtual appliance. If you're not routing the traffic through a network virtual appliance—and are routing directly through the hub VCN's gateways—there are no performance limits. The gateways are virtual devices that are highly available and dynamically scale to support the network bandwidth requirements of your network.

DHCP Options
What are DHCP options?

The Dynamic Host Configuration Protocol (DHCP) provides a framework for passing configuration information to hosts on an IP network. Configuration parameters and other control information are carried to the instance in the options field ( RFC 2132) of the DHCP message. Each subnet in a VCN can have a single set of DHCP options associated with it.

Which DHCP Options can I configure?
You can configure two options that control how instances in your VCN resolve domain name system (DNS) hostnames:

Search domain. You can specify a single search domain.
DNS type. Choose either:
Internet and VCN resolver (default)
Custom resolver (you can specify up to three DNS servers of your choice which you set up, managed, and maintain by you)
When resolving a DNS query, the instance's OS uses the DNS servers specified with DNS type and appends the search domain to the value being queried. For more information, see DHCP Options.

Can I change the DHCP options used by my subnet after I create the subnet?
Yes. You can edit subnet properties to change which set of DHCP options the subnet uses. You can also change the values of the DHCP options.

DNS
How do I configure a DNS hostname for my instance?
When you launch an instance, you can specify a hostname for the instance, along with a display name. This hostname, combined with the subnet's domain name, becomes the fully qualified domain name (FQDN) of your instance. This FQDN is unique within the VCN and resolves to the private IP address of your instance. For more details, see DNS in Your Virtual Cloud Network.

Note that to specify a hostname for the instance, the VCN and subnet must be configured to enable DNS hostnames.

How do I configure the VCN and subnet to enable hostnames?
When you create a VCN, you can specify its DNS label. This, combined with the parent domain oraclevcn.com, becomes the domain name of the VCN.

When you create a subnet, you can specify its DNS label. This, combined with the VCN's domain name, becomes the domain name of the subnet.

You can enable a hostname for a compute instance only if the VCN and subnet are both created with a DNS label.

What is a DNS hostname of a compute instance?
A DNS hostname is a name that corresponds to the IP address of an instance connected to a network. In case of an Oracle Cloud Infrastructure VCN, every instance can be configured with a DNS hostname that corresponds to the private address of the instance.

A fully qualified domain name (FQDN) of an instance looks like hostname.subnetdnslabel.vcndnslabel.oraclevcn.com, where hostname is the DNS hostname of the instance, subnetdnslabel and vcndnslabel are the DNS labels of the instance's subnet and the VCN respectively.

The parent domain oraclevcn.com is reserved for use with DNS hostnames created in Oracle Cloud Infrastructure.

Can I rename the hostname of my instance?
Yes.

Can I rename the DNS label of an existing VCN or a subnet?
No.

If my subnet is configured to use custom resolver for DNS, are DNS hostnames created for instances in this subnet?
Yes. DNS hostnames are created for instances regardless of the DNS type selected for the subnet.

Can my instance resolve hostnames of instances in other VCNs?
No. The instance can resolve hostnames only of instances within the same VCN.

Can I configure my custom DNS servers to resolve VCN internal DNS hostnames?
Yes, you can do this with custom DNS servers set up within the VCN. You can configure the custom DNS servers to use 169.254.169.254 as the forwarder for the VCN domain (like contoso.oraclevcn.com).

Note that the custom DNS servers must be configured in a subnet that uses "internet and VCN resolver" as the DNS type (to allow access to the 169.254.169.254 IP address).

For an example of an implementation with the Oracle Terraform provider, see Hybrid DNS Configuration.

Billing
Do I get charged for using a VCN?
There is no charge for creating VCNs and using them. However, usage charges for other Oracle Cloud Infrastructure services (including compute and block volumes) and data transfer charges apply at the published rates. There are no data transfer charges for any communication among resources within a VCN.

How will I be charged when I connect my VCN to my on-premise data center using an IPSec VPN?
You are charged only the published Oracle Cloud Infrastructure outbound data transfer rates. There is no hourly or monthly VPN connection charge.

What are my usage charges if I use other resources, such as the database or object storage service, from instances inside my VCN?
You don’t incur data transfer charges when accessing other public Oracle Cloud Infrastructure services, such as object storage, in the same region. All network traffic via private or public IPs between your instances and other resources inside your VCN, such as a database or load balancer, is free of data transfer charges.

If you access public Oracle Cloud Infrastructure resources via your IPSec VPN from inside your VCN, you incur the published outbound data transfer charges.

Do your prices include taxes?
Unless otherwise noted, the Oracle Cloud Infrastructure prices, including outbound data transfer charges, exclude applicable taxes and duties, including VAT and any applicable sales tax.

