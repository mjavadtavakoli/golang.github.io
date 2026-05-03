# Hexagonal Architecture (Ports & Adapters) in Go

## Table of Contents

1. **What's the Problem? Why Hexagonal at All?**
2. **Core Concepts: Domain, Ports, Adapters**
3. **Mapping the Architecture to Go Project Structure**
4. **Key Concepts: DIP, Testability, Composition Root**
5. **Step-by-Step Implementation: Order Management System**
6. **Common Mistakes**
7. **What's the Best Go Project Structure?**
8. **Comparison with Other Architectures**
9. **Advanced Topics**
10. **Suggested Teaching Order**

---

## 1. What's the Problem? Why Hexagonal at All?

### The Pain Everyone Has Felt

Before explaining what Hexagonal is, we need to see **what problem** it solves. Look at typical Go code:

```go
// handler.go — everything mixed together!
func CreateOrderHandler(c *gin.Context) {
    var req CreateOrderRequest
    c.BindJSON(&req)

    // business validation inside handler!
    if req.Total < 0 {
        c.JSON(400, gin.H{"error": "invalid total"})
        return
    }

    // direct database access!
    db := database.GetDB()
    _, err := db.Exec(
        "INSERT INTO orders (customer_id, total) VALUES (?, ?)",
        req.CustomerID, req.Total,
    )
    if err != nil {
        c.JSON(500, gin.H{"error": err.Error()})
        return
    }

    // direct email sending!
    smtp.SendMail("smtp.gmail.com:587", auth, from, to, body)

    c.JSON(201, gin.H{"message": "created"})
}
```

**Problems with this code:**

| Problem | Description |
|---------|-------------|
| **Direct framework dependency** | `gin.Context` is everywhere. If you switch from Gin to Echo or `net/http`, you have to change **all** the code |
| **Direct database dependency** | `database.GetDB()` and raw SQL inside the handler. If you switch from MySQL to PostgreSQL, the handler must change |
| **Untestable** | To test this handler you need Gin up, a database connected, and an SMTP server |
| **Business logic scattered** | Validation, persistence, notification — all in one function |

### Real-World Analogy: Restaurant Kitchen

Imagine a professional restaurant kitchen:

**The chef** (Domain/Core) only knows how to cook. They:
- **Don't know** whether the order came from an app or from a waiter
- **Don't know** whether the food is served on ceramic plates or in takeaway boxes
- **Only know**: “Give me ingredients, I'll deliver the dish”

Now imagine the chef had to:
- Take orders from customers (dependency on UI)
- Buy ingredients from the market (dependency on supplier)
- Package for delivery (dependency on delivery)

They wouldn't be a chef anymore — they'd be one person doing everything. **That's exactly what happened in the code above.**

In a professional restaurant:
- **Order port**: Where orders come in — from waiter, app, or phone
- **Delivery port**: Where food goes out — for dine-in or takeaway
- **Waiter, app, phone** (Driving Adapters): Different ways to place orders
- **Ceramic plate, takeaway box** (Driven Adapters): Different ways to deliver

The chef doesn't change. **Only the adapters change.**

### The Golden Test: Changing Technology

Ask yourself:

> If tomorrow the manager says: “Drop REST, use only gRPC” — **how many files must change?**

- **Bad architecture**: Dozens of files. Business logic is tangled with HTTP.
- **Hexagonal architecture**: **Only one new adapter**. Business logic stays untouched.

> If tomorrow they say: “Replace MySQL with PostgreSQL” — **how many files must change?**

- **Bad architecture**: Every place you wrote SQL. Handlers, services, everything.
- **Hexagonal architecture**: **Only the database adapter**. One folder changes.

### Dependency Direction — The Most Important Rule

This is the main rule of Hexagonal:

```
✅ Business logic does not depend on the outside. The outside depends on business logic.

                ┌──────────────┐
     Adapter ──►│   Domain     │◄── Adapter
     (HTTP)     │  (Business)  │    (MySQL)
                └──────────────┘

   Dependencies point inward, not the other way!
```

```
❌ Wrong:  Domain ──import──► mysql package
❌ Wrong:  Domain ──import──► gin package
✅ Right:  mysql adapter ──import──► Domain interface
✅ Right:  http adapter ──import──► Domain interface
```

**Real-world analogy:** Think of a power outlet as a Port. You plug in a laptop, phone charger, or vacuum (Adapters). The outlet doesn't know what device is plugged in. It only defines an interface (the plug shape), and anyone can implement it.

---

## 2. Core Concepts: Domain, Ports, Adapters

### 2.1 Core (Domain) — The Heart of the System

The Domain is your **pure business logic**. Here:
- **Entities** are defined (e.g. `Order`, `Product`, `Customer`)
- **Value Objects** are defined (e.g. `Money`, `Email`, `Address`)
- **Business rules** are written (e.g. “An order cannot have a negative amount”)

Unlike an Entity, which is identified by an ID, a **Value Object** is identified by the data it contains.

#### DTO vs Value Object

| Concept | Role |
|---------|------|
| **DTO** | Only returns data. |
| **Value Object** | Ensures that the data is valid. |

#### Golden Rule: Where Does the Logic Go?

| If the logic is… | Put it in… |
|------------------|------------|
| Related to the **identity and inherent behavior of an entity** | **Domain** (Entity / Value Object) |
| Related to the **coordination of multiple components** | **Service** |

> **⚠️ Anti-pattern:** Many teams write all the logic in the Service and the Entity becomes an empty struct — this is called an **Anemic Domain Model** and should be avoided.

```go
type Order struct {
    ID    int64
    Items []OrderItem
}
```
Service ❌
```go
func (s *OrderService) RemoveItem(orderID, productID int64) error {
    order, _ := s.repo.Get(orderID)

    var newItems []OrderItem
    for _, item := range order.Items {
        if item.ProductID != productID {
            newItems = append(newItems, item)
        }
    }

    if len(newItems) == 0 {
        return errors.New("order must have at least one item")
    }

    order.Items = newItems

    return s.repo.Save(order)
}
```
Problem:

Rule propagated inside Service

Items may change anywhere else

Invariant breaks

```go
func (o *Order) RemoveItem(productID int64) error {
    index := -1

    for i, item := range o.Items {
        if item.ProductID == productID {
            index = i
            break
        }
    }

    if index == -1 {
        return errors.New("item not found")
    }

    if len(o.Items) == 1 {
        return errors.New("order must have at least one item")
    }

    o.Items = append(o.Items[:index], o.Items[index+1:]...)
    return nil
}
```
Now:

✅ The rule is enforced inside the Order
✅ No one outside can break the invariant
✅ The behavior is set aside

**Golden rule of Domain: no external imports!**

```go
package domain

// ✅ Only standard Go imports are allowed
import (
    "time"
    "errors"
    "fmt"
)

// ❌ None of these should be in domain!
// import "database/sql"
// import "github.com/gin-gonic/gin"
// import "github.com/go-redis/redis"
// import "gorm.io/gorm"
```

**Why?** Because the Domain must be **pure Go**. If tomorrow Gin, Redis, or GORM are removed — your Domain keeps working unchanged.

**Real-world analogy:** Football rules (Domain) are independent of natural or artificial grass (Adapter). The offside rule doesn't change depending on the pitch. If the pitch changes from grass to dirt, the rules don't change.

#### Entity

An Entity is a business object with identity (ID):

