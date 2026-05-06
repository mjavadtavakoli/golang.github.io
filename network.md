# Networking in Go — A Comprehensive Guide
### For Developers Building Real-World Services (OMS Edition)

---

## Table of Contents

1. [Why Networking Matters in Go](#1-why-networking-matters-in-go)
2. [The OSI Model — Big Picture](#2-the-osi-model--big-picture)
3. [Layer 4 — Transport Layer & Ports](#3-layer-4--transport-layer--ports)
4. [TCP — Transmission Control Protocol](#4-tcp--transmission-control-protocol)
5. [UDP — User Datagram Protocol](#5-udp--user-datagram-protocol)
6. [TCP vs UDP — Side by Side](#6-tcp-vs-udp--side-by-side)
7. [Common Application Protocols](#7-common-application-protocols)
8. [Go Networking in Practice](#8-go-networking-in-practice)
9. [Building a TCP Server in Go (OMS Example)](#9-building-a-tcp-server-in-go-oms-example)
10. [Building a UDP Client in Go (OMS Metrics Example)](#10-building-a-udp-client-in-go-oms-metrics-example)
11. [HTTP Server in Go (OMS REST API)](#11-http-server-in-go-oms-rest-api)
12. [Summary & Best Practices](#12-summary--best-practices)

---

## 1. Why Networking Matters in Go

Go was built with networking at its core. The standard library provides powerful, production-ready packages for building servers, clients, and everything in between — with minimal dependencies.

In a real-world **Order Management System (OMS)**, services constantly talk to each other:

```
┌─────────────────────────────────────────────────────────────────┐
│                        OMS Architecture                         │
│                                                                 │
│  ┌──────────┐    HTTP     ┌──────────┐   TCP/gRPC  ┌─────────┐ │
│  │  Client  │ ──────────► │  Order   │ ──────────► │Payment  │ │
│  │  (App)   │             │  Service │             │Service  │ │
│  └──────────┘             └──────────┘             └─────────┘ │
│                                │                               │
│                          AMQP/TCP                              │
│                                ▼                               │
│                          ┌──────────┐    UDP      ┌─────────┐ │
│                          │  Queue   │ ──────────► │ Metrics │ │
│                          │(RabbitMQ)│             │ Server  │ │
│                          └──────────┘             └─────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

Every arrow in that diagram uses a **network protocol**. Understanding those protocols is what this guide is about.

---

## 2. The OSI Model — Big Picture

The OSI (Open Systems Interconnection) model breaks network communication into **7 layers**. Each layer has a specific job.

```
┌───────┬────────────────────┬──────────────────────────────────┐
│ Layer │      Name          │     What it does (OMS example)   │
├───────┼────────────────────┼──────────────────────────────────┤
│   7   │  Application       │  HTTP, gRPC, AMQP                │
│   6   │  Presentation      │  JSON encoding, TLS/SSL          │
│   5   │  Session           │  TCP session between OMS ↔ Pay  │
│   4   │  Transport    ◄──  │  TCP / UDP  +  PORTS  ◄── focus │
│   3   │  Network           │  IP addresses (10.2.3.44)        │
│   2   │  Data Link         │  MAC addresses, Ethernet         │
│   1   │  Physical          │  Cables, Wi-Fi signals           │
└───────┴────────────────────┴──────────────────────────────────┘
```

> **Key insight:** IP addresses (Layer 3) tell you *which machine* to reach.
> Ports (Layer 4) tell you *which service on that machine* to talk to.

---

## 3. Layer 4 — Transport Layer & Ports

### What is a Port?

A **port** is a number (0–65535) that identifies a specific service running on a machine.

Think of an IP address as a **building address**, and a port as an **apartment number** inside that building:

```
          IP: 10.2.3.44
         ┌─────────────────────────────────┐
         │          Server Machine         │
         │                                 │
         │  Port 80   ► HTTP (web server)  │
         │  Port 443  ► HTTPS              │
         │  Port 5672 ► RabbitMQ (AMQP)   │
         │  Port 8080 ► OMS Order Service  │
         │  Port 9090 ► OMS Metrics        │
         │  Port 5432 ► PostgreSQL DB      │
         └─────────────────────────────────┘
```

Without a port number, even if the client reaches the right machine, it doesn't know which service to knock on.

### Port Ranges

| Range         | Name             | Examples                         |
|---------------|------------------|----------------------------------|
| 0 – 1023      | Well-known ports | HTTP (80), HTTPS (443), SSH (22) |
| 1024 – 49151  | Registered ports | PostgreSQL (5432), Redis (6379)  |
| 49152 – 65535 | Dynamic/private  | Client source ports (OS picks)   |

### Real OMS Example

When the mobile app places an order:

```
Mobile App (client)              OMS Order Service (server)
IP: 192.168.1.5                  IP: 10.2.3.44
Port: 54321  (OS assigned) ───► Port: 8080  (OMS listens here)

Full address:
  Client socket: 192.168.1.5:54321
  Server socket: 10.2.3.44:8080
```

The combination `IP:Port` is called a **socket**.

---

## 4. TCP — Transmission Control Protocol

### What is TCP?

TCP is the most widely used **Layer 4 protocol**. It provides:

- **Reliable delivery** — every packet is acknowledged
- **Ordered data** — bytes arrive in the exact order sent
- **Error checking** — corrupted data is detected and retransmitted
- **Flow control** — sender won't overwhelm the receiver

The trade-off: TCP has **overhead** because of all these guarantees.

### The 3-Way Handshake

Before any data flows, TCP establishes a connection using **3 messages**:

```
   Client (Mobile App)              Server (OMS Service)
        │                                    │
        │  ─── SYN ──────────────────────►  │  "I want to connect"
        │                                    │
        │  ◄── SYN-ACK ────────────────────  │  "OK, I'm here, ready"
        │                                    │
        │  ─── ACK ──────────────────────►  │  "Got it, let's go"
        │                                    │
        │ ════════ CONNECTION ESTABLISHED ═══│
        │                                    │
        │  ─── HTTP POST /orders ─────────►  │  Now real data flows
        │  ◄── HTTP 201 Created ────────────  │
        │                                    │
```

**SYN** = Synchronize (a TCP header flag)
**ACK** = Acknowledge (a TCP header flag)

This handshake is why TCP is called a **connection-oriented** protocol. The connection is a **session** (Layer 5).

### What Happens When a Port is Closed?

If the client sends a SYN to a port that isn't open, the server replies with **RST** (Reset):

```
   Client (Mobile App)              Server (OMS Service)
        │                                    │
        │  ─── SYN ──► port 9999 ─────────►  │  (nothing listening on 9999)
        │                                    │
        │  ◄── RST ────────────────────────  │  "Nope, nothing here"
        │                                    │
        │      CONNECTION REFUSED            │
        │                                    │
```

In Go you'll see this as: `dial tcp 10.2.3.44:9999: connect: connection refused`

### TCP Connection Teardown (4-Way)

When done, the connection is cleanly closed:

```
   Client                           Server
      │                                │
      │  ─── FIN ──────────────────►  │  "I'm done sending"
      │  ◄── ACK ─────────────────────  │  "Got it"
      │  ◄── FIN ─────────────────────  │  "I'm done too"
      │  ─── ACK ──────────────────►  │  "Got it"
      │                                │
      │ ══════ CONNECTION CLOSED ══════│
```

### TCP in Go — Quick Example

```go
// Dialing a TCP connection (client side)
conn, err := net.Dial("tcp", "10.2.3.44:8080")
if err != nil {
    log.Fatal(err) // connection refused, timeout, etc.
}
defer conn.Close() // sends FIN to server

// Listening on a TCP port (server side)
listener, err := net.Listen("tcp", ":8080")
if err != nil {
    log.Fatal(err)
}
defer listener.Close()
```

---

## 5. UDP — User Datagram Protocol

### What is UDP?

UDP is the **fire-and-forget** protocol. It sends data without:

- Establishing a connection first
- Waiting for acknowledgements
- Guaranteeing order
- Retransmitting lost packets

This makes UDP **faster and lighter** than TCP — but unreliable.

### TCP vs UDP — Visual Comparison

```
TCP (with handshake & ACKs):
─────────────────────────────
Client         Server
  │  ──SYN────►  │
  │  ◄─SYN-ACK── │
  │  ──ACK────►  │
  │  ──Data1──►  │
  │  ◄─ACK──────  │
  │  ──Data2──►  │
  │  ◄─ACK──────  │
  │  ──FIN────►  │
  └──────────────┘
  (lots of round-trips)


UDP (no handshake, no ACK):
─────────────────────────────
Client         Server
  │  ──Data1──►  │  (maybe arrives)
  │  ──Data2──►  │  (maybe arrives out of order)
  │  ──Data3──►  │  (maybe lost — nobody knows)
  └──────────────┘
  (fast, minimal overhead)
```

### When Would You Use UDP in OMS?

| Use Case                          | Protocol | Why                                       |
|-----------------------------------|----------|-------------------------------------------|
| Place an order                    | TCP      | Must not lose data, order must be correct |
| Process payment                   | TCP      | Critical — reliability is non-negotiable  |
| Stream order metrics to dashboard | UDP      | A missed data point is acceptable         |
| DNS lookup for service discovery  | UDP      | Fast lookup, single packet, retry is easy |
| Live inventory count broadcast    | UDP      | Speed matters, occasional miss is OK      |

---

## 6. TCP vs UDP — Side by Side

```
┌──────────────────┬──────────────────────┬──────────────────────┐
│ Feature          │        TCP           │        UDP           │
├──────────────────┼──────────────────────┼──────────────────────┤
│ Connection       │ Connection-oriented  │ Connectionless       │
│ Handshake        │ 3-way (SYN/SYN-ACK) │ None                 │
│ Reliability      │ Guaranteed delivery  │ Best-effort          │
│ Ordering         │ In-order delivery    │ No order guarantee   │
│ Error recovery   │ Yes (retransmits)    │ No                   │
│ Speed            │ Slower (overhead)    │ Faster               │
│ Use in OMS       │ Orders, Payments     │ Metrics, Logs        │
│ Go package       │ net.Dial("tcp",...)  │ net.Dial("udp",...)  │
└──────────────────┴──────────────────────┴──────────────────────┘
```

---

## 7. Common Application Protocols

These are protocols you'll encounter when building OMS-like services. They all run **on top of** TCP or UDP:

```
┌───────────────────────────────────────────────────────────────┐
│                   Application Protocols                       │
├─────────────┬────────┬──────────────────────────────────────┐ │
│ Protocol    │  Port  │ Use in OMS                           │ │
├─────────────┼────────┼──────────────────────────────────────┤ │
│ HTTP        │   80   │ REST API for order submission        │ │
│ HTTPS       │  443   │ Secure REST API (TLS over HTTP)      │ │
│ DNS         │   53   │ Resolve "payment-svc" to IP          │ │
│ SSH         │   22   │ Deploy OMS to production servers     │ │
│ SMTP        │   25   │ Send order confirmation emails       │ │
│ AMQP        │  5672  │ RabbitMQ message queue for orders    │ │
│ gRPC        │  varies│ Internal service-to-service calls    │ │
│ SNMP        │  161   │ Monitor server health (UDP)          │ │
│ DHCP        │  67/68 │ Assign IPs to new servers            │ │
└─────────────┴────────┴──────────────────────────────────────┘ │
                         TCP-based ─────────────────────────────┘
                                    UDP-based: DNS, DHCP, SNMP
```

---

## 8. Go Networking in Practice

### Go's `net` Package

Go's standard library `net` package handles everything from raw TCP sockets to DNS resolution.

```
net package
├── net.Dial()         → Connect to a server (TCP/UDP client)
├── net.Listen()       → Start listening (TCP server)
├── net.ListenPacket() → Start listening (UDP server)
├── net.LookupHost()   → DNS resolution
├── net.ParseIP()      → Validate/parse an IP address
└── net.Conn           → Interface for reading/writing on a connection
```

### Key Interfaces in Go Networking

```go
// net.Conn — represents any network connection (TCP or UDP)
type Conn interface {
    Read(b []byte) (n int, err error)
    Write(b []byte) (n int, err error)
    Close() error
    LocalAddr() Addr
    RemoteAddr() Addr
    SetDeadline(t time.Time) error
}
```

This interface is powerful because a TCP conn and a test mock can both implement it — your business logic doesn't need to care.

---

## 9. Building a TCP Server in Go (OMS Example)

Let's build a simple **Order Receiver** — a TCP server that accepts raw order data from clients.

### Architecture

```
                    TCP Port 9000
                         │
  Client A ──────────────┤
  Client B ──────────────┼──► OMS TCP Server ──► Process Order
  Client C ──────────────┤         │
                         │    goroutine per
                         │    connection
```

### Server Code

```go
package main

import (
    "bufio"
    "fmt"
    "log"
    "net"
    "strings"
)

func main() {
    // Start listening on TCP port 9000
    listener, err := net.Listen("tcp", ":9000")
    if err != nil {
        log.Fatalf("failed to listen: %v", err)
    }
    defer listener.Close()

    fmt.Println("OMS TCP server listening on :9000")

    for {
        // Block until a new client connects (3-way handshake happens here)
        conn, err := listener.Accept()
        if err != nil {
            log.Printf("accept error: %v", err)
            continue
        }

        // Handle each connection in its own goroutine
        // This is idiomatic Go — lightweight, concurrent
        go handleOrderConnection(conn)
    }
}

func handleOrderConnection(conn net.Conn) {
    defer conn.Close() // sends FIN when handler returns

    clientAddr := conn.RemoteAddr().String()
    fmt.Printf("[+] New client connected: %s\n", clientAddr)

    scanner := bufio.NewScanner(conn)
    for scanner.Scan() {
        line := scanner.Text()

        // In a real OMS: parse JSON, validate, save to DB
        if strings.HasPrefix(line, "ORDER:") {
            orderID := strings.TrimPrefix(line, "ORDER:")
            fmt.Printf("[OMS] Received order %s from %s\n", orderID, clientAddr)

            // Send acknowledgement back to client
            fmt.Fprintf(conn, "ACK:%s\n", orderID)
        }
    }

    fmt.Printf("[-] Client disconnected: %s\n", clientAddr)
}
```

### Client Code

```go
package main

import (
    "bufio"
    "fmt"
    "log"
    "net"
)

func main() {
    // Perform 3-way handshake with OMS server
    conn, err := net.Dial("tcp", "localhost:9000")
    if err != nil {
        log.Fatalf("could not connect to OMS: %v", err)
    }
    defer conn.Close()

    fmt.Println("Connected to OMS server")

    // Send an order
    fmt.Fprintln(conn, "ORDER:ORD-2024-001")

    // Read the acknowledgement
    scanner := bufio.NewScanner(conn)
    if scanner.Scan() {
        fmt.Printf("Server response: %s\n", scanner.Text())
        // Output: Server response: ACK:ORD-2024-001
    }
}
```

### Connection Flow Diagram

```
Client                          OMS TCP Server (:9000)
   │                                     │
   │  ── SYN ──────────────────────────► │  net.Listen(":9000")
   │  ◄─ SYN-ACK ───────────────────────  │
   │  ── ACK ──────────────────────────► │  listener.Accept()  ← unblocks
   │                                     │        │
   │                                     │  go handleOrderConnection(conn)
   │  ── "ORDER:ORD-2024-001\n" ───────► │        │ scanner.Scan()
   │  ◄─ "ACK:ORD-2024-001\n" ──────────  │        │ fmt.Fprintf(conn,...)
   │                                     │        │
   │  ── FIN ──────────────────────────► │  conn.Close() (deferred)
   │  ◄─ ACK ───────────────────────────  │
   │  ◄─ FIN ───────────────────────────  │
   │  ── ACK ──────────────────────────► │
```

### Setting Timeouts (Important for Production)

Without timeouts, a slow or crashed client can hold a goroutine forever:

```go
func handleOrderConnection(conn net.Conn) {
    defer conn.Close()

    // Client must send data within 30 seconds
    conn.SetDeadline(time.Now().Add(30 * time.Second))

    // ... rest of handler
}
```

---

## 10. Building a UDP Client in Go (OMS Metrics Example)

OMS needs to send metrics (order count, response time, errors) to a monitoring server. Metrics are a perfect UDP use case — if one packet is lost, it's not catastrophic.

### Architecture

```
OMS Order Service                    Metrics Server (:9001/udp)
       │                                        │
       │── "metric:orders_placed=42" ─────────► │  (no ACK, no connection)
       │── "metric:avg_response_ms=34" ────────► │
       │── "metric:errors=0" ─────────────────► │
       │                                        │
       │  (some packets may be lost — that's OK) │
```

### UDP Sender (OMS Service)

```go
package main

import (
    "fmt"
    "log"
    "net"
    "time"
)

type MetricsClient struct {
    conn net.Conn
}

func NewMetricsClient(serverAddr string) (*MetricsClient, error) {
    // net.Dial with "udp" — no handshake, just creates a local socket
    conn, err := net.Dial("udp", serverAddr)
    if err != nil {
        return nil, fmt.Errorf("failed to create UDP socket: %w", err)
    }
    return &MetricsClient{conn: conn}, nil
}

func (m *MetricsClient) Send(metric string, value int) {
    msg := fmt.Sprintf("metric:%s=%d ts:%d", metric, value, time.Now().Unix())
    _, err := fmt.Fprintln(m.conn, msg)
    if err != nil {
        // With UDP we log and move on — we never block the main flow
        log.Printf("metrics send failed (non-critical): %v", err)
    }
}

func (m *MetricsClient) Close() {
    m.conn.Close()
}

func main() {
    metrics, err := NewMetricsClient("10.2.3.44:9001")
    if err != nil {
        log.Fatal(err)
    }
    defer metrics.Close()

    // Simulate OMS processing orders and reporting metrics
    for i := 1; i <= 5; i++ {
        fmt.Printf("Processing order #%d\n", i)
        // ... process order logic ...

        // Fire-and-forget — doesn't slow down order processing
        metrics.Send("orders_placed", i)
        metrics.Send("avg_response_ms", 25+i)

        time.Sleep(500 * time.Millisecond)
    }
}
```

### UDP Server (Metrics Collector)

```go
package main

import (
    "fmt"
    "log"
    "net"
)

func main() {
    // ListenPacket for UDP (not Listen — that's for TCP)
    pc, err := net.ListenPacket("udp", ":9001")
    if err != nil {
        log.Fatal(err)
    }
    defer pc.Close()

    fmt.Println("Metrics server listening on UDP :9001")

    buf := make([]byte, 1024)
    for {
        n, addr, err := pc.ReadFrom(buf)
        if err != nil {
            log.Printf("read error: %v", err)
            continue
        }
        // No ACK sent back — UDP is one-way here
        fmt.Printf("[METRIC from %s] %s\n", addr, string(buf[:n]))
    }
}
```

---

## 11. HTTP Server in Go (OMS REST API)

HTTP is built on top of TCP. Go's `net/http` package handles the TCP layer for you.

### OMS Order API

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "time"
)

type Order struct {
    ID        string    `json:"id"`
    Product   string    `json:"product"`
    Quantity  int       `json:"quantity"`
    CreatedAt time.Time `json:"created_at"`
}

type OrderResponse struct {
    Status  string `json:"status"`
    OrderID string `json:"order_id"`
    Message string `json:"message"`
}

func createOrderHandler(w http.ResponseWriter, r *http.Request) {
    if r.Method != http.MethodPost {
        http.Error(w, "method not allowed", http.StatusMethodNotAllowed)
        return
    }

    var order Order
    if err := json.NewDecoder(r.Body).Decode(&order); err != nil {
        http.Error(w, "invalid JSON", http.StatusBadRequest)
        return
    }

    // Assign an order ID (in real OMS: save to DB, publish to queue, etc.)
    order.ID = fmt.Sprintf("ORD-%d", time.Now().UnixNano())
    order.CreatedAt = time.Now()

    log.Printf("New order received: %+v", order)

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusCreated) // 201

    json.NewEncoder(w).Encode(OrderResponse{
        Status:  "created",
        OrderID: order.ID,
        Message: "Your order has been received",
    })
}

func healthHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(map[string]string{"status": "ok"})
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/orders", createOrderHandler)
    mux.HandleFunc("/health", healthHandler)

    server := &http.Server{
        Addr:         ":8080",
        Handler:      mux,
        ReadTimeout:  10 * time.Second,
        WriteTimeout: 10 * time.Second,
        IdleTimeout:  60 * time.Second,
    }

    fmt.Println("OMS HTTP server listening on :8080")
    log.Fatal(server.ListenAndServe())
}
```

### What Happens Under the Hood (HTTP over TCP)

```
Mobile App                          OMS HTTP Server (:8080)
    │                                        │
    │  TCP: SYN ───────────────────────────► │
    │  TCP: SYN-ACK ◄───────────────────────  │
    │  TCP: ACK ───────────────────────────► │  (TCP connection established)
    │                                        │
    │  POST /orders HTTP/1.1 ──────────────► │  (HTTP request over TCP)
    │  Content-Type: application/json        │
    │  {"product":"Laptop","quantity":1}     │
    │                                        │
    │  HTTP/1.1 201 Created ◄───────────────  │  (HTTP response over TCP)
    │  {"status":"created","order_id":"..."}  │
    │                                        │
    │  TCP: FIN ───────────────────────────► │  (connection closes or reused)
```

### Test it with curl

```bash
# Place an order
curl -X POST http://localhost:8080/orders \
  -H "Content-Type: application/json" \
  -d '{"product": "Laptop", "quantity": 2}'

# Expected output:
# {"status":"created","order_id":"ORD-1234567890","message":"Your order has been received"}

# Health check
curl http://localhost:8080/health
# {"status":"ok"}
```

---

## 12. Summary & Best Practices

### Core Concepts Recap

```
┌─────────────────────────────────────────────────────────────┐
│                    What You Learned                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  IP Address   → Identifies the machine (10.2.3.44)         │
│  Port         → Identifies the service (:8080, :5432)      │
│  Socket       → IP + Port (10.2.3.44:8080)                 │
│                                                             │
│  TCP          → Reliable, ordered, connection-oriented      │
│                 Uses 3-way handshake (SYN/SYN-ACK/ACK)     │
│                 Use for: orders, payments, any critical data │
│                                                             │
│  UDP          → Fast, connectionless, fire-and-forget       │
│                 No handshake, no acknowledgement            │
│                 Use for: metrics, logs, live broadcasts     │
│                                                             │
│  HTTP         → Application protocol built on TCP           │
│                 Go's net/http handles TCP automatically     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Best Practices for Go Network Code

| Practice | Why It Matters |
|---|---|
| Always call `defer conn.Close()` | Prevents connection/resource leaks |
| Set `SetDeadline()` on connections | Prevents goroutine leaks from stalled clients |
| Handle each connection in a goroutine | Go's strength — cheap concurrency |
| Use `bufio.Scanner` for line-based reads | Avoids partial reads from TCP stream |
| Use `net/http` for HTTP (not raw TCP) | Battle-tested, handles edge cases for you |
| Log `RemoteAddr()` in server handlers | Essential for debugging in production |
| Check errors on every `Write` | TCP write errors mean the client disconnected |

### Decision Tree: TCP or UDP?

```
Does the data NEED to arrive?
         │
    YES ─┤                           NO ─────► UDP
         │                                      │
    Is order important?                   Is speed critical?
         │                                      │
    YES ─┼──► TCP                         YES ──► UDP
    NO ──┘                                NO ───► UDP (still)

Examples:
  Order placement   → TCP  ✓  (can't lose an order)
  Payment request   → TCP  ✓  (must complete or fail cleanly)
  Metrics emission  → UDP  ✓  (losing one data point is fine)
  DNS lookup        → UDP  ✓  (fast, and client retries if needed)
```

---

## Further Reading

- [Go `net` package documentation](https://pkg.go.dev/net)
- [Go `net/http` package documentation](https://pkg.go.dev/net/http)
- [Effective Go — Concurrency](https://go.dev/doc/effective_go#concurrency)
- RFC 793 — TCP specification
- RFC 768 — UDP specification
