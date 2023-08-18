---
title: Journey of an Ethernet Frame
type: page
description: Diving Deep into how electrical signals get transformed into useful data
topic: career
---

### Ethernet

Most of us use Ethernet in some way or another. Ethernet describes a family of wired computer networking technologies. While most of us opt for Wi-Fi due to convenience reasons, Ethernet powers the servers and data centers which deliver the services we enjoy.  

{{< figure src="/images/dcswitch.jpg" title="" >}}
*A Typical Switch*

I've always wondered what goes on after I plug in a CAT 5E cable into my switch. Previously, it would be a simple matter of plug-and-play (or plug and PuTTY in). Having recently learnt about Operating Systems, Digital Signal Processing and Computer Architecture through courses at Imperial, combined with hands-on experience doing Cloud Computing and Software Defined Networking through my work at AWS, I thought it would be time to re-examine this phenomenon. I also recently chanced upon Container Network Operating Systems and the SONiC ecosystem, which made me rediscover my interest in networking.   

Do note that this post can get quite lengthy. I recommend you use the table of contents and jump to the respective OSI layer that you are interested in.  

#### Network Interface Card

The critical bit that I did not know about before college was the role of the Network Interface Card (NIC). NICs are hardware components that connect a computer or device to a network. It provides an interface between the device and network medium, enabling data to be transmitted and received. While NICs operate on Layer 2 of the OSI Model, they interrupt the CPU and interact with drivers, which interface with the operating system - so they actually have impact across different OSI layers. 

{{< figure src="/images/nic.jpg" title="" >}}
*A Network Interface Card*

Interestingly, Virtual Machines (VMs) also have NICs - virtual ones (vNIC). Virtualisation emulates hardware components, including network adapters. Hypervisors create a virtual switch that handles communication between VMs and the physical network, with each vNIC connected to the virtual switch. Containers too have virtual network interfaces, which I previously worked with when I set up a docker network for my Year 2 Project. When a Docker container is created and a network is specified (think Docker Compose), Docker sets up a pair of virtual Ethernet interfaces, with one end attached to the Docker host network namespace, and the other inside the container network namespace.  

#### Layer 1 - Physical

Back to the journey of a frame. We will explain this using the OSI layers as a framework - the benefit is that we can separate each layer and each layer doesn't depend on other layers. Think of a CAT 5e cable plugged into a switch. Now, raw electrical signals representing the Ethernet frame are received by the NIC in the switch (a frame is, like most data, fundamentally 0s and 1s). These signals are transmitted over physical medium - copper wires in this case. This section explains how the NIC hardware processes signals to extract bits that contain the Ethernet frame.    

#### Twisted Pair - Electrical Signals

Ethernet is powered by twisted pair technologies. What this means is that in each cable, we have eight wires - this is to increase bandwidth and to support various networking standard. Twisted pair - as the name implies - means that each wire is actually a twisted pair of copper wires. 

{{< figure src="/images/twistedCable.jpg" title="" >}}
*A Twisted Pair Cable - CAT 5E*

This enables differential signalling, where one wire carries a higher voltage (a 1 bit) and the other, a lower voltage (a 0 bit). This reduces electromagnetic interference and improves cable noise immunity.  

{{< figure src="/images/twistedpair.png" title="" >}}
*Twisted Pair and why it reduces electromagnetic interference - interference cancels out*

We have four pairs of wires to give us higher bandwidth and data rates - critical for Gigabit Ethernet. One area of confusion I had was - when I crimped 5e cables, I crimped them in 'straight-through' configuration - where is the twisted pair? I later realised that twisted pair means that the cables are twisted within the cable, but we align them in order when crimping.  

#### Encoding and Line Codes

Now, we will explain more about encoding and line codes, why it matters and how it works - specific to wired Ethernet. We seldom send raw data over serial connections - for example, if you received a stream of zeroes - how do you know whether that's raw data or no signal? This mandates the requirement for an encoding scheme. I will be explaining 8b/10b encoding, which is a line code that maps 8-bit words to 10-bit words. This is used in Gigabit Ethernet.  

Suppose that we want to encode an 8-bit stream - `10111001`.  

We use a lookup table to map the 8-bit binary data to a 10-bit symbol - from `10111001` to `0100100111`.  

This creates a neutral running disparity (the difference between 0s and 1s) of 0.  