```go
// internal/domain/order.go
package domain

import (
    "errors"
    "time"
)

var (
    ErrEmptyCustomerID = errors.New("customer ID cannot be empty")
    ErrNoItems         = errors.New("order must have at least one item")
    ErrInvalidQuantity = errors.New("quantity must be positive")
    ErrInvalidPrice    = errors.New("price must be positive")
)

type OrderStatus string

const (
    OrderStatusPending   OrderStatus = "pending"
    OrderStatusConfirmed OrderStatus = "confirmed"
    OrderStatusCancelled OrderStatus = "cancelled"
    OrderStatusDelivered OrderStatus = "delivered"
)

type Order struct {
    ID         int64
    CustomerID string
    Items      []OrderItem
    Status     OrderStatus
    CreatedAt  time.Time
}

type OrderItem struct {
    ProductID int64
    Name      string
    Price     float64
    Quantity  int
}
//primitive obsession

//Value objecitve
//type CustomerID string // may has rules
//type Quantity int // quantity has rules
//type Money struct {
//    amount int64 // if it be float, it is dangrous
//    currency string
//}

func NewOrder(customerID string, items []OrderItem) (*Order, error) {
    //Validation
    if customerID == "" {
        return nil, ErrEmptyCustomerID
    }
    if len(items) == 0 {
        return nil, ErrNoItems
    }
    for _, item := range items {
        //Business Rules
        if item.Quantity <= 0 {
            return nil, ErrInvalidQuantity
        }

        //Business Rules
        if item.Price <= 0 {
            return nil, ErrInvalidPrice
        }
    }

    return &Order{
        CustomerID: customerID,
        Items:      items,
        Status:     OrderStatusPending,
        CreatedAt:  time.Now(),
    }, nil
}

func (o *Order) TotalPrice() float64 {
    var total float64
    for _, item := range o.Items {
        total += item.Price * float64(item.Quantity)
    }
    return total
}
//Business Rules
func (o *Order) Cancel() error {
    if o.Status == OrderStatusDelivered {
        return errors.New("cannot cancel delivered order")
    }
    o.Status = OrderStatusCancelled
    return nil
}
//Business Rules
func (o *Order) Confirm() error {
    if o.Status != OrderStatusPending {
        return errors.New("can only confirm pending orders")
    }
    o.Status = OrderStatusConfirmed
    return nil
}
```

Notice: **no trace** of database, HTTP, Redis, or any framework. This is a plain Go struct with business rules.

**Real-world analogy:** `Order` is like a paper invoice. The invoice doesn't know which warehouse stores it (MySQL or PostgreSQL). It only knows: “I contain line items and have a total.”

#### What is difference between business rules and business logic?
Rule:
Expired voucher codes cannot be used.

Logic:
```go
func (v Voucher) Validate(now time.Time) error {
    if now.After(v.ExpiryDate) {
        return errors.New("voucher expired")
    }
    return nil
}
```

#### Value Object

A Value Object has no identity — only its **value** matters:

```go
// internal/domain/money.go
package domain

import "fmt"

type Money struct {
    Amount   float64
    Currency string
}

func NewMoney(amount float64, currency string) (Money, error) {
    if amount < 0 {
        return Money{}, fmt.Errorf("amount cannot be negative: %f", amount)
    }
    if currency == "" {
        return Money{}, fmt.Errorf("currency cannot be empty")
    }
    return Money{Amount: amount, Currency: currency}, nil
}

func (m Money) Add(other Money) (Money, error) {
    if m.Currency != other.Currency {
        return Money{}, fmt.Errorf("cannot add %s to %s", m.Currency, other.Currency)
    }
    return Money{Amount: m.Amount + other.Amount, Currency: m.Currency}, nil
}
```
In a production-scale system, it is better to make these Value Objects.
```go
type CustomerID string
type Quantity int
type Money struct {
    amount int64
    currency string
}
```
Why?

Because:

float64 is dangerous for money

Quantity has rules

CustomerID may have a special format

**Real-world analogy:** “50 dollars” is a Value Object. It doesn't matter which 50-dollar bill — the value matters, not its identity. But “Bank account #1234” is an Entity because it has a specific identity.

### 2.2 Ports — Contracts (Interfaces)

A Port is an **interface** that the **Core defines** and the outside must implement.

**Important:** Ports are defined in domain or application, **not in the adapter!**

There are two kinds of Ports:

#### Inbound Port — Use Case

What **enters the system from outside**. The outside world talks to the system through this.

```go
// internal/domain/ports.go (or internal/application/ports.go)
package domain

import "context"

// Inbound Port: the outside world uses this interface to talk to our system
type OrderService interface {
    CreateOrder(ctx context.Context, customerID string, items []OrderItem) (*Order, error)
    CancelOrder(ctx context.Context, orderID int64) error
    GetOrder(ctx context.Context, orderID int64) (*Order, error)
}
```

**Real-world analogy:** The Inbound Port is like a **restaurant menu**. The menu says “what you can order.” It doesn't matter if the customer is in person, on the phone, or in an app — everyone uses the same menu.

#### Outbound Port — Repository and Gateway

What **the system needs** but whose implementation lives outside.

```go
// internal/domain/repository.go
package domain

import "context"

// Outbound Port: our system needs this capability
// but doesn't know (or care) how it's implemented
type OrderRepository interface {
    Save(ctx context.Context, order *Order) error
    FindByID(ctx context.Context, id int64) (*Order, error)
    FindByCustomerID(ctx context.Context, customerID string) ([]*Order, error)
    Update(ctx context.Context, order *Order) error
}

// Outbound Port: sending notifications
type NotificationSender interface {
    SendOrderConfirmation(ctx context.Context, order *Order) error
}
```

**Real-world analogy:** An Outbound Port is like a **job description**. The kitchen says “we need someone who delivers food to the customer.” The description doesn't say whether the deliverer uses a bike, car, or scooter — it only says “food must arrive.” MySQL, PostgreSQL, MongoDB are different “deliverers” that fulfill the same “job” (interface).

### 2.3 Adapters — Implementations

Adapters are the real implementations of Ports. **An adapter only translates; it doesn't make business decisions.**

There are two kinds of Adapters:

#### Driving Adapter (Inbound) — HTTP Handler, gRPC, CLI

These adapters **receive** requests from the outside and pass them to the inbound port:

```go
// internal/adapters/inbound/http/order_handler.go
package http

import (
    "encoding/json"
    "net/http"
    "strconv"

    "myapp/internal/domain"
)

type OrderHandler struct {
    service domain.OrderService
}

func NewOrderHandler(service domain.OrderService) *OrderHandler {
    return &OrderHandler{service: service}
}

type createOrderRequest struct {
    CustomerID string             `json:"customer_id"`
    Items      []orderItemRequest `json:"items"`
}

type orderItemRequest struct {
    ProductID int64   `json:"product_id"`
    Name      string  `json:"name"`
    Price     float64 `json:"price"`
    Quantity  int     `json:"quantity"`
}

func (h *OrderHandler) CreateOrder(w http.ResponseWriter, r *http.Request) {
    var req createOrderRequest
    if err := json.NewDecoder(r.Body).Decode(&req); err != nil {
        http.Error(w, "invalid request body", http.StatusBadRequest)
        return
    }

    items := make([]domain.OrderItem, len(req.Items))
    for i, item := range req.Items {
        items[i] = domain.OrderItem{
            ProductID: item.ProductID,
            Name:      item.Name,
            Price:     item.Price,
            Quantity:  item.Quantity,
        }
    }

    order, err := h.service.CreateOrder(r.Context(), req.CustomerID, items)
    if err != nil {
        http.Error(w, err.Error(), http.StatusBadRequest)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusCreated)
    json.NewEncoder(w).Encode(order)
}

func (h *OrderHandler) GetOrder(w http.ResponseWriter, r *http.Request) {
    idStr := r.PathValue("id")
    id, err := strconv.ParseInt(idStr, 10, 64)
    if err != nil {
        http.Error(w, "invalid order ID", http.StatusBadRequest)
        return
    }

    order, err := h.service.GetOrder(r.Context(), id)
    if err != nil {
        http.Error(w, err.Error(), http.StatusNotFound)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(order)
}

func (h *OrderHandler) CancelOrder(w http.ResponseWriter, r *http.Request) {
    idStr := r.PathValue("id")
    id, err := strconv.ParseInt(idStr, 10, 64)
    if err != nil {
        http.Error(w, "invalid order ID", http.StatusBadRequest)
        return
    }

    if err := h.service.CancelOrder(r.Context(), id); err != nil {
        http.Error(w, err.Error(), http.StatusBadRequest)
        return
    }

    w.WriteHeader(http.StatusNoContent)
}
```

