<center><font size="5"><strong>Table of Content</strong></font></center>



[TOC]



# Introduction

In today's rapidly evolving landscape of network management and security, the Network Traffic Broker (NTB) stands out as a pivotal tool, transforming the way organizations modify their existing networks to incorporate NTB and enhance security systems. SSL inspection, a crucial process for decrypting and inspecting secure traffic, and L3 bridging, a method for transparently passing traffic between different IP subnets or networks, have become indispensable components in today's complex network environments. NTB takes center stage by enabling devices like Intrusion Prevention Systems (IPS), Firewalls, Anti-Virus solutions, and more to seamlessly utilize SSL inspection and L3 bridging capabilities.

This strategic shift not only augments the overall flexibility of network configurations but also empowers organizations to efficiently integrate their existing equipment with new security functions. In this era of heightened cyber threats, mastering the implementation of NTB for SSL inspection and L3 bridge solutions is essential for network professionals seeking to fortify their network's resilience.

Transforming your existing network to incorporate the Network Traffic Broker (NTB) for SSL inspection and L3 bridge solutions is a multifaceted endeavor. In this training guide, we will delve into one of the primary methods of configuring NTB for existing networks and security systems: using the user-friendly Web GUI interface. While NTB can also be configured via the console interface, our focus will be on harnessing the web-based interface to effectively modify your network and integrate enhanced security measures. This guide aims to provide a step-by-step walkthrough, offering valuable insights into the process of configuring the NTB for SSL inspection and L3 bridge solutions through the web-based interface within the context of an existing network. By the end of this tutorial, you will have a solid foundation for seamlessly incorporating NTB into your network infrastructure, thus ensuring efficient SSL inspection and L3 bridging with ease.


# Web UI

To access the Web UI of any NTB model, you can connect to the MGMT port. The default IP address for the NTB is set to 192.168.1.150. When initially accessing the Web UI, you can utilize the default login credentials, where the username is "array," and the password is "admin," as illustrated in Figure 1. Once logged in, you will gain entry to the main page (depicted in Figure 2). From this main page, navigate to the "NTB XML" tab. This section allows users to harness the full capabilities of the NTB by utilizing XML tags for configuration and management.  

<center><img src="./pic/WebUI.png" width="120%"/></center>
In this training document, once you access the NTB XML interface, you will encounter file tabs, file action buttons, and file edit windows, as illustrated in Figure 3. Let's explore the functions of each element in detail :

1. ***File Tabs:*** The file tabs display the available files for configuring NTB data task rules. Currently, the system supports two types of files: JavaScript and XML. The active file's tab will be highlighted in white, while the inactive ones will appear in gray. You can also make modifications to the content of these files through the file edit windows . 

2. ***File Action Buttons:*** There are five types of action buttons: "Format," "Reset," "Submit," "Save," and "Load."

   * The "Format" button checks whether the current active file complies with the proper XML format.
   * Clicking "Reset" reverts the current active file to its last submitted state, discarding any unsaved changes.
   * The "Submit" button for JavaScript files saves the current status of the JavaScript file. However, when dealing with XML files, clicking "Submit" will adjust the NTB's settings accordingly.
   * The "Save" and "Load" buttons are used for saving and loading the current XML file to and from disk space. 
  
3. ***File Edit Windows:*** This window is designed for modifying the content of XML or JavaScript files. It's worth noting that its capabilities are somewhat rudimentary, making it less suitable for editing large volumes of content. For more extensive edits, it's recommended to use external software and subsequently copy-paste the revised content into this window for submission .   


