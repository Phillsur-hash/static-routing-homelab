    # Cisco Packet Tracer — Static Routing Homelab

    A hands-on networking lab demonstrating how to configure static routes on Cisco routers to enable communication between two separate LANs through an interconnected router network.
    
    Overview
    This lab was built in Cisco Packet Tracer to practice Layer 3 routing, IP addressing, default gateway configuration, and static route implementation.

    Two Cisco routers, NY and LA, connect two separate networks. Each router serves as the gateway for its local LAN and uses a static route to reach the remote LAN through the inter-router link.
    
    Objectives
        Configure IPv4 addresses on router interfaces.
        Assign IP addresses and default gateways to end devices.
        Configure static routes using Cisco IOS.
        Verify routing table entries.
        Test end-to-end connectivity using ICMP ping.
        Troubleshoot routing configuration errors.

    !Network topology

    The network consists of three IPv4 subnets:
        192.168.10.0/24 — New York LAN
        192.168.20.0/24 — Inter-router transit network
        192.168.30.0/24 — Los Angeles LAN
        
    Topology diagram

     PC0                                                     PC2
    192.168.10.10                                        192.168.30.10
          |                                                     |
         SW1                                                   SW2
          |                                                     |
     G0/1|                                                     |G0/1
          |                                                     |
       +------+           Transit Network                  +------+
       |  NY  | G0/0 --------------------------- G0/0 |  LA  |
       +------+        192.168.20.0/24                 +------+
          |          .1                     .2                |
          |                                                   |
          +------------------ Routed Networks ----------------+

    IP Addressing Plan
    
    Device	Interface	IP address	Subnet mask
    NY	G0/0	192.168.20.1	255.255.255.0
    NY	G0/1	192.168.10.1	255.255.255.0
    LA	G0/0	192.168.20.2	255.255.255.0
    LA	G0/1	192.168.30.1	255.255.255.0
    PC0	NIC	192.168.10.10	255.255.255.0
    PC2	NIC	192.168.30.10	255.255.255.0
    End-device default gateways
    Device	Default gateway
    PC0	192.168.10.1
    PC2	192.168.30.1
    
    Configuration
    
    1. Configure Router NY
    cisco

    enable
    configure terminal

    hostname NY

    interface gigabitEthernet0/0
     ip address 192.168.20.1 255.255.255.0
     no shutdown
     exit

    interface gigabitEthernet0/1
     ip address 192.168.10.1 255.255.255.0
     no shutdown
     exit

    ip route 192.168.30.0 255.255.255.0 192.168.20.2

    end
    copy running-config startup-config

    Explanation: NY knows its directly connected networks but needs a static route to reach the remote 192.168.30.0/24 LAN. Traffic for that network is forwarded to LA at 192.168.20.2.
    
    2. Configure Router LA
    cisco

    enable
    configure terminal

    hostname LA

    interface gigabitEthernet0/0
     ip address 192.168.20.2 255.255.255.0
     no shutdown
     exit

    interface gigabitEthernet0/1
     ip address 192.168.30.1 255.255.255.0
     no shutdown
     exit

    ip route 192.168.10.0 255.255.255.0 192.168.20.1

    end
    copy running-config startup-config

    Explanation: LA uses a static route to reach the remote 192.168.10.0/24 LAN through NY at 192.168.20.1.
    
    3. Configure the PCs

    In Cisco Packet Tracer, open each PC and navigate to Desktop → IP Configuration.

    PC0
    text

    IP Address:      192.168.10.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 192.168.10.1

    PC2
    text

    IP Address:      192.168.30.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 192.168.30.1

    Verification and Testing
    
    1. Verify interface status

    Run on both routers:
    cisco

    show ip interface brief

    Confirm that the configured interfaces have the correct IP addresses and that their status and protocol are up.
    2. Verify the routing table

    On NY:
    cisco

    show ip route

    Expected static route:
    text

    S    192.168.30.0/24 [1/0] via 192.168.20.2

    On LA:
    cisco

    show ip route

    Expected static route:
    text

    S    192.168.10.0/24 [1/0] via 192.168.20.1

    The S code indicates a static route. The [1/0] value represents the route's administrative distance and metric.
    
    3. Test connectivity

    From PC0, open Desktop → Command Prompt and run:
    text

    ping 192.168.20.2
    ping 192.168.30.1
    ping 192.168.30.10

    These tests verify connectivity to:
        The remote router's transit interface.
        The remote router's LAN interface.
        The destination PC on the remote LAN.
    Results

    The screenshot shows successful replies from 192.168.20.2, 192.168.30.1, and 192.168.30.10. The first ping to each destination experiences a timeout, followed by successful replies. This can occur while ARP resolves the MAC address of the next-hop device or destination on the local segment.

    The end-to-end ping to PC2 confirms that the static routes and host gateway configuration are working.
    Troubleshooting Notes

    During configuration, an incorrect next-hop address was initially entered on NY. The route was corrected to use LA's transit address, 192.168.20.2.

    Useful troubleshooting commands include:
    cisco

    show ip interface brief
    show ip route
    show running-config
    ping 192.168.20.2

    Common issues to check:
        Incorrect subnet masks or IP addresses.
        Missing or incorrect static routes.
        Incorrect PC default gateways.
        Router interfaces that are administratively down.
        Incorrect next-hop addresses.
        Missing return routes on the remote router.
        
    Key Takeaways

    This lab reinforced the following networking concepts:
        Static routing between multiple IPv4 networks.
        Router interface configuration using Cisco IOS.
        The role of next-hop IP addresses in route selection.
        The importance of return routes for two-way communication.
        The relationship between host default gateways and inter-network communication.
        Using routing tables and ICMP ping to validate network connectivity.
        
    Repository Structure

    static-routing-homelab/
    ├── README.md
    ├── packet-tracer/
    │   └── static-routing-homelab.pkt
    └── images/
        └── static-routing-topology.png

    Tools Used
        Cisco Packet Tracer
        Cisco IOS CLI
	Command Prompt
        IPv4 addressing and subnetting
        Static routing
        ICMP and ARP troubleshooting
   
 Author
Phillip Surrett

