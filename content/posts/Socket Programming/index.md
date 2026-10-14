---
date: '2026-09-16'
draft: false
title: 'Socket Programming: Foundation of Network Communication'
featureimage: "socket-feature.png"

heroStyle: "background"

showTableOfContents: true
---

# Socket Programming in Python: A Beginner's Guide

A socket is a communication endpoint that you can name and address in a network. Socket programming is about using socket APIs to set up communication links between local and remote processes.

The processes using a socket can live on the same machine or on different machines across different networks. Sockets work for both stand-alone and networked applications. They let you exchange information between processes, distribute work to the most suitable machine, and access centralized data with ease. Socket APIs are the standard way of doing network communication over TCP/IP.

---

# Components of Socket Programming

## What Are Sockets?

Sockets are one of the core tools a program uses to talk to other processes or nodes over a network. Put simply, a socket is the combination of an IP address and a port number, and together they form an endpoint for communication.

**Example:** `192.168.1.1:8080`

- `192.168.1.1` is the **IP address**
- `8080` is the **port number**

## Socket Types

There are two main types of sockets:

- **TCP socket (stream socket):** provides reliable, connection-based communication.
- **UDP socket (datagram socket):** provides connectionless communication that is faster but not reliable.

In any networked setup, two sockets are involved: a **client socket** and a **server socket**.

## TCP vs UDP

TCP and UDP are the two core protocols of the Transport Layer in both the OSI and TCP/IP models. Both handle end-to-end communication between applications, but they differ a lot in reliability, speed, and typical use cases.

**TCP** is a reliable, connection-oriented protocol that guarantees accurate, ordered delivery of data. It relies on control mechanisms to keep data correct, which makes it slower but dependable.

**UDP**, on the other hand, is a fast, connectionless protocol that sends data without any delivery guarantees. It works well where speed matters more than accuracy.

Here are the other key differences:

| TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
|:---:|:---:|
| Connection-oriented; uses a three-way handshake | Connectionless; no handshake |
| Guarantees reliable data delivery | Does not guarantee delivery |
| Uses acknowledgements (ACKs) | No acknowledgements |
| Retransmits lost packets | No retransmission |
| Delivers packets in order | Does not ensure ordering |
| Provides flow control and congestion control | No flow or congestion control |
| Slower due to higher overhead | Faster with minimal overhead |
| Variable header size (20–60 bytes) | Fixed header size (8 bytes) |
| Treats data as a continuous byte stream | Treats data as independent messages |
| Does not support broadcasting or multicasting | Supports broadcasting and multicasting |
| Used by HTTP, HTTPS, FTP, SMTP | Used by DNS, DHCP, VoIP, streaming |

## The Client-Server Model

The client-server model is the basic architecture behind socket programming. A client and a server interact to exchange information or services.

1. The **server** starts up, prepares a socket by binding it to an IP address and port number, and begins listening for incoming connections.
2. The **client** creates its own socket, connects to the server's socket, and starts sending and receiving data.

![Pictorial description of the client-server model](/client-server.png "Pictorial Description of Client-Server Model")

---

# A Basic Client-Server Implementation

Now that we have an overview of the socket API and how a client and server communicate, it's time to build our first pair. We'll begin with something simple: a server that echoes back whatever it receives from the client.

## Echo Server

Here is the server code:

```python
import socket

HOST = "127.0.0.1"  # Standard loopback interface address (localhost)
PORT = 65432  # Port to listen on (non-privileged ports are > 1023)

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen()
    conn, addr = s.accept()
    with conn:
        print(f"Connected by {addr}")
        while True:
            data = conn.recv(1024)
            if not data:
                break
            conn.sendall(data)
```

## Echo Client

And here is the client code:

```python
import socket

HOST = "127.0.0.1"  # The server's hostname or IP address
PORT = 65432  # The port used by the server

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.sendall(b"Hello, world")
    data = s.recv(1024)

print(f"Received {data!r}")
```

In the client, we create a socket object, use `.connect()` to reach the server, and call `.sendall()` to send our message. Finally, we call `.recv()` to read the server's reply and print it.

## Running the Echo Client and Server

Let's run both programs and see what happens behind the scenes.

First, start the server:

![Starting the server](/server1.png)

The terminal will seem to hang. Don't worry, that's expected: the server is blocked on `.accept()`, waiting for a client to connect.

Now open another terminal and run the client:

![Running the client](/client1.png)

As soon as the client runs, you'll see the received string printed. Back in the server window, you'll see something like this:

![Server output](/server12.png)

The server prints the address tuple returned by `.accept()`, which contains the client's IP address and TCP port number. The port number will differ from machine to machine.

---

# What's Next: Multi-Connection Server

So far our server handles only a single connection. In the next blog, we'll explore how to handle multiple connections and use socket programming to transfer files and messages between a client and server over a network. Stay tuned!
