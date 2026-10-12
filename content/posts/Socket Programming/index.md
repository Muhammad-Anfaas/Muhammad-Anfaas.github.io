---
date: '2026-09-25'
draft: false
title: 'Socket Programming: Foundation of Network Communication'
featureimage: "socket-feature.png"

heroStyle: "background"

showTableOfContents: true
---

A socket is a communications connection point (endpoint) that you can name and address in a network. Socket programming shows how to use socket APIs to establish communication links between remote and local processes.
The processes that use a socket can reside on the same system or different systems on different networks. Sockets are useful for both stand-alone and network applications. Sockets allow you to exchange information between processes on the same machine or across a network, distribute work to the most efficient machine, and they easily allow access to centralized data. Socket application program interfaces (APIs) are the network standard for TCP/IP. 

# Components of Socket Programming
## What are Sockets
Sockets are one of the core components used by the program to access the network to communicate with other processes/nodes over the network. It is simply a combination of an IP address and a port number that acts as an endpoint for Communication.\
Example: 192.168.1.1:8080, where the two parts separated by the colon represent
>IP address( 192.168.1.1 )\
>port number( 8080 )

### Socket Types
There are two types of sockets:

**TCP Socket *(Stream Socket)***: Provides reliable, connection-based communication.\
**UDP Socket *(Datagram Socket)***: Provides connectionless communication, faster but unreliable.

In a networked environment, two sockets are involved: a client socket and a server socket.

### TCP vs UDP
They both are two core protocols of the Transport Layer of the OSI and TCP/IP models. Both are responsible for end-to-end communication between applications, but they differ significantly in terms of reliability, speed, and use cases.\
TCP is a reliable, connection-oriented transport protocol that ensures accurate and ordered data delivery. It uses control mechanisms to guarantee data correctness, which makes it slower but dependable.\
On Contrary, UDP is a fast, connectionless transport protocol that sends data without reliability guarantees. It is efficient for applications where speed is more important than accuracy.
Other Differences are elisted in the table below


| TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
|:---:|:---:|
| Connection-oriented; uses a three-way handshake | Connectionless; no handshake |
| Guarantees reliable data delivery | Does not guarantee delivery |
| Uses acknowledgements (ACKs) | No acknowledgements |
| Supports retransmission of lost packets | No retransmission support |
| Ensures packets are delivered in order | Does not ensure ordering |
| Provides flow control and congestion control | No flow or congestion control |
| Slower due to higher overhead | Faster with minimal overhead |
| Variable header size (20–60 bytes) | Fixed header size (8 bytes) |
| Treats data as a continuous byte stream | Treats data as independent messages |
| Does not support broadcasting or multicasting | Supports broadcasting and multicasting |
| Used by HTTP, HTTPS, FTP, SMTP | Used by DNS, DHCP, VoIP, Streaming |

### Client Server Model
The client-server model is the architecture used in socket programming, where a client and a server to interact with each other to exchange information or services.\
The server starts up, prepares a socket (binding to an IP address and port number), and begins listening for incoming connections.\
The client creates its own socket, connects to the server socket, and begins sending and receiving data.
![output](/client-server.png "Pictorial Description of Client-Server Model")
---
continued...