Notice: the handler **makes no business decisions**. It only:
1. Parses the request
2. Calls the service
3. Formats the response

**Real-world analogy:** A Driving Adapter is like a **waiter**. The waiter takes the customer's order (HTTP request), translates it into kitchen language (domain input), and gives it to the chef. The waiter **doesn't cook** (has no business logic).

#### Driven Adapter (Outbound) — MySQL, Redis, SMTP

These adapters **implement** the outbound port:

```go
// internal/adapters/outbound/mysql/order_repository.go
package mysql

import (
    "context"
    "database/sql"

    "myapp/internal/domain"
)

type OrderRepository struct {
    db *sql.DB
}

func NewOrderRepository(db *sql.DB) *OrderRepository {
    return &OrderRepository{db: db}
}

func (r *OrderRepository) Save(ctx context.Context, order *domain.Order) error {
    result, err := r.db.ExecContext(ctx,
        "INSERT INTO orders (customer_id, status, created_at) VALUES (?, ?, ?)",
        order.CustomerID, order.Status, order.CreatedAt,
    )
    if err != nil {
        return err
    }

    id, err := result.LastInsertId()
    if err != nil {
        return err
    }
    order.ID = id

    for _, item := range order.Items {
        _, err := r.db.ExecContext(ctx,
            "INSERT INTO order_items (order_id, product_id, name, price, quantity) VALUES (?, ?, ?, ?, ?)",
            order.ID, item.ProductID, item.Name, item.Price, item.Quantity,
        )
        if err != nil {
            return err
        }
    }

    return nil
}

func (r *OrderRepository) FindByID(ctx context.Context, id int64) (*domain.Order, error) {
    row := r.db.QueryRowContext(ctx,
        "SELECT id, customer_id, status, created_at FROM orders WHERE id = ?", id,
    )

    var order domain.Order
    if err := row.Scan(&order.ID, &order.CustomerID, &order.Status, &order.CreatedAt); err != nil {
        if err == sql.ErrNoRows {
            return nil, domain.ErrOrderNotFound
        }
        return nil, err
    }

    rows, err := r.db.QueryContext(ctx,
        "SELECT product_id, name, price, quantity FROM order_items WHERE order_id = ?", id,
    )
    if err != nil {
        return nil, err
    }
    defer rows.Close()

    for rows.Next() {
        var item domain.OrderItem
        if err := rows.Scan(&item.ProductID, &item.Name, &item.Price, &item.Quantity); err != nil {
            return nil, err
        }
        order.Items = append(order.Items, item)
    }

    return &order, nil
}

func (r *OrderRepository) FindByCustomerID(ctx context.Context, customerID string) ([]*domain.Order, error) {
    rows, err := r.db.QueryContext(ctx,
        "SELECT id, customer_id, status, created_at FROM orders WHERE customer_id = ?", customerID,
    )
    if err != nil {
        return nil, err
    }
    defer rows.Close()

    var orders []*domain.Order
    for rows.Next() {
        var order domain.Order
        if err := rows.Scan(&order.ID, &order.CustomerID, &order.Status, &order.CreatedAt); err != nil {
            return nil, err
        }
        orders = append(orders, &order)
    }

    return orders, nil
}

func (r *OrderRepository) Update(ctx context.Context, order *domain.Order) error {
    _, err := r.db.ExecContext(ctx,
        "UPDATE orders SET status = ? WHERE id = ?",
        order.Status, order.ID,
    )
    return err
}
```

**Real-world analogy:** A Driven Adapter is like a **storekeeper**. The chef (Domain) says “store this dish” (Save). The storekeeper can put it in the fridge (MySQL), freezer (PostgreSQL), or on the counter (In-Memory). The chef doesn't care **where** it's stored.

### Overview: Who Knows Whom?

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Outside World (Inbound)                               │
│   Browser    Mobile App    CLI    gRPC Client    Other Services             │
└────────────────────┬────────────────────────────────┬───────────────────────┘
                     │                                │
                     ▼                                ▼
┌────────────────────────────────────────────────────────────────────────────┐
│                   Driving Adapters (Inbound Adapters)                        │
│        HTTP Handler       gRPC Handler       CLI Command                    │
│     ──────────────────────────────────────────────────                     │
│       These import domain (they know the interface)                         │
└────────────────────┬────────────────────────────────┬──────────────────────┘
                     │                                │
                     │    ┌─────────────────────┐     │
                     └───►│  Inbound Ports       │◄────┘
                          │  (OrderService)      │
                          └──────────┬───────────┘
                                     │
                          ┌──────────▼───────────┐
                          │      DOMAIN          │
                          │   (Business Logic)   │
                          │   Pure Go            │
                          │   No external imports│
                          └──────────┬───────────┘
                                     │
                          ┌──────────▼───────────┐
                     ┌────│  Outbound Ports       │────┐
                     │    │  (OrderRepository)    │    │
                     │    │  (NotificationSender) │    │
                     │    └──────────────────────-┘    │
                     │                                 │
┌────────────────────▼─────────────────────────────────▼─────────────────────┐
│                  Driven Adapters (Outbound Adapters)                          │
│     MySQLRepo     PostgresRepo     InMemoryRepo     SMTPSender                │
│     ──────────────────────────────────────────────────                       │
│       These import domain (they implement the interface)                     │
└────────────────────┬─────────────────────────────────┬─────────────────────┘
                     │                                 │
                     ▼                                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                     Outside World (Outbound)                                 │
│        MySQL       PostgreSQL       Redis       SMTP Server                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Mapping the Architecture to Go Project Structure

### The Eternal Student Question: “What Should the Folder Structure Be?”

**Important answer:** Folder structure is a tool, not the goal. **Dependency direction** matters, not folder names.

For practical guidance, here are two suggested structures:

### Full Structure (Larger Projects)

```
myapp/
├── cmd/
│   └── api/
│       └── main.go                    # Composition Root — wiring
│
├── internal/
│   ├── app/                           # Composition Root — dependency wiring
│   │   └── wire.go
│   │
│   ├── domain/                        # Core — heart of the system
│   │   ├── order.go                   # Entity: Order, OrderItem
│   │   └── errors.go                  # Business errors
│   │
│   ├── port/                          # Interfaces (inbound/outbound ports)
│   │   └── repository.go              # Outbound Port: OrderRepository interface
│   │
│   ├── service/                       # Use Cases — orchestrator
│   │   └── order_service.go           # OrderService implementation
│   │
│   ├── handler/                       # Inbound adapters (driving)
│   │   ├── http/
│   │   │   └── order_handler.go       # REST API
│   │   └── grpc/
│   │       └── order_server.go        # gRPC API
│   │
│   ├── infra/                         # Outbound adapters (driven)
│   │   ├── mysql/
│   │   │   └── order_repository.go
│   │   ├── postgres/
│   │   │   └── order_repository.go
│   │   └── memory/
│   │       └── order_repository.go
│   │
│   └── utils/                         # Helpers, shared utilities
│       └── ...
│
├── go.mod
└── go.sum
```

### Simple Structure (Small to Medium Projects)

```
myapp/
├── cmd/
│   └── main.go
│
├── internal/
│   ├── domain/          # Entities
│   ├── port/            # Interfaces
│   ├── service/         # Application Logic / Orchestration
│   ├── handler/         # Driving Adapters (HTTP, gRPC, CLI)
│   ├── infra/           # Driven Adapters (persistence implementations)
│   └── utils/           # Helpers
│
├── go.mod
└── go.sum
```