# XML Element

 The NTB utilizes XML scripts to establish control over data flow and perform packet engineering. XML files employ various elements to achieve distinct effects on the NTB. A comprehensive list of all XML elements can be found on the website [https://arraynetworks.gitbook.io/array-xml/](https://arraynetworks.gitbook.io/array-xml/). In this training document, we will specifically introduce the elements that will be utilized.


1. ***Element <run>:*** This should be the first element and encapsulate the entire document. Only elements inside the <run> element will be considered legal use of the XML elements.

2. ***Element <filter>:*** This element defines the filter rules to be employed in the XML script. Within this element, you have the option to assign attributes such as 'id' and 'name' to identify the filter for future reference. Additionally, other attributes like 'sessionBase' and 'matchedlog' can be used to control the filter's behavior. Furthermore, there are three types of elements available to establish filter rules: <find>, <and>, and <or>.  

   * Element <find>: This element defines the attribute used to distinguish the desired packet from the rest. Available attributes for selection include, but are not limited to, vlan.id, ip.addr, tcp.port, and more.
   * Elements <and> and <or>: These two elements define the relationships between different <find> elements. When various <find> elements are enclosed within <or> </or> tags, it will only be necessary to satisfy one condition. In contrast, for <and> </and>, it will be necessary to satisfy all find conditions.  
  
3. Element <output>: This element defines various actions and packet manipulations that can be performed when an internet packet exits a port. By using the <port> element to specify the exit port, you can then utilize various elements for packet engineering, such as <strip> to remove packet information, <Q> to add VLAN ID, and many other tags.  

4. Element <chain>: This element serves to consolidate all the elements together. It utilizes the <in> element to specify the input port, <fid> to identify the filter used, <next> element is used to specify the path a packet is going to take after being filtered, and <out> elements to define the output port and any actions to be taken at the output.

For comprehensive documentation and detailed guidance on how to utilize each element, as well as explore all available attributes, please refer to [https://arraynetworks.gitbook.io/array-xml/](https://arraynetworks.gitbook.io/array-xml/).


# Training Case
## Case A
<center><img src="./pic/Original Topology.svg" width="70%"/></center>

In this document, our network layout (Figure 4) illustrates a client connected through two L3 routers to the internet. Integrating a firewall and an APV into this network involves meticulous IP configuration adjustments. These additions require intricate setup involving IP address configurations, routing rules, and the establishment of communication pathways between devices within the L3 network.

NTB serves as a pivotal tool to streamline this process. By intelligently managing L2 packet tags within the L3 environment, NTB significantly reduces the complexity of configuration. It minimizes the need for extensive IP address modifications and intricate reconfigurations on multiple devices, making the integration of the L3 firewall and APV more efficient( Figure 5).

<center><img src="./pic/Case A Modified Network Topology.svg" width="70%"/></center>

In Case A, our primary goal is to seamlessly integrate the firewall within the network, utilizing the APV for SSL interception prior to the firewall and subsequently re-encrypting packets, as depicted in Figure 5. NTB orchestrates a series of operations to ensure the smooth transmission of packets from the client to the internet.

Initially, NTB performs meticulous filtering of pertinent packets intended for the firewall, specifically targeting HTTPS packets (TCP/443) and DNS packets (UDP/53). This process involves scrutinizing incoming packets through port 6, and those originating from bypassed source/destination IPs or IP ranges. Additionally, all ARP packets are duplicated and sent to the internet router and APV.

For HTTPS (TCP/443) and DNS (UDP/53) packets, modifications to the destination MAC address occur to align with the APV's Port 1 MAC address, redirecting them to exit NTB through Port 1. Post-decryption by the APV, packets progress towards the firewall via Port 2, received by NTB through Port 2, and routed to Port 4 for transmission to the firewall (Figure 6-1). After traversing the firewall, packets re-enter NTB via Port 5, managing traffic redirection to exit through Port 3 for re-encryption.

Following re-encryption, packets pass through the APV's Port 1, re-enter NTB via Port 1, and undergo adjustments to their destination MAC address, aligning with the internet router's MAC address before exiting through Port 7 for direct transmission to the internet router (Figure 6-2). Moreover, at the initiation of an HTTPS session, an SSL Client Hello packet is sent from Port 2 of the APV to the internet router.

The return path of the packets mirrors the initial path but in reverse. Packets re-enter NTB through Port 7 after transmission from the internet router. NTB directs these packets to Port 1, modifying their MAC address to align with the APV's Port 1 MAC address. Once received by the APV through Port 1, packets undergo SSL re-encryption and are then sent to Port 3. Upon re-encryption, packets traverse NTB through Port 3 and Port 5, pass through the firewall via Port 4, and finally exit NTB through Port 7 for transmission to the client, completing the round-trip journey (Figures 7-1 & 7-2).

* Traffic to/from TCP port 443 and UDP port 53 requires SSL interception
* Packets originating from bypassed source/destination IPs or IP ranges are exempt from Firewall processing
* All ARP packets are duplicated, with one copy forwarded to the APV or two routers based on their origin
* Traffic entering NTB port 6 from the client router, directed to TCP port 443, is routed to the APV via port 1 with a modified destination MAC address
* TCP port 443 traffic from the APV, entering NTB via port 2, is directed to the firewall through port 4
* Return TCP port 443 traffic from the firewall, entering NTB via port 5, is redirected to the APV through port 3
* TCP port 443 traffic from the APV, entering NTB via port 1, is directed to exit via port 7
* The return packet follows the reverse order of the aforementioned path

### Case A Develop filter

Base on above criteria we are create the following <filter> element :

``````XML   
<!-- Filter Rules -->
    <!-- for 443 traffic -->
    <filter id="1" name="443/tcp, bidirection"  sessionBase="no">
        <or>
            <find name="tcp.dstport" relation="==" content="443"/>
        </or>
    </filter>
    <filter id="2" name="VS mac" sessionBase="no">
        <or>
            <find name="eth.dst" relation="==" content="cc:cc:cc:00:00:01"/>
        </or>
    </filter>
    <filter id="3" name="gateway ip" sessionBase="no">
        <or>
            <find name="ip.dst" relation="==" content="2.2.2.254"/>
        </or>
    </filter>
    <filter id="10" name="target Port" sessionBase="no">
        <or>
            <find name="tcp.dstport" relation="==" content="443"/>
            <find name="udp.dstport" relation="==" content="53"/>
        </or>
    </filter>
    <filter id="20" name="target Port for return packets" sessionBase="no">
        <or>
            <find name="tcp.srcport" relation="==" content="443"/>
            <find name="udp.srcport" relation="==" content="53"/>
        </or>
    </filter>
    
    <filter id="777" name="bypass src/dst ip or ip range" sessionBase="no">
        <or>
            <find name="ip.addr" relation="==" content="6.6.6.6"/>
            <find name="ip.addr" relation="==" content="7.7.7.0/24"/>
            <find name="ip.addr" relation="==" content="9.9.9.1-20"/>
        </or>
    </filter> 

    <filter id="999" name="arp" sessionBase="no">
        <or>
            <find name="arp" relation="==" content=""/>
        </or>
    </filter>

    <output id="1">
        <port>P1</port>
        <modify_dstmac>cc:cc:cc:00:00:01</modify_dstmac>
    </output>
    <output id="7">
        <port>P7</port>
        <modify_dstmac>00:00:00:00:00:33</modify_dstmac>
    </output>


``````

<div style="page-break-before:always"></div>
### Case A Apply Chain Element from client to internet

<center><img src="./pic/Case A forwarding1.svg" width="70%"/></center>
<center><img src="./pic/Case A forwarding2.svg" width="70%"/></center>

After the <filter>  element was created, we will start create <chain> element from client to internet.  

``````XML
<!-- Packet forwarding Chain -->
     <chain id="10">
        <in>P6</in>
        <fid>F999</fid>
        <out>P1,P7</out>
        <next type="notmatch">
            <fid>F777</fid>
            <out>P7</out>
            <next type="notmatch">
                <fid>F2,F10</fid>
                <out>O1</out>
                <next type="notmatch">
                    <out>P7</out>
                </next>
            </next>
        </next>
    </chain>

    <chain id="11">
        <in>P2</in>
        <fid>F1</fid>
        <out>O7</out>
        <next type="notmatch">
            <out>P4</out>
        </next>
    </chain>

    <chain id="12">
        <in>P5</in>
        <out>P3</out>
    </chain>

    <chain id="13">
        <in>P1</in>
        <fid>F999</fid>
        <out>P6,P7</out>
        <next type="notmatch">
            <fid type="or">F3,F10</fid>
            <out>P7</out>
            <next type="notmatch">
                <out>P6</out>
            </next>
        </next>
    </chain>


``````
<div style="page-break-before:always"></div>
### Case A Apply Chain Element from internet to client

<center><img src="./pic/Case A return1.svg" width="70%"/></center>
<center><img src="./pic/Case A return2.svg" width="70%"/></center>

Next, we will create the <chain> element for traffic from the internet to the client, as illustrated in Figure 7-1 & 7-2.

``````XML
<!-- Packet return chain -->
    <chain id="20">
        <in>P7</in>
        <fid>F999</fid>
        <out>P1,P6</out>
        <next type="notmatch">
            <fid>F777</fid>
            <out>P6</out>
            <next type="notmatch">
                <fid>F20</fid>
                <out>O1</out>
                <next type="notmatch">
                    <fid>F2</fid>
                    <out>P1</out>
                    <next type="notmatch">
                        <out>P6</out>
                    </next>
                </next>
            </next>
        </next>
    </chain>

    <chain id="21">
        <in>P3</in>
        <out>P5</out>
    </chain>

    <chain id="22">
        <in>P4</in>
        <out>P2</out>
    </chain>

``````

### Case A Complete Combine Script

The complete XML script .

``````XML
<run>
<!-- Filter Rules -->
    <!-- for 443 traffic -->
    <filter id="1" name="443/tcp, bidirection"  sessionBase="no">
        <or>
            <find name="tcp.dstport" relation="==" content="443"/>
        </or>
    </filter>
    <filter id="2" name="VS mac" sessionBase="no">
        <or>
            <find name="eth.dst" relation="==" content="cc:cc:cc:00:00:01"/>
        </or>
    </filter>
    <filter id="3" name="gateway ip" sessionBase="no">
        <or>
            <find name="ip.dst" relation="==" content="2.2.2.254"/>
        </or>
    </filter>
    <filter id="10" name="target Port" sessionBase="no">
        <or>
            <find name="tcp.dstport" relation="==" content="443"/>
            <find name="udp.dstport" relation="==" content="53"/>
        </or>
    </filter>
    <filter id="20" name="target Port for return packets" sessionBase="no">
        <or>
            <find name="tcp.srcport" relation="==" content="443"/>
            <find name="udp.srcport" relation="==" content="53"/>
        </or>
    </filter>
    
    <filter id="777" name="bypass src/dst ip or ip range" sessionBase="no">
        <or>
            <find name="ip.addr" relation="==" content="6.6.6.6"/>
            <find name="ip.addr" relation="==" content="7.7.7.0/24"/>
            <find name="ip.addr" relation="==" content="9.9.9.1-20"/>
        </or>
    </filter> 

    <filter id="999" name="arp" sessionBase="no">
        <or>
            <find name="arp" relation="==" content=""/>
        </or>
    </filter>

    <output id="1">
        <port>P1</port>
        <modify_dstmac>cc:cc:cc:00:00:01</modify_dstmac>
    </output>
    <output id="7">
        <port>P7</port>
        <modify_dstmac>00:00:00:00:00:33</modify_dstmac>
    </output>

<!-- Packet forwarding Chain -->
     <chain id="10">
        <in>P6</in>
        <fid>F999</fid>
        <out>P1,P7</out>
        <next type="notmatch">
            <fid>F777</fid>
            <out>P7</out>
            <next type="notmatch">
                <fid>F2,F10</fid>
                <out>O1</out>
                <next type="notmatch">
                    <out>P7</out>
                </next>
            </next>
        </next>
    </chain>

    <chain id="11">
        <in>P2</in>
        <fid>F1</fid>
        <out>O7</out>
        <next type="notmatch">
            <out>P4</out>
        </next>
    </chain>

    <chain id="12">
        <in>P5</in>
        <out>P3</out>
    </chain>

    <chain id="13">
        <in>P1</in>
        <fid>F999</fid>
        <out>P6,P7</out>
        <next type="notmatch">
            <fid type="or">F3,F10</fid>
            <out>P7</out>
            <next type="notmatch">
                <out>P6</out>
            </next>
        </next>
    </chain>

<!-- Packet return chain -->
    <chain id="20">
        <in>P7</in>
        <fid>F999</fid>
        <out>P1,P6</out>
        <next type="notmatch">
            <fid>F777</fid>
            <out>P6</out>
            <next type="notmatch">
                <fid>F20</fid>
                <out>O1</out>
                <next type="notmatch">
                    <fid>F2</fid>
                    <out>P1</out>
                    <next type="notmatch">
                        <out>P6</out>
                    </next>
                </next>
            </next>
        </next>
    </chain>

    <chain id="21">
        <in>P3</in>
        <out>P5</out>
    </chain>

    <chain id="22">
        <in>P4</in>
        <out>P2</out>
    </chain>
</run>
``````
<div style="page-break-before:always"></div>

## Case B
<center><img src="./pic/Case B Original Topology.svg" width="70%"/></center>

In Case B, we are working with a network configuration that utilizes the Link Aggregation Control Protocol (LACP) with two ports, as depicted in Figure 9. The process of integrating SSL interception and a security device into this network can be complex. It may require the deployment of additional pairs of devices, potentially leading to an inefficient use of data processing capacity.

By harnessing the capabilities of the Network Traffic Device (NTB), we can design a network topology that provides ample capacity and includes hardware bypass mechanisms to mitigate potential security device malfunctions, which could otherwise disrupt network operations. This approach enhances both the efficiency and reliability of network traffic management, as illustrated in Figure 10.

<center><img src="./pic/Case B Modified Network Topology.svg" width="70%"/></center>

In this setup, internet service remains available even if the security device experiences an outage. With the new topology depicted in Figure 10, the Network Traffic Broker (NTB) can integrate a security device configured to monitor the network's security via a heartbeat packet signal. When the security device in the network ceases to function, the NTB swiftly redirects network traffic away from the compromised security device, as illustrated in Figure 11.

<center><img src="./pic/Case B APV or Security Device offline.svg" width="70%"/></center>

Furthermore, with NTB's hardware bypass at ports 6 and 7, internet traffic can continue to flow even if the NTB itself is non-operational, as demonstrated in Figure 12.

<center><img src="./pic/Case B NTB offline only Port 7 and P6 has hardware bypass.svg" width="70%"/></center>

As depicted in Figure 10, the redirection of network traffic from Port 3 and Port 7 is a crucial step in this configuration. Specifically, HTTPS traffic from both Port 3 and Port 7 is directed to Port 0, where it undergoes processing through the Application Delivery Controller (APV) and the security device. To ensure that the network operates as intended and the functionalities of the APV and security device remain consistent, continuous monitoring is essential. Therefore, a heartbeat packet is sent from Port 0 to Port 1, providing ongoing health checks.

Upon passing through the APV and security device, the traffic must be properly segmented, and the VLAN tag added at Port 7's exit from Port 0 must be removed. This process guarantees the appropriate routing and isolation of network traffic, maintaining the integrity of the network's operation and security measures.

* Traffic to/from TCP port 443 undergoes SSL interception.
* TCP port 443 traffic from Port 7 exits through Port 0 with the addition of a VLAN tag (10) and then reset traffic before leaving Port 6.
* TCP port 443 traffic from Port 3 exits through Port 2, and the resets traffic leaves through Port 6.
* Traffic from Port 1, tagged with VLAN 10, has its VLAN tag removed and exits through Port 6.
* Traffic from Port 1 without VLAN tag 10 exits through Port 2.
* A heartbeat signal is sent from Port 0 to Port 1 for continuous monitoring.
* TCP port 443 traffic from Port 6 exits through Port 1 with the addition of a VLAN tag (10), resets, and then leaves through Port 7.
* TCP port 443 traffic from Port 2 exits through Port 1, resets, and then leaves through Port 3.
* Traffic from Port 0, tagged with VLAN 10, has its VLAN tag removed and exits through Port 7.
* Traffic from Port 0 without VLAN tag 10 exits through Port 3.

### Case B Develop filter

Base on above criteria we are create the following <filter> and <output> element :

``````XML
<!-- Filter Rules -->
    <!-- for 443 traffic -->
    <filter id="1" name="443/tcp, bidirection"  sessionBase="no">
        <or>
           <find name="tcp.port" relation="!=" content="443"/>
        </or>
    </filter>

    <filter id="2" sessionBase="no">
        <or>
            <find name="heartbeat.target.miss.id" relation="==" content="1"/>
        </or>
    </filter>

    <filter id="3" name="Path 1 Vlan Tag 10" sessionBase="no">
        <or>
            <find name="vlan.id" relation="==" content="10"/>
        </or>
    </filter>

    <output id="1">
        <port>P0</port>
        <Q>10</Q>
    </output>

    <output id="2">
        <port>P6</port>
        <stripping>vlan</stripping>
    </output>

    <output id="3">
        <port>P1</port>
        <Q>10</Q>
    </output>

    <output id="4">
        <port>P7</port>
        <stripping>vlan</stripping>
    </output>
``````
<div style="page-break-before:always"></div>
### Case B Apply Chain Element from client to internet

<center><img src="./pic/Case B forwarding.svg" width="70%"/></center>

After the <filter> and <output> element was created, we will start create <chain> element from client to internet as illustrate in figure 13.  

``````XML
<!-- Packet flow Chain -->
    <chain id="1">
        <in>P7</in>
        <fid>F1</fid>
        <out>P6</out>
        <next type="notmatch">
            <fid>F2</fid>
                <out>P6</out>
                <next type="notmatch">
                        <out>O1</out>
                </next>
        </next>
    </chain>

    <chain id="2">
        <in>P1</in>
        <fid>F3</fid>
        <out>O2</out>
        <next type="notmatch">
            <out>P2</out>
        </next>
    </chain>

     <chain id="3">
            <in>P3</in>
            <fid>F1</fid>
            <out>P2</out>
            <next type="notmatch">
                <fid>F2</fid>
                    <out>P2</out>
                    <next type="notmatch">
                            <out>P0</out>
                    </next>
            </next>
      </chain>
``````

<div style="page-break-before:always"></div>
### Case B Apply Chain Element from internet to client

<center><img src="./pic/Case B return.svg" width="70%"/></center>

Next, we will create the <chain> element for traffic from the internet to the client, as illustrated in Figure 14.

``````XML
<!-- return packet -->
    <chain id="4">
        <in>P6</in>
        <fid>F1</fid>
        <out>P7</out>
        <next type="notmatch">
            <fid>F2</fid>
            <out>P7</out>
            <next type="notmatch">
                <out>O3</out>
            </next>
        </next>
    </chain>

    <chain id="5">
          <in>P2</in>
          <fid>F1</fid>
          <out>P3</out>
          <next type="notmatch">
              <fid>F2</fid>
              <out>P3</out>
              <next type="notmatch">
                  <out>P1</out>
              </next>
           </next>
    </chain>    

    <chain id="6">
         <in>P0</in>
         <fid>F2</fid>
         <out>P3</out>    
        <next type="notmatch">
            <out>O4</out>
        </next>
    </chain>



``````

### Case B Complete Combine Script

The complete XML script .

``````XML
<?xml version="1.0" encoding="UTF-8"?>
<run>
<!-- Filter Rules -->
    <!-- for 443 traffic -->
    <filter id="1" name="443/tcp, bidirection"  sessionBase="no">
        <or>
            <find name="tcp.port" relation="!=" content="443"/>
        </or>
    </filter>

<filter id="2" sessionBase="no">
    <or>
        <find name="heartbeat.target.miss.id" relation="==" content="1"/>
    </or>
</filter>

<filter id="3" name="Path 1 Vlan Tag 10" sessionBase="no">
     <or>
        <find name="vlan.id" relation="==" content="10"/>
     </or>
</filter>

<output id="1">
    <port>P0</port>
    <Q>10</Q>
</output>

<output id="2">
    <port>P6</port>
    <stripping>vlan</stripping>
</output>

 <output id="3">
    <port>P1</port>
    <Q>10</Q>
</output>

<output id="4">
    <port>P7</port>
    <stripping>vlan</stripping>
</output>
<!-- Packet flow Chain -->
    <chain id="1">
        <in>P7</in>
        <fid>F1</fid>
        <out>P6</out>
        <next type="notmatch">
            <fid>F2</fid>
                <out>P6</out>
                <next type="notmatch">
                        <out>O1</out>
                </next>
        </next>
    </chain>

    <chain id="2">
        <in>P1</in>
        <fid>F3</fid>
        <out>O2</out>
        <next type="notmatch">
            <out>P2</out>
        </next>
    </chain>

     <chain id="3">
            <in>P3</in>
            <fid>F1</fid>
            <out>P2</out>
            <next type="notmatch">
                <fid>F2</fid>
                    <out>P2</out>
                    <next type="notmatch">
                            <out>P0</out>
                    </next>
            </next>
      </chain>
<!-- return packet -->
    <chain id="4">
        <in>P6</in>
        <fid>F1</fid>
        <out>P7</out>
        <next type="notmatch">
            <fid>F2</fid>
            <out>P7</out>
            <next type="notmatch">
                <out>O3</out>
            </next>
        </next>
    </chain>

    <chain id="5">
          <in>P2</in>
          <fid>F1</fid>
          <out>P3</out>
          <next type="notmatch">
              <fid>F2</fid>
              <out>P3</out>
              <next type="notmatch">
                  <out>P1</out>
              </next>
           </next>
    </chain>    

    <chain id="6">
         <in>P0</in>
         <fid>F2</fid>
         <out>P3</out>    
        <next type="notmatch">
            <out>O4</out>
        </next>
    </chain>
</run>
``````