We maintain running disparity within acceptible limits. So `0100100111` does not have more than five consecutive 0 bits or 1 bits.  

We transmit `0100100111` over the communication medium, retaining DC balance and running disparity properties, ensuring signal integrity. This also makes it easier to distinguish between data and no-transmission situations.  

8b/10b encoding also lets us demarcate the end of a frame by identifying unused symbols which specifically never appear within actual data inside the frame. For example, we can designate the 10b symbol `1100001100` as the end-of-frame marker.  

#### Frame Preamble and Start Frame Delimiter

Now we understand how bits are encoded and decoded by the NIC. We can therefore dive into the structure of the Ethernet frame. The frame has a preamble, which performs clock synchronisation between the receiving device and the incoming signal. How does this work?  

{{< figure src="/images/preamble.png" title="" >}}
*Ethernet Frame Preamble*

Ethernet does not have a shared bit-clock (since none of the pins / conductors carries a clock signal). Hence, teh receiver must synchronise the bit clock to the sender's block with every packet. The preamble allows the receiver's Phase Locked Loops (special circuits to synchronise bit clocks) to have enough time to lock into synchronisation with the sender before dat bits are transmitted. Interestingly, we can think about why are 7 bytes needed for this - since the release of the 802.3 standard, have PLLs improved to the point where less than 7 bytes are needed? But changing things would be too much of a hassle.  

The preamble is then followed by the Start Frame Delimiter (SFD), which is immediately followed by the destination MAC address. The SFD is distinguished from the Preamble because it consists of one byte that ends with 1 instead of 0.  


### Layer 2 - Data Link

{{< figure src="/images/macstructure.png" title="" >}}
*Detailed Ethernet Frame Structure*

Great! Now we can move on to MAC addresses. MAC stands for Media Access Control - a unique identifier assigned to the NIC.  

Let us start by defining the main 'features' of a NIC ([source](https://www.tritondatacenter.com/blog/virtualizing-nics)):
- It has a MAC Address that can be used to filter incoming packets
- It has a ring buffer to store packets received
- It has a ring buffer to store packets to be sent
- It can generate interrupts to the CPU
- We can program all of the above

#### Physical Addressing

The MAC Address is first checked to see if the NIC's MAC Address matches the destination MAC Address inside the frame. If this is a match, the frame is further processed; else if it is also not a multicast or broadcast packet, it is discarded.     

NICs can be put into promiscuous mode to place every frame received into the ring buffer regardless of destination MAC address.  

#### Error Correction - Frame Check Sequence (FCS)

The frame contains a four-octet cyclic redundancy check (CRC) that lets us detect corrupted data within the frame. FCS is a value that is computed as a function of protected MAC frame fields - source and destination address, length/type field, MAC client data and padding.  

This computation is often a left shift CRC-32. CRC can be implemented in hardware via a left shifting Linear Feedback Shift Register (interestingly, I implemented this in SystemVerilog for an unrelated class project).  

#### Frame Filtering

We can also implement filters at the MAC address level - with whitelists and blacklists for source MAC addresses. However this can be circumvented using packet analysis to find valid MAC addresses, and then performing MAC spoofing.  

#### Buffering

If all checks pass, the frame is stored temporarily in memory as a ring buffer. This buffer allows for efficient handling of incoming frames, especially when the NIC operates at higher speeds than the rest of the system.  

{{< figure src="/images/ringb.png" title="" >}}
*Ring Buffer*

A ring buffer is used as the data structure for storing frame data because (1) they consume a fixed amount of memory and (2) handle the model of single producer single consumer well. In a ring buffer, the producer places data in the buffer and moves a head pointer, while the consumer removes data from the buffer, moving the tail pointer.  

The ring consists of multiple entries, with each entry called a descriptor. A descriptor is made up of three parts: (1) a buffer address, (2) a buffer length, and (3) packet metadata.  


#### Interrupt Generation

When a packet arrives, the network card will generatea an interrupt, letting the OS know that it should check the ring. 

#### Receive Side Scaling

### Layer 4 - Transport

Now we can go further into how we obtain data from the payload inside the MAC frame.

#### TCP/IP Protocol Layering and Sockets


### Layer 5 - Network 


#### Top Half / Botton Half Processing


#### Protocol Handoff


#### Interrupt Coalescing


#### Queues


### Layer 6 - Presentation


#### Cache Locality and IRQ Steering