### Import Rules — Where Everything Becomes Clear

```
✅ delivery   → import → usecase    → import → domain
✅ repository → import → domain
✅ cmd/main   → import → everything (only for wiring)

❌ domain     → import → repository     (forbidden!)
❌ domain     → import → delivery       (forbidden!)
❌ usecase    → import → delivery       (forbidden!)
❌ usecase    → import → mysql          (forbidden!)
```

**Real-world analogy:** Think of the domain as a country's **constitution**. The constitution doesn't depend on any ministry. Ministries (adapters) must follow the constitution (domain). If a ministry is removed, the constitution doesn't change.

---

## 4. Key Concepts

### 4.1 Dependency Inversion Principle (DIP)

This principle says:

> High-level modules (Domain) must not depend on low-level modules (MySQL). Both should depend on abstractions (interfaces).

```
Without DIP:
    OrderService ──depends on──► MySQLRepository (concrete)
    If MySQL changes, OrderService must change too!

With DIP:
    OrderService ──depends on──► OrderRepository (interface)
    MySQLRepository ──implements──► OrderRepository (interface)
    If MySQL changes, only the adapter changes!
```

In Go this is natural because Go has implicit interfaces:

```go
// domain defines: "I need this capability"
type OrderRepository interface {
    Save(ctx context.Context, order *Order) error
    FindByID(ctx context.Context, id int64) (*Order, error)
}

// mysql adapter implements it — without declaring it!
type MySQLOrderRepo struct{ db *sql.DB }

func (r *MySQLOrderRepo) Save(ctx context.Context, order *domain.Order) error { /* ... */ }
func (r *MySQLOrderRepo) FindByID(ctx context.Context, id int64) (*domain.Order, error) { /* ... */ }

// Go infers that MySQLOrderRepo implements the interface!
```

**Real-world analogy:** When you install a power outlet (interface) at home, you don't say “this outlet is only for HP laptops.” The outlet defines a standard (interface), and any device that follows it can plug in. The domain does the same: it defines the interface, and any adapter that implements it can be used.

### 4.2 Testability

The biggest practical benefit of Hexagonal is **testability**.

When the Domain only depends on interfaces, we can build a **Fake** and write tests without a database, HTTP server, or any external service:

```go
// internal/adapters/outbound/memory/order_repository.go
package memory

import (
    "context"
    "sync"

    "myapp/internal/domain"
)

type OrderRepository struct {
    mu     sync.RWMutex
    orders map[int64]*domain.Order
    nextID int64
}

func NewOrderRepository() *OrderRepository {
    return &OrderRepository{
        orders: make(map[int64]*domain.Order),
        nextID: 1,
    }
}

func (r *OrderRepository) Save(ctx context.Context, order *domain.Order) error {
    r.mu.Lock()
    defer r.mu.Unlock()
    order.ID = r.nextID
    r.nextID++
    r.orders[order.ID] = order
    return nil
}

func (r *OrderRepository) FindByID(ctx context.Context, id int64) (*domain.Order, error) {
    r.mu.RLock()
    defer r.mu.RUnlock()
    order, ok := r.orders[id]
    if !ok {
        return nil, domain.ErrOrderNotFound
    }
    return order, nil
}

func (r *OrderRepository) FindByCustomerID(ctx context.Context, customerID string) ([]*domain.Order, error) {
    r.mu.RLock()
    defer r.mu.RUnlock()
    var result []*domain.Order
    for _, order := range r.orders {
        if order.CustomerID == customerID {
            result = append(result, order)
        }
    }
    return result, nil
}

func (r *OrderRepository) Update(ctx context.Context, order *domain.Order) error {
    r.mu.Lock()
    defer r.mu.Unlock()
    r.orders[order.ID] = order
    return nil
}
```

Now tests with no external dependencies:

```go
// internal/application/order_service_test.go
package application_test

import (
    "context"
    "testing"

    "myapp/internal/application"
    "myapp/internal/adapters/outbound/memory"
    "myapp/internal/domain"
)

func TestCreateOrder_Success(t *testing.T) {
    repo := memory.NewOrderRepository()
    service := application.NewOrderService(repo, nil)

    items := []domain.OrderItem{
        {ProductID: 1, Name: "Go Book", Price: 50000, Quantity: 2},
        {ProductID: 2, Name: "Docker Book", Price: 35000, Quantity: 1},
    }

    order, err := service.CreateOrder(context.Background(), "customer-1", items)
    if err != nil {
        t.Fatalf("expected no error, got %v", err)
    }

    if order.ID == 0 {
        t.Error("expected order ID to be set")
    }
    if order.TotalPrice() != 135000 {
        t.Errorf("expected total 135000, got %f", order.TotalPrice())
    }
    if order.Status != domain.OrderStatusPending {
        t.Errorf("expected status pending, got %s", order.Status)
    }
}

func TestCreateOrder_EmptyCustomerID(t *testing.T) {
    repo := memory.NewOrderRepository()
    service := application.NewOrderService(repo, nil)

    items := []domain.OrderItem{
        {ProductID: 1, Name: "Book", Price: 50000, Quantity: 1},
    }

    _, err := service.CreateOrder(context.Background(), "", items)
    if err == nil {
        t.Fatal("expected error for empty customer ID")
    }
}

func TestCreateOrder_NoItems(t *testing.T) {
    repo := memory.NewOrderRepository()
    service := application.NewOrderService(repo, nil)

    _, err := service.CreateOrder(context.Background(), "customer-1", nil)
    if err == nil {
        t.Fatal("expected error for no items")
    }
}

func TestCancelOrder_Success(t *testing.T) {
    repo := memory.NewOrderRepository()
    service := application.NewOrderService(repo, nil)

    items := []domain.OrderItem{
        {ProductID: 1, Name: "Book", Price: 50000, Quantity: 1},
    }

    order, _ := service.CreateOrder(context.Background(), "customer-1", items)
    err := service.CancelOrder(context.Background(), order.ID)
    if err != nil {
        t.Fatalf("expected no error, got %v", err)
    }

    cancelled, _ := service.GetOrder(context.Background(), order.ID)
    if cancelled.Status != domain.OrderStatusCancelled {
        t.Errorf("expected cancelled status, got %s", cancelled.Status)
    }
}
```

**Notice:**
- No MySQL running
- No HTTP server
- No Docker
- Tests run in **milliseconds**

> If the architecture is right, tests are fast and simple.

**Real-world analogy:** To check that a recipe is correct (test business logic), you don't need to open the restaurant, bring customers, and serve. You can cook at home with simple ingredients (Fake Repository) and taste it.

### 4.3 Composition Root — Wiring Only in main

The **Composition Root** is where all pieces are connected. This happens only in `main.go`.

```go
// cmd/api/main.go
package main

import (
    "database/sql"
    "log"
    "net/http"

    _ "github.com/go-sql-driver/mysql"

    apphttp "myapp/internal/adapters/inbound/http"
    "myapp/internal/adapters/outbound/mysql"
    "myapp/internal/application"
)

func main() {
    db, err := sql.Open("mysql", "user:pass@tcp(localhost:3306)/orders")
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()

    // 1. Build Driven Adapter (outbound)
    orderRepo := mysql.NewOrderRepository(db)

    // 2. Build Application Service (Use Case) and inject the adapter
    orderService := application.NewOrderService(orderRepo, nil)

    // 3. Build Driving Adapter (inbound) and inject the service
    orderHandler := apphttp.NewOrderHandler(orderService)

    // 4. Define routes
    mux := http.NewServeMux()
    mux.HandleFunc("POST /orders", orderHandler.CreateOrder)
    mux.HandleFunc("GET /orders/{id}", orderHandler.GetOrder)
    mux.HandleFunc("DELETE /orders/{id}", orderHandler.CancelOrder)

    log.Println("Server starting on :8080")
    log.Fatal(http.ListenAndServe(":8080", mux))
}
```

**Important:** Only `main.go` **imports all packages**. The Core knows nothing about MySQL.

```
main.go knows: mysql, http, application, domain  (all for wiring)
application knows: domain                        (only interfaces)
mysql adapter knows: domain                      (only interfaces)
http adapter knows: domain                       (only interfaces)
domain knows: nobody!                            (independent and pure)
```

**Real-world analogy:** The Composition Root is like the **restaurant manager**. The manager decides: “This chef works with this supplier and this waiter.” The chef doesn't know who the supplier is. The waiter doesn't know where the ingredients came from. Only the manager (main.go) knows everyone and wires them together.

---

## 5. Step-by-Step Implementation: Order Management System

### Use Cases:
- **CreateOrder**: Create a new order
- **CancelOrder**: Cancel an order
- **GetOrder**: View an order

### Step 1: Design the Domain

Start with entities, interfaces, and errors:

```go
// internal/domain/order.go
package domain

import (
    "errors"
    "time"
)

var (
    ErrEmptyCustomerID   = errors.New("customer ID cannot be empty")
    ErrNoItems           = errors.New("order must have at least one item")
    ErrInvalidQuantity   = errors.New("quantity must be positive")
    ErrInvalidPrice      = errors.New("price must be positive")
    ErrOrderNotFound     = errors.New("order not found")
    ErrCannotCancel      = errors.New("cannot cancel this order")
    ErrCannotConfirm     = errors.New("can only confirm pending orders")
)

type OrderStatus string

const (
    OrderStatusPending   OrderStatus = "pending"
    OrderStatusConfirmed OrderStatus = "confirmed"
    OrderStatusCancelled OrderStatus = "cancelled"
    OrderStatusDelivered OrderStatus = "delivered"
)

type Order struct {
    ID         int64
    CustomerID string
    Items      []OrderItem
    Status     OrderStatus
    CreatedAt  time.Time
}

type OrderItem struct {
    ProductID int64
    Name      string
    Price     float64
    Quantity  int
}

func NewOrder(customerID string, items []OrderItem) (*Order, error) {
    if customerID == "" {
        return nil, ErrEmptyCustomerID
    }
    if len(items) == 0 {
        return nil, ErrNoItems
    }
    for _, item := range items {
        if item.Quantity <= 0 {
            return nil, ErrInvalidQuantity
        }
        if item.Price <= 0 {
            return nil, ErrInvalidPrice
        }
    }
    return &Order{
        CustomerID: customerID,
        Items:      items,
        Status:     OrderStatusPending,
        CreatedAt:  time.Now(),
    }, nil
}

func (o *Order) TotalPrice() float64 {
    var total float64
    for _, item := range o.Items {
        total += item.Price * float64(item.Quantity)
    }
    return total
}

func (o *Order) Cancel() error {
    if o.Status == OrderStatusDelivered {
        return ErrCannotCancel
    }
    if o.Status == OrderStatusCancelled {
        return ErrCannotCancel
    }
    o.Status = OrderStatusCancelled
    return nil
}

func (o *Order) Confirm() error {
    if o.Status != OrderStatusPending {
        return ErrCannotConfirm
    }
    o.Status = OrderStatusConfirmed
    return nil
}
```

### Step 2: Repository Interface (Outbound Port)

```go
// internal/domain/repository.go
package domain

import "context"

type OrderRepository interface {
    Save(ctx context.Context, order *Order) error
    FindByID(ctx context.Context, id int64) (*Order, error)
    FindByCustomerID(ctx context.Context, customerID string) ([]*Order, error)
    Update(ctx context.Context, order *Order) error
}
```

### Step 3: Application Service (Use Case)

```go
// internal/application/order_service.go
package application

import (
    "context"

    "myapp/internal/domain"
)

type OrderService struct {
    repo     domain.OrderRepository
    notifier domain.NotificationSender
}

func NewOrderService(repo domain.OrderRepository, notifier domain.NotificationSender) *OrderService {
    return &OrderService{
        repo:     repo,
        notifier: notifier,
    }
}

func (s *OrderService) CreateOrder(ctx context.Context, customerID string, items []domain.OrderItem) (*domain.Order, error) {
    order, err := domain.NewOrder(customerID, items)
    if err != nil {
        return nil, err
    }

    if err := s.repo.Save(ctx, order); err != nil {
        return nil, err
    }

    if s.notifier != nil {
        _ = s.notifier.SendOrderConfirmation(ctx, order)
    }

    return order, nil
}

func (s *OrderService) CancelOrder(ctx context.Context, orderID int64) error {
    order, err := s.repo.FindByID(ctx, orderID)
    if err != nil {
        return err
    }

    if err := order.Cancel(); err != nil {
        return err
    }

    return s.repo.Update(ctx, order)
}

func (s *OrderService) GetOrder(ctx context.Context, orderID int64) (*domain.Order, error) {
    return s.repo.FindByID(ctx, orderID)
}
```

Notice: `OrderService`:
- Only knows `domain.OrderRepository` (interface)
- Doesn't know if it's MySQL, PostgreSQL, or In-Memory
- **Orchestrates**: builds business entity, delegates validation to the entity, delegates persistence to the repository

### Step 4: MySQL Adapter (Driven)

```go
// internal/adapters/outbound/mysql/order_repository.go
package mysql

import (
    "context"
    "database/sql"

    "myapp/internal/domain"
)

type OrderRepository struct {
    db *sql.DB
}

func NewOrderRepository(db *sql.DB) *OrderRepository {
    return &OrderRepository{db: db}
}

func (r *OrderRepository) Save(ctx context.Context, order *domain.Order) error {
    tx, err := r.db.BeginTx(ctx, nil)
    if err != nil {
        return err
    }
    defer tx.Rollback()

    result, err := tx.ExecContext(ctx,
        "INSERT INTO orders (customer_id, status, created_at) VALUES (?, ?, ?)",
        order.CustomerID, string(order.Status), order.CreatedAt,
    )
    if err != nil {
        return err
    }

    id, err := result.LastInsertId()
    if err != nil {
        return err
    }
    order.ID = id

    for _, item := range order.Items {
        _, err := tx.ExecContext(ctx,
            `INSERT INTO order_items (order_id, product_id, name, price, quantity)
             VALUES (?, ?, ?, ?, ?)`,
            order.ID, item.ProductID, item.Name, item.Price, item.Quantity,
        )
        if err != nil {
            return err
        }
    }

    return tx.Commit()
}

func (r *OrderRepository) FindByID(ctx context.Context, id int64) (*domain.Order, error) {
    row := r.db.QueryRowContext(ctx,
        "SELECT id, customer_id, status, created_at FROM orders WHERE id = ?", id,
    )

    var order domain.Order
    var status string
    if err := row.Scan(&order.ID, &order.CustomerID, &status, &order.CreatedAt); err != nil {
        if err == sql.ErrNoRows {
            return nil, domain.ErrOrderNotFound
        }
        return nil, err
    }
    order.Status = domain.OrderStatus(status)

    rows, err := r.db.QueryContext(ctx,
        "SELECT product_id, name, price, quantity FROM order_items WHERE order_id = ?", id,
    )
    if err != nil {
        return nil, err
    }
    defer rows.Close()

    for rows.Next() {
        var item domain.OrderItem
        if err := rows.Scan(&item.ProductID, &item.Name, &item.Price, &item.Quantity); err != nil {
            return nil, err
        }
        order.Items = append(order.Items, item)
    }

    return &order, nil
}

func (r *OrderRepository) FindByCustomerID(ctx context.Context, customerID string) ([]*domain.Order, error) {
    rows, err := r.db.QueryContext(ctx,
        "SELECT id, customer_id, status, created_at FROM orders WHERE customer_id = ?",
        customerID,
    )
    if err != nil {
        return nil, err
    }
    defer rows.Close()

    var orders []*domain.Order
    for rows.Next() {
        var order domain.Order
        var status string
        if err := rows.Scan(&order.ID, &order.CustomerID, &status, &order.CreatedAt); err != nil {
            return nil, err
        }
        order.Status = domain.OrderStatus(status)
        orders = append(orders, &order)
    }
    return orders, nil
}

func (r *OrderRepository) Update(ctx context.Context, order *domain.Order) error {
    _, err := r.db.ExecContext(ctx,
        "UPDATE orders SET status = ? WHERE id = ?",
        string(order.Status), order.ID,
    )
    return err
}
```

### Step 5: In-Memory Adapter (for Tests)

```go
// internal/adapters/outbound/memory/order_repository.go
package memory

import (
    "context"
    "sync"

    "myapp/internal/domain"
)

type OrderRepository struct {
    mu     sync.RWMutex
    orders map[int64]*domain.Order
    nextID int64
}

func NewOrderRepository() *OrderRepository {
    return &OrderRepository{
        orders: make(map[int64]*domain.Order),
        nextID: 1,
    }
}

func (r *OrderRepository) Save(ctx context.Context, order *domain.Order) error {
    r.mu.Lock()
    defer r.mu.Unlock()
    order.ID = r.nextID
    r.nextID++
    stored := *order
    stored.Items = make([]domain.OrderItem, len(order.Items))
    copy(stored.Items, order.Items)
    r.orders[order.ID] = &stored
    return nil
}

func (r *OrderRepository) FindByID(ctx context.Context, id int64) (*domain.Order, error) {
    r.mu.RLock()
    defer r.mu.RUnlock()
    order, ok := r.orders[id]
    if !ok {
        return nil, domain.ErrOrderNotFound
    }
    return order, nil
}

func (r *OrderRepository) FindByCustomerID(ctx context.Context, customerID string) ([]*domain.Order, error) {
    r.mu.RLock()
    defer r.mu.RUnlock()
    var result []*domain.Order
    for _, order := range r.orders {
        if order.CustomerID == customerID {
            result = append(result, order)
        }
    }
    return result, nil
}

func (r *OrderRepository) Update(ctx context.Context, order *domain.Order) error {
    r.mu.Lock()
    defer r.mu.Unlock()
    if _, ok := r.orders[order.ID]; !ok {
        return domain.ErrOrderNotFound
    }
    r.orders[order.ID] = order
    return nil
}
```

### Step 6: HTTP Handler (Driving Adapter)

```go
// internal/adapters/inbound/http/order_handler.go
package http

import (
    "encoding/json"
    "errors"
    "net/http"
    "strconv"

    "myapp/internal/domain"
    "myapp/internal/application"
)

type OrderHandler struct {
    service *application.OrderService
}

func NewOrderHandler(service *application.OrderService) *OrderHandler {
    return &OrderHandler{service: service}
}

type createOrderRequest struct {
    CustomerID string             `json:"customer_id"`
    Items      []orderItemRequest `json:"items"`
}

type orderItemRequest struct {
    ProductID int64   `json:"product_id"`
    Name      string  `json:"name"`
    Price     float64 `json:"price"`
    Quantity  int     `json:"quantity"`
}

type orderResponse struct {
    ID         int64               `json:"id"`
    CustomerID string              `json:"customer_id"`
    Items      []orderItemResponse `json:"items"`
    Total      float64             `json:"total"`
    Status     string              `json:"status"`
    CreatedAt  string              `json:"created_at"`
}

type orderItemResponse struct {
    ProductID int64   `json:"product_id"`
    Name      string  `json:"name"`
    Price     float64 `json:"price"`
    Quantity  int     `json:"quantity"`
}

func toOrderResponse(order *domain.Order) orderResponse {
    items := make([]orderItemResponse, len(order.Items))
    for i, item := range order.Items {
        items[i] = orderItemResponse{
            ProductID: item.ProductID,
            Name:      item.Name,
            Price:     item.Price,
            Quantity:  item.Quantity,
        }
    }
    return orderResponse{
        ID:         order.ID,
        CustomerID: order.CustomerID,
        Items:      items,
        Total:      order.TotalPrice(),
        Status:     string(order.Status),
        CreatedAt:  order.CreatedAt.Format("2006-01-02 15:04:05"),
    }
}

func (h *OrderHandler) CreateOrder(w http.ResponseWriter, r *http.Request) {
    var req createOrderRequest
    if err := json.NewDecoder(r.Body).Decode(&req); err != nil {
        writeError(w, "invalid request body", http.StatusBadRequest)
        return
    }

    items := make([]domain.OrderItem, len(req.Items))
    for i, item := range req.Items {
        items[i] = domain.OrderItem{
            ProductID: item.ProductID,
            Name:      item.Name,
            Price:     item.Price,
            Quantity:  item.Quantity,
        }
    }

    order, err := h.service.CreateOrder(r.Context(), req.CustomerID, items)
    if err != nil {
        status := http.StatusInternalServerError
        if errors.Is(err, domain.ErrEmptyCustomerID) ||
            errors.Is(err, domain.ErrNoItems) ||
            errors.Is(err, domain.ErrInvalidQuantity) ||
            errors.Is(err, domain.ErrInvalidPrice) {
            status = http.StatusBadRequest
        }
        writeError(w, err.Error(), status)
        return
    }

    writeJSON(w, toOrderResponse(order), http.StatusCreated)
}

func (h *OrderHandler) GetOrder(w http.ResponseWriter, r *http.Request) {
    id, err := strconv.ParseInt(r.PathValue("id"), 10, 64)
    if err != nil {
        writeError(w, "invalid order ID", http.StatusBadRequest)
        return
    }

    order, err := h.service.GetOrder(r.Context(), id)
    if err != nil {
        if errors.Is(err, domain.ErrOrderNotFound) {
            writeError(w, "order not found", http.StatusNotFound)
            return
        }
        writeError(w, "internal server error", http.StatusInternalServerError)
        return
    }

    writeJSON(w, toOrderResponse(order), http.StatusOK)
}

func (h *OrderHandler) CancelOrder(w http.ResponseWriter, r *http.Request) {
    id, err := strconv.ParseInt(r.PathValue("id"), 10, 64)
    if err != nil {
        writeError(w, "invalid order ID", http.StatusBadRequest)
        return
    }

    if err := h.service.CancelOrder(r.Context(), id); err != nil {
        if errors.Is(err, domain.ErrOrderNotFound) {
            writeError(w, "order not found", http.StatusNotFound)
            return
        }
        if errors.Is(err, domain.ErrCannotCancel) {
            writeError(w, err.Error(), http.StatusConflict)
            return
        }
        writeError(w, "internal server error", http.StatusInternalServerError)
        return
    }

    w.WriteHeader(http.StatusNoContent)
}

func writeJSON(w http.ResponseWriter, data any, status int) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(status)
    json.NewEncoder(w).Encode(data)
}

func writeError(w http.ResponseWriter, message string, status int) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(status)
    json.NewEncoder(w).Encode(map[string]string{"error": message})
}
```

### Step 7: Wiring in main.go

```go
// cmd/api/main.go
package main

import (
    "database/sql"
    "log"
    "net/http"

    _ "github.com/go-sql-driver/mysql"

    apphttp "myapp/internal/adapters/inbound/http"
    "myapp/internal/adapters/outbound/mysql"
    "myapp/internal/application"
)

func main() {
    // --- Driven Adapters ---
    db, err := sql.Open("mysql", "user:pass@tcp(localhost:3306)/orders")
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()

    orderRepo := mysql.NewOrderRepository(db)

    // --- Application Services ---
    orderService := application.NewOrderService(orderRepo, nil)

    // --- Driving Adapters ---
    orderHandler := apphttp.NewOrderHandler(orderService)

    // --- Routes ---
    mux := http.NewServeMux()
    mux.HandleFunc("POST /orders", orderHandler.CreateOrder)
    mux.HandleFunc("GET /orders/{id}", orderHandler.GetOrder)
    mux.HandleFunc("DELETE /orders/{id}", orderHandler.CancelOrder)

    log.Println("Server starting on :8080")
    log.Fatal(http.ListenAndServe(":8080", mux))
}
```

### Step 8: Tests Without a Database

```go
// internal/application/order_service_test.go
package application_test

import (
    "context"
    "testing"

    "myapp/internal/application"
    "myapp/internal/adapters/outbound/memory"
    "myapp/internal/domain"
)

func setupService() *application.OrderService {
    repo := memory.NewOrderRepository()
    return application.NewOrderService(repo, nil)
}

func TestCreateOrder_Success(t *testing.T) {
    service := setupService()

    items := []domain.OrderItem{
        {ProductID: 1, Name: "Laptop", Price: 25000000, Quantity: 1},
        {ProductID: 2, Name: "Mouse", Price: 500000, Quantity: 2},
    }

    order, err := service.CreateOrder(context.Background(), "customer-1", items)
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }

    if order.ID == 0 {
        t.Error("expected order ID to be assigned")
    }

    expectedTotal := 26000000.0
    if order.TotalPrice() != expectedTotal {
        t.Errorf("expected total %f, got %f", expectedTotal, order.TotalPrice())
    }
}

func TestCreateOrder_EmptyCustomerID_ReturnsError(t *testing.T) {
    service := setupService()

    items := []domain.OrderItem{
        {ProductID: 1, Name: "Book", Price: 50000, Quantity: 1},
    }

    _, err := service.CreateOrder(context.Background(), "", items)
    if err == nil {
        t.Fatal("expected error for empty customer ID")
    }
}

func TestCancelOrder_Success(t *testing.T) {
    service := setupService()

    items := []domain.OrderItem{
        {ProductID: 1, Name: "Book", Price: 50000, Quantity: 1},
    }

    order, _ := service.CreateOrder(context.Background(), "cust-1", items)

    err := service.CancelOrder(context.Background(), order.ID)
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }

    cancelled, _ := service.GetOrder(context.Background(), order.ID)
    if cancelled.Status != domain.OrderStatusCancelled {
        t.Errorf("expected cancelled, got %s", cancelled.Status)
    }
}

func TestCancelOrder_NotFound(t *testing.T) {
    service := setupService()

    err := service.CancelOrder(context.Background(), 999)
    if err == nil {
        t.Fatal("expected error for non-existent order")
    }
}

func TestGetOrder_NotFound(t *testing.T) {
    service := setupService()

    _, err := service.GetOrder(context.Background(), 999)
    if err == nil {
        t.Fatal("expected error for non-existent order")
    }
}
```

### The Power of the Architecture: Adding a New Adapter Without Touching the Domain

Suppose tomorrow they say: “Use PostgreSQL instead of MySQL.” You only add a new adapter:

```go
// internal/adapters/outbound/postgres/order_repository.go
package postgres

import (
    "context"
    "database/sql"

    "myapp/internal/domain"
)

type OrderRepository struct {
    db *sql.DB
}

func NewOrderRepository(db *sql.DB) *OrderRepository {
    return &OrderRepository{db: db}
}

func (r *OrderRepository) Save(ctx context.Context, order *domain.Order) error {
    err := r.db.QueryRowContext(ctx,
        `INSERT INTO orders (customer_id, status, created_at)
         VALUES ($1, $2, $3) RETURNING id`,
        order.CustomerID, string(order.Status), order.CreatedAt,
    ).Scan(&order.ID)
    if err != nil {
        return err
    }

    for _, item := range order.Items {
        _, err := r.db.ExecContext(ctx,
            `INSERT INTO order_items (order_id, product_id, name, price, quantity)
             VALUES ($1, $2, $3, $4, $5)`,
            order.ID, item.ProductID, item.Name, item.Price, item.Quantity,
        )
        if err != nil {
            return err
        }
    }
    return nil
}

func (r *OrderRepository) FindByID(ctx context.Context, id int64) (*domain.Order, error) {
    row := r.db.QueryRowContext(ctx,
        "SELECT id, customer_id, status, created_at FROM orders WHERE id = $1", id,
    )
    var order domain.Order
    var status string
    if err := row.Scan(&order.ID, &order.CustomerID, &status, &order.CreatedAt); err != nil {
        if err == sql.ErrNoRows {
            return nil, domain.ErrOrderNotFound
        }
        return nil, err
    }
    order.Status = domain.OrderStatus(status)
    return &order, nil
}

func (r *OrderRepository) FindByCustomerID(ctx context.Context, customerID string) ([]*domain.Order, error) {
    rows, err := r.db.QueryContext(ctx,
        "SELECT id, customer_id, status, created_at FROM orders WHERE customer_id = $1",
        customerID,
    )
    if err != nil {
        return nil, err
    }
    defer rows.Close()

    var orders []*domain.Order
    for rows.Next() {
        var o domain.Order
        var status string
        if err := rows.Scan(&o.ID, &o.CustomerID, &status, &o.CreatedAt); err != nil {
            return nil, err
        }
        o.Status = domain.OrderStatus(status)
        orders = append(orders, &o)
    }
    return orders, nil
}

func (r *OrderRepository) Update(ctx context.Context, order *domain.Order) error {
    _, err := r.db.ExecContext(ctx,
        "UPDATE orders SET status = $1 WHERE id = $2",
        string(order.Status), order.ID,
    )
    return err
}
```

And in `main.go` you only change the wiring:

```go
// Before (MySQL):
orderRepo := mysql.NewOrderRepository(db)

// After (PostgreSQL):
orderRepo := postgres.NewOrderRepository(db)

// application and domain didn't change at all!
```

**That's it!** No change to a single line in domain or application.

---

## 6. Common Mistakes

### Mistake 1: Defining the Interface in the Adapter

```go
// ❌ Wrong: interface defined in mysql package
package mysql

type OrderRepository interface {
    Save(order *Order) error
}

type mysqlOrderRepo struct { db *sql.DB }
```

```go
// ✅ Right: interface defined in port
package port

type OrderRepository interface {
    Save(ctx context.Context, order *Order) error
}
```

**Why wrong?** If the interface lives in the adapter, the core (domain/port) would have to import the adapter — reversing the dependency direction!

**Real-world analogy:** Imagine posting a job ad for “food delivery driver.” The ad should be written by the **restaurant** (port/domain), not by **Uber Eats** (adapter). If Uber Eats defines the requirements, the restaurant depends on Uber Eats.

### Mistake 2: Importing an ORM in the Domain

```go
// ❌ Wrong: GORM in domain!
package domain

import "gorm.io/gorm"

type Order struct {
    gorm.Model
    CustomerID string
    Total      float64
}
```

```go
// ✅ Right: pure domain
package domain

type Order struct {
    ID         int64
    CustomerID string
    Total      float64
}

// If you need GORM, create a separate model in the adapter:
// internal/adapters/outbound/mysql/models.go
package mysql

import "gorm.io/gorm"

type orderModel struct {
    gorm.Model
    CustomerID string
    Total      float64
}
```

**Why wrong?** If you later replace GORM with `sqlx`, the entire domain would have to change.

### Mistake 3: Merging Database Entity with Domain Entity

```go
// ❌ Wrong: domain entity with database tags
package domain

type Order struct {
    ID         int64   `gorm:"primaryKey" json:"id" db:"id"`
    CustomerID string  `gorm:"column:customer_id" json:"customer_id" db:"customer_id"`
    Total      float64 `gorm:"column:total" json:"total" db:"total"`
}
```

```go
// ✅ Right: pure domain entity — no technology-specific tags
package domain

type Order struct {
    ID         int64
    CustomerID string
    Total      float64
}

// In adapters, use separate models:
// mysql adapter:
type orderDBModel struct {
    ID         int64   `db:"id"`
    CustomerID string  `db:"customer_id"`
    Total      float64 `db:"total"`
}

// http adapter:
type orderHTTPResponse struct {
    ID         int64   `json:"id"`
    CustomerID string  `json:"customer_id"`
    Total      float64 `json:"total"`
}
```

**Real-world analogy:** A building blueprint (Domain Entity) shouldn't include brick type (MySQL tag) or wall paint color (JSON tag). The blueprint only shows **structure**. Execution details belong to the builder (adapter).

### Mistake 4: Putting Business Validation in the Handler

```go
// ❌ Wrong: business rule in HTTP handler
func (h *Handler) CreateOrder(w http.ResponseWriter, r *http.Request) {
    var req CreateOrderRequest
    json.NewDecoder(r.Body).Decode(&req)

    // This is a business rule! It shouldn't be here!
    if req.Total > 10000000 {
        http.Error(w, "order too large, needs manager approval", 400)
        return
    }

    // ...
}
```

```go
// ✅ Right: business rule in domain
package domain

func NewOrder(customerID string, items []OrderItem) (*Order, error) {
    total := calculateTotal(items)
    if total > 10000000 {
        return nil, ErrOrderNeedsApproval
    }
    // ...
}
```

**Why wrong?** If orders are also created from a CLI, you'd have to repeat the same rule. Business rules are written **once** in the domain and used by all adapters.

### Mistake 5: Using Framework Types in the Port

```go
// ❌ Wrong: gin.Context in the port!
type OrderService interface {
    CreateOrder(c *gin.Context) error
}

// ✅ Right: standard Go types
type OrderService interface {
    CreateOrder(ctx context.Context, customerID string, items []OrderItem) (*Order, error)
}
```

**Why wrong?** If Gin is removed, all ports and domain would have to change. Ports must be **technology-agnostic**.

---

## 7. What's the Best Go Project Structure?

### The Professional Answer

**Go is not opinionated about architecture.** Go only emphasizes:
- Simplicity
- Explicit dependencies
- Small packages
- No circular dependencies

**So structure doesn't matter?** Structure matters, but **dependency direction matters more.**

The structure should **reflect** the dependency direction, not the other way around.

### Comparing Structures

**Bad structure (unclear dependencies):**

```
project/
├── controllers/
├── models/
├── services/
└── utils/
```

Problem: `models` is both database entity and domain entity. `services` is everything. Dependency direction is unclear.

**Good structure (clear dependencies):**

```
project/
├── cmd/main.go              # Composition Root
├── internal/
│   ├── domain/              # Center — depends on nothing
│   ├── application/         # Use Case — depends only on domain
│   └── adapters/            # Outside — depends on domain
└── go.mod
```

Looking at the structure, you immediately see: **domain depends on nothing. Everything depends on domain.**

### Idiomatic Go

```go
// Good: explicit dependency via constructor
func NewOrderService(repo OrderRepository) *OrderService {
    return &OrderService{repo: repo}
}

// Bad: hidden dependency via global
var DB *sql.DB // global!
func CreateOrder(order Order) error {
    DB.Exec(...)
}
```

---

## 8. Comparison with Other Architectures

### Hexagonal vs Traditional Layered (3-Tier)

```
Layered:                          Hexagonal:
┌─────────────────┐               ┌───────────────────┐
│  Presentation   │──depends──►   │  Adapter (HTTP)   │──depends──►┐
├─────────────────┤               └───────────────────┘            │
│    Business     │──depends──►            ┌────────┐              │
├─────────────────┤               ┌───────►│ Domain │◄─────────────┘
│   Data Access   │               │        └────────┘
└─────────────────┘               │  Adapter (MySQL) │──depends──►┘
                                  └───────────────────┘
```

| Aspect | Layered | Hexagonal |
|--------|---------|-----------|
| Dependency direction | Top to bottom | Outside to center |
| Does Core know the database? | Often yes | No (only interface) |
| Adding a new UI | Business may change | Only new adapter |
| Testability | Full stack often needed | Mock ports |

### Hexagonal vs Clean Architecture

Both put the domain at the center. Clean Architecture has **more layers** (Entities, Use Cases, Interface Adapters, Frameworks). Hexagonal is simpler: **domain + ports + adapters**.

| Hexagonal | Clean Architecture |
|-----------|--------------------|
| Ports + Adapters | Use Cases + Entities + Interface Adapters + Frameworks |
| Simpler | More layers |
| “Hexagon” metaphor | “Onion” metaphor |
| Common in Go | Common in Java/C# |

### Hexagonal vs MVC

MVC only separates Model, View, and Controller. It doesn't say **where** business logic lives (it often ends up in Model or Controller). Hexagonal explicitly says: **business logic in the center**, and View/Controller become **driving adapters**.

---

## 9. Advanced Topics

### Domain Events

```go
type OrderCreatedEvent struct {
    OrderID    int64
    CustomerID string
    Total      float64
    OccurredAt time.Time
}

type EventPublisher interface {
    Publish(ctx context.Context, event any) error
}
```

When an order is created, publish an event. Subscribers (e.g. send email, update inventory) react.

### CQRS (Command Query Responsibility Segregation)

Separate ports for read and write:

```go
type OrderCommandRepo interface {
    Save(ctx context.Context, order *Order) error
    Update(ctx context.Context, order *Order) error
}

type OrderQueryRepo interface {
    FindByID(ctx context.Context, id int64) (*Order, error)
    FindByCustomerID(ctx context.Context, customerID string) ([]*Order, error)
    Search(ctx context.Context, filter OrderFilter) ([]*Order, error)
}
```

Benefit: you can use PostgreSQL for writes and Elasticsearch for reads.

### Transaction Boundary and Unit of Work

```go
type UnitOfWork interface {
    Begin(ctx context.Context) (context.Context, error)
    Commit(ctx context.Context) error
    Rollback(ctx context.Context) error
}
```

### Outbox Pattern

To ensure the event and the save either both happen or neither:

```go
func (s *OrderService) CreateOrder(ctx context.Context, ...) (*Order, error) {
    // In one transaction:
    // 1. Save the order
    // 2. Save the event in an outbox table
    // A separate worker reads from the outbox and publishes events
}
```

---

### Stage 7 (Optional): New Driving Adapter

Add a CLI adapter that calls the same service from the command line:

```go
// internal/adapters/inbound/cli/order_command.go
package cli

import (
    "context"
    "fmt"

    "myapp/internal/application"
    "myapp/internal/domain"
)

type OrderCommand struct {
    service *application.OrderService
}

func NewOrderCommand(service *application.OrderService) *OrderCommand {
    return &OrderCommand{service: service}
}

func (c *OrderCommand) CreateOrder(customerID string, items []domain.OrderItem) {
    order, err := c.service.CreateOrder(context.Background(), customerID, items)
    if err != nil {
        fmt.Printf("Error: %v\n", err)
        return
    }
    fmt.Printf("Order created: ID=%d, Total=%.0f, Status=%s\n",
        order.ID, order.TotalPrice(), order.Status)
}
```

**Same service, same domain, just one new adapter!**

---

## Summary

| Concept | Meaning | Real-World Analogy |
|---------|---------|---------------------|
| **Domain** | Pure business logic | Football rules (independent of the pitch) |
| **Port** | Interface (contract) | Restaurant menu (what's on offer) |
| **Driving Adapter** | Inbound (HTTP, CLI, gRPC) | Waiter, food-ordering app |
| **Driven Adapter** | Outbound (MySQL, Redis, SMTP) | Storekeeper, delivery driver |
| **Composition Root** | Wiring everything in main | Restaurant manager |
| **DIP** | Depend on interface, not concrete | Power outlet (any device can plug in) |

### The Golden Rule

```
The Domain depends on nothing.
Everything depends on the Domain.
Wiring happens only in main.go.
```
