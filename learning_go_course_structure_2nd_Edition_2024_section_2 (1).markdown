# 🎯 Go Advanced Topics: Pointers, Memory, and Performance

![Advanced Go](https://images.unsplash.com/photo-1451187580459-43490279c0fa?auto=format&fit=crop&w=600&q=80)

### 📚 Table of Contents
1. **Section 6: Pointers - Quick Primer**
2. **Section 7: Mutable Parameters**
3. **Section 8: Pointers as a Last Resort**
4. **Section 9: Performance Considerations**
5. **Section 10: Zero vs No Value**
6. **Section 11: Difference Between Maps and Slices**
7. **Section 12: Slices as Buffers**
8. **Section 13: GC Optimization**
9. **Section 14: Tuning the GC**
10. **Exercises and Challenges**
11. **Wrapping Up**

---

## 📖 Section 6: Pointers - Quick Primer

### 6.1 Understanding Pointers 👉

**What is a Pointer?**
- A variable that **stores the memory address** of another variable
- Allows indirect access to the value
- Essential for modifying function parameters
- Enables sharing data without copying

**Memory Visualization:**
```
Regular Variable:
┌─────────┬─────────┐
│  name   │  value  │
│    x    │   42    │
└─────────┴─────────┘

Pointer Variable:
┌─────────┬─────────────┐
│  name   │   address   │
│   ptr   │ 0x1234abcd  │
└─────────┴─────────────┘
            │
            ▼
         ┌─────────┐
         │   42    │  (actual value at that address)
         └─────────┘
```

### 6.2 Pointer Syntax 📝

**Creating and Using Pointers:**
```go
package main

import "fmt"

func main() {
    // 1. Regular variable
    x := 42
    
    // 2. Create pointer to x using & (address operator)
    ptr := &x
    
    // 3. Print the address
    fmt.Printf("Address of x: %p\n", ptr)
    
    // 4. Access value through pointer using * (dereference operator)
    fmt.Printf("Value at address: %d\n", *ptr)
    
    // 5. Modify value through pointer
    *ptr = 100
    
    // 6. Original variable is changed!
    fmt.Printf("x is now: %d\n", x)  // 100
}
```

**Key Operators:**
| Operator | Name | Purpose | Example |
|----------|------|---------|---------|
| `&` | Address-of | Get memory address | `ptr := &x` |
| `*` | Dereference | Access value at address | `value := *ptr` |
| `*Type` | Pointer type | Declare pointer variable | `var ptr *int` |

**Example with Different Types:**
```go
package main

import "fmt"

func main() {
    // Integer pointer
    var age int = 25
    var agePtr *int = &age
    fmt.Printf("Age: %d, Address: %p\n", *agePtr, agePtr)
    
    // String pointer
    name := "Alice"
    namePtr := &name
    *namePtr = "Bob"  // Change via pointer
    fmt.Println(name)  // "Bob"
    
    // Struct pointer
    type Person struct {
        Name string
        Age  int
    }
    
    person := Person{"Charlie", 30}
    personPtr := &person
    personPtr.Age = 31  // Shorthand for (*personPtr).Age = 31
    fmt.Printf("%+v\n", person)  // {Name:Charlie Age:31}
}
```

### 6.3 Pointer Zero Value 🧊

**Zero Value of Pointer:**
```go
var ptr *int  // nil (doesn't point anywhere)

fmt.Println(ptr == nil)  // true

// ❌ Dereferencing nil pointer causes panic!
// fmt.Println(*ptr)  // Runtime panic!

// ✅ Always check before dereferencing
if ptr != nil {
    fmt.Println(*ptr)
}
```

**Safe Pointer Usage:**
```go
func processOrder(order *Order) error {
    // ✅ Check for nil first
    if order == nil {
        return fmt.Errorf("order is nil")
    }
    
    // Now safe to use
    fmt.Printf("Processing order #%d\n", order.ID)
    return nil
}
```

### 6.4 When to Use Pointers 🤔

**Use Pointers When:**
1. ✅ Need to modify function parameters
2. ✅ Working with large structs (avoid copying)
3. ✅ Implementing data structures (linked lists, trees)
4. ✅ Need to represent "no value" vs "zero value"

**Example 1: Modify Function Parameter**
```go
// ❌ Without pointer (doesn't modify original)
func incrementBad(x int) {
    x++  // Only modifies the copy
}

// ✅ With pointer (modifies original)
func incrementGood(x *int) {
    *x++  // Modifies the original value
}

func main() {
    value := 10
    
    incrementBad(value)
    fmt.Println(value)  // 10 (unchanged)
    
    incrementGood(&value)
    fmt.Println(value)  // 11 (changed!)
}
```

**Example 2: Large Struct (Avoid Copying)**
```go
type LargeOrder struct {
    ID       int
    Items    [1000]Product
    Customer Customer
    // ... many more fields
}

// ❌ Bad: Copies entire struct (expensive!)
func processOrderBad(order LargeOrder) {
    // Processing...
}

// ✅ Good: Passes pointer (just 8 bytes on 64-bit)
func processOrderGood(order *LargeOrder) {
    // Processing...
}

```

---

## 📖 Section 7: Mutable Parameters

### 7.1 Understanding Mutability 🔄

**Go is "Pass by Value":**
- All arguments are **copied** when passed to functions
- Changes to the copy don't affect the original
- **Exception:** Pointers, slices, maps, channels (reference types)

**Demonstration:**
```go
package main

import "fmt"

// Pass by value (immutable)
func modifyInt(x int) {
    x = 999
    fmt.Println("Inside function:", x)  // 999
}

// Pass by pointer (mutable)
func modifyIntPtr(x *int) {
    *x = 999
    fmt.Println("Inside function:", *x)  // 999
}

func main() {
    value := 42
    
    modifyInt(value)
    fmt.Println("After modifyInt:", value)     // 42 (unchanged)
    
    modifyIntPtr(&value)
    fmt.Println("After modifyIntPtr:", value)  // 999 (changed!)
}
```

### 7.2 Mutable Structs 🏗️

**E-Commerce Example: Update Order Status**
```go
package main

import "fmt"

type Order struct {
    ID     int
    Status string
    Total  float64
}

// ❌ Doesn't modify original (pass by value)
func updateStatusBad(order Order, newStatus string) {
    order.Status = newStatus  // Only modifies the copy
}

// ✅ Modifies original (pass by pointer)
func updateStatusGood(order *Order, newStatus string) {
    order.Status = newStatus  // Modifies the original
}

// ✅ Alternative: Return new value
func updateStatusReturn(order Order, newStatus string) Order {
    order.Status = newStatus
    return order  // Caller must capture return value
}

func main() {
    order := Order{ID: 1001, Status: "pending", Total: 99.99}
    
    // Method 1: Pass by value (doesn't work)
    updateStatusBad(order, "shipped")
    fmt.Println(order.Status)  // "pending" (unchanged)
    
    // Method 2: Pass by pointer (works!)
    updateStatusGood(&order, "shipped")
    fmt.Println(order.Status)  // "shipped" (changed!)
    
    // Method 3: Return new value (works, but creates copy)
    order = updateStatusReturn(order, "delivered")
    fmt.Println(order.Status)  // "delivered"
}
```

### 7.3 Slices and Maps (Already Mutable) 🗺️

**Important: Slices and Maps are reference types!**
```go
package main

import "fmt"

// Slices are already reference types
func addItem(cart []string, item string) {
    // This modifies the underlying array!
    cart = append(cart, item)
    // ⚠️ But if slice grows beyond capacity, it creates new array
    // and caller won't see the change!
}

// ✅ Better: Return the slice
func addItemGood(cart []string, item string) []string {
    return append(cart, item)
}

// Maps are reference types
func updateInventory(inventory map[string]int, product string, delta int) {
    // This DOES modify the original map
    inventory[product] += delta
}

func main() {
    // Slice example
    cart := []string{"Laptop", "Mouse"}
    cart = addItemGood(cart, "Keyboard")
    fmt.Println(cart)  // [Laptop Mouse Keyboard]
    
    // Map example
    inventory := map[string]int{"Laptop": 10, "Mouse": 50}
    updateInventory(inventory, "Laptop", -1)  // Sell one laptop
    fmt.Println(inventory)  // map[Laptop:9 Mouse:50]
}
```

### 7.4 Real-World Pattern: Update Methods 🎯

**Common Pattern: Pointer Receiver Methods**
```go
package main

import (
    "fmt"
    "time"
)

type ShoppingCart struct {
    ID        string
    Items     []CartItem
    Total     float64
    UpdatedAt time.Time
}

type CartItem struct {
    ProductID int
    Name      string
    Price     float64
    Quantity  int
}

// ✅ Pointer receiver (modifies cart)
func (c *ShoppingCart) AddItem(item CartItem) {
    c.Items = append(c.Items, item)
    c.Total += item.Price * float64(item.Quantity)
    c.UpdatedAt = time.Now()
}

// ✅ Pointer receiver (modifies cart)
func (c *ShoppingCart) RemoveItem(productID int) {
    for i, item := range c.Items {
        if item.ProductID == productID {
            c.Total -= item.Price * float64(item.Quantity)
            c.Items = append(c.Items[:i], c.Items[i+1:]...)
            c.UpdatedAt = time.Now()
            return
        }
    }
}

// Value receiver (doesn't modify cart, just reads)
func (c ShoppingCart) GetItemCount() int {
    return len(c.Items)
}

// Value receiver (doesn't modify cart, just reads)
func (c ShoppingCart) IsEmpty() bool {
    return len(c.Items) == 0
}

func main() {
    cart := ShoppingCart{
        ID:    "cart-123",
        Items: []CartItem{},
    }
    
    cart.AddItem(CartItem{1001, "Laptop", 999.99, 1})
    cart.AddItem(CartItem{1002, "Mouse", 29.99, 2})
    
    fmt.Printf("Cart total: $%.2f\n", cart.Total)
    fmt.Printf("Items: %d\n", cart.GetItemCount())
    
    cart.RemoveItem(1002)
    fmt.Printf("Cart total: $%.2f\n", cart.Total)
}
```

---

## 📖 Section 8: Pointers as a Last Resort

### 8.1 The Go Philosophy 🧘

**Go's Preference:**
- Use **values** by default
- Use **pointers** only when necessary
- Simpler code = fewer bugs

**Why Prefer Values?**
```go
// ✅ Value: Simple, safe, no nil checks
func calculateTotal(prices []float64) float64 {
    total := 0.0
    for _, price := range prices {
        total += price
    }
    return total
}

// ⚠️ Pointer: More complex, need nil checks
func calculateTotalPtr(prices *[]float64) float64 {
    if prices == nil {  // Always need this check!
        return 0.0
    }
    total := 0.0
    for _, price := range *prices {
        total += price
    }
    return total
}
```

### 8.2 When Pointers Are NOT Needed ❌

**1. Small Types (Pass by Value is Fine):**
```go
// ❌ Unnecessary pointer for small types
func processIDBad(id *int) {
    if id == nil {
        return
    }
    // Process *id
}

// ✅ Just use value (int is small, copying is cheap)
func processIDGood(id int) {
    // Process id
}
```

**2. Immutable Data (No Modifications):**
```go
type Product struct {
    ID    int
    Name  string
    Price float64
}

// ❌ Unnecessary pointer (not modifying)
func displayProductBad(p *Product) {
    if p == nil {
        return
    }
    fmt.Printf("%s: $%.2f\n", p.Name, p.Price)
}

// ✅ Use value (read-only operation)
func displayProductGood(p Product) {
    fmt.Printf("%s: $%.2f\n", p.Name, p.Price)
}
```

**3. Slices and Maps (Already References):**
```go
// ❌ Redundant pointer (slices are already references)
func addProductBad(products *[]Product, p Product) {
    *products = append(*products, p)
}

// ✅ Just use slice (no pointer needed)
func addProductGood(products []Product, p Product) []Product {
    return append(products, p)
}
```

### 8.3 When Pointers ARE Needed ✅

**1. Modify Parameter:**
```go
type Order struct {
    ID     int
    Status string
}

// ✅ Pointer needed to modify
func updateOrderStatus(order *Order, status string) {
    order.Status = status
}
```

**2. Large Structs (Avoid Copying):**
```go
type LargeReport struct {
    Data      [10000]float64
    Metadata  map[string]string
    // ... many fields
}

// ✅ Pointer to avoid expensive copy
func processReport(report *LargeReport) {
    // Process report...
}
```

**3. Optional Values (nil = absence):**
```go
type Config struct {
    Timeout *int  // nil means "use default"
    MaxRetries *int
}

func getConfig() Config {
    timeout := 30
    return Config{
        Timeout: &timeout,  // Explicit value
        MaxRetries: nil,    // Use default
    }
}
```

**4. Interface Satisfaction:**
```go
type Saver interface {
    Save() error
}

type Order struct {
    ID int
}

// ✅ Pointer receiver to satisfy interface
func (o *Order) Save() error {
    // Save to database...
    return nil
}
```

### 8.4 Decision Flowchart 📊

```
Do you need to modify the parameter?
├─ No → Use value
└─ Yes → Is it a slice/map/channel?
    ├─ Yes → Just return new value (use value parameter)
    └─ No → Is it a large struct (>100 bytes)?
        ├─ Yes → Use pointer
        └─ No → Use pointer only if needed
```

**Practical Examples:**
```go
package main

import "fmt"

// Small struct, immutable → VALUE
type Point struct {
    X, Y int
}

func distance(p Point) float64 {
    return math.Sqrt(float64(p.X*p.X + p.Y*p.Y))
}

// Large struct, read-only → POINTER (avoid copy)
type LargeData struct {
    Values [10000]int
}

func sumValues(data *LargeData) int {
    sum := 0
    for _, v := range data.Values {
        sum += v
    }
    return sum
}

// Need to modify → POINTER
type Counter struct {
    Count int
}

func (c *Counter) Increment() {
    c.Count++
}

// Slice → VALUE (but return new slice)
func addElement(slice []int, elem int) []int {
    return append(slice, elem)
}

// Map → VALUE (maps are references, changes visible)
func updateMap(m map[string]int, key string, value int) {
    m[key] = value  // Modifies original map
}
```

---

## 📖 Section 9: Performance Considerations

### 9.1 Memory and CPU Trade-offs ⚖️

**Understanding the Costs:**

| Operation | Memory | CPU | Use Case |
|-----------|--------|-----|----------|
| Copy small value | Low | Low | Best for small types (<100 bytes) |
| Copy large struct | High | Medium | Avoid for large structs |
| Pass pointer | Very Low | Low | Good for large structs |
| Dereference pointer | Low | Low | Slightly slower than direct access |

**Benchmark Example:**
```go
package main

import (
    "testing"
)

type SmallStruct struct {
    A, B, C int
}

type LargeStruct struct {
    Data [1000]int
}

// Benchmark small struct by value
func BenchmarkSmallStructValue(b *testing.B) {
    s := SmallStruct{1, 2, 3}
    for i := 0; i < b.N; i++ {
        processSmallValue(s)
    }
}

// Benchmark small struct by pointer
func BenchmarkSmallStructPointer(b *testing.B) {
    s := SmallStruct{1, 2, 3}
    for i := 0; i < b.N; i++ {
        processSmallPointer(&s)
    }
}

// Benchmark large struct by value
func BenchmarkLargeStructValue(b *testing.B) {
    s := LargeStruct{}
    for i := 0; i < b.N; i++ {
        processLargeValue(s)
    }
}

// Benchmark large struct by pointer
func BenchmarkLargeStructPointer(b *testing.B) {
    s := LargeStruct{}
    for i := 0; i < b.N; i++ {
        processLargePointer(&s)
    }
}

func processSmallValue(s SmallStruct) int {
    return s.A + s.B + s.C
}

func processSmallPointer(s *SmallStruct) int {
    return s.A + s.B + s.C
}

func processLargeValue(s LargeStruct) int {
    sum := 0
    for _, v := range s.Data {
        sum += v
    }
    return sum
}

func processLargePointer(s *LargeStruct) int {
    sum := 0
    for _, v := range s.Data {
        sum += v
    }
    return sum
}

// Run with: go test -bench=. -benchmem
```

**Typical Results:**
```
BenchmarkSmallStructValue-8      1000000000    0.25 ns/op    0 B/op    0 allocs/op
BenchmarkSmallStructPointer-8    1000000000    0.26 ns/op    0 B/op    0 allocs/op
BenchmarkLargeStructValue-8      50000000      35.2 ns/op    0 B/op    0 allocs/op
BenchmarkLargeStructPointer-8    500000000     3.1 ns/op     0 B/op    0 allocs/op
```

**Key Insight:** For large structs, pointers are ~10x faster!

### 9.2 Escape Analysis 🔍

**What is Escape Analysis?**
- Compiler determines if a variable can live on the **stack** or must go to the **heap**
- Stack allocation is faster (no GC overhead)
- Heap allocation is slower (requires GC)

**When Variables "Escape" to Heap:**
```go

package main

import "fmt"

type Order struct {
    ID int
}

// Case 1: Returning pointer to local variable
func createOrderBad() *Order {
    order := Order{ID: 1001}
    return &order  // ⚠️ Escapes to heap!
}

// Case 2: Storing in slice/map
func addToListBad() {
    var list []*Order
    for i := 0; i < 10; i++ {
        order := Order{ID: i}
        list = append(list, &order)  // ⚠️ All escape to heap!
    }
}

// Case 3: Interface conversion
func processOrderBad(order Order) {
    var i interface{} = order  // ⚠️ May escape to heap
    fmt.Println(i)
}


func main() {
	createOrderBad()
	addToListBad()
	processOrderBad(Order{ID: 1001})
}
```

**Check Escape Analysis:**
```bash
go build -gcflags="-m" main.go
```

**Output:**
```
./main.go:5:2: moved to heap: order
./main.go:12:2: moved to heap: order
```

**Optimization Tips:**
```go
// ✅ Avoid unnecessary escapes
func createOrder() Order {
    return Order{ID: 1001}  // Stays on stack!
}

// ✅ Pre-allocate slices
func processOrders() {
    orders := make([]Order, 0, 100)  // Not pointers
    for i := 0; i < 100; i++ {
        orders = append(orders, Order{ID: i})
    }
    // All orders stay on stack!
}
```

### 9.3 E-Commerce Performance Example 🛒

```go
package main

import (
    "fmt"
    "time"
)

type OrderItem struct {
    ProductID int
    Quantity  int
    Price     float64
}

type Order struct {
    ID        int
    Items     []OrderItem
    Total     float64
    CreatedAt time.Time
}

// ❌ Slow: Returns pointer (heap allocation)
func calculateTotalBad(items []OrderItem) *float64 {
    total := 0.0
    for _, item := range items {
        total += item.Price * float64(item.Quantity)
    }
    return &total  // Escapes to heap!
}

// ✅ Fast: Returns value (stack allocation)
func calculateTotalGood(items []OrderItem) float64 {
    total := 0.0
    for _, item := range items {
        total += item.Price * float64(item.Quantity)
    }
    return total  // Stays on stack!
}

// ✅ Efficient: Process in place
func updateOrderTotals(orders []Order) {
    for i := range orders {
        total := 0.0
        for _, item := range orders[i].Items {
            total += item.Price * float64(item.Quantity)
        }
        orders[i].Total = total
    }
}

func main() {
    items := []OrderItem{
        {1001, 2, 29.99},
        {1002, 1, 99.99},
    }
    
    // Measure performance
    start := time.Now()
    for i := 0; i < 1000000; i++ {
        _ = calculateTotalGood(items)
    }
    fmt.Printf("Took: %v\n", time.Since(start))
}
```

---

## 📖 Section 10: Zero vs No Value

### 10.1 The Problem with Zero Values 🤔

**Ambiguity of Zero:**
```go
type Config struct {
    Port    int     // 0 could mean "not set" or "use port 0"
    Timeout int     // 0 could mean "no timeout" or "not set"
    MaxSize int     // 0 could mean "no limit" or "not set"
}

func getConfig() Config {
    return Config{
        Port: 0,  // Not set or port 0?
    }
}
```

**Problem:**
```go
config := getConfig()

// How do we know if Port was set intentionally?
if config.Port == 0 {
    // Is this "use default" or "use port 0"?
}
```

### 10.2 Solution: Pointer for Optional Fields 🎯

**Using Pointers to Distinguish: sentinel**
```go
type Config struct {
    Port    *int   // nil = not set, &0 = explicitly 0
    Timeout *int   // nil = use default
    MaxSize *int   // nil = no limit
}

func getConfig() Config {
    port := 8080
    timeout := 30
    
    return Config{
        Port:    &port,      // Explicitly set to 8080
        Timeout: &timeout,   // Explicitly set to 30
        MaxSize: nil,        // Not set (use default)
    }
}

func applyDefaults(config *Config) {
    if config.Port == nil {
        defaultPort := 3000
        config.Port = &defaultPort
    }
    
    if config.Timeout == nil {
        defaultTimeout := 60
        config.Timeout = &defaultTimeout
    }
    
    if config.MaxSize == nil {
        defaultSize := 1024
        config.MaxSize = &defaultSize
    }
}

func main() {
    config := getConfig()
    
    // Check if Port was explicitly set
    if config.Port != nil {
        fmt.Printf("Port: %d\n", *config.Port)
    } else {
        fmt.Println("Port not set")
    }
    
    applyDefaults(&config)
    fmt.Printf("Final Port: %d\n", *config.Port)
}
```

### 10.3 E-Commerce Example: Product Search Filters 🔍

```go
package main

import "fmt"

type SearchFilter struct {
    MinPrice *float64  // nil = no minimum
    MaxPrice *float64  // nil = no maximum
    Category *string   // nil = all categories
    InStock  *bool     // nil = show all, true = only in stock
}

type Product struct {
    Name     string
    Price    float64
    Category string
    InStock  bool
}

func searchProducts(products []Product, filter SearchFilter) []Product {
    results := []Product{}
    
    for _, product := range products {
        // Check min price
        if filter.MinPrice != nil && product.Price < *filter.MinPrice {
            continue
        }
        
        // Check max price
        if filter.MaxPrice != nil && product.Price > *filter.MaxPrice {
            continue
        }
        
        // Check category
        if filter.Category != nil && product.Category != *filter.Category {
            continue
        }
        
        // Check stock
        if filter.InStock != nil && product.InStock != *filter.InStock {
            continue
        }
        
        results = append(results, product)
    }
    
    return results
}

func main() {
    products := []Product{
        {"Laptop", 999.99, "Electronics", true},
        {"Mouse", 29.99, "Electronics", true},
        {"Desk", 299.99, "Furniture", false},
        {"Chair", 199.99, "Furniture", true},
    }
    
    // Search 1: Electronics, in stock only
    category := "Electronics"
    inStock := true
    filter1 := SearchFilter{
        Category: &category,
        InStock:  &inStock,
    }
    results1 := searchProducts(products, filter1)
    fmt.Printf("Electronics in stock: %v\n", len(results1))
    
    // Search 2: Price range $100-$500, any category
    minPrice := 100.0
    maxPrice := 500.0
    filter2 := SearchFilter{
        MinPrice: &minPrice,
        MaxPrice: &maxPrice,
    }
    results2 := searchProducts(products, filter2)
    fmt.Printf("Products $100-$500: %v\n", len(results2))
    
    // Search 3: All products (no filters)
    filter3 := SearchFilter{}
    results3 := searchProducts(products, filter3)
    fmt.Printf("All products: %v\n", len(results3))
}
```

### 10.4 Helper Functions for Pointers 🛠️

**Creating Pointer Helpers:**
```go
// Helper functions to create pointers
func IntPtr(v int) *int {
    return &v
}

func Float64Ptr(v float64) *float64 {
    return &v
}

func StringPtr(v string) *string {
    return &v
}

func BoolPtr(v bool) *bool {
    return &v
}

// Usage: Much cleaner!
filter := SearchFilter{
    MinPrice: Float64Ptr(100.0),
    MaxPrice: Float64Ptr(500.0),
    Category: StringPtr("Electronics"),
    InStock:  BoolPtr(true),
}
```

**Safe Dereferencing Helpers:**
```go
// Get value or default
func IntValue(ptr *int, defaultValue int) int {
    if ptr == nil {
        return defaultValue
    }
    return *ptr
}

func Float64Value(ptr *float64, defaultValue float64) float64 {
    if ptr == nil {
        return defaultValue
    }
    return *ptr
}

// Usage
config := getConfig()
port := IntValue(config.Port, 3000)       // Use 3000 if nil
timeout := IntValue(config.Timeout, 60)   // Use 60 if nil
```

---

## 📖 Section 11: Difference Between Maps and Slices

### 11.1 Key Differences 🔑

**Comparison Table:**

| Feature | Slices | Maps |
|---------|--------|------|
| **Type** | Sequence | Key-value pairs |
| **Order** | Ordered | Unordered |
| **Access** | By index (0, 1, 2...) | By key (any comparable type) |
| **Zero value** | nil | nil |
| **Nil behavior** | Can read (panics on write) | Cannot read or write |
| **Modification** | Append, modify elements | Add, update, delete keys |
| **Iteration** | Predictable order | Random order |
| **Performance** | O(1) index access | O(1) key lookup (average) |

### 11.2 Working with Nil 🧊

**Nil Slice vs Nil Map:**
```go
package main

import "fmt"

func main() {
    // Nil slice
    var slice []int
    fmt.Println(slice == nil)         // true
    fmt.Println(len(slice))           // 0 (safe)
    slice = append(slice, 1)          // ✅ Works! Auto-allocates
    fmt.Println(slice)                // [1]
    
    // Nil map
    var m map[string]int
    fmt.Println(m == nil)             // true
    fmt.Println(len(m))               // 0 (safe)
    value := m["key"]                 // ✅ Returns zero value (0)
    fmt.Println(value)                // 0
    // m["key"] = 1                   // ❌ Panic! Cannot write to nil map
    
    // Fix: Initialize map
    m = make(map[string]int)
    m["key"] = 1                      // ✅ Works now
}
```

**Safe Patterns:**
```go
// ✅ Slice: Safe to append to nil slice
func addItems(items []string, newItems ...string) []string {
    return append(items, newItems...)  // Works even if items is nil
}

// ❌ Map: Must initialize first
func addToMapBad(m map[string]int, key string, value int) {
    m[key] = value  // Panics if m is nil!
}

// ✅ Map: Check and initialize
func addToMapGood(m map[string]int, key string, value int) map[string]int {
    if m == nil {
        m = make(map[string]int)
    }
    m[key] = value
    return m
}
```

### 11.3 When to Use Each 🤔

**Use Slices When:**
- ✅ Order matters
- ✅ Need to iterate in sequence
- ✅ Duplicate values are allowed
- ✅ Index-based access
- ✅ Growing/shrinking collection

**Use Maps When:**
- ✅ Fast lookups by key
- ✅ Unique keys (like a set)
- ✅ Key-value associations
- ✅ Order doesn't matter
- ✅ Frequent add/delete operations

**E-Commerce Examples:**
```go
package main

import "fmt"

// ✅ Use slice: Shopping cart (order matters, duplicates allowed)
type ShoppingCart struct {
    Items []CartItem  // Order of addition matters
}

type CartItem struct {
    ProductID int
    Quantity  int
}

// ✅ Use map: Product inventory (fast lookup by ID)
type Inventory map[int]Product  // Fast lookup by product ID

type Product struct {
    ID    int
    Name  string
    Stock int
}

// ✅ Use map: User sessions (fast lookup by session ID)
type SessionStore map[string]Session  // Fast lookup by session ID

type Session struct {
    UserID    int
    CreatedAt time.Time
}

// ✅ Use slice: Order history (chronological order)
type OrderHistory []Order  // Order of orders matters

type Order struct {
    ID        int
    CreatedAt time.Time
    Total     float64
}

// ✅ Use map: Cache (fast lookup)
type ProductCache map[int]Product

func main() {
    // Slice example: Cart maintains insertion order
    cart := ShoppingCart{
        Items: []CartItem{
            {ProductID: 101, Quantity: 2},
            {ProductID: 102, Quantity: 1},
            {ProductID: 101, Quantity: 1},  // Same product again
        },
    }
    fmt.Printf("Cart has %d items\n", len(cart.Items))
    
    // Map example: Fast inventory lookup
    inventory := Inventory{
        101: {101, "Laptop", 10},
        102: {102, "Mouse", 50},
    }
    
    if product, exists := inventory[101]; exists {
        fmt.Printf("%s: %d in stock\n", product.Name, product.Stock)
    }
}
```

### 11.4 Conversion Between Slices and Maps 🔄

**Slice to Map (Indexing):**
```go
// Create lookup map from slice
func sliceToMap(products []Product) map[int]Product {
    productMap := make(map[int]Product, len(products))
    for _, product := range products {
        productMap[product.ID] = product
    }
    return productMap
}

// Usage
products := []Product{
    {1001, "Laptop", 10},
    {1002, "Mouse", 50},
}

productMap := sliceToMap(products)
fmt.Println(productMap[1001])  // Fast O(1) lookup
```

**Map to Slice (Ordering):**
```go
import "sort"

// Convert map to sorted slice
func mapToSortedSlice(productMap map[int]Product) []Product {
    products := make([]Product, 0, len(productMap))
    
    // Extract to slice
    for _, product := range productMap {
        products = append(products, product)
    }
    
    // Sort by ID
    sort.Slice(products, func(i, j int) bool {
        return products[i].ID < products[j].ID
    })
    
    return products
}
```

---

## 📖 Section 12: Slices as Buffers

### 12.1 Understanding Slice Buffers 📦

**What is a Buffer?**
- Reusable slice for temporary data
- Avoids repeated allocations
- Common in I/O operations

**Basic Buffer Pattern:**
```go
package main

import (
    "fmt"
    "io"
)

func processLargeFile(r io.Reader) error {
    // Reuse buffer for reading
    buffer := make([]byte, 4096)  // 4KB buffer
    
    for {
        n, err := r.Read(buffer)
        if err == io.EOF {
            break
        }
        if err != nil {
            return err
        }
        
        // Process buffer[:n]
        processChunk(buffer[:n])
        
        // Buffer is reused in next iteration!
    }
    
    return nil
}

func processChunk(data []byte) {
    fmt.Printf("Processing %d bytes\n", len(data))
}
```

### 12.2 E-Commerce Batch Processing 🛒

**Example: Processing Orders in Batches**
```go
package main

import (
    "fmt"
    "time"
)

type Order struct {
    ID        int
    Total     float64
    CreatedAt time.Time
}

// Process orders in batches for efficiency
func processOrdersInBatches(orders []Order, batchSize int) {
    // Reusable buffer for each batch
    batch := make([]Order, 0, batchSize)
    
    for _, order := range orders {
        batch = append(batch, order)
        
        // When batch is full, process it
        if len(batch) >= batchSize {
            processBatch(batch)
            batch = batch[:0]  // Reset buffer (keep capacity)
        }
    }
    
    // Process remaining orders
    if len(batch) > 0 {
        processBatch(batch)
    }
}

func processBatch(orders []Order) {
    fmt.Printf("Processing batch of %d orders\n", len(orders))
    
    // Calculate total
    total := 0.0
    for _, order := range orders {
        total += order.Total
    }
    
    fmt.Printf("Batch total: $%.2f\n", total)
}

func main() {
    // Generate 100 orders
    orders := make([]Order, 100)
    for i := range orders {
        orders[i] = Order{
            ID:        1000 + i,
            Total:     float64(i) * 10.5,
            CreatedAt: time.Now(),
        }
    }
    
    // Process in batches of 20
    processOrdersInBatches(orders, 20)
}
```

### 12.3 String Building with Buffers 📝

**Problem: String Concatenation is Slow**
```go
// ❌ Slow: Creates new string on each +
func buildMessageBad(items []string) string {
    result := ""
    for _, item := range items {
        result += item + "\n"  // New allocation each time!
    }
    return result
}

// ✅ Fast: Use strings.Builder (uses buffer internally)
import "strings"

func buildMessageGood(items []string) string {
    var builder strings.Builder
    builder.Grow(len(items) * 20)  // Pre-allocate estimated size
    
    for _, item := range items {
        builder.WriteString(item)
        builder.WriteString("\n")
    }
    
    return builder.String()
}

// ✅ Alternative: Use bytes.Buffer
import "bytes"

func buildMessageBuffer(items []string) string {
    var buffer bytes.Buffer
    buffer.Grow(len(items) * 20)
    
    for _, item := range items {
        buffer.WriteString(item)
        buffer.WriteString("\n")
    }
    
    return buffer.String()
}
```

**Benchmark Comparison:**
```go
func BenchmarkStringConcatBad(b *testing.B) {
    items := []string{"item1", "item2", "item3", "item4", "item5"}
    for i := 0; i < b.N; i++ {
        buildMessageBad(items)
    }
}

func BenchmarkStringBuilderGood(b *testing.B) {
    items := []string{"item1", "item2", "item3", "item4", "item5"}
    for i := 0; i < b.N; i++ {
        buildMessageGood(items)
    }
}

// Results:
// BenchmarkStringConcatBad-8       5000000    250 ns/op   120 B/op   5 allocs/op
// BenchmarkStringBuilderGood-8    20000000     60 ns/op    32 B/op   1 allocs/op
```

### 12.4 Pooling Buffers for Performance 🏊

**Using sync.Pool for Buffer Reuse:**
```go
package main

import (
    "bytes"
    "fmt"
    "sync"
)

// Pool of reusable buffers
var bufferPool = sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}

// Get buffer from pool
func getBuffer() *bytes.Buffer {
    return bufferPool.Get().(*bytes.Buffer)
}

// Return buffer to pool
func putBuffer(buf *bytes.Buffer) {
    buf.Reset()  // Clear buffer
    bufferPool.Put(buf)
}

// Process with pooled buffer
func formatOrder(order Order) string {
    buf := getBuffer()
    defer putBuffer(buf)  // Return to pool when done
    
    fmt.Fprintf(buf, "Order #%d\n", order.ID)
    fmt.Fprintf(buf, "Total: $%.2f\n", order.Total)
    fmt.Fprintf(buf, "Date: %s\n", order.CreatedAt.Format("2006-01-02"))
    
    return buf.String()
}

func main() {
    orders := []Order{
        {1001, 99.99, time.Now()},
        {1002, 149.99, time.Now()},
        {1003, 79.99, time.Now()},
    }
    
    for _, order := range orders {
        message := formatOrder(order)
        fmt.Println(message)
    }
}
```

---

## 📖 Section 13: GC Optimization

### 13.1 Understanding Garbage Collection 🗑️

**What is GC?**
- Automatic memory management
- Frees memory that's no longer used
- Prevents memory leaks
- Has performance cost

**How Go's GC Works:**
```
1. Mark Phase: Find all reachable objects
2. Sweep Phase: Free unreachable objects
3. Concurrent: Runs alongside your program
```

**GC Triggers:**
- Memory usage reaches threshold
- Manual trigger: `runtime.GC()`
- Time-based (every 2 minutes if idle)

### 13.2 Reducing GC Pressure 🎯

**1. Reuse Objects (Object Pooling):**
```go
import "sync"

// Pool for large objects
var orderPool = sync.Pool{
    New: func() interface{} {
        return &Order{
            Items: make([]OrderItem, 0, 10),
        }
    },
}

func processOrder(id int) {
    order := orderPool.Get().(*Order)
    defer orderPool.Put(order)
    
    // Reset order
    order.ID = id
    order.Items = order.Items[:0]
    order.Total = 0
    
    // Use order...
}
```

**2. Pre-allocate Slices:**
```go
// ❌ Bad: Multiple allocations
func processBad(n int) []int {
    var result []int  // cap = 0
    for i := 0; i < n; i++ {
        result = append(result, i)  // Reallocates multiple times
    }
    return result
}

// ✅ Good: Single allocation
func processGood(n int) []int {
    result := make([]int, 0, n)  // Pre-allocate
    for i := 0; i < n; i++ {
        result = append(result, i)  // No reallocation
    }
    return result
}
```

**3. Avoid Pointer-Heavy Structures:**
```go
// ❌ Bad: Many pointers (more GC work)
type OrderBad struct {
    ID        *int
    Total     *float64
    Items     []*OrderItem
    Customer  *Customer
    Status    *string
}

// ✅ Good: Values where possible
type OrderGood struct {
    ID       int
    Total    float64
    Items    []OrderItem  // Slice of values
    Customer Customer     // Embedded struct
    Status   string
}
```

**4. Use Value Receivers:**
```go
// ❌ Creates garbage
func (o *Order) GetTotalBad() *float64 {
    total := o.Total * 1.09  // Tax
    return &total  // Escapes to heap!
}

// ✅ No garbage
func (o Order) GetTotalGood() float64 {
    return o.Total * 1.09  // Stays on stack
}
```

### 13.3 Monitoring GC Performance 📊

**Enable GC Tracing:**
```bash
GODEBUG=gctrace=1 ./myapp

# Output:
# gc 1 @0.001s 0%: 0.015+0.59+0.096 ms clock, 0.18+0.075/0.44/0.048+1.1 ms cpu, 4->4->1 MB, 5 MB goal, 12 P
# gc 2 @0.003s 0%: 0.018+0.46+0.11 ms clock, 0.22+0.13/0.45/0.052+1.3 ms cpu, 4->4->1 MB, 5 MB goal, 12 P
```

**Interpret GC Trace:**
- `gc 1`: GC cycle number
- `@0.001s`: Time since program start
- `0%`: Percent of time in GC
- `4->4->1 MB`: Heap size (before, after, live)
- `5 MB goal`: Next GC trigger

**Programmatic Monitoring:**
```go
package main

import (
    "fmt"
    "runtime"
    "time"
)

func printGCStats() {
    var stats runtime.MemStats
    runtime.ReadMemStats(&stats)
    
    fmt.Printf("GC Stats:\n")
    fmt.Printf("  Alloc: %d MB\n", stats.Alloc/1024/1024)
    fmt.Printf("  TotalAlloc: %d MB\n", stats.TotalAlloc/1024/1024)
    fmt.Printf("  Sys: %d MB\n", stats.Sys/1024/1024)
    fmt.Printf("  NumGC: %d\n", stats.NumGC)
    fmt.Printf("  PauseTotal: %v\n", time.Duration(stats.PauseTotalNs))
}

func main() {
    // Before processing
    printGCStats()
    
    // Do work...
    processOrders()
    
    // After processing
    printGCStats()
}
```

---

## 📖 Section 14: Tuning the GC

### 14.1 GOGC Environment Variable 🎛️

**What is GOGC?**
- Controls GC aggressiveness
- Default: `GOGC=100` (100%)
- Higher value = less frequent GC, more memory
- Lower value = more frequent GC, less memory

**Formula:**
```
Next GC = Live Heap * (1 + GOGC/100)

Example with GOGC=100 and 10MB live heap:
Next GC = 10MB * (1 + 100/100) = 10MB * 2 = 20MB
```

**Tuning Examples:**
```bash
# Default (100%)
./myapp

# More memory, less GC (200%)
GOGC=200 ./myapp

# Less memory, more GC (50%)
GOGC=50 ./myapp

# Disable GC (not recommended!)
GOGC=off ./myapp
```

### 14.2 GOMEMLIMIT (Go 1.19+) 💾

**Soft Memory Limit:**
```bash
# Limit memory to 2GB
GOMEMLIMIT=2GiB ./myapp

# Limit to 512MB
GOMEMLIMIT=512MiB ./myapp
```

**How it Works:**
- GC runs more frequently as it approaches the limit
- Prevents OOM (Out of Memory) errors
- Works with GOGC

**Example:**
```bash
# Use both GOGC and GOMEMLIMIT
GOGC=100 GOMEMLIMIT=1GiB ./myapp
```

### 14.3 Practical Tuning Example 🎯

**E-Commerce Order Processing Service:**
```go
package main

import (
    "fmt"
    "runtime"
    "runtime/debug"
    "time"
)

func main() {
    // Option 1: Set GOGC programmatically
    debug.SetGCPercent(200)  // Same as GOGC=200
    
    // Option 2: Set memory limit (Go 1.19+)
    debug.SetMemoryLimit(1024 * 1024 * 1024)  // 1GB
    
    // Monitor GC
    go monitorGC()
    
    // Simulate order processing
    processOrders()
}

func monitorGC() {
    ticker := time.NewTicker(5 * time.Second)
    defer ticker.Stop()
    
    var lastNumGC uint32
    
    for range ticker.C {
        var stats runtime.MemStats
        runtime.ReadMemStats(&stats)
        
        gcRate := stats.NumGC - lastNumGC
        lastNumGC = stats.NumGC
        
        fmt.Printf("[Monitor] Alloc: %dMB, GC: %d (rate: %d/5s)\n",
            stats.Alloc/1024/1024,
            stats.NumGC,
            gcRate)
    }
}

func processOrders() {
    // Simulate workload
    for i := 0; i < 1000; i++ {
        orders := make([]Order, 1000)
        for j := range orders {
            orders[j] = Order{
                ID:    i*1000 + j,
                Total: float64(j) * 1.5,
            }
        }
        
        // Process batch
        time.Sleep(10 * time.Millisecond)
        
        if i%100 == 0 {
            fmt.Printf("Processed %d batches\n", i)
        }
    }
}

type Order struct {
    ID    int
    Total float64
}
```

### 14.4 Tuning Guidelines 📋

**When to Increase GOGC (>100):**
- ✅ Plenty of memory available
- ✅ Want lower GC pause times
- ✅ CPU is more important than memory
- ✅ Batch processing jobs

**When to Decrease GOGC (<100):**
- ✅ Limited memory (containers, embedded)
- ✅ Memory is more important than CPU
- ✅ Running many services on one machine

**Recommended Settings by Use Case:**

| Use Case | GOGC | GOMEMLIMIT | Reason |
|----------|------|------------|---------|
| Web API (low traffic) | 100 | Container limit * 0.9 | Default, with safety limit |
| Web API (high traffic) | 150-200 | Container limit * 0.9 | Reduce GC frequency |
| Batch processing | 200-400 | 80% of available RAM | Minimize GC interruptions |
| Microservice (tight memory) | 50-75 | Container limit * 0.9 | Stay within limits |
| Development | 100 | none | Default is fine |

**E-Commerce Service Example:**
```bash
# High-traffic API server (16GB RAM container)
GOGC=200 GOMEMLIMIT=14GiB ./api-server

# Background worker (4GB RAM container)
GOGC=100 GOMEMLIMIT=3.5GiB ./worker

# Batch processor (32GB RAM)
GOGC=300 GOMEMLIMIT=28GiB ./batch-processor
```

---

## 🏋️ Exercises and Challenges

### Exercise 1: Pointer Basics 📝

**Simple Level:**

1. Create a function that swaps two integers using pointers
2. Modify a struct field using a pointer
3. Return a pointer from a function

**Solution:**
```go
package main

import "fmt"

// 1. Swap two integers
func swap(a, b *int) {
    *a, *b = *b, *a
}

// 2. Modify struct using pointer
type Product struct {
    Name  string
    Price float64
}

func applyDiscount(p *Product, discount float64) {
    p.Price = p.Price * (1 - discount)
}

// 3. Return pointer (use with caution!)
func createProduct(name string, price float64) *Product {
    return &Product{Name: name, Price: price}
}

func main() {
    // Test swap
    x, y := 10, 20
    fmt.Printf("Before swap: x=%d, y=%d\n", x, y)
    swap(&x, &y)
    fmt.Printf("After swap: x=%d, y=%d\n", x, y)
    
    // Test modify struct
    product := Product{"Laptop", 1000.0}
    fmt.Printf("Before discount: $%.2f\n", product.Price)
    applyDiscount(&product, 0.10)  // 10% discount
    fmt.Printf("After discount: $%.2f\n", product.Price)
    
    // Test return pointer
    newProduct := createProduct("Mouse", 29.99)
    fmt.Printf("New product: %+v\n", newProduct)
}
```

### Exercise 2: Mutable Cart 🛒

**Medium Level:**

Build a shopping cart with pointer methods:
- Add item
- Remove item
- Update quantity
- Calculate total

**Solution:**
```go
package main

import "fmt"

type CartItem struct {
    ProductID int
    Name      string
    Price     float64
    Quantity  int
}

type Cart struct {
    Items []CartItem
    Total float64
}

// Add item (pointer receiver)
func (c *Cart) AddItem(item CartItem) {
    c.Items = append(c.Items, item)
    c.Total += item.Price * float64(item.Quantity)
}

// Remove item (pointer receiver)
func (c *Cart) RemoveItem(productID int) bool {
    for i, item := range c.Items {
        if item.ProductID == productID {
            c.Total -= item.Price * float64(item.Quantity)
            c.Items = append(c.Items[:i], c.Items[i+1:]...)
            return true
        }
    }
    return false
}

// Update quantity (pointer receiver)
func (c *Cart) UpdateQuantity(productID, newQuantity int) bool {
    for i, item := range c.Items {
        if item.ProductID == productID {
            // Adjust total
            oldValue := item.Price * float64(item.Quantity)
            newValue := item.Price * float64(newQuantity)
            c.Total += newValue - oldValue
            
            // Update quantity
            c.Items[i].Quantity = newQuantity
            return true
        }
    }
    return false
}

// Get total (value receiver - read-only)
func (c Cart) GetTotal() float64 {
    return c.Total
}

func main() {
    cart := &Cart{}
    
    cart.AddItem(CartItem{1001, "Laptop", 999.99, 1})
    cart.AddItem(CartItem{1002, "Mouse", 29.99, 2})
    fmt.Printf("Cart total: $%.2f\n", cart.GetTotal())
    
    cart.UpdateQuantity(1002, 1)  // Change mouse quantity to 1
    fmt.Printf("Cart total: $%.2f\n", cart.GetTotal())
    
    cart.RemoveItem(1002)
    fmt.Printf("Cart total: $%.2f\n", cart.GetTotal())
}
```

### Exercise 3: Zero vs No Value 🔍

**Medium Level:**

Create a search filter with optional fields using pointers.

**Solution:**
```go
package main

import "fmt"

type ProductFilter struct {
    MinPrice  *float64
    MaxPrice  *float64
    Category  *string
    Available *bool
}

type Product struct {
    Name      string
    Price     float64
    Category  string
    Available bool
}

func filterProducts(products []Product, filter ProductFilter) []Product {
    result := []Product{}
    
    for _, p := range products {
        if filter.MinPrice != nil && p.Price < *filter.MinPrice {
            continue
        }
        if filter.MaxPrice != nil && p.Price > *filter.MaxPrice {
            continue
        }
        if filter.Category != nil && p.Category != *filter.Category {
            continue
        }
        if filter.Available != nil && p.Available != *filter.Available {
            continue
        }
        result = append(result, p)
    }
    
    return result
}

// Helper functions
func Float64Ptr(v float64) *float64 { return &v }
func StringPtr(v string) *string    { return &v }
func BoolPtr(v bool) *bool          { return &v }

func main() {
    products := []Product{
        {"Laptop", 999.99, "Electronics", true},
        {"Mouse", 29.99, "Electronics", true},
        {"Desk", 299.99, "Furniture", false},
        {"Chair", 199.99, "Furniture", true},
    }
    
    // Filter 1: Electronics only
    filter1 := ProductFilter{
        Category: StringPtr("Electronics"),
    }
    results := filterProducts(products, filter1)
    fmt.Printf("Electronics: %d products\n", len(results))
    
    // Filter 2: Price range $100-$500
    filter2 := ProductFilter{
        MinPrice: Float64Ptr(100.0),
        MaxPrice: Float64Ptr(500.0),
    }
    results = filterProducts(products, filter2)
    fmt.Printf("Price $100-$500: %d products\n", len(results))
    
    // Filter 3: Available furniture
    filter3 := ProductFilter{
        Category:  StringPtr("Furniture"),
        Available: BoolPtr(true),
    }
    results = filterProducts(products, filter3)
    fmt.Printf("Available furniture: %d products\n", len(results))
}
```

### Challenge 1: Buffer Pool 🏊

**Hard Level:**

Implement a buffer pool for processing large batches efficiently.

**Solution:**
```go
package main

import (
    "fmt"
    "sync"
    "time"
)

// Buffer pool
var bufferPool = sync.Pool{
    New: func() interface{} {
        return make([]Order, 0, 100)  // Pre-allocate capacity
    },
}

type Order struct {
    ID    int
    Total float64
}

func getBuffer() []Order {
    return bufferPool.Get().([]Order)
}

func putBuffer(buf []Order) {
    buf = buf[:0]  // Reset length, keep capacity
    bufferPool.Put(buf)
}

func processBatch(orders []Order) float64 {
    total := 0.0
    for _, order := range orders {
        total += order.Total
    }
    return total
}

func processOrdersEfficiently(allOrders []Order, batchSize int) {
    buffer := getBuffer()
    defer putBuffer(buffer)
    
    batchNum := 1
    for _, order := range allOrders {
        buffer = append(buffer, order)
        
        if len(buffer) >= batchSize {
            total := processBatch(buffer)
            fmt.Printf("Batch %d: $%.2f\n", batchNum, total)
            buffer = buffer[:0]  // Reset
            batchNum++
        }
    }
    
    // Process remaining
    if len(buffer) > 0 {
        total := processBatch(buffer)
        fmt.Printf("Batch %d: $%.2f\n", batchNum, total)
    }
}

func main() {
    // Generate 1000 orders
    orders := make([]Order, 1000)
    for i := range orders {
        orders[i] = Order{
            ID:    i + 1,
            Total: float64(i%100) * 10.5,
        }
    }
    
    start := time.Now()
    processOrdersEfficiently(orders, 100)
    fmt.Printf("Took: %v\n", time.Since(start))
}
```

### Challenge 2: LeetCode - Linked List Cycle 🔗

**Medium Level:**

Detect if a linked list has a cycle using pointers.

**Solution:**
```go
package main

import "fmt"

type ListNode struct {
    Val  int
    Next *ListNode
}

// Floyd's Cycle Detection (Two Pointers)
func hasCycle(head *ListNode) bool {
    if head == nil {
        return false
    }
    
    slow := head
    fast := head
    
    for fast != nil && fast.Next != nil {
        slow = slow.Next       // Move 1 step
        fast = fast.Next.Next  // Move 2 steps
        
        if slow == fast {
            return true  // Cycle detected
        }
    }
    
    return false  // No cycle
}

func main() {
    // Create list: 1 -> 2 -> 3 -> 4
    //                   ^         |
    //                   |_________|
    node1 := &ListNode{Val: 1}
    node2 := &ListNode{Val: 2}
    node3 := &ListNode{Val: 3}
    node4 := &ListNode{Val: 4}
    
    node1.Next = node2
    node2.Next = node3
    node3.Next = node4
    node4.Next = node2  // Create cycle
    
    fmt.Println("Has cycle:", hasCycle(node1))  // true
    
    // Create list without cycle: 1 -> 2 -> 3
    node1.Next = node2
    node2.Next = node3
    node3.Next = nil
    
    fmt.Println("Has cycle:", hasCycle(node1))  // false
}
```

---

## 🎁 Wrapping Up

### ✅ What You Learned:

**1. Pointers:**
- Memory addresses and dereferencing
- When to use pointers vs values
- Nil pointer safety

**2. Mutable Parameters:**
- Pass by value vs pass by pointer
- Pointer receivers for methods
- Slices and maps (already references)

**3. Pointers as Last Resort:**
- Prefer values when possible
- Use pointers only when needed
- Decision flowchart

**4. Performance:**
- Memory vs CPU trade-offs
- Escape analysis
- Benchmarking

**5. Zero vs No Value:**
- Pointer for optional fields
- Distinguishing "not set" from "zero"
- Helper functions

**6. Maps vs Slices:**
- Key differences
- Nil behavior
- When to use each

**7. Slice Buffers:**
- Reusable buffers
- String building
- Buffer pooling

**8. GC Optimization:**
- Reducing allocations
- Object pooling
- Monitoring GC

**9. GC Tuning:**
- GOGC environment variable
- GOMEMLIMIT (Go 1.19+)
- Tuning guidelines

### 🎯 Key Takeaways:

1. **Pointers are powerful but use wisely** - Prefer values, use pointers when needed
2. **Performance matters in production** - Profile first, optimize later
3. **GC is usually fine** - Only tune when you have specific problems
4. **Memory efficiency = faster code** - Reduce allocations, reuse buffers
5. **Optional fields need pointers** - Distinguish between zero and nil

### 📚 Best Practices Summary:

**DO:**
- ✅ Use values by default
- ✅ Use pointers for large structs
- ✅ Use pointers to modify parameters
- ✅ Pre-allocate slices when size known
- ✅ Reuse buffers with sync.Pool
- ✅ Monitor GC in production

**DON'T:**
- ❌ Use pointers for everything
- ❌ Return pointers to local variables unnecessarily
- ❌ Ignore GC metrics
- ❌ Optimize prematurely
- ❌ Forget to check nil pointers
- ❌ Use pointer to map/slice/channel

### 🚀 Next Steps:

Now that you've mastered advanced topics, you can:
- Build high-performance Go applications
- Optimize memory usage
- Tune GC for your workload
- Write efficient, idiomatic Go code

**Keep learning, keep building! 🎉**

---

**Congratulations! You've completed all advanced topics! 🏆**

You now have comprehensive knowledge of:
- Go fundamentals (Chapters 1-2)
- Composite types (Chapter 3)
- Control structures (Chapter 4)
- Functions (Chapter 5)
- Advanced topics (Pointers, Memory, GC)

**Ready to build amazing Go applications! 🚀**

---

## 🎯 Final Practice Tasks

### Task 1: Simple - Memory-Efficient Product Catalog ⭐

**Level:** Simple

**Description:**
Create a product catalog system that efficiently stores and retrieves products. Focus on choosing the right data structures.

**Requirements:**
1. Create a `Product` struct with: ID, Name, Price, Stock
2. Implement `AddProduct` function to add products to a catalog
3. Implement `GetProduct` function to retrieve product by ID
4. Implement `ListProducts` function to list all products
5. Use appropriate data structures (map for catalog, slice for listing)

**Expected Behavior:**
```go
catalog := NewCatalog()
catalog.AddProduct(Product{ID: 1001, Name: "Laptop", Price: 999.99, Stock: 10})
catalog.AddProduct(Product{ID: 1002, Name: "Mouse", Price: 29.99, Stock: 50})

product, found := catalog.GetProduct(1001)
// Should return: Product{1001, "Laptop", 999.99, 10}, true

products := catalog.ListProducts()
// Should return all products in a slice
```

**Template:**
```go
package main

import "fmt"

type Product struct {
    ID    int
    Name  string
    Price float64
    Stock int
}

type Catalog struct {
    // Your fields here
    // Hint: Use a map for fast lookups
}

func NewCatalog() *Catalog {
    // Initialize and return catalog
}

func (c *Catalog) AddProduct(p Product) {
    // Add product to catalog
}

func (c *Catalog) GetProduct(id int) (Product, bool) {
    // Retrieve product by ID
    // Return product and true if found, zero value and false if not found
}

func (c *Catalog) ListProducts() []Product {
    // Return all products as a slice
}

func main() {
    // Test your implementation
}
```

**Solution:**
```go
package main

import "fmt"

type Product struct {
    ID    int
    Name  string
    Price float64
    Stock int
}

type Catalog struct {
    products map[int]Product  // Map for O(1) lookups
}

func NewCatalog() *Catalog {
    return &Catalog{
        products: make(map[int]Product),
    }
}

func (c *Catalog) AddProduct(p Product) {
    c.products[p.ID] = p
}

func (c *Catalog) GetProduct(id int) (Product, bool) {
    product, found := c.products[id]
    return product, found
}

func (c *Catalog) ListProducts() []Product {
    // Convert map to slice
    products := make([]Product, 0, len(c.products))
    for _, product := range c.products {
        products = append(products, product)
    }
    return products
}

func main() {
    catalog := NewCatalog()
    
    // Add products
    catalog.AddProduct(Product{ID: 1001, Name: "Laptop", Price: 999.99, Stock: 10})
    catalog.AddProduct(Product{ID: 1002, Name: "Mouse", Price: 29.99, Stock: 50})
    catalog.AddProduct(Product{ID: 1003, Name: "Keyboard", Price: 79.99, Stock: 25})
    
    // Get specific product
    if product, found := catalog.GetProduct(1001); found {
        fmt.Printf("Found: %s - $%.2f (Stock: %d)\n", 
            product.Name, product.Price, product.Stock)
    }
    
    // List all products
    fmt.Println("\nAll Products:")
    for _, product := range catalog.ListProducts() {
        fmt.Printf("  [%d] %s - $%.2f (Stock: %d)\n", 
            product.ID, product.Name, product.Price, product.Stock)
    }
}
```

**Expected Output:**
```
Found: Laptop - $999.99 (Stock: 10)

All Products:
  [1001] Laptop - $999.99 (Stock: 10)
  [1002] Mouse - $29.99 (Stock: 50)
  [1003] Keyboard - $79.99 (Stock: 25)
```

**Key Learning Points:**
- ✅ When to use map vs slice
- ✅ Map for fast lookups (O(1))
- ✅ Converting map to slice for ordering
- ✅ Struct methods with pointer receivers

---

### Task 2: Medium - Order Processing with Pointer Optimization 🔶

**Level:** Medium

**Description:**
Build an order processing system that efficiently handles large orders using pointers and optimizes memory usage.

**Requirements:**
1. Create `Order` struct with: ID, CustomerID, Items (slice), Total, Status
2. Create `OrderItem` struct with: ProductID, Name, Price, Quantity
3. Implement method to add items to order (pointer receiver)
4. Implement method to calculate total (value receiver for read-only)
5. Implement method to update status (pointer receiver)
6. Use pointers appropriately to avoid unnecessary copying
7. Pre-allocate slices when size is known

**Expected Behavior:**
```go
order := NewOrder(1001, "customer-123")
order.AddItem(OrderItem{ProductID: 101, Name: "Laptop", Price: 999.99, Quantity: 1})
order.AddItem(OrderItem{ProductID: 102, Name: "Mouse", Price: 29.99, Quantity: 2})

total := order.CalculateTotal()  // Should return 1059.97

order.UpdateStatus("shipped")
// Status should change from "pending" to "shipped"
```

**Template:**
```go
package main

import (
    "fmt"
    "time"
)

type OrderItem struct {
    ProductID int
    Name      string
    Price     float64
    Quantity  int
}

type Order struct {
    ID         int
    CustomerID string
    Items      []OrderItem
    Total      float64
    Status     string
    CreatedAt  time.Time
}

func NewOrder(id int, customerID string) *Order {
    // Create and return new order
    // Pre-allocate Items slice with reasonable capacity
}

func (o *Order) AddItem(item OrderItem) {
    // Add item and update total
}

func (o Order) CalculateTotal() float64 {
    // Calculate and return total (read-only, use value receiver)
}

func (o *Order) UpdateStatus(status string) {
    // Update order status
}

func (o Order) PrintSummary() {
    // Print order summary
}

func main() {
    // Test your implementation
}
```

**Solution:**
```go
package main

import (
    "fmt"
    "time"
)

type OrderItem struct {
    ProductID int
    Name      string
    Price     float64
    Quantity  int
}

type Order struct {
    ID         int
    CustomerID string
    Items      []OrderItem
    Total      float64
    Status     string
    CreatedAt  time.Time
}

func NewOrder(id int, customerID string) *Order {
    return &Order{
        ID:         id,
        CustomerID: customerID,
        Items:      make([]OrderItem, 0, 10),  // Pre-allocate
        Status:     "pending",
        CreatedAt:  time.Now(),
    }
}

func (o *Order) AddItem(item OrderItem) {
    o.Items = append(o.Items, item)
    o.Total += item.Price * float64(item.Quantity)
}

func (o Order) CalculateTotal() float64 {
    total := 0.0
    for _, item := range o.Items {
        total += item.Price * float64(item.Quantity)
    }
    return total
}

func (o *Order) UpdateStatus(status string) {
    o.Status = status
}

func (o Order) PrintSummary() {
    fmt.Printf("Order #%d\n", o.ID)
    fmt.Printf("Customer: %s\n", o.CustomerID)
    fmt.Printf("Status: %s\n", o.Status)
    fmt.Printf("Created: %s\n", o.CreatedAt.Format("2006-01-02 15:04:05"))
    fmt.Println("Items:")
    for i, item := range o.Items {
        subtotal := item.Price * float64(item.Quantity)
        fmt.Printf("  %d. %s x%d @ $%.2f = $%.2f\n",
            i+1, item.Name, item.Quantity, item.Price, subtotal)
    }
    fmt.Printf("Total: $%.2f\n", o.Total)
}

func main() {
    // Create order
    order := NewOrder(1001, "customer-123")
    
    // Add items
    order.AddItem(OrderItem{
        ProductID: 101,
        Name:      "Laptop",
        Price:     999.99,
        Quantity:  1,
    })
    order.AddItem(OrderItem{
        ProductID: 102,
        Name:      "Mouse",
        Price:     29.99,
        Quantity:  2,
    })
    order.AddItem(OrderItem{
        ProductID: 103,
        Name:      "Keyboard",
        Price:     79.99,
        Quantity:  1,
    })
    
    // Print summary
    order.PrintSummary()
    
    // Update status
    fmt.Println("\n--- Updating Status ---")
    order.UpdateStatus("processing")
    fmt.Printf("New status: %s\n", order.Status)
    
    // Verify total
    calculatedTotal := order.CalculateTotal()
    fmt.Printf("\nCalculated total: $%.2f\n", calculatedTotal)
    fmt.Printf("Stored total: $%.2f\n", order.Total)
}
```

**Expected Output:**
```
Order #1001
Customer: customer-123
Status: pending
Created: 2024-12-19 10:30:45
Items:
  1. Laptop x1 @ $999.99 = $999.99
  2. Mouse x2 @ $29.99 = $59.98
  3. Keyboard x1 @ $79.99 = $79.99
Total: $1139.96

--- Updating Status ---
New status: processing

Calculated total: $1139.96
Stored total: $1139.96
```

**Key Learning Points:**
- ✅ Pointer receivers for modification
- ✅ Value receivers for read-only operations
- ✅ Pre-allocating slices for performance
- ✅ Proper struct design for e-commerce
- ✅ When to return pointers vs values

---

### Task 3: Hard - High-Performance Batch Processor with GC Optimization 🔴

**Level:** Hard

**Description:**
Build a high-performance batch processor that processes thousands of orders efficiently using buffer pooling, pre-allocation, and GC optimization techniques.

**Requirements:**
1. Process 10,000 orders in batches of 100
2. Use `sync.Pool` to reuse buffers
3. Calculate statistics: total revenue, average order value, max/min order
4. Pre-allocate all slices with known capacity
5. Minimize GC pressure (avoid unnecessary allocations)
6. Track and report memory usage
7. Benchmark against naive implementation

**Template:**
```go
package main

import (
    "fmt"
    "runtime"
    "sync"
    "time"
)

type Order struct {
    ID     int
    Amount float64
}

type Statistics struct {
    TotalRevenue float64
    AverageOrder float64
    MaxOrder     float64
    MinOrder     float64
    OrderCount   int
}

// Buffer pool for reuse
var bufferPool = sync.Pool{
    New: func() interface{} {
        // Create new buffer
    },
}

func getBuffer() []Order {
    // Get buffer from pool
}

func putBuffer(buf []Order) {
    // Return buffer to pool (reset it first)
}

func processBatch(orders []Order) Statistics {
    // Process a batch and return statistics
}

func processOrdersEfficiently(orders []Order, batchSize int) Statistics {
    // Process orders in batches using buffer pool
    // Aggregate statistics from all batches
}

func processOrdersNaive(orders []Order, batchSize int) Statistics {
    // Naive implementation (for comparison)
    // No buffer pooling, lots of allocations
}

func printMemStats(label string) {
    // Print memory statistics
}

func main() {
    // Generate 10,000 orders
    // Run both implementations
    // Compare performance and memory usage
}
```

**Solution:**
```go
package main

import (
    "fmt"
    "math"
    "runtime"
    "sync"
    "time"
)

type Order struct {
    ID     int
    Amount float64
}

type Statistics struct {
    TotalRevenue float64
    AverageOrder float64
    MaxOrder     float64
    MinOrder     float64
    OrderCount   int
}

// Buffer pool for reuse
var bufferPool = sync.Pool{
    New: func() interface{} {
        return make([]Order, 0, 100)
    },
}

func getBuffer() []Order {
    return bufferPool.Get().([]Order)
}

func putBuffer(buf []Order) {
    buf = buf[:0]  // Reset length, keep capacity
    bufferPool.Put(buf)
}

func processBatch(orders []Order) Statistics {
    if len(orders) == 0 {
        return Statistics{}
    }
    
    stats := Statistics{
        MaxOrder: math.Inf(-1),
        MinOrder: math.Inf(1),
    }
    
    for _, order := range orders {
        stats.TotalRevenue += order.Amount
        stats.OrderCount++
        
        if order.Amount > stats.MaxOrder {
            stats.MaxOrder = order.Amount
        }
        if order.Amount < stats.MinOrder {
            stats.MinOrder = order.Amount
        }
    }
    
    stats.AverageOrder = stats.TotalRevenue / float64(stats.OrderCount)
    return stats
}

func mergeStats(s1, s2 Statistics) Statistics {
    if s1.OrderCount == 0 {
        return s2
    }
    if s2.OrderCount == 0 {
        return s1
    }
    
    return Statistics{
        TotalRevenue: s1.TotalRevenue + s2.TotalRevenue,
        OrderCount:   s1.OrderCount + s2.OrderCount,
        MaxOrder:     math.Max(s1.MaxOrder, s2.MaxOrder),
        MinOrder:     math.Min(s1.MinOrder, s2.MinOrder),
        AverageOrder: (s1.TotalRevenue + s2.TotalRevenue) / float64(s1.OrderCount+s2.OrderCount),
    }
}

// ✅ Efficient implementation with buffer pooling
func processOrdersEfficiently(orders []Order, batchSize int) Statistics {
    buffer := getBuffer()
    defer putBuffer(buffer)
    
    var globalStats Statistics
    globalStats.MaxOrder = math.Inf(-1)
    globalStats.MinOrder = math.Inf(1)
    
    for _, order := range orders {
        buffer = append(buffer, order)
        
        if len(buffer) >= batchSize {
            batchStats := processBatch(buffer)
            globalStats = mergeStats(globalStats, batchStats)
            buffer = buffer[:0]  // Reset
        }
    }
    
    // Process remaining
    if len(buffer) > 0 {
        batchStats := processBatch(buffer)
        globalStats = mergeStats(globalStats, batchStats)
    }
    
    return globalStats
}

// ❌ Naive implementation (for comparison)
func processOrdersNaive(orders []Order, batchSize int) Statistics {
    var globalStats Statistics
    globalStats.MaxOrder = math.Inf(-1)
    globalStats.MinOrder = math.Inf(1)
    
    buffer := []Order{}  // No pre-allocation
    
    for _, order := range orders {
        buffer = append(buffer, order)
        
        if len(buffer) >= batchSize {
            batchStats := processBatch(buffer)
            globalStats = mergeStats(globalStats, batchStats)
            buffer = []Order{}  // New allocation each time!
        }
    }
    
    if len(buffer) > 0 {
        batchStats := processBatch(buffer)
        globalStats = mergeStats(globalStats, batchStats)
    }
    
    return globalStats
}

func printMemStats(label string) {
    var m runtime.MemStats
    runtime.ReadMemStats(&m)
    fmt.Printf("\n%s:\n", label)
    fmt.Printf("  Alloc: %d MB\n", m.Alloc/1024/1024)
    fmt.Printf("  TotalAlloc: %d MB\n", m.TotalAlloc/1024/1024)
    fmt.Printf("  Sys: %d MB\n", m.Sys/1024/1024)
    fmt.Printf("  NumGC: %d\n", m.NumGC)
}

func main() {
    // Generate 10,000 orders
    const orderCount = 10000
    orders := make([]Order, orderCount)
    for i := range orders {
        orders[i] = Order{
            ID:     i + 1,
            Amount: float64(50 + (i%100)*10),
        }
    }
    
    fmt.Printf("Processing %d orders...\n", orderCount)
    
    // Warm up GC
    runtime.GC()
    
    // Test efficient implementation
    printMemStats("Before Efficient")
    startEfficient := time.Now()
    statsEfficient := processOrdersEfficiently(orders, 100)
    durationEfficient := time.Since(startEfficient)
    printMemStats("After Efficient")
    
    fmt.Printf("\nEfficient Results:\n")
    fmt.Printf("  Duration: %v\n", durationEfficient)
    fmt.Printf("  Total Revenue: $%.2f\n", statsEfficient.TotalRevenue)
    fmt.Printf("  Average Order: $%.2f\n", statsEfficient.AverageOrder)
    fmt.Printf("  Max Order: $%.2f\n", statsEfficient.MaxOrder)
    fmt.Printf("  Min Order: $%.2f\n", statsEfficient.MinOrder)
    
    // Force GC before naive test
    runtime.GC()
    time.Sleep(100 * time.Millisecond)
    
    // Test naive implementation
    printMemStats("Before Naive")
    startNaive := time.Now()
    statsNaive := processOrdersNaive(orders, 100)
    durationNaive := time.Since(startNaive)
    printMemStats("After Naive")
    
    fmt.Printf("\nNaive Results:\n")
    fmt.Printf("  Duration: %v\n", durationNaive)
    fmt.Printf("  Total Revenue: $%.2f\n", statsNaive.TotalRevenue)
    fmt.Printf("  Average Order: $%.2f\n", statsNaive.AverageOrder)
    fmt.Printf("  Max Order: $%.2f\n", statsNaive.MaxOrder)
    fmt.Printf("  Min Order: $%.2f\n", statsNaive.MinOrder)
    
    // Compare
    fmt.Printf("\n=== Performance Comparison ===\n")
    speedup := float64(durationNaive) / float64(durationEfficient)
    fmt.Printf("Speedup: %.2fx faster\n", speedup)
    
    if durationEfficient < durationNaive {
        fmt.Println("✅ Efficient implementation is faster!")
    } else {
        fmt.Println("⚠️ Naive implementation was faster (unexpected)")
    }
}
```

**Expected Output:**
```
Processing 10000 orders...

Before Efficient:
  Alloc: 2 MB
  TotalAlloc: 2 MB
  Sys: 10 MB
  NumGC: 0

After Efficient:
  Alloc: 2 MB
  TotalAlloc: 3 MB
  Sys: 10 MB
  NumGC: 0

Efficient Results:
  Duration: 1.5ms
  Total Revenue: $5494500.00
  Average Order: $549.45
  Max Order: $1040.00
  Min Order: $50.00

Before Naive:
  Alloc: 2 MB
  TotalAlloc: 3 MB
  Sys: 10 MB
  NumGC: 1

After Naive:
  Alloc: 3 MB
  TotalAlloc: 15 MB
  Sys: 11 MB
  NumGC: 3

Naive Results:
  Duration: 2.8ms
  Total Revenue: $5494500.00
  Average Order: $549.45
  Max Order: $1040.00
  Min Order: $50.00

=== Performance Comparison ===
Speedup: 1.87x faster
✅ Efficient implementation is faster!
```

**Key Learning Points:**
- ✅ Buffer pooling with `sync.Pool`
- ✅ Pre-allocation for performance
- ✅ Reducing GC pressure
- ✅ Memory profiling
- ✅ Benchmarking techniques
- ✅ Batch processing patterns

---

## 🎯 LeetCode Challenge: LRU Cache Implementation 🔥

**Level:** Hard (LeetCode Medium)

**Problem:**
Design a data structure that follows the constraints of a **Least Recently Used (LRU) cache**.

Implement the `LRUCache` class:
- `LRUCache(int capacity)` Initialize the LRU cache with positive size capacity
- `int Get(key int)` Return the value of the key if the key exists, otherwise return -1
- `void Put(key int, value int)` Update the value of the key if exists, otherwise add the key-value pair. When the cache reaches its capacity, it should invalidate the **least recently used** item before inserting a new item.

**Example:**
```go
cache := Constructor(2)  // capacity = 2

cache.Put(1, 1)  // cache: {1=1}
cache.Put(2, 2)  // cache: {1=1, 2=2}
cache.Get(1)     // returns 1, cache: {2=2, 1=1}
cache.Put(3, 3)  // evicts key 2, cache: {1=1, 3=3}
cache.Get(2)     // returns -1 (not found)
cache.Put(4, 4)  // evicts key 1, cache: {3=3, 4=4}
cache.Get(1)     // returns -1 (not found)
cache.Get(3)     // returns 3
cache.Get(4)     // returns 4
```

**Requirements:**
- `Get` and `Put` must run in **O(1)** average time complexity
- Use a combination of **map** and **doubly linked list**

**E-Commerce Context:**
LRU caches are commonly used in e-commerce for:
- Caching product details
- Session management
- API response caching
- Recently viewed items

**Template:**
```go
package main

import "fmt"

type Node struct {
    key   int
    value int
    prev  *Node
    next  *Node
}

type LRUCache struct {
    capacity int
    cache    map[int]*Node
    head     *Node  // Most recently used
    tail     *Node  // Least recently used
}

func Constructor(capacity int) LRUCache {
    // Initialize LRU cache
}

func (this *LRUCache) Get(key int) int {
    // Get value and mark as recently used
}

func (this *LRUCache) Put(key int, value int) {
    // Add/update key-value pair
    // Evict LRU if capacity exceeded
}

// Helper methods
func (this *LRUCache) removeNode(node *Node) {
    // Remove node from linked list
}

func (this *LRUCache) addToHead(node *Node) {
    // Add node to head (most recently used)
}

func (this *LRUCache) moveToHead(node *Node) {
    // Move existing node to head
}

func (this *LRUCache) removeTail() *Node {
    // Remove and return tail node (LRU)
}

func main() {
    // Test your implementation
}
```

**Solution:**
```go
package main

import "fmt"

type Node struct {
    key   int
    value int
    prev  *Node
    next  *Node
}

type LRUCache struct {
    capacity int
    cache    map[int]*Node
    head     *Node  // Dummy head
    tail     *Node  // Dummy tail
}

func Constructor(capacity int) LRUCache {
    lru := LRUCache{
        capacity: capacity,
        cache:    make(map[int]*Node),
        head:     &Node{},
        tail:     &Node{},
    }
    lru.head.next = lru.tail
    lru.tail.prev = lru.head
    return lru
}

func (this *LRUCache) Get(key int) int {
    if node, exists := this.cache[key]; exists {
        // Move to head (mark as recently used)
        this.moveToHead(node)
        return node.value
    }
    return -1
}

func (this *LRUCache) Put(key int, value int) {
    if node, exists := this.cache[key]; exists {
        // Update existing node
        node.value = value
        this.moveToHead(node)
    } else {
        // Create new node
        newNode := &Node{key: key, value: value}
        this.cache[key] = newNode
        this.addToHead(newNode)
        
        // Check capacity
        if len(this.cache) > this.capacity {
            // Remove LRU
            removed := this.removeTail()
            delete(this.cache, removed.key)
        }
    }
}

func (this *LRUCache) removeNode(node *Node) {
    node.prev.next = node.next
    node.next.prev = node.prev
}

func (this *LRUCache) addToHead(node *Node) {
    node.prev = this.head
    node.next = this.head.next
    this.head.next.prev = node
    this.head.next = node
}

func (this *LRUCache) moveToHead(node *Node) {
    this.removeNode(node)
    this.addToHead(node)
}

func (this *LRUCache) removeTail() *Node {
    node := this.tail.prev
    this.removeNode(node)
    return node
}

// Helper method to print cache state
func (this *LRUCache) Print() {
    fmt.Print("Cache (MRU -> LRU): ")
    current := this.head.next
    for current != this.tail {
        fmt.Printf("[%d=%d] ", current.key, current.value)
        current = current.next
    }
    fmt.Println()
}

func main() {
    cache := Constructor(2)
    
    fmt.Println("=== LRU Cache Test ===\n")
    
    cache.Put(1, 1)
    fmt.Println("Put(1, 1)")
    cache.Print()
    
    cache.Put(2, 2)
    fmt.Println("Put(2, 2)")
    cache.Print()
    
    result := cache.Get(1)
    fmt.Printf("Get(1) = %d\n", result)
    cache.Print()
    
    cache.Put(3, 3)
    fmt.Println("Put(3, 3) - evicts key 2")
    cache.Print()
    
    result = cache.Get(2)
    fmt.Printf("Get(2) = %d (not found)\n", result)
    
    cache.Put(4, 4)
    fmt.Println("Put(4, 4) - evicts key 1")
    cache.Print()
    
    result = cache.Get(1)
    fmt.Printf("Get(1) = %d (not found)\n", result)
    
    result = cache.Get(3)
    fmt.Printf("Get(3) = %d\n", result)
    cache.Print()
    
    result = cache.Get(4)
    fmt.Printf("Get(4) = %d\n", result)
    cache.Print()
    
    // E-Commerce Use Case
    fmt.Println("\n=== E-Commerce Product Cache ===\n")
    productCache := Constructor(3)
    
    // Cache product details
    productCache.Put(1001, 99999)   // Laptop: $999.99
    productCache.Put(1002, 2999)    // Mouse: $29.99
    productCache.Put(1003, 7999)    // Keyboard: $79.99
    
    fmt.Println("Initial cache (3 products):")
    productCache.Print()
    
    // Access product (moves to front)
    price := productCache.Get(1001)
    fmt.Printf("Get product 1001 price: $%.2f\n", float64(price)/100)
    productCache.Print()
    
    // Add new product (evicts LRU)
    productCache.Put(1004, 29999)   // Monitor: $299.99
    fmt.Println("Add product 1004 (evicts 1002 - Mouse):")
    productCache.Print()
}
```

**Expected Output:**
```
=== LRU Cache Test ===

Put(1, 1)
Cache (MRU -> LRU): [1=1] 
Put(2, 2)
Cache (MRU -> LRU): [2=2] [1=1] 
Get(1) = 1
Cache (MRU -> LRU): [1=1] [2=2] 
Put(3, 3) - evicts key 2
Cache (MRU -> LRU): [3=3] [1=1] 
Get(2) = -1 (not found)
Put(4, 4) - evicts key 1
Cache (MRU -> LRU): [4=4] [3=3] 
Get(1) = -1 (not found)
Get(3) = 3
Cache (MRU -> LRU): [3=3] [4=4] 
Get(4) = 4
Cache (MRU -> LRU): [4=4] [3=3] 

=== E-Commerce Product Cache ===

Initial cache (3 products):
Cache (MRU -> LRU): [1003=7999] [1002=2999] [1001=99999] 
Get product 1001 price: $999.99
Cache (MRU -> LRU): [1001=99999] [1003=7999] [1002=2999] 
Add product 1004 (evicts 1002 - Mouse):
Cache (MRU -> LRU): [1004=29999] [1001=99999] [1003=7999] 
```

**Complexity Analysis:**
- **Time Complexity:** O(1) for both Get and Put operations
- **Space Complexity:** O(capacity) for storing the cache

**Key Concepts Used:**
- ✅ Map for O(1) lookup
- ✅ Doubly linked list for O(1) add/remove
- ✅ Pointers for efficient node manipulation
- ✅ Dummy head/tail nodes (simplifies edge cases)

**Why This Problem Is Important:**
1. **Real-world application** - Used in production systems
2. **Combines data structures** - Map + Linked List
3. **Tests pointer knowledge** - Heavy use of pointers
4. **Performance critical** - O(1) requirement
5. **Common interview question** - Asked by FAANG companies

---

## 🎉 Congratulations!

You've completed all tasks and challenges! You now have:

✅ Solid understanding of Go fundamentals  
✅ Practical experience with real-world scenarios  
✅ Performance optimization skills  
✅ Data structure implementation expertise  
✅ LeetCode problem-solving abilities  

**Keep practicing and building amazing Go applications! 🚀**

---
---

# 🎨 Chapter 7: Types, Methods, and Interfaces

![Types and Interfaces](https://images.unsplash.com/photo-1558618666-fcd25c85cd64?auto=format&fit=crop&w=600&q=80)

### 📚 Table of Contents for This Chapter
1. **Section 1: Type Declarations**
2. **Section 2: Methods**
3. **Section 3: Interfaces - The Heart of Go**
4. **Section 4: Type Assertions and Type Switches**
5. **Section 5: Advanced Interface Patterns**
6. **Section 6: Dependency Injection and Wire**
7. **Section 7: Object-Orientation in Go**
8. **Exercises and Challenges**
9. **Wrapping Up**

---

## 📖 Section 1: Type Declarations

### 1.1 Creating Custom Types 🏗️

**What is a Type Declaration?**
- Creates a new named type from an existing type
- Adds semantic meaning to your code
- Enables method attachment
- Provides type safety

**Basic Syntax:**
```go
type TypeName UnderlyingType
```

**Examples:**
```go
package main

import "fmt"

// Custom types based on primitives
type UserID int
type ProductID int
type Email string
type Age int

// Custom types based on structs
type Person struct {
    Name  string
    Email Email
    Age   Age
}

// Custom types based on slices
type OrderIDs []int
type Tags []string

// Custom types based on maps
type Inventory map[ProductID]int
type UserSessions map[UserID]string

func main() {
    // Type safety in action
    var userID UserID = 1001
    var productID ProductID = 5001
    
    // ❌ Cannot mix types (even though both are int)
    // userID = productID  // Compile error!
    
    // ✅ Must convert explicitly
    userID = UserID(productID)  // OK
    
    fmt.Printf("User ID: %d\n", userID)
}
```

### 1.2 Why Use Custom Types? 🤔

**1. Type Safety:**
```go
// ❌ Without custom types (error-prone)
func GetUserOrders(id int) []Order {
    // Is this a user ID or product ID? Not clear!
}

func GetProductDetails(id int) Product {
    // Same problem!
}

// ✅ With custom types (clear and safe)
type UserID int
type ProductID int

func GetUserOrders(userID UserID) []Order {
    // Clear: expects user ID
}

func GetProductDetails(productID ProductID) Product {
    // Clear: expects product ID
}

// Can't mix them up!
// GetUserOrders(productID)  // Compile error! ✅
```

**2. Semantic Clarity:**
```go
// ❌ Unclear what the int represents
func ProcessPayment(amount int) error {
    // Is this in cents? dollars? yen?
}

// ✅ Clear with custom types
type Cents int

func ProcessPayment(amount Cents) error {
    // Clear: amount is in cents
}

func main() {
    price := Cents(9999)  // $99.99
    ProcessPayment(price)
}
```

**3. Method Attachment:**
```go
type Money int  // cents

// Can add methods to custom types
func (m Money) Dollars() float64 {
    return float64(m) / 100
}

func (m Money) String() string {
    return fmt.Sprintf("$%.2f", m.Dollars())
}

func main() {
    price := Money(9999)
    fmt.Println(price)              // $99.99
    fmt.Println(price.Dollars())    // 99.99
}
```

### 1.3 E-Commerce Type System Example 🛒

**Complete Type System:**
```go
package main

import (
    "fmt"
    "time"
)

// Domain types
type UserID int
type ProductID int
type OrderID int
type CategoryID int

// Value objects
type Email string
type Money int  // cents
type Quantity int

// Status types
type OrderStatus string

const (
    OrderPending    OrderStatus = "pending"
    OrderConfirmed  OrderStatus = "confirmed"
    OrderShipped    OrderStatus = "shipped"
    OrderDelivered  OrderStatus = "delivered"
    OrderCancelled  OrderStatus = "cancelled"
)

// Entity types
type User struct {
    ID        UserID
    Email     Email
    Name      string
    CreatedAt time.Time
}

type Product struct {
    ID          ProductID
    Name        string
    Description string
    Price       Money
    CategoryID  CategoryID
    Stock       Quantity
}

type OrderItem struct {
    ProductID ProductID
    Quantity  Quantity
    Price     Money
}

type Order struct {
    ID         OrderID
    UserID     UserID
    Items      []OrderItem
    Total      Money
    Status     OrderStatus
    CreatedAt  time.Time
}

// Methods on custom types
func (m Money) Dollars() float64 {
    return float64(m) / 100
}

func (m Money) String() string {
    return fmt.Sprintf("$%.2f", m.Dollars())
}

func (e Email) IsValid() bool {
    return len(e) > 3 && contains(string(e), "@")
}

func contains(s, substr string) bool {
    return len(s) > 0 && len(substr) > 0
}

func (o Order) CalculateTotal() Money {
    total := Money(0)
    for _, item := range o.Items {
        total += item.Price * Money(item.Quantity)
    }
    return total
}

func (o Order) CanBeCancelled() bool {
    return o.Status == OrderPending || o.Status == OrderConfirmed
}

func main() {
    // Create entities with type safety
    user := User{
        ID:    UserID(1001),
        Email: Email("alice@example.com"),
        Name:  "Alice",
    }
    
    product := Product{
        ID:    ProductID(5001),
        Name:  "Laptop",
        Price: Money(99999),  // $999.99
        Stock: Quantity(10),
    }
    
    order := Order{
        ID:     OrderID(8001),
        UserID: user.ID,
        Items: []OrderItem{
            {ProductID: product.ID, Quantity: 1, Price: product.Price},
        },
        Status: OrderPending,
    }
    
    // Use methods
    fmt.Printf("Product price: %s\n", product.Price)
    fmt.Printf("Order total: %s\n", order.CalculateTotal())
    fmt.Printf("Can cancel: %t\n", order.CanBeCancelled())
    
    // Type safety prevents errors
    // var wrongID ProductID = user.ID  // ❌ Compile error!
}
```

---

## 📖 Section 2: Methods

### 2.1 Understanding Methods 🎯

**What is a Method?**
- Function associated with a type
- Has a **receiver** parameter
- Can be called using dot notation

**Syntax:**
```go
func (receiver Type) MethodName(parameters) returnType {
    // method body
}
```

**Example:**
```go
type Rectangle struct {
    Width  float64
    Height float64
}

// Method with value receiver
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Method with pointer receiver
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

func main() {
    rect := Rectangle{Width: 10, Height: 5}
    
    area := rect.Area()       // 50
    fmt.Println(area)
    
    rect.Scale(2)             // Modifies rect
    fmt.Println(rect.Width)   // 20
}
```

### 2.2 Value Receivers vs Pointer Receivers 🔄

**Value Receiver (Copy):**
```go
func (r Rectangle) ValueMethod() {
    r.Width = 999  // Only modifies the copy
}

func main() {
    rect := Rectangle{Width: 10, Height: 5}
    rect.ValueMethod()
    fmt.Println(rect.Width)  // Still 10 (unchanged)
}
```

**Pointer Receiver (Modify Original):**
```go
func (r *Rectangle) PointerMethod() {
    r.Width = 999  // Modifies the original
}

func main() {
    rect := Rectangle{Width: 10, Height: 5}
    rect.PointerMethod()
    fmt.Println(rect.Width)  // 999 (changed)
}
```

**When to Use Each:**

| Use Value Receiver | Use Pointer Receiver |
|-------------------|---------------------|
| Small structs (<100 bytes) | Large structs |
| Immutable operations | Need to modify receiver |
| No side effects | Implement interfaces |
| Read-only methods | Performance critical |

**E-Commerce Example:**
```go
package main

import "fmt"

type ShoppingCart struct {
    Items []CartItem
    Total float64
}

type CartItem struct {
    ProductID int
    Name      string
    Price     float64
    Quantity  int
}

// ✅ Value receiver: Read-only, doesn't modify
func (c ShoppingCart) GetItemCount() int {
    return len(c.Items)
}

// ✅ Value receiver: Read-only calculation
func (c ShoppingCart) GetTotal() float64 {
    total := 0.0
    for _, item := range c.Items {
        total += item.Price * float64(item.Quantity)
    }
    return total
}

// ✅ Pointer receiver: Modifies cart
func (c *ShoppingCart) AddItem(item CartItem) {
    c.Items = append(c.Items, item)
    c.Total += item.Price * float64(item.Quantity)
}

// ✅ Pointer receiver: Modifies cart
func (c *ShoppingCart) RemoveItem(productID int) bool {
    for i, item := range c.Items {
        if item.ProductID == productID {
            c.Total -= item.Price * float64(item.Quantity)
            c.Items = append(c.Items[:i], c.Items[i+1:]...)
            return true
        }
    }
    return false
}

// ✅ Pointer receiver: Modifies cart
func (c *ShoppingCart) Clear() {
    c.Items = []CartItem{}
    c.Total = 0
}

// ✅ Value receiver: Returns new cart (doesn't modify)
func (c ShoppingCart) ApplyDiscount(percent float64) ShoppingCart {
    discountedCart := c  // Copy
    discountedCart.Total = c.Total * (1 - percent/100)
    return discountedCart
}

func main() {
    cart := ShoppingCart{}
    
    cart.AddItem(CartItem{1001, "Laptop", 999.99, 1})
    cart.AddItem(CartItem{1002, "Mouse", 29.99, 2})
    
    fmt.Printf("Items: %d\n", cart.GetItemCount())
    fmt.Printf("Total: $%.2f\n", cart.GetTotal())
    
    // Apply discount (returns new cart)
    discounted := cart.ApplyDiscount(10)
    fmt.Printf("Discounted: $%.2f\n", discounted.Total)
    fmt.Printf("Original: $%.2f\n", cart.Total)  // Unchanged
}
```

### 2.3 Methods on nil 🔍

**Go allows calling methods on nil receivers!**

```go
package main

import "fmt"

type IntList struct {
    Value int
    Next  *IntList
}

// ✅ Safe method that handles nil receiver
func (list *IntList) Sum() int {
    if list == nil {
        return 0  // Handle nil gracefully
    }
    return list.Value + list.Next.Sum()  // Recursive
}

// ✅ Safe method
func (list *IntList) Length() int {
    if list == nil {
        return 0
    }
    return 1 + list.Next.Length()
}

func main() {
    var list *IntList  // nil
    
    // These work! No panic!
    fmt.Println(list.Sum())     // 0
    fmt.Println(list.Length())  // 0
    
    // Create actual list
    list = &IntList{Value: 1, Next: &IntList{Value: 2, Next: nil}}
    fmt.Println(list.Sum())     // 3
    fmt.Println(list.Length())  // 2
}
```

**E-Commerce Example: Safe Order Operations:**
```go
package main

import "fmt"

type Order struct {
    ID    int
    Items []string
    Total float64
}

// ✅ Safe method handles nil
func (o *Order) IsEmpty() bool {
    if o == nil {
        return true
    }
    return len(o.Items) == 0
}

// ✅ Safe method handles nil
func (o *Order) GetTotal() float64 {
    if o == nil {
        return 0.0
    }
    return o.Total
}

// ✅ Safe method handles nil
func (o *Order) String() string {
    if o == nil {
        return "No order"
    }
    return fmt.Sprintf("Order #%d: $%.2f", o.ID, o.Total)
}

func main() {
    var order *Order  // nil
    
    fmt.Println(order.IsEmpty())   // true (no panic!)
    fmt.Println(order.GetTotal())  // 0.0 (no panic!)
    fmt.Println(order)             // No order (no panic!)
    
    order = &Order{ID: 1001, Total: 99.99}
    fmt.Println(order)  // Order #1001: $99.99
}
```

### 2.4 Methods vs Functions 📊

**Key Differences:**

```go
// Function
func CalculateArea(width, height float64) float64 {
    return width * height
}

// Method
type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Usage comparison
func main() {
    width, height := 10.0, 5.0
    
    // Function: Pass data as parameters
    area1 := CalculateArea(width, height)
    
    // Method: Call on object
    rect := Rectangle{Width: width, Height: height}
    area2 := rect.Area()
    
    fmt.Println(area1, area2)  // Both are 50
}
```

**When to Use Methods vs Functions:**

| Use Methods | Use Functions |
|------------|--------------|
| Operation on specific type | Generic utility |
| Implementing interfaces | Package-level operations |
| Object behavior | Data transformation |
| Type-specific logic | Cross-type operations |

**E-Commerce Example:**
```go
package main

import (
    "fmt"
    "time"
)

type Order struct {
    ID        int
    Total     float64
    CreatedAt time.Time
}

// ✅ Method: Specific to Order
func (o Order) Age() time.Duration {
    return time.Since(o.CreatedAt)
}

// ✅ Method: Specific to Order
func (o Order) IsRecent() bool {
    return o.Age() < 24*time.Hour
}

// ✅ Function: Works with multiple orders
func CalculateTotalRevenue(orders []Order) float64 {
    total := 0.0
    for _, order := range orders {
        total += order.Total
    }
    return total
}

// ✅ Function: Generic utility
func FilterRecentOrders(orders []Order) []Order {
    recent := []Order{}
    for _, order := range orders {
        if order.IsRecent() {  // Uses method
            recent = append(recent, order)
        }
    }
    return recent
}

func main() {
    orders := []Order{
        {1001, 99.99, time.Now().Add(-2 * time.Hour)},
        {1002, 149.99, time.Now().Add(-30 * time.Hour)},
    }
    
    // Use method
    fmt.Println("Order 1 is recent:", orders[0].IsRecent())
    
    // Use function
    revenue := CalculateTotalRevenue(orders)
    fmt.Printf("Total revenue: $%.2f\n", revenue)
    
    recent := FilterRecentOrders(orders)
    fmt.Printf("Recent orders: %d\n", len(recent))
}
```

### 2.5 Composition over Inheritance 🧩

**Go doesn't have inheritance, it has composition!**

**Embedding Types:**
```go
package main

import "fmt"

// Base types
type Address struct {
    Street  string
    City    string
    ZipCode string
}

type Contact struct {
    Email string
    Phone string
}

// Composed type
type Customer struct {
    ID      int
    Name    string
    Address Address  // Has-a relationship
    Contact Contact  // Has-a relationship
}

// Anonymous embedding (composition)
type VIPCustomer struct {
    Customer          // Embedded (promoted fields)
    LoyaltyPoints int
    DiscountPercent float64
}

func main() {
    vip := VIPCustomer{
        Customer: Customer{
            ID:   1001,
            Name: "Alice",
            Address: Address{
                Street: "123 Main St",
                City:   "New York",
            },
            Contact: Contact{
                Email: "alice@example.com",
            },
        },
        LoyaltyPoints: 5000,
        DiscountPercent: 20,
    }
    
    // Can access embedded fields directly!
    fmt.Println(vip.Name)           // Alice (promoted from Customer)
    fmt.Println(vip.Email)          // alice@example.com (promoted from Contact)
    fmt.Println(vip.LoyaltyPoints)  // 5000
}
```

**Methods with Composition:**
```go
package main

import "fmt"

type Product struct {
    ID    int
    Name  string
    Price float64
}

// Method on Product
func (p Product) String() string {
    return fmt.Sprintf("%s ($%.2f)", p.Name, p.Price)
}

// Composed type
type DiscountedProduct struct {
    Product           // Embed Product
    DiscountPercent float64
}

// Override/extend behavior
func (dp DiscountedProduct) FinalPrice() float64 {
    return dp.Price * (1 - dp.DiscountPercent/100)
}

// String method is automatically promoted from Product!

func main() {
    dp := DiscountedProduct{
        Product: Product{
            ID:    1001,
            Name:  "Laptop",
            Price: 999.99,
        },
        DiscountPercent: 10,
    }
    
    // Promoted method from Product
    fmt.Println(dp.String())      // Laptop ($999.99)
    
    // Own method
    fmt.Printf("Final: $%.2f\n", dp.FinalPrice())  // $899.99
}
```

**Complete E-Commerce Composition Example:**
```go
package main

import (
    "fmt"
    "time"
)

// Base components
type Auditable struct {
    CreatedAt time.Time
    UpdatedAt time.Time
    CreatedBy string
}

func (a *Auditable) Touch() {
    a.UpdatedAt = time.Now()
}

type Identifiable struct {
    ID int
}

func (i Identifiable) GetID() int {
    return i.ID
}

// Domain entities using composition
type Product struct {
    Identifiable          // Has ID
    Auditable            // Has timestamps
    Name         string
    Price        float64
    Stock        int
}

type Order struct {
    Identifiable          // Has ID
    Auditable            // Has timestamps
    CustomerID   int
    Items        []OrderItem
    Total        float64
}

type OrderItem struct {
    ProductID int
    Quantity  int
    Price     float64
}

func (o *Order) AddItem(item OrderItem) {
    o.Items = append(o.Items, item)
    o.Total += item.Price * float64(item.Quantity)
    o.Touch()  // Update timestamp (from Auditable)
}

func main() {
    product := Product{
        Identifiable: Identifiable{ID: 1001},
        Auditable:    Auditable{CreatedAt: time.Now(), CreatedBy: "admin"},
        Name:         "Laptop",
        Price:        999.99,
        Stock:        10,
    }
    
    order := Order{
        Identifiable: Identifiable{ID: 5001},
        Auditable:    Auditable{CreatedAt: time.Now(), CreatedBy: "system"},
        CustomerID:   2001,
    }
    
    order.AddItem(OrderItem{
        ProductID: product.GetID(),  // Use promoted method
        Quantity:  1,
        Price:     product.Price,
    })
    
    fmt.Printf("Order #%d\n", order.GetID())
    fmt.Printf("Created: %s\n", order.CreatedAt.Format("2006-01-02"))
    fmt.Printf("Updated: %s\n", order.UpdatedAt.Format("2006-01-02"))
    fmt.Printf("Total: $%.2f\n", order.Total)
}
```

---

## 📖 Section 3: Interfaces - The Heart of Go ❤️

### 3.1 What is an Interface? 🎭

**Definition:**
- Contract that specifies behavior (methods)
- **Implicitly** satisfied (no `implements` keyword!)
- Enables polymorphism
- Core to Go's design philosophy

**Basic Syntax:**
```go
type InterfaceName interface {
    Method1(parameters) returnType
    Method2(parameters) returnType
}
```

**Simple Example:**
```go
package main

import "fmt"

// Define interface
type Speaker interface {
    Speak() string
}

// Types that implement Speaker
type Dog struct {
    Name string
}

func (d Dog) Speak() string {
    return "Woof!"
}

type Cat struct {
    Name string
}

func (c Cat) Speak() string {
    return "Meow!"
}

// Function that accepts interface
func MakeSound(s Speaker) {
    fmt.Println(s.Speak())
}

func main() {
    dog := Dog{Name: "Buddy"}
    cat := Cat{Name: "Whiskers"}
    
    // Polymorphism!
    MakeSound(dog)  // Woof!
    MakeSound(cat)  // Meow!
}
```

### 3.2 Duck Typing 🦆

**"If it walks like a duck and quacks like a duck, it's a duck"**

```go
package main

import "fmt"

// Interface
type Swimmer interface {
    Swim() string
}

// Different types, all can swim
type Fish struct{}
func (f Fish) Swim() string { return "Fish swimming" }

type Duck struct{}
func (d Duck) Swim() string { return "Duck swimming" }

type Person struct {
    Name string
}
func (p Person) Swim() string {
    return p.Name + " is swimming"
}

// Function doesn't care about concrete type
func StartSwimming(s Swimmer) {
    fmt.Println(s.Swim())
}

func main() {
    // All automatically implement Swimmer!
    StartSwimming(Fish{})
    StartSwimming(Duck{})
    StartSwimming(Person{"Alice"})
}
```

### 3.3 Accept Interfaces, Return Structs 📥📤

**Golden Rule:**
- **Accept interfaces** → Maximum flexibility
- **Return concrete types** → Clear contracts

```go
package main

import (
    "fmt"
    "io"
    "strings"
)

// ❌ Bad: Function accepts concrete type
func ProcessFileBad(file *os.File) error {
    // Only works with files!
}

// ✅ Good: Function accepts interface
func ProcessGood(r io.Reader) error {
    // Works with files, strings, network, anything!
    data, err := io.ReadAll(r)
    if err != nil {
        return err
    }
    fmt.Println(string(data))
    return nil
}

// ✅ Good: Return concrete type
func NewReader(s string) *strings.Reader {
    return strings.NewReader(s)
}

func main() {
    // Can use with string
    r := NewReader("Hello, World!")
    ProcessGood(r)
    
    // Can use with file (if we had one)
    // file, _ := os.Open("data.txt")
    // ProcessGood(file)
}
```

**E-Commerce Example:**
```go
package main

import "fmt"

// Interface for payment methods
type PaymentProcessor interface {
    ProcessPayment(amount float64) error
    GetName() string
}

// Concrete implementations
type CreditCard struct {
    CardNumber string
    CVV        string
}

func (cc CreditCard) ProcessPayment(amount float64) error {
    fmt.Printf("Processing $%.2f with credit card\n", amount)
    return nil
}

func (cc CreditCard) GetName() string {
    return "Credit Card"
}

type PayPal struct {
    Email string
}

func (pp PayPal) ProcessPayment(amount float64) error {
    fmt.Printf("Processing $%.2f with PayPal\n", amount)
    return nil
}

func (pp PayPal) GetName() string {
    return "PayPal"
}

// ✅ Accept interface (flexible)
func Checkout(processor PaymentProcessor, amount float64) error {
    fmt.Printf("Using %s for payment\n", processor.GetName())
    return processor.ProcessPayment(amount)
}

// ✅ Return concrete type (clear)
func NewCreditCardProcessor(cardNumber, cvv string) CreditCard {
    return CreditCard{
        CardNumber: cardNumber,
        CVV:        cvv,
    }
}

func main() {
    cc := NewCreditCardProcessor("1234-5678", "123")
    pp := PayPal{Email: "user@example.com"}
    
    Checkout(cc, 99.99)
    Checkout(pp, 149.99)
}
```

### 3.4 Interface Embedding 🪆

**Interfaces can embed other interfaces:**

```go
package main

import "fmt"

// Small interfaces
type Reader interface {
    Read() string
}

type Writer interface {
    Write(data string) error
}

type Closer interface {
    Close() error
}

// Composed interface
type ReadWriteCloser interface {
    Reader
    Writer
    Closer
}

// Implementation
type File struct {
    data string
}

func (f *File) Read() string {
    return f.data
}

func (f *File) Write(data string) error {
    f.data += data
    return nil
}

func (f *File) Close() error {
    fmt.Println("File closed")
    return nil
}

func main() {
    file := &File{}
    
    // file implements ReadWriteCloser automatically!
    var rwc ReadWriteCloser = file
    
    rwc.Write("Hello")
    fmt.Println(rwc.Read())
    rwc.Close()
}
```

**E-Commerce Example:**
```go
package main

import "fmt"

// Base interfaces
type Priceable interface {
    GetPrice() float64
}

type Stockable interface {
    GetStock() int
    UpdateStock(delta int) error
}

type Describable interface {
    GetName() string
    GetDescription() string
}

// Composed interface
type Product interface {
    Priceable
    Stockable
    Describable
}

// Implementation
type Laptop struct {
    name        string
    description string
    price       float64
    stock       int
}

func (l Laptop) GetPrice() float64 {
    return l.price
}

func (l Laptop) GetName() string {
    return l.name
}

func (l Laptop) GetDescription() string {
    return l.description
}

func (l Laptop) GetStock() int {
    return l.stock
}

func (l *Laptop) UpdateStock(delta int) error {
    l.stock += delta
    return nil
}

// Function that works with Product interface
func DisplayProduct(p Product) {
    fmt.Printf("Product: %s\n", p.GetName())
    fmt.Printf("Price: $%.2f\n", p.GetPrice())
    fmt.Printf("Stock: %d\n", p.GetStock())
}

func main() {
    laptop := &Laptop{
        name:        "Gaming Laptop",
        description: "High-end gaming laptop",
        price:       1299.99,
        stock:       5,
    }
    
    // laptop implements Product interface!
    DisplayProduct(laptop)
}
```

### 3.5 Empty Interface and `any` 🌐

**The empty interface accepts any type:**

```go
package main

import "fmt"

// Before Go 1.18
func PrintAnything(value interface{}) {
    fmt.Println(value)
}

// Go 1.18+: any is alias for interface{}
func PrintAnything2(value any) {
    fmt.Println(value)
}

func main() {
    PrintAnything(42)
    PrintAnything("hello")
    PrintAnything(3.14)
    PrintAnything([]int{1, 2, 3})
    
    // Store different types
    var values []any = []any{
        42,
        "hello",
        true,
        struct{ Name string }{"Alice"},
    }
    
    for _, v := range values {
        fmt.Printf("%T: %v\n", v, v)
    }
}
```

**E-Commerce JSON Response:**
```go
package main

import (
    "encoding/json"
    "fmt"
)

type Response struct {
    Success bool   `json:"success"`
    Message string `json:"message"`
    Data    any    `json:"data"`  // Can be anything!
}

func main() {
    // Different data types
    resp1 := Response{
        Success: true,
        Message: "Product found",
        Data: map[string]any{
            "id":    1001,
            "name":  "Laptop",
            "price": 999.99,
        },
    }
    
    resp2 := Response{
        Success: true,
        Message: "Orders retrieved",
        Data: []int{1001, 1002, 1003},
    }
    
    json1, _ := json.Marshal(resp1)
    json2, _ := json.Marshal(resp2)
    
    fmt.Println(string(json1))
    fmt.Println(string(json2))
}
```

### 3.6 nil Interfaces and Comparison ⚠️

**Important: nil interface vs nil value**

```go
package main

import "fmt"

type Speaker interface {
    Speak() string
}

type Dog struct {
    Name string
}

func (d *Dog) Speak() string {
    if d == nil {
        return "..."
    }
    return "Woof!"
}

func main() {
    // Case 1: nil interface
    var s1 Speaker  // nil interface
    fmt.Println(s1 == nil)  // true
    
    // Case 2: interface with nil value
    var dog *Dog  // nil pointer
    var s2 Speaker = dog  // interface with nil value
    fmt.Println(s2 == nil)  // false! (has type *Dog)
    
    // But can still call methods!
    fmt.Println(s2.Speak())  // ... (nil-safe method)
    
    // Comparison
    fmt.Printf("s1 == nil: %t\n", s1 == nil)  // true
    fmt.Printf("s2 == nil: %t\n", s2 == nil)  // false!
}
```

**Safe Pattern:**
```go
func IsNil(i any) bool {
    if i == nil {
        return true
    }
    
    // Use reflection to check if value is nil
    v := reflect.ValueOf(i)
    switch v.Kind() {
    case reflect.Ptr, reflect.Interface, reflect.Slice, reflect.Map, reflect.Chan, reflect.Func:
        return v.IsNil()
    }
    
    return false
}
```

---

## 📖 Section 4: Type Assertions and Type Switches

### 4.1 Type Assertions 🎯

**Extract concrete type from interface:**

**Syntax:**
```go
concreteValue, ok := interfaceValue.(ConcreteType)
```

**Examples:**
```go
package main

import "fmt"

func main() {
    var i any = "hello"
    
    // Type assertion
    s, ok := i.(string)
    if ok {
        fmt.Println("String:", s)  // String: hello
    }
    
    // This would panic!
    // n := i.(int)  // panic: interface conversion
    
    // Safe version
    n, ok := i.(int)
    if !ok {
        fmt.Println("Not an int")
    }
}
```

**E-Commerce Example:**
```go
package main

import "fmt"

type PaymentMethod interface {
    Process(amount float64) error
}

type CreditCard struct {
    Last4 string
}

func (cc CreditCard) Process(amount float64) error {
    fmt.Printf("Charging $%.2f to card ****%s\n", amount, cc.Last4)
    return nil
}

type PayPal struct {
    Email string
}

func (pp PayPal) Process(amount float64) error {
    fmt.Printf("Charging $%.2f to PayPal %s\n", amount, pp.Email)
    return nil
}

func ProcessPayment(pm PaymentMethod, amount float64) {
    // Process payment
    pm.Process(amount)
    
    // Get extra info using type assertion
    if cc, ok := pm.(CreditCard); ok {
        fmt.Println("  Card ending in:", cc.Last4)
    } else if pp, ok := pm.(PayPal); ok {
        fmt.Println("  PayPal account:", pp.Email)
    }
}

func main() {
    cc := CreditCard{Last4: "1234"}
    pp := PayPal{Email: "user@example.com"}
    
    ProcessPayment(cc, 99.99)
    fmt.Println()
    ProcessPayment(pp, 149.99)
}
```

### 4.2 Type Switches 🔀

**Handle multiple types elegantly:**

**Syntax:**
```go
switch v := value.(type) {
case Type1:
    // v is Type1
case Type2:
    // v is Type2
default:
    // v is original type
}
```

**Example:**
```go
package main

import "fmt"

func Describe(i any) {
    switch v := i.(type) {
    case int:
        fmt.Printf("Integer: %d\n", v)
    case string:
        fmt.Printf("String: %s (length: %d)\n", v, len(v))
    case bool:
        fmt.Printf("Boolean: %t\n", v)
    case []int:
        fmt.Printf("Int slice: %v (length: %d)\n", v, len(v))
    default:
        fmt.Printf("Unknown type: %T\n", v)
    }
}

func main() {
    Describe(42)
    Describe("hello")
    Describe(true)
    Describe([]int{1, 2, 3})
    Describe(3.14)
}
```

**E-Commerce Response Handler:**
```go
package main

import (
    "fmt"
    "time"
)

type APIResponse struct {
    Data any
}

type Product struct {
    ID    int
    Name  string
    Price float64
}

type Order struct {
    ID        int
    Total     float64
    CreatedAt time.Time
}

type ErrorResponse struct {
    Code    int
    Message string
}

func HandleResponse(resp APIResponse) {
    switch data := resp.Data.(type) {
    case Product:
        fmt.Printf("Product Response:\n")
        fmt.Printf("  ID: %d\n", data.ID)
        fmt.Printf("  Name: %s\n", data.Name)
        fmt.Printf("  Price: $%.2f\n", data.Price)
        
    case Order:
        fmt.Printf("Order Response:\n")
        fmt.Printf("  ID: %d\n", data.ID)
        fmt.Printf("  Total: $%.2f\n", data.Total)
        fmt.Printf("  Created: %s\n", data.CreatedAt.Format("2006-01-02"))
        
    case []Product:
        fmt.Printf("Product List: %d products\n", len(data))
        for _, p := range data {
            fmt.Printf("  - %s: $%.2f\n", p.Name, p.Price)
        }
        
    case ErrorResponse:
        fmt.Printf("Error Response:\n")
        fmt.Printf("  Code: %d\n", data.Code)
        fmt.Printf("  Message: %s\n", data.Message)
        
    default:
        fmt.Printf("Unknown response type: %T\n", data)
    }
}

func main() {
    // Test different response types
    resp1 := APIResponse{
        Data: Product{1001, "Laptop", 999.99},
    }
    HandleResponse(resp1)
    
    fmt.Println()
    
    resp2 := APIResponse{
        Data: []Product{
            {1001, "Laptop", 999.99},
            {1002, "Mouse", 29.99},
        },
    }
    HandleResponse(resp2)
    
    fmt.Println()
    
    resp3 := APIResponse{
        Data: ErrorResponse{404, "Product not found"},
    }
    HandleResponse(resp3)
}
```

---

## 📖 Section 5: Advanced Interface Patterns

### 5.1 Function Types Implementing Interfaces 🎭

**Functions can implement interfaces!**

**The HandlerFunc Pattern:**
```go
package main

import "fmt"

// Interface
type Handler interface {
    Handle(request string) string
}

// Regular struct implementation
type EchoHandler struct {
    Prefix string
}

func (h EchoHandler) Handle(request string) string {
    return h.Prefix + ": " + request
}

// Function type that implements Handler
type HandlerFunc func(string) string

func (f HandlerFunc) Handle(request string) string {
    return f(request)  // Call the function
}

// Processor accepts Handler interface
func ProcessRequest(h Handler, request string) {
    response := h.Handle(request)
    fmt.Println("Response:", response)
}

func main() {
    // Use struct
    echoHandler := EchoHandler{Prefix: "Echo"}
    ProcessRequest(echoHandler, "Hello")
    
    // Use function directly!
    upperHandler := HandlerFunc(func(req string) string {
        return "UPPER: " + req
    })
    ProcessRequest(upperHandler, "Hello")
    
    // Even simpler
    ProcessRequest(HandlerFunc(func(req string) string {
        return "LOWER: " + req
    }), "HELLO")
}
```

**E-Commerce Middleware Pattern:**
```go
package main

import (
    "fmt"
    "time"
)

type Order struct {
    ID     int
    Amount float64
}

// Interface for order processing
type OrderProcessor interface {
    Process(order Order) error
}

// Function type implementing interface
type ProcessorFunc func(Order) error

func (f ProcessorFunc) Process(order Order) error {
    return f(order)
}

// Middleware pattern
func LoggingMiddleware(next OrderProcessor) OrderProcessor {
    return ProcessorFunc(func(order Order) error {
        fmt.Printf("[%s] Processing order #%d\n", 
            time.Now().Format("15:04:05"), order.ID)
        
        err := next.Process(order)
        
        if err != nil {
            fmt.Printf("[%s] Failed: %v\n", 
                time.Now().Format("15:04:05"), err)
        } else {
            fmt.Printf("[%s] Success\n", 
                time.Now().Format("15:04:05"))
        }
        
        return err
    })
}

func ValidationMiddleware(next OrderProcessor) OrderProcessor {
    return ProcessorFunc(func(order Order) error {
        if order.Amount <= 0 {
            return fmt.Errorf("invalid amount: %.2f", order.Amount)
        }
        return next.Process(order)
    })
}

func main() {
    // Base processor
    baseProcessor := ProcessorFunc(func(order Order) error {
        fmt.Printf("  → Charging $%.2f\n", order.Amount)
        return nil
    })
    
    // Wrap with middleware
    processor := LoggingMiddleware(
        ValidationMiddleware(baseProcessor),
    )
    
    // Process orders
    processor.Process(Order{ID: 1001, Amount: 99.99})
    fmt.Println()
    processor.Process(Order{ID: 1002, Amount: -10.00})
}
```

### 5.2 Dependency Injection (DI) 💉

**What is Dependency Injection?**
- Pattern for providing dependencies
- Improves testability
- Reduces coupling
- Enables swapping implementations

**Without DI (Tightly Coupled):**
```go
// ❌ Bad: Hard to test, tightly coupled
type OrderService struct {
    // Creates dependencies internally
}

func NewOrderService() *OrderService {
    return &OrderService{}
}

func (s *OrderService) CreateOrder(order Order) error {
    // Hardcoded database connection
    db := ConnectToRealDatabase()
    
    // Hardcoded payment gateway
    payment := NewStripePayment()
    
    // Can't easily test or swap implementations!
    return s.process(db, payment, order)
}
```

**With DI (Loosely Coupled):**
```go
// ✅ Good: Dependencies injected, easy to test
type Database interface {
    Save(order Order) error
}

type PaymentGateway interface {
    Charge(amount float64) error
}

type OrderService struct {
    db      Database        // Injected
    payment PaymentGateway  // Injected
}

func NewOrderService(db Database, payment PaymentGateway) *OrderService {
    return &OrderService{
        db:      db,
        payment: payment,
    }
}

func (s *OrderService) CreateOrder(order Order) error {
    // Use injected dependencies
    if err := s.payment.Charge(order.Total); err != nil {
        return err
    }
    return s.db.Save(order)
}
```

**Complete E-Commerce DI Example:**
```go
package main

import (
    "fmt"
    "errors"
)

// Domain types
type Order struct {
    ID       int
    UserID   int
    Total    float64
    Items    []OrderItem
}

type OrderItem struct {
    ProductID int
    Quantity  int
    Price     float64
}

// Interfaces (contracts)
type OrderRepository interface {
    Save(order Order) error
    FindByID(id int) (Order, error)
}

type PaymentProcessor interface {
    ProcessPayment(amount float64) (string, error)
}

type EmailService interface {
    SendConfirmation(email string, order Order) error
}

type InventoryService interface {
    ReserveItems(items []OrderItem) error
}

// Concrete implementations
type PostgresOrderRepo struct {
    connectionString string
}

func (r *PostgresOrderRepo) Save(order Order) error {
    fmt.Printf("💾 Saving order #%d to PostgreSQL\n", order.ID)
    return nil
}

func (r *PostgresOrderRepo) FindByID(id int) (Order, error) {
    fmt.Printf("🔍 Finding order #%d in PostgreSQL\n", id)
    return Order{ID: id}, nil
}

type StripePaymentProcessor struct {
    apiKey string
}

func (p *StripePaymentProcessor) ProcessPayment(amount float64) (string, error) {
    fmt.Printf("💳 Processing $%.2f with Stripe\n", amount)
    return "txn_12345", nil
}

type SMTPEmailService struct {
    host string
    port int
}

func (e *SMTPEmailService) SendConfirmation(email string, order Order) error {
    fmt.Printf("📧 Sending confirmation to %s\n", email)
    return nil
}

type DefaultInventoryService struct{}

func (i *DefaultInventoryService) ReserveItems(items []OrderItem) error {
    fmt.Printf("📦 Reserving %d items\n", len(items))
    return nil
}

// Service with injected dependencies
type OrderService struct {
    orderRepo OrderRepository
    payment   PaymentProcessor
    email     EmailService
    inventory InventoryService
}

func NewOrderService(
    orderRepo OrderRepository,
    payment PaymentProcessor,
    email EmailService,
    inventory InventoryService,
) *OrderService {
    return &OrderService{
        orderRepo: orderRepo,
        payment:   payment,
        email:     email,
        inventory: inventory,
    }
}

func (s *OrderService) CreateOrder(order Order, userEmail string) error {
    fmt.Println("\n=== Creating Order ===")
    
    // Reserve inventory
    if err := s.inventory.ReserveItems(order.Items); err != nil {
        return fmt.Errorf("inventory reservation failed: %w", err)
    }
    
    // Process payment
    txnID, err := s.payment.ProcessPayment(order.Total)
    if err != nil {
        return fmt.Errorf("payment failed: %w", err)
    }
    fmt.Printf("✅ Payment successful: %s\n", txnID)
    
    // Save order
    if err := s.orderRepo.Save(order); err != nil {
        return fmt.Errorf("failed to save order: %w", err)
    }
    
    // Send confirmation
    if err := s.email.SendConfirmation(userEmail, order); err != nil {
        return fmt.Errorf("failed to send email: %w", err)
    }
    
    fmt.Println("✅ Order created successfully!")
    return nil
}

func main() {
    // Production setup
    orderRepo := &PostgresOrderRepo{connectionString: "postgres://..."}
    payment := &StripePaymentProcessor{apiKey: "sk_test_..."}
    email := &SMTPEmailService{host: "smtp.gmail.com", port: 587}
    inventory := &DefaultInventoryService{}
    
    // Inject dependencies
    service := NewOrderService(orderRepo, payment, email, inventory)
    
    // Use service
    order := Order{
        ID:     1001,
        UserID: 2001,
        Total:  199.99,
        Items: []OrderItem{
            {ProductID: 101, Quantity: 1, Price: 199.99},
        },
    }
    
    service.CreateOrder(order, "customer@example.com")
}
```

**Testing with DI (Mock Dependencies):**
```go
package main

import (
    "fmt"
    "testing"
)

// Mock implementations for testing
type MockOrderRepo struct {
    savedOrders []Order
}

func (m *MockOrderRepo) Save(order Order) error {
    m.savedOrders = append(m.savedOrders, order)
    return nil
}

func (m *MockOrderRepo) FindByID(id int) (Order, error) {
    for _, order := range m.savedOrders {
        if order.ID == id {
            return order, nil
        }
    }
    return Order{}, errors.New("not found")
}

type MockPaymentProcessor struct {
    shouldFail bool
}

func (m *MockPaymentProcessor) ProcessPayment(amount float64) (string, error) {
    if m.shouldFail {
        return "", errors.New("payment declined")
    }
    return "mock_txn_123", nil
}

// Test function
func TestOrderService_CreateOrder(t *testing.T) {
    // Setup mocks
    mockRepo := &MockOrderRepo{}
    mockPayment := &MockPaymentProcessor{shouldFail: false}
    mockEmail := &MockEmailService{}
    mockInventory := &MockInventoryService{}
    
    // Create service with mocks
    service := NewOrderService(mockRepo, mockPayment, mockEmail, mockInventory)
    
    // Test
    order := Order{ID: 1001, Total: 99.99}
    err := service.CreateOrder(order, "test@example.com")
    
    // Verify
    if err != nil {
        t.Errorf("Expected no error, got %v", err)
    }
    
    if len(mockRepo.savedOrders) != 1 {
        t.Errorf("Expected 1 saved order, got %d", len(mockRepo.savedOrders))
    }
}
```

### 5.3 Wire - Dependency Injection Framework 🔌

**What is Wire?**
- Code generation tool by Google
- Compile-time dependency injection
- No runtime reflection
- Type-safe

**Installation:**
```bash
go install github.com/google/wire/cmd/wire@latest
```

**Example Setup:**
```go
// wire.go
//go:build wireinject
// +build wireinject

package main

import "github.com/google/wire"

func InitializeOrderService() *OrderService {
    wire.Build(
        NewPostgresOrderRepo,
        NewStripePaymentProcessor,
        NewSMTPEmailService,
        NewDefaultInventoryService,
        NewOrderService,
    )
    return &OrderService{}
}
```

**Run Wire:**
```bash
wire gen ./...
```

**Generated Code (wire_gen.go):**
```go
// Code generated by Wire. DO NOT EDIT.

func InitializeOrderService() *OrderService {
    postgresOrderRepo := NewPostgresOrderRepo()
    stripePaymentProcessor := NewStripePaymentProcessor()
    smtpEmailService := NewSMTPEmailService()
    defaultInventoryService := NewDefaultInventoryService()
    orderService := NewOrderService(
        postgresOrderRepo,
        stripePaymentProcessor,
        smtpEmailService,
        defaultInventoryService,
    )
    return orderService
}
```

**Simple Wire Example:**
```go
package main

import "fmt"

// Components
type Database struct {
    ConnectionString string
}

func NewDatabase() *Database {
    return &Database{
        ConnectionString: "postgres://localhost:5432/mydb",
    }
}

type Cache struct {
    Host string
}

func NewCache() *Cache {
    return &Cache{
        Host: "redis://localhost:6379",
    }
}

type UserService struct {
    db    *Database
    cache *Cache
}

func NewUserService(db *Database, cache *Cache) *UserService {
    return &UserService{
        db:    db,
        cache: cache,
    }
}

// Wire provider
//go:build wireinject
// +build wireinject

func InitializeUserService() *UserService {
    wire.Build(NewDatabase, NewCache, NewUserService)
    return &UserService{}
}

func main() {
    service := InitializeUserService()
    fmt.Printf("User service initialized with DB: %s\n", 
        service.db.ConnectionString)
}
```

---

## 📖 Section 6: Object-Orientation in Go 🎨

### 6.1 Go's Approach to OOP 🤔

**Go is NOT a traditional OOP language, but supports:**
- ✅ Encapsulation (via packages and unexported fields)
- ✅ Composition (via embedding)
- ✅ Polymorphism (via interfaces)
- ❌ NO class inheritance
- ❌ NO class hierarchies

**Comparison:**

| Feature | Traditional OOP (Java/C++) | Go |
|---------|---------------------------|-----|
| Classes | Yes | No (structs instead) |
| Inheritance | Yes | No (composition instead) |
| Polymorphism | Yes | Yes (via interfaces) |
| Encapsulation | Yes | Yes (package level) |
| Method overloading | Yes | No |
| Constructors | Yes | Functions (convention: NewX) |

### 6.2 Encapsulation in Go 🔒

**Package-level encapsulation:**

```go
// product/product.go
package product

// Exported (public)
type Product struct {
    ID          int     // Exported
    Name        string  // Exported
    price       float64 // Unexported (private)
    stock       int     // Unexported (private)
}

// Exported constructor
func New(id int, name string, price float64, stock int) *Product {
    return &Product{
        ID:    id,
        Name:  name,
        price: price,
        stock: stock,
    }
}

// Exported getter
func (p *Product) GetPrice() float64 {
    return p.price
}

// Exported setter with validation
func (p *Product) SetPrice(price float64) error {
    if price < 0 {
        return fmt.Errorf("price cannot be negative")
    }
    p.price = price
    return nil
}

// Exported method
func (p *Product) IsInStock() bool {
    return p.stock > 0
}

// Unexported (private) method
func (p *Product) updateStock(delta int) {
    p.stock += delta
}
```

```go
// main.go
package main

import "product"

func main() {
    p := product.New(1001, "Laptop", 999.99, 10)
    
    // ✅ Can access exported fields
    fmt.Println(p.Name)
    
    // ❌ Cannot access unexported fields
    // fmt.Println(p.price)  // Compile error!
    
    // ✅ Use getter
    fmt.Println(p.GetPrice())
    
    // ✅ Use setter
    p.SetPrice(899.99)
}
```

### 6.3 Polymorphism via Interfaces 🎭

**Complete E-Commerce Payment System:**

```go
package main

import (
    "fmt"
    "time"
)

// Interface (contract)
type PaymentMethod interface {
    Authorize(amount float64) error
    Capture() (string, error)
    Refund(transactionID string) error
    GetName() string
}

// Implementation 1: Credit Card
type CreditCardPayment struct {
    CardNumber string
    CVV        string
    authorized bool
    amount     float64
}

func (cc *CreditCardPayment) Authorize(amount float64) error {
    fmt.Printf("💳 Authorizing $%.2f on card ****%s\n", 
        amount, cc.CardNumber[len(cc.CardNumber)-4:])
    cc.authorized = true
    cc.amount = amount
    return nil
}

func (cc *CreditCardPayment) Capture() (string, error) {
    if !cc.authorized {
        return "", fmt.Errorf("not authorized")
    }
    fmt.Printf("💳 Capturing $%.2f\n", cc.amount)
    return "cc_txn_" + time.Now().Format("20060102150405"), nil
}

func (cc *CreditCardPayment) Refund(transactionID string) error {
    fmt.Printf("💳 Refunding transaction %s\n", transactionID)
    return nil
}

func (cc *CreditCardPayment) GetName() string {
    return "Credit Card"
}

// Implementation 2: PayPal
type PayPalPayment struct {
    Email      string
    authorized bool
    amount     float64
}

func (pp *PayPalPayment) Authorize(amount float64) error {
    fmt.Printf("🅿️ Authorizing $%.2f on PayPal %s\n", amount, pp.Email)
    pp.authorized = true
    pp.amount = amount
    return nil
}

func (pp *PayPalPayment) Capture() (string, error) {
    if !pp.authorized {
        return "", fmt.Errorf("not authorized")
    }
    fmt.Printf("🅿️ Capturing $%.2f\n", pp.amount)
    return "pp_txn_" + time.Now().Format("20060102150405"), nil
}

func (pp *PayPalPayment) Refund(transactionID string) error {
    fmt.Printf("🅿️ Refunding transaction %s\n", transactionID)
    return nil
}

func (pp *PayPalPayment) GetName() string {
    return "PayPal"
}

// Implementation 3: Bank Transfer
type BankTransferPayment struct {
    AccountNumber string
    RoutingNumber string
    authorized    bool
    amount        float64
}

func (bt *BankTransferPayment) Authorize(amount float64) error {
    fmt.Printf("🏦 Authorizing $%.2f on account ****%s\n", 
        amount, bt.AccountNumber[len(bt.AccountNumber)-4:])
    bt.authorized = true
    bt.amount = amount
    return nil
}

func (bt *BankTransferPayment) Capture() (string, error) {
    if !bt.authorized {
        return "", fmt.Errorf("not authorized")
    }
    fmt.Printf("🏦 Capturing $%.2f\n", bt.amount)
    return "bt_txn_" + time.Now().Format("20060102150405"), nil
}

func (bt *BankTransferPayment) Refund(transactionID string) error {
    fmt.Printf("🏦 Refunding transaction %s\n", transactionID)
    return nil
}

func (bt *BankTransferPayment) GetName() string {
    return "Bank Transfer"
}

// Service that uses interface (polymorphism!)
type CheckoutService struct {
    paymentMethod PaymentMethod
}

func NewCheckoutService(pm PaymentMethod) *CheckoutService {
    return &CheckoutService{paymentMethod: pm}
}

func (s *CheckoutService) ProcessPayment(amount float64) error {
    fmt.Printf("\n=== Processing %s Payment ===\n", s.paymentMethod.GetName())
    
    // Authorize
    if err := s.paymentMethod.Authorize(amount); err != nil {
        return err
    }
    
    // Capture
    txnID, err := s.paymentMethod.Capture()
    if err != nil {
        return err
    }
    
    fmt.Printf("✅ Payment successful! Transaction: %s\n", txnID)
    return nil
}

func main() {
    // Polymorphism: Same service, different implementations!
    
    // Credit Card
    cc := &CreditCardPayment{
        CardNumber: "4532-1234-5678-9010",
        CVV:        "123",
    }
    checkout1 := NewCheckoutService(cc)
    checkout1.ProcessPayment(199.99)
    
    // PayPal
    pp := &PayPalPayment{
        Email: "user@example.com",
    }
    checkout2 := NewCheckoutService(pp)
    checkout2.ProcessPayment(149.99)
    
    // Bank Transfer
    bt := &BankTransferPayment{
        AccountNumber: "1234567890",
        RoutingNumber: "021000021",
    }
    checkout3 := NewCheckoutService(bt)
    checkout3.ProcessPayment(299.99)
}
```

### 6.4 Composition over Inheritance 🧩

**Complete Example:**

```go
package main

import (
    "fmt"
    "time"
)

// Base functionality (mixins)
type Timestamped struct {
    CreatedAt time.Time
    UpdatedAt time.Time
}

func (t *Timestamped) Touch() {
    t.UpdatedAt = time.Now()
}

type Identifiable struct {
    ID int
}

type Auditable struct {
    CreatedBy string
    UpdatedBy string
}

// Composed entities
type Product struct {
    Identifiable          // Has ID
    Timestamped          // Has timestamps
    Auditable            // Has audit fields
    Name         string
    Price        float64
    Stock        int
}

type Customer struct {
    Identifiable          // Has ID
    Timestamped          // Has timestamps
    Auditable            // Has audit fields
    Email        string
    Name         string
    LoyaltyPoints int
}

type Order struct {
    Identifiable          // Has ID
    Timestamped          // Has timestamps
    Auditable            // Has audit fields
    CustomerID   int
    Items        []OrderItem
    Total        float64
    Status       string
}

type OrderItem struct {
    ProductID int
    Quantity  int
    Price     float64
}

// Methods
func (p *Product) UpdatePrice(newPrice float64, updatedBy string) {
    p.Price = newPrice
    p.UpdatedBy = updatedBy
    p.Touch()
}

func (o *Order) AddItem(item OrderItem, updatedBy string) {
    o.Items = append(o.Items, item)
    o.Total += item.Price * float64(item.Quantity)
    o.UpdatedBy = updatedBy
    o.Touch()
}

func main() {
    now := time.Now()
    
    // Create product
    product := Product{
        Identifiable: Identifiable{ID: 1001},
        Timestamped:  Timestamped{CreatedAt: now, UpdatedAt: now},
        Auditable:    Auditable{CreatedBy: "admin", UpdatedBy: "admin"},
        Name:         "Laptop",
        Price:        999.99,
        Stock:        10,
    }
    
    // Update product
    time.Sleep(100 * time.Millisecond)
    product.UpdatePrice(899.99, "manager")
    
    // Create order
    order := Order{
        Identifiable: Identifiable{ID: 5001},
        Timestamped:  Timestamped{CreatedAt: now, UpdatedAt: now},
        Auditable:    Auditable{CreatedBy: "system", UpdatedBy: "system"},
        CustomerID:   2001,
        Status:       "pending",
    }
    
    // Add item to order
    time.Sleep(100 * time.Millisecond)
    order.AddItem(OrderItem{
        ProductID: product.ID,
        Quantity:  1,
        Price:     product.Price,
    }, "customer")
    
    // Display
    fmt.Printf("Product #%d: %s - $%.2f\n", product.ID, product.Name, product.Price)
    fmt.Printf("  Created: %s by %s\n", 
        product.CreatedAt.Format("15:04:05"), product.CreatedBy)
    fmt.Printf("  Updated: %s by %s\n", 
        product.UpdatedAt.Format("15:04:05"), product.UpdatedBy)
    
    fmt.Printf("\nOrder #%d: $%.2f\n", order.ID, order.Total)
    fmt.Printf("  Created: %s by %s\n", 
        order.CreatedAt.Format("15:04:05"), order.CreatedBy)
    fmt.Printf("  Updated: %s by %s\n", 
        order.UpdatedAt.Format("15:04:05"), order.UpdatedBy)
}
```

---

## 🏋️ Exercises and Challenges

### Exercise 1: Interface Implementation 📝

**Simple Level:**

Create a `Shape` interface and implement it for `Circle` and `Rectangle`.

**Requirements:**
- Interface with `Area()` and `Perimeter()` methods
- Circle with radius
- Rectangle with width and height

**Solution:**
```go
package main

import (
    "fmt"
    "math"
)

type Shape interface {
    Area() float64
    Perimeter() float64
}

type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return math.Pi * c.Radius * c.Radius
}

func (c Circle) Perimeter() float64 {
    return 2 * math.Pi * c.Radius
}

type Rectangle struct {
    Width  float64
    Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func (r Rectangle) Perimeter() float64 {
    return 2 * (r.Width + r.Height)
}

func PrintShapeInfo(s Shape) {
    fmt.Printf("Area: %.2f\n", s.Area())
    fmt.Printf("Perimeter: %.2f\n", s.Perimeter())
}

func main() {
    circle := Circle{Radius: 5}
    rectangle := Rectangle{Width: 10, Height: 5}
    
    fmt.Println("Circle:")
    PrintShapeInfo(circle)
    
    fmt.Println("\nRectangle:")
    PrintShapeInfo(rectangle)
}
```

### Exercise 2: E-Commerce Plugin System 🔌

**Medium Level:**

Build a plugin system for shipping calculators using interfaces.

**Requirements:**
- `ShippingCalculator` interface
- Multiple implementations (Standard, Express, International)
- Type switch to handle different calculators

**Solution:**
```go
package main

import "fmt"

type ShippingCalculator interface {
    CalculateCost(weight float64, distance float64) float64
    GetName() string
    GetEstimatedDays() int
}

type StandardShipping struct{}

func (s StandardShipping) CalculateCost(weight float64, distance float64) float64 {
    return 5.99 + (weight * 0.5) + (distance * 0.01)
}

func (s StandardShipping) GetName() string {
    return "Standard Shipping"
}

func (s StandardShipping) GetEstimatedDays() int {
    return 5
}

type ExpressShipping struct{}

func (e ExpressShipping) CalculateCost(weight float64, distance float64) float64 {
    return 15.99 + (weight * 1.0) + (distance * 0.05)
}

func (e ExpressShipping) GetName() string {
    return "Express Shipping"
}

func (e ExpressShipping) GetEstimatedDays() int {
    return 2
}

type InternationalShipping struct {
    Country string
}

func (i InternationalShipping) CalculateCost(weight float64, distance float64) float64 {
    baseCost := 25.99
    // Different rates for different countries
    switch i.Country {
    case "CA", "MX":
        baseCost = 20.99
    case "UK", "EU":
        baseCost = 30.99
    default:
        baseCost = 40.99
    }
    return baseCost + (weight * 2.0) + (distance * 0.10)
}

func (i InternationalShipping) GetName() string {
    return fmt.Sprintf("International Shipping to %s", i.Country)
}

func (i InternationalShipping) GetEstimatedDays() int {
    return 10
}

func DisplayShippingOption(calc ShippingCalculator, weight, distance float64) {
    cost := calc.CalculateCost(weight, distance)
    fmt.Printf("%s:\n", calc.GetName())
    fmt.Printf("  Cost: $%.2f\n", cost)
    fmt.Printf("  Estimated: %d days\n", calc.GetEstimatedDays())
    
    // Type switch for specific information
    switch c := calc.(type) {
    case InternationalShipping:
        fmt.Printf("  Destination: %s\n", c.Country)
    }
}

func main() {
    weight := 5.0  // kg
    distance := 500.0  // km
    
    calculators := []ShippingCalculator{
        StandardShipping{},
        ExpressShipping{},
        InternationalShipping{Country: "UK"},
    }
    
    for i, calc := range calculators {
        if i > 0 {
            fmt.Println()
        }
        DisplayShippingOption(calc, weight, distance)
    }
}
```

### Exercise 3: Advanced DI System 🔥

**Hard Level:**

Build a complete order processing system with dependency injection and multiple implementations.

**(Solution in file due to length - over 200 lines)**

### LeetCode Challenge: Design Underground System 🚇

**Problem:**
Design a system that tracks customer travel times between stations. Implement the `UndergroundSystem` class with these methods:
- `CheckIn(id int, stationName string, t int)`
- `CheckOut(id int, stationName string, t int)`  
- `GetAverageTime(startStation string, endStation string) float64`

**Solution:**
```go
package main

import "fmt"

type CheckInInfo struct {
    Station   string
    Time      int
}

type TravelInfo struct {
    TotalTime int
    Count     int
}

type UndergroundSystem struct {
    checkIns map[int]CheckInInfo                    // userID -> check-in info
    travels  map[string]TravelInfo                  // "start->end" -> travel stats
}

func Constructor() UndergroundSystem {
    return UndergroundSystem{
        checkIns: make(map[int]CheckInInfo),
        travels:  make(map[string]TravelInfo),
    }
}

func (u *UndergroundSystem) CheckIn(id int, stationName string, t int) {
    u.checkIns[id] = CheckInInfo{
        Station: stationName,
        Time:    t,
    }
}

func (u *UndergroundSystem) CheckOut(id int, stationName string, t int) {
    checkIn := u.checkIns[id]
    delete(u.checkIns, id)
    
    duration := t - checkIn.Time
    route := checkIn.Station + "->" + stationName
    
    travel := u.travels[route]
    travel.TotalTime += duration
    travel.Count++
    u.travels[route] = travel
}

func (u *UndergroundSystem) GetAverageTime(startStation string, endStation string) float64 {
    route := startStation + "->" + endStation
    travel := u.travels[route]
    return float64(travel.TotalTime) / float64(travel.Count)
}

func main() {
    system := Constructor()
    
    system.CheckIn(10, "Leyton", 3)
    system.CheckIn(32, "Paradise", 8)
    system.CheckIn(2, "Leyton", 10)
    system.CheckOut(10, "Paradise", 8)
    system.CheckOut(32, "Cambridge", 22)
    system.CheckOut(2, "Leyton", 20)
    
    avg1 := system.GetAverageTime("Leyton", "Paradise")
    avg2 := system.GetAverageTime("Paradise", "Cambridge")
    
    fmt.Printf("Average Leyton->Paradise: %.2f\n", avg1)
    fmt.Printf("Average Paradise->Cambridge: %.2f\n", avg2)
}
```

---

## 🎁 Wrapping Up Chapter 7

### ✅ What You Learned:

**1. Type Declarations:**
- Creating custom types
- Type safety benefits
- Semantic clarity

**2. Methods:**
- Value vs pointer receivers
- Methods on nil
- Methods vs functions

**3. Composition:**
- Embedding types
- Promoted fields and methods
- Composition over inheritance

**4. Interfaces:**
- Implicit satisfaction
- Duck typing
- Empty interface (`any`)
- Interface embedding

**5. Advanced Patterns:**
- Function types as interfaces
- Type assertions and switches
- Accept interfaces, return structs

**6. Dependency Injection:**
- Why DI matters
- Implementation patterns
- Testing with DI
- Wire framework

**7. OOP in Go:**
- Encapsulation via packages
- Polymorphism via interfaces
- No inheritance, use composition

### 🎯 Key Takeaways:

1. **Interfaces are powerful** - Enable polymorphism and testability
2. **Composition over inheritance** - More flexible and maintainable
3. **Accept interfaces, return structs** - Maximum flexibility
4. **DI improves testability** - Easier to mock and test
5. **Go's OOP is different** - But just as powerful!

### 📚 Best Practices:

**DO:**
- ✅ Keep interfaces small (1-3 methods)
- ✅ Accept interfaces, return structs
- ✅ Use composition for code reuse
- ✅ Inject dependencies via constructors
- ✅ Make zero values useful

**DON'T:**
- ❌ Create interfaces "just in case"
- ❌ Make interfaces with many methods
- ❌ Use empty interface everywhere
- ❌ Hardcode dependencies
- ❌ Try to recreate class hierarchies

### 🚀 Next Steps:

You're now ready for advanced Go topics:
- Concurrency (Goroutines & Channels)
- Error Handling Patterns
- Testing Strategies
- Performance Optimization

**Congratulations on completing Chapter 7! 🎉**

---
---

## 💪 Practice Tasks for Students

### 📝 Task 1: Discount System (Easy)

**🎯 Goal:**
Create a flexible discount system using interfaces and methods.

**Requirements:**
1. Create a `Discounter` interface with a `ApplyDiscount(price float64) float64` method
2. Implement three discount types:
   - `PercentageDiscount` - Reduces price by a percentage (e.g., 10%, 20%)
   - `FixedDiscount` - Reduces price by a fixed amount (e.g., $5 off)
   - `NoDiscount` - Returns original price (useful for default case)
3. Create a `Product` struct with:
   - Name
   - BasePrice
   - Method to calculate final price with a discount
4. Write a function that accepts a list of products and a `Discounter`, returning total price after discount

**Template:**
```go
package main

import "fmt"

// TODO: Define Discounter interface

// TODO: Implement PercentageDiscount type

// TODO: Implement FixedDiscount type

// TODO: Implement NoDiscount type

// TODO: Define Product struct

// TODO: Implement Product method to calculate final price

// TODO: Implement function to calculate total for multiple products

func main() {
    products := []Product{
        {Name: "Laptop", BasePrice: 999.99},
        {Name: "Mouse", BasePrice: 29.99},
        {Name: "Keyboard", BasePrice: 79.99},
    }
    
    // Test with different discounts
    // TODO: Create discount instances and test
}
```

**Expected Output:**
```
=== No Discount ===
Laptop: $999.99
Mouse: $29.99
Keyboard: $79.99
Total: $1109.97

=== 10% Discount ===
Laptop: $899.99
Mouse: $26.99
Keyboard: $71.99
Total: $998.97

=== $50 Fixed Discount ===
Laptop: $949.99
Mouse: $29.99 (minimum $0)
Keyboard: $29.99
Total: $1009.97
```

**Hints:**
- For `FixedDiscount`, make sure price doesn't go below 0
- Use pointer receivers if you need to modify the discount object
- Test edge cases (e.g., discount larger than price)

---

### 📝 Task 2: Notification System with Composition (Medium)

**🎯 Goal:**
Build a notification system for an e-commerce platform using composition and interfaces.

**Requirements:**
1. Create base components using composition:
   - `Timestamped` struct with `CreatedAt` and `SentAt` fields
   - `Retryable` struct with `Attempts` and `MaxAttempts` fields
   - `Prioritized` struct with `Priority` field (Low, Medium, High)

2. Create a `NotificationSender` interface:
   - `Send(message string, recipient string) error`
   - `GetType() string`

3. Implement three notification types:
   - `EmailNotification` - Sends email
   - `SMSNotification` - Sends SMS
   - `PushNotification` - Sends push notification
   - Each should embed `Timestamped`, `Retryable`, and `Prioritized`

4. Create a `NotificationService` that:
   - Accepts any `NotificationSender`
   - Implements retry logic using the `Retryable` fields
   - Records timestamps
   - Handles different priority levels

5. Create an `Order` notification that uses composition to combine order info with notification

**Template:**
```go
package main

import (
    "fmt"
    "time"
)

// TODO: Define base components
type Timestamped struct {
    // TODO
}

type Retryable struct {
    // TODO
}

type Prioritized struct {
    Priority string // "Low", "Medium", "High"
}

// TODO: Define NotificationSender interface

// TODO: Implement EmailNotification
type EmailNotification struct {
    // TODO: Embed base components
    EmailAddress string
}

// TODO: Implement SMSNotification
type SMSNotification struct {
    // TODO: Embed base components
    PhoneNumber string
}

// TODO: Implement PushNotification
type PushNotification struct {
    // TODO: Embed base components
    DeviceToken string
}

// TODO: Implement NotificationService
type NotificationService struct {
    sender NotificationSender
}

// TODO: Implement SendWithRetry method

func main() {
    // TODO: Test different notification types
    // TODO: Test retry logic
    // TODO: Test priority handling
}
```

**Expected Behavior:**
- Email should retry up to 3 times on failure
- High priority notifications should be sent first
- Timestamps should be recorded for creation and sending
- Each notification type should have unique sending logic

**Bonus Points:**
- Implement a queue system for notifications
- Add a method to display notification history
- Implement exponential backoff for retries

---

### 📝 Task 3: Complete Order Processing System with DI (Hard)

**🎯 Goal:**
Build a complete, production-ready order processing system with proper dependency injection, interfaces, and testability.

**Requirements:**

**1. Define Core Interfaces:**
   - `ProductRepository` - Save, Find, UpdateStock
   - `OrderRepository` - Save, Find, UpdateStatus
   - `PaymentGateway` - Authorize, Capture, Refund
   - `InventoryService` - Reserve, Release, Check
   - `NotificationService` - Notify
   - `Logger` - Log (Info, Error, Debug)

**2. Create Domain Types:**
   - `Product` with ID, Name, Price, Stock (with methods)
   - `Order` with ID, Items, Status, Total (with methods)
   - `OrderItem` with ProductID, Quantity, Price
   - Order status: Pending, Confirmed, Shipped, Delivered, Cancelled

**3. Implement OrderService:**
   - `CreateOrder(customerID int, items []OrderItem) (*Order, error)`
   - `CancelOrder(orderID int) error`
   - `GetOrder(orderID int) (*Order, error)`
   - Proper error handling
   - Transaction-like behavior (rollback on failure)

**4. Order Creation Flow:**
   ```
   1. Validate items
   2. Check inventory
   3. Reserve inventory
   4. Authorize payment
   5. Create order (save to DB)
   6. Capture payment
   7. Send confirmation
   8. Log success
   
   If any step fails: Rollback (release inventory, refund payment)
   ```

**5. Implement at least 2 versions of each interface:**
   - Real implementations (e.g., `PostgresOrderRepo`)
   - Mock implementations for testing (e.g., `MockOrderRepo`)

**6. Write Tests:**
   - Test successful order creation
   - Test order creation with insufficient inventory
   - Test order creation with payment failure
   - Test order cancellation

**Template:**
```go
package main

import (
    "fmt"
    "errors"
    "time"
)

// ========================================
// Domain Types
// ========================================

type OrderStatus string

const (
    OrderPending    OrderStatus = "pending"
    OrderConfirmed  OrderStatus = "confirmed"
    OrderShipped    OrderStatus = "shipped"
    OrderDelivered  OrderStatus = "delivered"
    OrderCancelled  OrderStatus = "cancelled"
)

type Product struct {
    ID    int
    Name  string
    Price float64
    Stock int
}

type OrderItem struct {
    ProductID int
    Quantity  int
    Price     float64
}

type Order struct {
    ID         int
    CustomerID int
    Items      []OrderItem
    Total      float64
    Status     OrderStatus
    CreatedAt  time.Time
}

// TODO: Add methods to Order (CalculateTotal, CanBeCancelled, etc.)

// ========================================
// Interfaces
// ========================================

// TODO: Define all required interfaces
type ProductRepository interface {
    // TODO
}

type OrderRepository interface {
    // TODO
}

type PaymentGateway interface {
    // TODO
}

type InventoryService interface {
    // TODO
}

type NotificationService interface {
    // TODO
}

type Logger interface {
    // TODO
}

// ========================================
// Real Implementations
// ========================================

// TODO: Implement real versions (can be in-memory for this exercise)

type InMemoryProductRepo struct {
    products map[int]*Product
}

// TODO: Implement methods

type InMemoryOrderRepo struct {
    orders   map[int]*Order
    nextID   int
}

// TODO: Implement methods

type MockPaymentGateway struct {
    shouldFail bool
}

// TODO: Implement methods

// ========================================
// Mock Implementations (for testing)
// ========================================

// TODO: Implement mock versions

type MockOrderRepo struct {
    orders []Order
}

// TODO: Implement methods

// ========================================
// OrderService
// ========================================

type OrderService struct {
    productRepo ProductRepository
    orderRepo   OrderRepository
    payment     PaymentGateway
    inventory   InventoryService
    notifier    NotificationService
    logger      Logger
}

func NewOrderService(
    productRepo ProductRepository,
    orderRepo OrderRepository,
    payment PaymentGateway,
    inventory InventoryService,
    notifier NotificationService,
    logger Logger,
) *OrderService {
    return &OrderService{
        productRepo: productRepo,
        orderRepo:   orderRepo,
        payment:     payment,
        inventory:   inventory,
        notifier:    notifier,
        logger:      logger,
    }
}

func (s *OrderService) CreateOrder(customerID int, items []OrderItem) (*Order, error) {
    s.logger.Info("Creating order for customer", customerID)
    
    // TODO: Implement complete flow with rollback on failure
    // 1. Validate items
    // 2. Check inventory
    // 3. Reserve inventory
    // 4. Authorize payment
    // 5. Create order
    // 6. Capture payment
    // 7. Send notification
    
    return nil, nil
}

func (s *OrderService) CancelOrder(orderID int) error {
    // TODO: Implement
    return nil
}

func (s *OrderService) GetOrder(orderID int) (*Order, error) {
    // TODO: Implement
    return nil, nil
}

// ========================================
// Main & Tests
// ========================================

func main() {
    // TODO: Setup dependencies
    productRepo := NewInMemoryProductRepo()
    orderRepo := NewInMemoryOrderRepo()
    payment := &MockPaymentGateway{shouldFail: false}
    inventory := NewDefaultInventoryService(productRepo)
    notifier := &ConsoleNotificationService{}
    logger := &ConsoleLogger{}
    
    // Create service
    service := NewOrderService(
        productRepo,
        orderRepo,
        payment,
        inventory,
        notifier,
        logger,
    )
    
    // TODO: Add test products
    
    // TODO: Test order creation
    
    // TODO: Test order cancellation
}

// ========================================
// Test Functions
// ========================================

func TestSuccessfulOrderCreation() {
    fmt.Println("=== Test: Successful Order Creation ===")
    // TODO: Implement test
}

func TestOrderCreationWithInsufficientInventory() {
    fmt.Println("=== Test: Insufficient Inventory ===")
    // TODO: Implement test
}

func TestOrderCreationWithPaymentFailure() {
    fmt.Println("=== Test: Payment Failure ===")
    // TODO: Implement test
}

func TestOrderCancellation() {
    fmt.Println("=== Test: Order Cancellation ===")
    // TODO: Implement test
}
```

**Expected Features:**
- ✅ Complete order creation flow
- ✅ Proper error handling
- ✅ Rollback mechanism
- ✅ Dependency injection
- ✅ Interface-based design
- ✅ Testable with mocks
- ✅ Logging at each step
- ✅ Inventory management
- ✅ Payment processing
- ✅ Notifications

**Success Criteria:**
1. All dependencies are injected (no hardcoded dependencies)
2. Each interface has at least 2 implementations
3. Order creation handles all failure scenarios
4. Proper rollback on any failure
5. All tests pass
6. Code is clean and well-documented

**Bonus Challenges:**
1. Add transaction support (rollback database changes)
2. Implement idempotency (prevent duplicate orders)
3. Add order status change history
4. Implement webhook notifications
5. Add metrics/monitoring
6. Implement circuit breaker pattern for payment gateway

---

## 🎓 Evaluation Rubric

### Task 1 (Easy) - 30 points
- ✅ Interface correctly defined (5 pts)
- ✅ All three discount types implemented (10 pts)
- ✅ Product struct with methods (5 pts)
- ✅ Calculate total function works (5 pts)
- ✅ Edge cases handled (5 pts)

### Task 2 (Medium) - 35 points
- ✅ Composition used correctly (10 pts)
- ✅ Interface defined and implemented (10 pts)
- ✅ Retry logic implemented (5 pts)
- ✅ Timestamps recorded (5 pts)
- ✅ Priority handling (5 pts)

### Task 3 (Hard) - 35 points
- ✅ All interfaces defined (5 pts)
- ✅ OrderService implements complete flow (10 pts)
- ✅ Proper DI implementation (5 pts)
- ✅ Rollback mechanism works (5 pts)
- ✅ Mock implementations for testing (5 pts)
- ✅ Tests written and passing (5 pts)

**Total: 100 points**

---

## 💡 Tips for Students

**General Tips:**
1. **Start with interfaces** - Define contracts before implementation
2. **Test as you go** - Don't wait until the end
3. **Use meaningful names** - Make your code self-documenting
4. **Handle errors properly** - Don't ignore error returns
5. **Keep functions small** - Each function should do one thing well

**Task 1 Tips:**
- Think about the Strategy pattern
- Make sure discounts never result in negative prices
- Test with edge cases (zero price, huge discount, etc.)

**Task 2 Tips:**
- Use embedding to avoid repeating fields
- Think about how to make retry logic reusable
- Consider using goroutines for async notifications (bonus)

**Task 3 Tips:**
- Draw a flow diagram before coding
- Think about what needs to be rolled back at each step
- Use defer for cleanup operations
- Write helper functions to reduce complexity
- Test each component independently first

**Common Pitfalls to Avoid:**
- ❌ Not checking for nil
- ❌ Ignoring error returns
- ❌ Not validating input
- ❌ Hardcoding dependencies
- ❌ Not handling partial failures
- ❌ Forgetting to rollback on errors

**Resources:**
- Go interfaces: https://go.dev/tour/methods/9
- Dependency injection: https://blog.drewolson.org/dependency-injection-in-go
- Testing in Go: https://go.dev/doc/tutorial/add-a-test
- Error handling: https://go.dev/blog/error-handling-and-go

---

## 🎯 Submission Guidelines

**What to Submit:**
1. Complete source code for each task
2. Test output showing your code works
3. Brief explanation of your design decisions (1-2 paragraphs per task)
4. Any challenges you faced and how you solved them

**Code Quality Checklist:**
- [ ] Code compiles without errors
- [ ] All requirements implemented
- [ ] Proper error handling
- [ ] Meaningful variable/function names
- [ ] Comments for complex logic
- [ ] Tests included (Task 3)
- [ ] No hardcoded values where inappropriate

**Good luck! 🚀**

---

**End of Chapter 7 Practice Tasks**

---
---

# 🎁 Chapter 8: Generics

![Go Generics](https://images.unsplash.com/photo-1555066931-4365d14bab8c?auto=format&fit=crop&w=600&q=80)

### 📚 Table of Contents for This Chapter
1. **Section 1: Reducing Repetition with Generics**
2. **Section 2: Introducing Generics**
3. **Section 3: Generic Functions and Type Parameters**
4. **Section 4: Constraints: any and comparable**
5. **Section 5: Type Terms and Union Types**
6. **Section 6: Type Inference**
7. **Section 7: Limitations and Trade-offs**
8. **Section 8: Combining Generics with Data Structures**
9. **Section 9: Idiomatic Use of Generics**
10. **Section 10: Generics in the Standard Library**
11. **Exercises and Practice Tasks**
12. **Wrapping Up**

---

## 🌟 Introduction: What Are Generics?

### Why Do We Need Generics? 🤔

Imagine you're building an e-commerce platform and you need to create different lists:
- A list of products
- A list of orders
- A list of customers
- A list of categories

**Without generics**, you'd have to write the same list implementation multiple times:

```go
// Product list
type ProductList struct {
    items []Product
}
func (l *ProductList) Add(p Product) { }
func (l *ProductList) Get(i int) Product { }

// Order list  
type OrderList struct {
    items []Order
}
func (l *OrderList) Add(o Order) { }
func (l *OrderList) Get(i int) Order { }

// ... repeat for every type! 😫
```

**With generics**, you write the implementation **once** and use it with **any type**:

```go
// One generic list for ALL types!
type List[T any] struct {
    items []T
}
func (l *List[T]) Add(item T) { }
func (l *List[T]) Get(i int) T { }

// Use with different types
productList := List[Product]{}
orderList := List[Order]{}
customerList := List[Customer]{}
```

---

### What Are Generics? 📦

**Generics** (also called **Type Parameters** or **Parametric Polymorphism**) allow you to:

1. **Write code once** that works with multiple types
2. **Maintain type safety** at compile time
3. **Avoid code duplication** and reduce maintenance
4. **Create reusable** data structures and algorithms

**Simple Definition:**
> Generics let you use **types as parameters**, just like you use values as parameters in functions.

**Function with value parameters:**
```go
func Add(a int, b int) int {
    return a + b
}
Add(5, 10)  // Pass values
```

**Function with type parameters:**
```go
func Add[T Numeric](a T, b T) T {
    return a + b
}
Add[int](5, 10)        // Pass type AND values
Add[float64](5.5, 10.2) // Works with different type
```

---

### Where Are Generics Used? 🎯

**1. Data Structures (Collections)**

The most common use case! Build once, use with any type:

```go
// Generic Stack
type Stack[T any] struct {
    items []T
}

// Use with different types
intStack := Stack[int]{}
stringStack := Stack[string]{}
productStack := Stack[Product]{}
```

**Real-World Examples:**
- ✅ Lists, Stacks, Queues
- ✅ Trees, Graphs
- ✅ Maps, Sets
- ✅ Caches
- ✅ Pools

---

**2. Algorithms**

Write algorithms once, work with any compatible type:

```go
// Generic sort function
func Sort[T Ordered](slice []T) []T {
    // One implementation works for int, float64, string, etc.
}

// Use with different types
Sort([]int{3, 1, 2})          // [1, 2, 3]
Sort([]float64{3.5, 1.2, 2.8}) // [1.2, 2.8, 3.5]
Sort([]string{"c", "a", "b"})  // ["a", "b", "c"]
```

**Real-World Examples:**
- ✅ Sorting, Searching
- ✅ Filtering, Mapping, Reducing
- ✅ Finding min/max
- ✅ Grouping, Aggregating

---

**3. API Responses**

Type-safe API responses for different data types:

```go
type APIResponse[T any] struct {
    Success bool   `json:"success"`
    Message string `json:"message"`
    Data    T      `json:"data"`  // Generic data field
}

// Product response
productResp := APIResponse[Product]{
    Success: true,
    Data: Product{ID: 1, Name: "Laptop"},
}

// List response
listResp := APIResponse[[]Product]{
    Success: true,
    Data: []Product{ /* ... */ },
}

// Error response
errorResp := APIResponse[interface{}]{
    Success: false,
    Message: "Not found",
    Data: nil,
}
```

**Real-World Examples:**
- ✅ REST API responses
- ✅ GraphQL responses
- ✅ RPC responses
- ✅ WebSocket messages

---

**4. Result/Option Types**

Handle success and error cases elegantly:

```go
type Result[T any] struct {
    value T
    err   error
}

func (r Result[T]) IsOk() bool {
    return r.err == nil
}

func (r Result[T]) Unwrap() T {
    if r.err != nil {
        panic(r.err)
    }
    return r.value
}

// Usage
func GetProduct(id int) Result[Product] {
    // ... fetch product
    if err != nil {
        return Err[Product](err)
    }
    return Ok(product)
}

result := GetProduct(123)
if result.IsOk() {
    product := result.Unwrap()
}
```

**Real-World Examples:**
- ✅ Result types (like Rust)
- ✅ Option/Maybe types
- ✅ Either types
- ✅ Validation results

---

**5. Caching Systems**

Generic caches that work with any key-value types:

```go
type Cache[K comparable, V any] struct {
    data map[K]V
}

// Product cache
productCache := Cache[int, Product]{}
productCache.Set(1, Product{Name: "Laptop"})

// User session cache
sessionCache := Cache[string, Session]{}
sessionCache.Set("user123", session)

// Price cache
priceCache := Cache[int, float64]{}
priceCache.Set(productID, 99.99)
```

**Real-World Examples:**
- ✅ In-memory caches (Redis-like)
- ✅ LRU caches
- ✅ TTL caches
- ✅ Distributed caches

---

**6. Database Operations**

Generic repository pattern:

```go
type Repository[T Entity] interface {
    Create(entity T) error
    FindByID(id int) (T, error)
    FindAll() ([]T, error)
    Update(entity T) error
    Delete(id int) error
}

// One repository works with all entities
productRepo := NewRepository[Product]()
orderRepo := NewRepository[Order]()
customerRepo := NewRepository[Customer]()
```

**Real-World Examples:**
- ✅ Repository pattern
- ✅ DAO pattern
- ✅ Query builders
- ✅ ORM helpers

---

**7. Event Systems**

Type-safe event buses:

```go
type EventBus[T any] struct {
    handlers []func(T)
}

// Order events
orderBus := NewEventBus[OrderCreatedEvent]()
orderBus.Subscribe(func(e OrderCreatedEvent) {
    fmt.Printf("Order #%d created\n", e.OrderID)
})

// Payment events
paymentBus := NewEventBus[PaymentProcessedEvent]()
paymentBus.Subscribe(func(e PaymentProcessedEvent) {
    fmt.Printf("Payment $%.2f processed\n", e.Amount)
})
```

**Real-World Examples:**
- ✅ Event-driven architecture
- ✅ Pub/Sub systems
- ✅ Message queues
- ✅ Webhooks

---

**8. Validation & Transformation**

Generic validation and transformation functions:

```go
// Generic validator
func Validate[T any](value T, rules []Rule[T]) error {
    for _, rule := range rules {
        if err := rule(value); err != nil {
            return err
        }
    }
    return nil
}

// Generic transformer
func Transform[From any, To any](value From, fn func(From) To) To {
    return fn(value)
}

// Usage
Validate(email, []Rule[string]{IsEmail, NotEmpty})
Validate(age, []Rule[int]{IsPositive, IsAdult})

prices := Transform(products, func(p Product) float64 {
    return p.Price
})
```

**Real-World Examples:**
- ✅ Input validation
- ✅ Data transformation
- ✅ Serialization/Deserialization
- ✅ Type conversion

---

### Real-World E-Commerce Use Cases 🛒

**1. Product Catalog System:**
```go
// Generic search
func Search[T any](items []T, predicate func(T) bool) []T

// Find expensive products
expensive := Search(products, func(p Product) bool {
    return p.Price > 100
})

// Find out of stock
outOfStock := Search(products, func(p Product) bool {
    return p.Stock == 0
})
```

**2. Order Processing Queue:**
```go
// Generic priority queue
type PriorityQueue[T any] struct { }

// Queue orders by urgency
orderQueue := PriorityQueue[Order]{}
orderQueue.Push(urgentOrder, priority=10)
orderQueue.Push(normalOrder, priority=1)
```

**3. Multi-Level Cache:**
```go
// Generic cache with TTL
type Cache[K comparable, V any] struct { }

// Cache products, prices, inventory
productCache := Cache[int, Product]{}
priceCache := Cache[int, float64]{}
inventoryCache := Cache[int, int]{}
```

**4. Event-Driven Architecture:**
```go
// Type-safe events
orderBus := EventBus[OrderCreatedEvent]{}
paymentBus := EventBus[PaymentEvent]{}
shippingBus := EventBus[ShippingEvent]{}
```

---

### Benefits of Generics ✅

| Benefit | Description | Example |
|---------|-------------|---------|
| **Type Safety** | Errors caught at compile time | `List[int]` can't store strings |
| **Code Reuse** | Write once, use everywhere | One `Stack[T]` for all types |
| **Performance** | No runtime overhead | Same as hand-written code |
| **Maintainability** | Change once, affects all uses | Update `Sort[T]` fixes all sorts |
| **Readability** | Intent is clear | `Cache[int, Product]` is obvious |
| **No Type Assertions** | No runtime checks needed | Type safety guaranteed |

---

### When to Use Generics 🎯

**✅ USE Generics When:**

1. **Same logic, different types**
   ```go
   func Max[T Ordered](a, b T) T {
       if a > b { return a }
       return b
   }
   ```

2. **Data structures**
   ```go
   type Stack[T any] struct { items []T }
   ```

3. **Algorithms**
   ```go
   func Filter[T any](slice []T, predicate func(T) bool) []T
   ```

4. **Type-safe containers**
   ```go
   type Cache[K comparable, V any] struct { }
   ```

**❌ DON'T Use Generics When:**

1. **Different logic per type**
   ```go
   // BAD - use interfaces instead
   func Process[T any](value T) {
       switch v := value.(type) {
       case int: // int logic
       case string: // string logic
       }
   }
   ```

2. **Only one type**
   ```go
   // BAD - just use the type directly
   type IntList[T int] struct { }
   ```

3. **Simple one-off functions**
   ```go
   // BAD - over-engineering
   func AddInts[T int](a, b T) T { return a + b }
   // GOOD - just use int
   func AddInts(a, b int) int { return a + b }
   ```

---

### Generics vs Other Approaches 📊

**1. Generics vs interface{}:**

```go
// ❌ With interface{} (old way)
type Container struct {
    value interface{}
}
func (c *Container) Get() interface{} {
    return c.value
}
// Need type assertion (runtime check)
value := container.Get().(int)  // Can panic!

// ✅ With Generics (new way)
type Container[T any] struct {
    value T
}
func (c *Container[T]) Get() T {
    return c.value
}
// Type safe at compile time
value := container.Get()  // Always int, no assertion needed
```

**2. Generics vs Code Generation:**

```go
// ❌ Code Generation (old way)
//go:generate genlist -type=int
//go:generate genlist -type=string
// Creates IntList and StringList files

// ✅ Generics (new way)
type List[T any] struct { items []T }
// One implementation for all types
```

**3. Generics vs Reflection:**

```go
// ❌ Reflection (slow, error-prone)
func Max(a, b interface{}) interface{} {
    // Complex reflection code
    // Runtime overhead
    // No type safety
}

// ✅ Generics (fast, type-safe)
func Max[T Ordered](a, b T) T {
    if a > b { return a }
    return b
}
```

---

### Quick Start Example 🚀

Let's build a simple generic stack:

```go
package main

import "fmt"

// Generic Stack - works with ANY type!
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, bool) {
    if len(s.items) == 0 {
        var zero T
        return zero, false
    }
    item := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return item, true
}

func main() {
    // Stack of integers
    intStack := Stack[int]{}
    intStack.Push(1)
    intStack.Push(2)
    intStack.Push(3)
    
    val, ok := intStack.Pop()
    fmt.Println(val, ok)  // 3 true
    
    // Stack of strings (same code!)
    stringStack := Stack[string]{}
    stringStack.Push("hello")
    stringStack.Push("world")
    
    str, ok := stringStack.Pop()
    fmt.Println(str, ok)  // world true
    
    // Stack of products
    type Product struct {
        Name  string
        Price float64
    }
    
    productStack := Stack[Product]{}
    productStack.Push(Product{"Laptop", 999.99})
    
    product, ok := productStack.Pop()
    fmt.Printf("%s: $%.2f\n", product.Name, product.Price)
    // Laptop: $999.99
}
```

**Output:**
```
3 true
world true
Laptop: $999.99
```

---

### What You'll Learn in This Chapter 📚

By the end of this chapter, you'll be able to:

1. ✅ Understand what generics are and why they exist
2. ✅ Write generic functions and types
3. ✅ Use constraints (`any`, `comparable`, custom)
4. ✅ Work with type inference
5. ✅ Build generic data structures (Stack, Queue, Tree, etc.)
6. ✅ Use generics in the standard library (`slices`, `maps`)
7. ✅ Know when to use (and when NOT to use) generics
8. ✅ Apply generics to real-world e-commerce scenarios

---

### Chapter Roadmap 🗺️

```
Introduction (You Are Here!)
    ↓
Section 1: Code Duplication Problem
    ↓
Section 2: Basic Generics Syntax
    ↓
Section 3: Generic Functions
    ↓
Section 4: Constraints (any, comparable)
    ↓
Section 5: Advanced Constraints
    ↓
Section 6: Type Inference
    ↓
Section 7: Limitations to Know
    ↓
Section 8: Data Structures (Stack, Queue, Tree)
    ↓
Section 9: Idiomatic Usage
    ↓
Section 10: Standard Library Generics
    ↓
Exercises & Practice Tasks
    ↓
Wrapping Up
```

**Let's dive in! 🚀**

---

## 📖 Section 1: Reducing Repetition with Generics

### 1.1 The Problem: Code Duplication 😫

**Before Generics (Go 1.17 and earlier):**

```go
package main

import "fmt"

// Separate function for each type!
func MaxInt(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func MaxFloat64(a, b float64) float64 {
    if a > b {
        return a
    }
    return b
}

func MaxString(a, b string) string {
    if a > b {
        return a
    }
    return b
}

func main() {
    fmt.Println(MaxInt(10, 20))          // 20
    fmt.Println(MaxFloat64(10.5, 20.3))  // 20.3
    fmt.Println(MaxString("apple", "banana"))  // banana
}
```

**❌ Problems:**
- Code duplication
- Hard to maintain
- Need new function for each type
- Error-prone

### 1.2 Solution: Empty Interface (Pre-Generics Workaround)

```go
func Max(a, b interface{}) interface{} {
    // ❌ Lost type safety!
    // ❌ Runtime type assertions needed
    // ❌ Can compare incompatible types
    return nil  // Complex implementation needed
}
```

**❌ More Problems:**
- No type safety
- Runtime overhead
- Can't enforce comparable types
- Confusing API

### 1.3 The Generic Solution ✅

**With Generics (Go 1.18+):**

```go
package main

import "fmt"

// ONE function for ALL comparable types!
func Max[T comparable](a, b T) T {
    if a > b {
        return a
    }
    return b
}

func main() {
    fmt.Println(Max(10, 20))              // 20
    fmt.Println(Max(10.5, 20.3))          // 20.3
    fmt.Println(Max("apple", "banana"))   // banana
    
    // Type safety maintained!
    // result := Max(10, "hello")  // ❌ Compile error!
}
```

**✅ Benefits:**
- No code duplication
- Type safe at compile time
- One implementation
- Easy to maintain

---

## 📖 Section 2: Introducing Generics

### 2.1 What Are Generics? 🤔

**Definition:**
- Write code that works with **multiple types**
- Types are **parameters** (like function parameters)
- Checked at **compile time** (no runtime overhead)
- Also called **parametric polymorphism**

**Syntax:**
```go
func FunctionName[TypeParameter Constraint](param Type) ReturnType {
    // function body
}
```

### 2.2 Type Parameters

**Basic Example:**

```go
package main

import "fmt"

// [T any] is the type parameter
// T is like a variable for types
func Print[T any](value T) {
    fmt.Println(value)
}

func main() {
    Print[int](42)           // Explicit type
    Print[string]("hello")   // Explicit type
    Print(3.14)              // Type inferred
    Print(true)              // Type inferred
}
```

**Multiple Type Parameters:**

```go
package main

import "fmt"

// Two type parameters: K and V
func Pair[K any, V any](key K, value V) {
    fmt.Printf("Key: %v, Value: %v\n", key, value)
}

func main() {
    Pair(1, "one")           // K=int, V=string
    Pair("name", "Alice")    // K=string, V=string
    Pair(true, 100)          // K=bool, V=int
}
```

### 2.3 E-Commerce Example: Generic Response

```go
package main

import (
    "encoding/json"
    "fmt"
)

// Generic API response
type APIResponse[T any] struct {
    Success bool   `json:"success"`
    Message string `json:"message"`
    Data    T      `json:"data"`  // Generic data field
}

type Product struct {
    ID    int     `json:"id"`
    Name  string  `json:"name"`
    Price float64 `json:"price"`
}

type Order struct {
    ID    int     `json:"id"`
    Total float64 `json:"total"`
}

func main() {
    // Product response
    productResp := APIResponse[Product]{
        Success: true,
        Message: "Product found",
        Data: Product{
            ID:    1001,
            Name:  "Laptop",
            Price: 999.99,
        },
    }
    
    // Order response
    orderResp := APIResponse[Order]{
        Success: true,
        Message: "Order retrieved",
        Data: Order{
            ID:    5001,
            Total: 199.99,
        },
    }
    
    // List response
    listResp := APIResponse[[]Product]{
        Success: true,
        Message: "Products found",
        Data: []Product{
            {ID: 1001, Name: "Laptop", Price: 999.99},
            {ID: 1002, Name: "Mouse", Price: 29.99},
        },
    }
    
    // Convert to JSON
    json1, _ := json.MarshalIndent(productResp, "", "  ")
    json2, _ := json.MarshalIndent(orderResp, "", "  ")
    
    fmt.Println("Product Response:")
    fmt.Println(string(json1))
    
    fmt.Println("\nOrder Response:")
    fmt.Println(string(json2))
    
    fmt.Printf("\nList has %d products\n", len(listResp.Data))
}
```

---

## 📖 Section 3: Generic Functions and Type Parameters

### 3.1 Generic Functions 🎯

**Simple Generic Function:**

```go
package main

import "fmt"

// First - Returns first element or zero value
func First[T any](slice []T) T {
    if len(slice) == 0 {
        var zero T  // Zero value of T
        return zero
    }
    return slice[0]
}

func main() {
    nums := []int{1, 2, 3}
    fmt.Println(First(nums))  // 1
    
    names := []string{"Alice", "Bob"}
    fmt.Println(First(names))  // Alice
    
    empty := []int{}
    fmt.Println(First(empty))  // 0 (zero value)
}
```

**Generic Filter:**

```go
package main

import "fmt"

// Filter - Filter slice by predicate
func Filter[T any](slice []T, predicate func(T) bool) []T {
    result := []T{}
    for _, item := range slice {
        if predicate(item) {
            result = append(result, item)
        }
    }
    return result
}

func main() {
    nums := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
    
    // Filter even numbers
    evens := Filter(nums, func(n int) bool {
        return n%2 == 0
    })
    fmt.Println("Evens:", evens)  // [2 4 6 8 10]
    
    // Filter > 5
    greaterThan5 := Filter(nums, func(n int) bool {
        return n > 5
    })
    fmt.Println("Greater than 5:", greaterThan5)  // [6 7 8 9 10]
    
    // Works with strings too!
    words := []string{"apple", "banana", "apricot", "cherry"}
    startsWithA := Filter(words, func(s string) bool {
        return s[0] == 'a'
    })
    fmt.Println("Starts with 'a':", startsWithA)  // [apple apricot]
}
```

**Generic Map:**

```go
package main

import "fmt"

// Map - Transform slice elements
func Map[T any, U any](slice []T, transform func(T) U) []U {
    result := make([]U, len(slice))
    for i, item := range slice {
        result[i] = transform(item)
    }
    return result
}

func main() {
    nums := []int{1, 2, 3, 4, 5}
    
    // Double each number
    doubled := Map(nums, func(n int) int {
        return n * 2
    })
    fmt.Println("Doubled:", doubled)  // [2 4 6 8 10]
    
    // Convert to strings
    strings := Map(nums, func(n int) string {
        return fmt.Sprintf("Number %d", n)
    })
    fmt.Println("Strings:", strings)
    // [Number 1 Number 2 Number 3 Number 4 Number 5]
}
```

### 3.2 E-Commerce Generic Functions

```go
package main

import "fmt"

type Product struct {
    ID    int
    Name  string
    Price float64
    Stock int
}

// FindBy - Generic find function
func FindBy[T any](slice []T, predicate func(T) bool) (T, bool) {
    for _, item := range slice {
        if predicate(item) {
            return item, true
        }
    }
    var zero T
    return zero, false
}

// Contains - Check if item exists
func Contains[T comparable](slice []T, item T) bool {
    for _, v := range slice {
        if v == item {
            return true
        }
    }
    return false
}

// Distinct - Remove duplicates
func Distinct[T comparable](slice []T) []T {
    seen := make(map[T]bool)
    result := []T{}
    
    for _, item := range slice {
        if !seen[item] {
            seen[item] = true
            result = append(result, item)
        }
    }
    
    return result
}

func main() {
    products := []Product{
        {1, "Laptop", 999.99, 5},
        {2, "Mouse", 29.99, 50},
        {3, "Keyboard", 79.99, 30},
        {4, "Monitor", 299.99, 0},
    }
    
    // Find expensive product
    expensive, found := FindBy(products, func(p Product) bool {
        return p.Price > 100
    })
    if found {
        fmt.Printf("Expensive: %s ($%.2f)\n", expensive.Name, expensive.Price)
    }
    
    // Find out of stock
    outOfStock, found := FindBy(products, func(p Product) bool {
        return p.Stock == 0
    })
    if found {
        fmt.Printf("Out of stock: %s\n", outOfStock.Name)
    }
    
    // Check if ID exists
    ids := []int{1, 2, 3, 4}
    fmt.Printf("Has ID 2: %t\n", Contains(ids, 2))
    fmt.Printf("Has ID 99: %t\n", Contains(ids, 99))
    
    // Remove duplicate IDs
    duplicateIDs := []int{1, 2, 2, 3, 3, 3, 4}
    uniqueIDs := Distinct(duplicateIDs)
    fmt.Printf("Unique IDs: %v\n", uniqueIDs)
}
```

---

## 📖 Section 4: Constraints: any and comparable

### 4.1 Understanding Constraints 🔒

**What are Constraints?**
- Specify what operations are allowed on type parameters
- Similar to interfaces for types
- Defined using interface syntax

**Built-in Constraints:**

```go
any         // No constraints (any type)
comparable  // Types that support == and !=
```

### 4.2 The `any` Constraint

**Definition:**
```go
type any = interface{}  // Alias for empty interface
```

**Usage:**

```go
package main

import "fmt"

// Accept any type
func Print[T any](value T) {
    fmt.Println(value)
}

// Store any type
type Box[T any] struct {
    Value T
}

func main() {
    // Works with any type
    Print(42)
    Print("hello")
    Print([]int{1, 2, 3})
    
    // Box can hold anything
    intBox := Box[int]{Value: 42}
    stringBox := Box[string]{Value: "hello"}
    
    fmt.Println(intBox.Value)
    fmt.Println(stringBox.Value)
}
```

### 4.3 The `comparable` Constraint

**For types that support == and !=:**

```go
package main

import "fmt"

// Only works with comparable types
func Contains[T comparable](slice []T, target T) bool {
    for _, item := range slice {
        if item == target {  // Requires comparable
            return true
        }
    }
    return false
}

// Index of element
func IndexOf[T comparable](slice []T, target T) int {
    for i, item := range slice {
        if item == target {
            return i
        }
    }
    return -1
}

func main() {
    nums := []int{1, 2, 3, 4, 5}
    fmt.Println(Contains(nums, 3))    // true
    fmt.Println(Contains(nums, 99))   // false
    fmt.Println(IndexOf(nums, 4))     // 3
    
    names := []string{"Alice", "Bob", "Charlie"}
    fmt.Println(Contains(names, "Bob"))  // true
    fmt.Println(IndexOf(names, "Charlie"))  // 2
    
    // ❌ Slices are not comparable
    // slices := [][]int{{1}, {2}}
    // Contains(slices, []int{1})  // Compile error!
}
```

### 4.4 Custom Constraints

**Define your own constraints:**

```go
package main

import "fmt"

// Custom constraint: types that support + operator
type Numeric interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 |
        ~float32 | ~float64
}

// Sum works with any numeric type
func Sum[T Numeric](numbers []T) T {
    var total T
    for _, n := range numbers {
        total += n  // + operator allowed
    }
    return total
}

// Min works with ordered types
type Ordered interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 |
        ~float32 | ~float64 | ~string
}

func Min[T Ordered](a, b T) T {
    if a < b {  // < operator allowed
        return a
    }
    return b
}

func main() {
    ints := []int{1, 2, 3, 4, 5}
    fmt.Println("Sum of ints:", Sum(ints))  // 15
    
    floats := []float64{1.5, 2.5, 3.5}
    fmt.Println("Sum of floats:", Sum(floats))  // 7.5
    
    fmt.Println("Min:", Min(10, 20))  // 10
    fmt.Println("Min:", Min("apple", "banana"))  // apple
}
```

### 4.5 The `constraints` Package

**Import from standard library:**

```go
package main

import (
    "fmt"
    "golang.org/x/exp/constraints"
)

// Use predefined constraints
func Max[T constraints.Ordered](a, b T) T {
    if a > b {
        return a
    }
    return b
}

func Abs[T constraints.Signed | constraints.Float](n T) T {
    if n < 0 {
        return -n
    }
    return n
}

func main() {
    fmt.Println(Max(10, 20))  // 20
    fmt.Println(Max(3.14, 2.71))  // 3.14
    
    fmt.Println(Abs(-42))  // 42
    fmt.Println(Abs(-3.14))  // 3.14
}
```

**Available Constraints:**

```go
constraints.Signed     // Signed integers
constraints.Unsigned   // Unsigned integers
constraints.Integer    // All integers
constraints.Float      // float32, float64
constraints.Complex    // complex64, complex128
constraints.Ordered    // Supports < <= > >=
```

---

## 📖 Section 5: Type Terms and Union Types

### 5.1 Union Types with | (OR)

**Combine multiple types:**

```go
package main

import "fmt"

// Accept int OR string
type IntOrString interface {
    int | string
}

func PrintValue[T IntOrString](value T) {
    fmt.Printf("Value: %v (type: %T)\n", value, value)
}

func main() {
    PrintValue(42)       // OK
    PrintValue("hello")  // OK
    // PrintValue(3.14)  // ❌ Compile error!
}
```

### 5.2 The ~ (Tilde) Operator

**Allow underlying types:**

```go
package main

import "fmt"

type MyInt int

// Without ~: only exact type
type ExactInt interface {
    int
}

// With ~: int and types based on int
type ApproximateInt interface {
    ~int
}

func Double1[T ExactInt](n T) T {
    return n * 2
}

func Double2[T ApproximateInt](n T) T {
    return n * 2
}

func main() {
    var regular int = 10
    var custom MyInt = 20
    
    fmt.Println(Double1(regular))  // OK
    // fmt.Println(Double1(custom))  // ❌ Error: MyInt != int
    
    fmt.Println(Double2(regular))  // OK
    fmt.Println(Double2(custom))   // OK: MyInt has underlying type int
}
```

### 5.3 Complex Constraints

**E-Commerce Example:**

```go
package main

import "fmt"

// ID types
type UserID int
type ProductID int
type OrderID int

// Constraint for all ID types
type ID interface {
    ~int  // All based on int
}

// Generic cache for any ID type
type Cache[K ID, V any] struct {
    data map[K]V
}

func NewCache[K ID, V any]() *Cache[K, V] {
    return &Cache[K, V]{
        data: make(map[K]V),
    }
}

func (c *Cache[K, V]) Set(key K, value V) {
    c.data[key] = value
}

func (c *Cache[K, V]) Get(key K) (V, bool) {
    value, ok := c.data[key]
    return value, ok
}

// Price types
type USD float64
type EUR float64

type Money interface {
    ~float64
}

func Total[M Money](prices []M) M {
    var sum M
    for _, price := range prices {
        sum += price
    }
    return sum
}

func main() {
    // ID cache works with all ID types
    userCache := NewCache[UserID, string]()
    userCache.Set(UserID(1), "Alice")
    
    productCache := NewCache[ProductID, string]()
    productCache.Set(ProductID(100), "Laptop")
    
    if name, ok := userCache.Get(UserID(1)); ok {
        fmt.Println("User:", name)
    }
    
    // Money calculation
    usdPrices := []USD{99.99, 149.99, 29.99}
    fmt.Printf("Total (USD): $%.2f\n", Total(usdPrices))
    
    eurPrices := []EUR{89.99, 129.99, 24.99}
    fmt.Printf("Total (EUR): €%.2f\n", Total(eurPrices))
}
```

---

## 📖 Section 6: Type Inference

### 6.1 Automatic Type Inference ✨

**Go infers types from arguments:**

```go
package main

import "fmt"

func Print[T any](value T) {
    fmt.Printf("%v (%T)\n", value, value)
}

func Pair[K any, V any](key K, value V) {
    fmt.Printf("%v: %v\n", key, value)
}

func main() {
    // Explicit type parameters
    Print[int](42)
    Print[string]("hello")
    
    // Type inference (preferred!)
    Print(42)          // T inferred as int
    Print("hello")     // T inferred as string
    Print(3.14)        // T inferred as float64
    
    Pair(1, "one")     // K=int, V=string
    Pair("name", 100)  // K=string, V=int
}
```

### 6.2 When Type Inference Doesn't Work

**Cases requiring explicit types:**

```go
package main

import "fmt"

func Make[T any]() T {
    var zero T
    return zero
}

func Convert[T any, U any](value T) U {
    // Complex conversion logic
    var result U
    return result
}

func main() {
    // ❌ Can't infer - no parameters
    // x := Make()  // Error!
    
    // ✅ Must specify explicitly
    x := Make[int]()
    y := Make[string]()
    
    fmt.Println(x)  // 0
    fmt.Println(y)  // ""
    
    // ❌ Can't infer U
    // result := Convert(42)  // Error!
    
    // ✅ Must specify U
    result := Convert[int, string](42)
    fmt.Println(result)
}
```

### 6.3 E-Commerce Type Inference Examples

```go
package main

import "fmt"

type Product struct {
    Name  string
    Price float64
}

// Extract field from slice of structs
func PluckField[T any, F any](slice []T, extractor func(T) F) []F {
    result := make([]F, len(slice))
    for i, item := range slice {
        result[i] = extractor(item)
    }
    return result
}

// Group by key
func GroupBy[T any, K comparable](slice []T, keyFunc func(T) K) map[K][]T {
    groups := make(map[K][]T)
    for _, item := range slice {
        key := keyFunc(item)
        groups[key] = append(groups[key], item)
    }
    return groups
}

func main() {
    products := []Product{
        {"Laptop", 999.99},
        {"Mouse", 29.99},
        {"Keyboard", 79.99},
        {"Monitor", 299.99},
    }
    
    // Extract names (type inference!)
    names := PluckField(products, func(p Product) string {
        return p.Name
    })
    fmt.Println("Names:", names)
    
    // Extract prices
    prices := PluckField(products, func(p Product) float64 {
        return p.Price
    })
    fmt.Println("Prices:", prices)
    
    // Group by price range
    grouped := GroupBy(products, func(p Product) string {
        if p.Price < 100 {
            return "cheap"
        } else if p.Price < 500 {
            return "medium"
        }
        return "expensive"
    })
    
    for category, prods := range grouped {
        fmt.Printf("%s: %d products\n", category, len(prods))
    }
}
```

---

## 📖 Section 7: Limitations and Trade-offs

### 7.1 No Generic Constants ❌

```go
// ❌ Can't do this:
// const MaxSize[T any] T = 100  // Compile error!

// ✅ Use functions instead:
func MaxSize[T Numeric]() T {
    return 100
}
```

### 7.2 No Generic Methods (Only Functions) ❌

```go
type Container struct {
    data []int
}

// ❌ Can't add type parameter to method
// func (c *Container) Add[T any](item T) {  // Error!
// }

// ✅ Make the type generic instead
type GenericContainer[T any] struct {
    data []T
}

func (c *GenericContainer[T]) Add(item T) {
    c.data = append(c.data, item)
}
```

### 7.3 Type Parameters on Methods Must Match Type

```go
package main

import "fmt"

type Stack[T any] struct {
    items []T
}

// ✅ OK: Uses T from Stack
func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

// ❌ Can't add new type parameter
// func (s *Stack[T]) ConvertAndPush[U any](item U) {
// }

// ✅ Workaround: Use a function
func ConvertAndPush[T any, U any](s *Stack[T], item U, convert func(U) T) {
    s.Push(convert(item))
}

func main() {
    stack := &Stack[int]{}
    stack.Push(10)
    
    // Convert string to int and push
    ConvertAndPush(stack, "20", func(s string) int {
        // Simple conversion
        return len(s)
    })
    
    fmt.Println(stack.items)  // [10 2]
}
```

### 7.4 No Type Assertions on Type Parameters ❌

```go
func Process[T any](value T) {
    // ❌ Can't do type assertion on T
    // if str, ok := value.(string); ok {
    // }
    
    // ✅ Use interface{} if needed
    var anyValue any = value
    if str, ok := anyValue.(string); ok {
        fmt.Println("It's a string:", str)
    }
}
```

### 7.5 When NOT to Use Generics 🚫

**Don't use generics when:**

```go
// ❌ Bad: Different logic for each type
func Process[T any](value T) {
    // This is a code smell!
    switch v := any(value).(type) {
    case int:
        // int logic
    case string:
        // string logic
    case bool:
        // bool logic
    }
}

// ✅ Good: Use interface instead
type Processor interface {
    Process()
}

// ❌ Bad: Only used with one type
type IntContainer[T int] struct {  // Why generic?
    value T
}

// ✅ Good: Just use the type directly
type IntContainer struct {
    value int
}
```

**Use generics when:**
- ✅ Same logic for multiple types
- ✅ Data structures (Stack, Queue, etc.)
- ✅ Algorithms (Sort, Filter, Map, etc.)
- ✅ Type-safe containers

---

## 📖 Section 8: Combining Generics with Data Structures

### 8.1 Generic Stack 📚

**Complete implementation:**

```go
package main

import (
    "errors"
    "fmt"
)

type Stack[T any] struct {
    items []T
}

func NewStack[T any]() *Stack[T] {
    return &Stack[T]{
        items: []T{},
    }
}

func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, error) {
    if s.IsEmpty() {
        var zero T
        return zero, errors.New("stack is empty")
    }
    
    index := len(s.items) - 1
    item := s.items[index]
    s.items = s.items[:index]
    
    return item, nil
}

func (s *Stack[T]) Peek() (T, error) {
    if s.IsEmpty() {
        var zero T
        return zero, errors.New("stack is empty")
    }
    
    return s.items[len(s.items)-1], nil
}

func (s *Stack[T]) IsEmpty() bool {
    return len(s.items) == 0
}

func (s *Stack[T]) Size() int {
    return len(s.items)
}

func main() {
    // Stack of integers
    intStack := NewStack[int]()
    intStack.Push(1)
    intStack.Push(2)
    intStack.Push(3)
    
    fmt.Println("Size:", intStack.Size())
    
    if top, err := intStack.Peek(); err == nil {
        fmt.Println("Top:", top)
    }
    
    for !intStack.IsEmpty() {
        item, _ := intStack.Pop()
        fmt.Println("Popped:", item)
    }
    
    // Stack of strings
    stringStack := NewStack[string]()
    stringStack.Push("hello")
    stringStack.Push("world")
    
    fmt.Println(stringStack.Pop())  // world
    fmt.Println(stringStack.Pop())  // hello
}
```

### 8.2 Generic Queue 🎫

```go
package main

import (
    "errors"
    "fmt"
)

type Queue[T any] struct {
    items []T
}

func NewQueue[T any]() *Queue[T] {
    return &Queue[T]{
        items: []T{},
    }
}

func (q *Queue[T]) Enqueue(item T) {
    q.items = append(q.items, item)
}

func (q *Queue[T]) Dequeue() (T, error) {
    if q.IsEmpty() {
        var zero T
        return zero, errors.New("queue is empty")
    }
    
    item := q.items[0]
    q.items = q.items[1:]
    
    return item, nil
}

func (q *Queue[T]) Peek() (T, error) {
    if q.IsEmpty() {
        var zero T
        return zero, errors.New("queue is empty")
    }
    
    return q.items[0], nil
}

func (q *Queue[T]) IsEmpty() bool {
    return len(q.items) == 0
}

func (q *Queue[T]) Size() int {
    return len(q.items)
}

func main() {
    queue := NewQueue[string]()
    
    queue.Enqueue("First")
    queue.Enqueue("Second")
    queue.Enqueue("Third")
    
    fmt.Println("Size:", queue.Size())
    
    for !queue.IsEmpty() {
        item, _ := queue.Dequeue()
        fmt.Println("Processing:", item)
    }
}
```

### 8.3 Generic Linked List 🔗

```go
package main

import "fmt"

type Node[T any] struct {
    Value T
    Next  *Node[T]
}

type LinkedList[T any] struct {
    Head *Node[T]
    size int
}

func NewLinkedList[T any]() *LinkedList[T] {
    return &LinkedList[T]{}
}

func (l *LinkedList[T]) Append(value T) {
    newNode := &Node[T]{Value: value}
    
    if l.Head == nil {
        l.Head = newNode
    } else {
        current := l.Head
        for current.Next != nil {
            current = current.Next
        }
        current.Next = newNode
    }
    
    l.size++
}

func (l *LinkedList[T]) Prepend(value T) {
    newNode := &Node[T]{Value: value, Next: l.Head}
    l.Head = newNode
    l.size++
}

func (l *LinkedList[T]) Size() int {
    return l.size
}

func (l *LinkedList[T]) ToSlice() []T {
    result := make([]T, 0, l.size)
    current := l.Head
    
    for current != nil {
        result = append(result, current.Value)
        current = current.Next
    }
    
    return result
}

func main() {
    list := NewLinkedList[int]()
    
    list.Append(1)
    list.Append(2)
    list.Append(3)
    list.Prepend(0)
    
    fmt.Println("Size:", list.Size())
    fmt.Println("Items:", list.ToSlice())
}
```

### 8.4 Generic Binary Search Tree 🌳

```go
package main

import "fmt"

type Ordered interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 |
        ~float32 | ~float64 | ~string
}

type BSTNode[T Ordered] struct {
    Value T
    Left  *BSTNode[T]
    Right *BSTNode[T]
}

type BST[T Ordered] struct {
    Root *BSTNode[T]
}

func NewBST[T Ordered]() *BST[T] {
    return &BST[T]{}
}

func (bst *BST[T]) Insert(value T) {
    bst.Root = insertNode(bst.Root, value)
}

func insertNode[T Ordered](node *BSTNode[T], value T) *BSTNode[T] {
    if node == nil {
        return &BSTNode[T]{Value: value}
    }
    
    if value < node.Value {
        node.Left = insertNode(node.Left, value)
    } else if value > node.Value {
        node.Right = insertNode(node.Right, value)
    }
    
    return node
}

func (bst *BST[T]) Contains(value T) bool {
    return contains(bst.Root, value)
}

func contains[T Ordered](node *BSTNode[T], value T) bool {
    if node == nil {
        return false
    }
    
    if value == node.Value {
        return true
    } else if value < node.Value {
        return contains(node.Left, value)
    } else {
        return contains(node.Right, value)
    }
}

func (bst *BST[T]) InOrder() []T {
    result := []T{}
    inOrder(bst.Root, &result)
    return result
}

func inOrder[T Ordered](node *BSTNode[T], result *[]T) {
    if node != nil {
        inOrder(node.Left, result)
        *result = append(*result, node.Value)
        inOrder(node.Right, result)
    }
}

func main() {
    bst := NewBST[int]()
    
    values := []int{50, 30, 70, 20, 40, 60, 80}
    for _, v := range values {
        bst.Insert(v)
    }
    
    fmt.Println("In-order traversal:", bst.InOrder())
    fmt.Println("Contains 40:", bst.Contains(40))
    fmt.Println("Contains 100:", bst.Contains(100))
}
```

### 8.5 Generic Result/Option Types 🎁

```go
package main

import (
    "errors"
    "fmt"
)

// Result type (like Rust's Result<T, E>)
type Result[T any] struct {
    value T
    err   error
}

func Ok[T any](value T) Result[T] {
    return Result[T]{value: value, err: nil}
}

func Err[T any](err error) Result[T] {
    var zero T
    return Result[T]{value: zero, err: err}
}

func (r Result[T]) IsOk() bool {
    return r.err == nil
}

func (r Result[T]) IsErr() bool {
    return r.err != nil
}

func (r Result[T]) Unwrap() T {
    if r.IsErr() {
        panic(r.err)
    }
    return r.value
}

func (r Result[T]) UnwrapOr(defaultValue T) T {
    if r.IsErr() {
        return defaultValue
    }
    return r.value
}

// Option type (like Rust's Option<T>)
type Option[T any] struct {
    value  T
    isSome bool
}

func Some[T any](value T) Option[T] {
    return Option[T]{value: value, isSome: true}
}

func None[T any]() Option[T] {
    var zero T
    return Option[T]{value: zero, isSome: false}
}

func (o Option[T]) IsSome() bool {
    return o.isSome
}

func (o Option[T]) IsNone() bool {
    return !o.isSome
}

func (o Option[T]) Unwrap() T {
    if o.IsNone() {
        panic("called Unwrap on None")
    }
    return o.value
}

func (o Option[T]) UnwrapOr(defaultValue T) T {
    if o.IsNone() {
        return defaultValue
    }
    return o.value
}

// E-Commerce example
type Product struct {
    ID    int
    Name  string
    Price float64
}

func FindProduct(id int) Result[Product] {
    products := map[int]Product{
        1: {1, "Laptop", 999.99},
        2: {2, "Mouse", 29.99},
    }
    
    if product, ok := products[id]; ok {
        return Ok(product)
    }
    
    return Err[Product](errors.New("product not found"))
}

func GetDiscount(productID int) Option[float64] {
    discounts := map[int]float64{
        1: 10.0,  // 10% off
    }
    
    if discount, ok := discounts[productID]; ok {
        return Some(discount)
    }
    
    return None[float64]()
}

func main() {
    // Result example
    result1 := FindProduct(1)
    if result1.IsOk() {
        product := result1.Unwrap()
        fmt.Printf("Found: %s - $%.2f\n", product.Name, product.Price)
    }
    
    result2 := FindProduct(999)
    if result2.IsErr() {
        fmt.Println("Error:", result2.err)
    }
    
    // With default
    product := FindProduct(999).UnwrapOr(Product{0, "Unknown", 0.0})
    fmt.Printf("Product: %s\n", product.Name)
    
    // Option example
    discount1 := GetDiscount(1)
    if discount1.IsSome() {
        fmt.Printf("Discount: %.2f%%\n", discount1.Unwrap())
    }
    
    discount2 := GetDiscount(2)
    if discount2.IsNone() {
        fmt.Println("No discount available")
    }
    
    // With default
    discount := GetDiscount(999).UnwrapOr(0.0)
    fmt.Printf("Discount: %.2f%%\n", discount)
}
```

---

## 📖 Section 9: Idiomatic Use of Generics

### 9.1 When to Use Generics ✅

**Good Use Cases:**

```go
// ✅ Data structures
type Stack[T any] struct { items []T }
type Queue[T any] struct { items []T }
type Tree[T Ordered] struct { root *Node[T] }

// ✅ Algorithms
func Sort[T Ordered](slice []T) []T { }
func Filter[T any](slice []T, predicate func(T) bool) []T { }
func Map[T, U any](slice []T, transform func(T) U) []U { }

// ✅ Type-safe containers
type Cache[K comparable, V any] struct { data map[K]V }
type Pool[T any] struct { objects []T }

// ✅ Utility functions
func Max[T Ordered](a, b T) T { }
func Contains[T comparable](slice []T, item T) bool { }
```

### 9.2 When NOT to Use Generics ❌

**Bad Use Cases:**

```go
// ❌ Different behavior per type - use interface!
func Process[T any](value T) {
    switch v := any(value).(type) {
    case int: // int-specific logic
    case string: // string-specific logic
    }
}

// ✅ Better: Use interface
type Processor interface {
    Process()
}

// ❌ Only one type - don't use generics!
type StringList[T string] struct { items []T }

// ✅ Better: Just use string
type StringList struct { items []string }

// ❌ Overcomplicating simple code
func Add[T int](a, b T) T { return a + b }

// ✅ Better: No need for generics
func Add(a, b int) int { return a + b }
```

### 9.3 Best Practices 📝

**1. Keep Type Parameters Simple:**

```go
// ✅ Good: Clear names
func Map[T any, U any](slice []T, fn func(T) U) []U { }

// ❌ Bad: Too many type parameters
func ComplexFunc[A, B, C, D, E, F any](a A, b B, c C, d D, e E, f F) { }
```

**2. Use Descriptive Constraint Names:**

```go
// ✅ Good
type Numeric interface {
    ~int | ~int64 | ~float32 | ~float64
}

// ❌ Bad
type T1 interface {
    ~int | ~float64
}
```

**3. Prefer Simpler Alternatives:**

```go
// Sometimes interface{} is fine
func Print(value any) {
    fmt.Println(value)
}

// Sometimes interfaces are better
type Reader interface {
    Read([]byte) (int, error)
}
```

---

## 📖 Section 10: Generics in the Standard Library

### 10.1 The `slices` Package 📦

```go
package main

import (
    "fmt"
    "slices"
)

func main() {
    nums := []int{3, 1, 4, 1, 5, 9, 2, 6}
    
    // Sort
    slices.Sort(nums)
    fmt.Println("Sorted:", nums)
    
    // Contains
    fmt.Println("Contains 5:", slices.Contains(nums, 5))
    
    // Index
    index := slices.Index(nums, 5)
    fmt.Println("Index of 5:", index)
    
    // Max/Min
    fmt.Println("Max:", slices.Max(nums))
    fmt.Println("Min:", slices.Min(nums))
    
    // Clone
    clone := slices.Clone(nums)
    fmt.Println("Clone:", clone)
    
    // Equal
    fmt.Println("Equal:", slices.Equal(nums, clone))
    
    // Reverse
    slices.Reverse(nums)
    fmt.Println("Reversed:", nums)
}
```

### 10.2 The `maps` Package 🗺️

```go
package main

import (
    "fmt"
    "maps"
)

func main() {
    map1 := map[string]int{
        "apple":  5,
        "banana": 3,
    }
    
    // Clone
    map2 := maps.Clone(map1)
    fmt.Println("Clone:", map2)
    
    // Equal
    fmt.Println("Equal:", maps.Equal(map1, map2))
    
    // Copy
    map3 := make(map[string]int)
    maps.Copy(map3, map1)
    fmt.Println("Copied:", map3)
    
    // DeleteFunc
    maps.DeleteFunc(map3, func(k string, v int) bool {
        return v < 4
    })
    fmt.Println("After delete:", map3)
}
```

### 10.3 The `cmp` Package ⚖️

```go
package main

import (
    "cmp"
    "fmt"
)

func main() {
    // Compare
    fmt.Println(cmp.Compare(10, 20))  // -1
    fmt.Println(cmp.Compare(20, 10))  // 1
    fmt.Println(cmp.Compare(10, 10))  // 0
    
    // Or (return first non-zero)
    result := cmp.Or(0, 0, 5)
    fmt.Println("Or:", result)  // 5
}
```

### 10.4 `sync.Pool` with Generics (Go 1.23+) 🏊

```go
package main

import (
    "fmt"
    "sync"
)

// Generic wrapper for sync.Pool
type Pool[T any] struct {
    pool sync.Pool
}

func NewPool[T any](newFunc func() T) *Pool[T] {
    return &Pool[T]{
        pool: sync.Pool{
            New: func() any {
                return newFunc()
            },
        },
    }
}

func (p *Pool[T]) Get() T {
    return p.pool.Get().(T)
}

func (p *Pool[T]) Put(item T) {
    p.pool.Put(item)
}

func main() {
    // Pool of byte slices
    bufferPool := NewPool(func() []byte {
        return make([]byte, 1024)
    })
    
    // Get buffer
    buffer := bufferPool.Get()
    fmt.Printf("Got buffer: %d bytes\n", len(buffer))
    
    // Use buffer...
    
    // Put back
    bufferPool.Put(buffer)
}
```

---

## 🏋️ Exercises and Challenges

### Exercise 1: Generic Utility Functions

**Implement these generic utility functions:**

```go
package main

import "fmt"

// 1. Reverse - Reverse a slice
func Reverse[T any](slice []T) []T {
    result := make([]T, len(slice))
    for i, v := range slice {
        result[len(slice)-1-i] = v
    }
    return result
}

// 2. Reduce - Reduce a slice to a single value
func Reduce[T any, U any](slice []T, initial U, fn func(U, T) U) U {
    result := initial
    for _, v := range slice {
        result = fn(result, v)
    }
    return result
}

// 3. Zip - Combine two slices
func Zip[T any, U any](slice1 []T, slice2 []U) []struct {
    First  T
    Second U
} {
    length := len(slice1)
    if len(slice2) < length {
        length = len(slice2)
    }
    
    result := make([]struct {
        First  T
        Second U
    }, length)
    
    for i := 0; i < length; i++ {
        result[i].First = slice1[i]
        result[i].Second = slice2[i]
    }
    
    return result
}

// 4. Unique - Remove duplicates
func Unique[T comparable](slice []T) []T {
    seen := make(map[T]bool)
    result := []T{}
    
    for _, v := range slice {
        if !seen[v] {
            seen[v] = true
            result = append(result, v)
        }
    }
    
    return result
}

func main() {
    // Test Reverse
    nums := []int{1, 2, 3, 4, 5}
    fmt.Println("Reverse:", Reverse(nums))  // [5 4 3 2 1]
    
    // Test Reduce (sum)
    sum := Reduce(nums, 0, func(acc, v int) int {
        return acc + v
    })
    fmt.Println("Sum:", sum)  // 15
    
    // Test Zip
    names := []string{"Alice", "Bob", "Charlie"}
    ages := []int{25, 30, 35}
    zipped := Zip(names, ages)
    fmt.Println("Zipped:", zipped)
    
    // Test Unique
    duplicates := []int{1, 2, 2, 3, 3, 3, 4, 5, 5}
    fmt.Println("Unique:", Unique(duplicates))  // [1 2 3 4 5]
}
```

### Exercise 2: Generic Set Implementation

```go
package main

import "fmt"

type Set[T comparable] struct {
    items map[T]struct{}
}

func NewSet[T comparable]() *Set[T] {
    return &Set[T]{
        items: make(map[T]struct{}),
    }
}

func (s *Set[T]) Add(item T) {
    s.items[item] = struct{}{}
}

func (s *Set[T]) Remove(item T) {
    delete(s.items, item)
}

func (s *Set[T]) Contains(item T) bool {
    _, ok := s.items[item]
    return ok
}

func (s *Set[T]) Size() int {
    return len(s.items)
}

func (s *Set[T]) Items() []T {
    result := make([]T, 0, len(s.items))
    for item := range s.items {
        result = append(result, item)
    }
    return result
}

func (s *Set[T]) Union(other *Set[T]) *Set[T] {
    result := NewSet[T]()
    for item := range s.items {
        result.Add(item)
    }
    for item := range other.items {
        result.Add(item)
    }
    return result
}

func (s *Set[T]) Intersection(other *Set[T]) *Set[T] {
    result := NewSet[T]()
    for item := range s.items {
        if other.Contains(item) {
            result.Add(item)
        }
    }
    return result
}

func (s *Set[T]) Difference(other *Set[T]) *Set[T] {
    result := NewSet[T]()
    for item := range s.items {
        if !other.Contains(item) {
            result.Add(item)
        }
    }
    return result
}

func main() {
    set1 := NewSet[int]()
    set1.Add(1)
    set1.Add(2)
    set1.Add(3)
    
    set2 := NewSet[int]()
    set2.Add(3)
    set2.Add(4)
    set2.Add(5)
    
    fmt.Println("Set1:", set1.Items())
    fmt.Println("Set2:", set2.Items())
    fmt.Println("Union:", set1.Union(set2).Items())
    fmt.Println("Intersection:", set1.Intersection(set2).Items())
    fmt.Println("Difference:", set1.Difference(set2).Items())
}
```

---

## 💪 Practice Tasks for Students

### 📝 Task 1: Generic Cache with TTL (Easy-Medium)

**🎯 Goal:**
Build a generic in-memory cache with Time-To-Live (TTL) support.

**Requirements:**
1. Create a generic `Cache[K comparable, V any]` type
2. Support basic operations:
   - `Set(key K, value V, ttl time.Duration)`
   - `Get(key K) (V, bool)`
   - `Delete(key K)`
   - `Clear()`
   - `Size() int`
3. Items should expire after their TTL
4. Add a method to clean up expired items
5. Make it thread-safe (bonus)

**Template:**
```go
package main

import (
    "fmt"
    "sync"
    "time"
)

type CacheItem[V any] struct {
    Value      V
    ExpiresAt  time.Time
}

type Cache[K comparable, V any] struct {
    items map[K]CacheItem[V]
    mu    sync.RWMutex
}

func NewCache[K comparable, V any]() *Cache[K, V] {
    return &Cache[K, V]{
        items: make(map[K]CacheItem[V]),
    }
}

func (c *Cache[K, V]) Set(key K, value V, ttl time.Duration) {
    // TODO: Lock mutex
    // TODO: Store with expiration time
    // TODO: Unlock mutex
}

func (c *Cache[K, V]) Get(key K) (V, bool) {
    // TODO: Lock mutex for reading
    // TODO: Check if key exists
    // TODO: Check if expired
    // TODO: Return value or zero value
    var zero V
    return zero, false
}

func (c *Cache[K, V]) Delete(key K) {
    // TODO: Implement
}

func (c *Cache[K, V]) Clear() {
    // TODO: Implement
}

func (c *Cache[K, V]) Size() int {
    // TODO: Implement with read lock
    return 0
}

func (c *Cache[K, V]) CleanExpired() int {
    // TODO: Remove all expired items
    // Return number of items removed
    return 0
}

func main() {
    // Test with products
    type Product struct {
        Name  string
        Price float64
    }
    
    cache := NewCache[int, Product]()
    
    // Set with 2 second TTL
    cache.Set(1, Product{"Laptop", 999.99}, 2*time.Second)
    cache.Set(2, Product{"Mouse", 29.99}, 5*time.Second)
    
    // Get immediately
    if product, ok := cache.Get(1); ok {
        fmt.Printf("Found: %s - $%.2f\n", product.Name, product.Price)
    }
    
    fmt.Printf("Cache size: %d\n", cache.Size())
    
    // Wait 3 seconds
    fmt.Println("Waiting 3 seconds...")
    time.Sleep(3 * time.Second)
    
    // Try to get expired item
    if _, ok := cache.Get(1); !ok {
        fmt.Println("Product 1 expired")
    }
    
    // Should still have product 2
    if product, ok := cache.Get(2); ok {
        fmt.Printf("Found: %s - $%.2f\n", product.Name, product.Price)
    }
    
    // Clean expired
    removed := cache.CleanExpired()
    fmt.Printf("Cleaned %d expired items\n", removed)
    fmt.Printf("Cache size after cleanup: %d\n", cache.Size())
}
```

**Expected Output:**
```
Found: Laptop - $999.99
Cache size: 2
Waiting 3 seconds...
Product 1 expired
Found: Mouse - $29.99
Cleaned 1 expired items
Cache size after cleanup: 1
```

**Bonus Challenges:**
- Add automatic cleanup goroutine that runs periodically
- Add statistics (hits, misses, evictions)
- Implement LRU eviction policy
- Add OnExpire callback function

---

### 📝 Task 2: Generic Event Bus (Medium)

**🎯 Goal:**
Create a type-safe event bus system using generics for event-driven architecture.

**Requirements:**
1. Create an `EventBus[T any]` type
2. Support subscribing to events with handlers
3. Support publishing events
4. Allow multiple subscribers
5. Each subscriber should receive the event
6. Support unsubscribing

**Template:**
```go
package main

import (
    "fmt"
    "sync"
)

type EventHandler[T any] func(T)

type Subscription struct {
    id string
}

type EventBus[T any] struct {
    handlers map[string]EventHandler[T]
    nextID   int
    mu       sync.RWMutex
}

func NewEventBus[T any]() *EventBus[T] {
    return &EventBus[T]{
        handlers: make(map[string]EventHandler[T]),
        nextID:   1,
    }
}

func (eb *EventBus[T]) Subscribe(handler EventHandler[T]) *Subscription {
    // TODO: Lock mutex
    // TODO: Generate unique ID
    // TODO: Store handler
    // TODO: Return subscription
    return nil
}

func (eb *EventBus[T]) Unsubscribe(sub *Subscription) {
    // TODO: Lock mutex
    // TODO: Remove handler by subscription ID
}

func (eb *EventBus[T]) Publish(event T) {
    // TODO: Lock for reading
    // TODO: Call all handlers with the event
    // TODO: Consider using goroutines for async (bonus)
}

func (eb *EventBus[T]) SubscriberCount() int {
    // TODO: Implement with read lock
    return 0
}

// Example: E-Commerce Events
type OrderCreatedEvent struct {
    OrderID    int
    CustomerID int
    Total      float64
}

type PaymentProcessedEvent struct {
    OrderID       int
    Amount        float64
    TransactionID string
}

func main() {
    // Create event buses for different event types
    orderBus := NewEventBus[OrderCreatedEvent]()
    paymentBus := NewEventBus[PaymentProcessedEvent]()
    
    // Subscribe to order events
    sub1 := orderBus.Subscribe(func(e OrderCreatedEvent) {
        fmt.Printf("📧 Sending email for order #%d\n", e.OrderID)
    })
    
    sub2 := orderBus.Subscribe(func(e OrderCreatedEvent) {
        fmt.Printf("📊 Logging order #%d to analytics\n", e.OrderID)
    })
    
    orderBus.Subscribe(func(e OrderCreatedEvent) {
        fmt.Printf("📦 Starting fulfillment for order #%d\n", e.OrderID)
    })
    
    // Subscribe to payment events
    paymentBus.Subscribe(func(e PaymentProcessedEvent) {
        fmt.Printf("💳 Payment processed: $%.2f (txn: %s)\n", 
            e.Amount, e.TransactionID)
    })
    
    // Publish events
    fmt.Println("=== Publishing Order Event ===")
    orderBus.Publish(OrderCreatedEvent{
        OrderID:    1001,
        CustomerID: 2001,
        Total:      199.99,
    })
    
    fmt.Println("\n=== Publishing Payment Event ===")
    paymentBus.Publish(PaymentProcessedEvent{
        OrderID:       1001,
        Amount:        199.99,
        TransactionID: "txn_abc123",
    })
    
    // Unsubscribe
    orderBus.Unsubscribe(sub1)
    
    fmt.Println("\n=== After Unsubscribe ===")
    orderBus.Publish(OrderCreatedEvent{
        OrderID:    1002,
        CustomerID: 2002,
        Total:      299.99,
    })
    
    fmt.Printf("\nOrder subscribers: %d\n", orderBus.SubscriberCount())
    fmt.Printf("Payment subscribers: %d\n", paymentBus.SubscriberCount())
}
```

**Expected Output:**
```
=== Publishing Order Event ===
📧 Sending email for order #1001
📊 Logging order #1001 to analytics
📦 Starting fulfillment for order #1001

=== Publishing Payment Event ===
💳 Payment processed: $199.99 (txn: txn_abc123)

=== After Unsubscribe ===
📊 Logging order #1002 to analytics
📦 Starting fulfillment for order #1002

Order subscribers: 2
Payment subscribers: 1
```

**Bonus Challenges:**
- Add async event handling (use goroutines)
- Add event filtering based on conditions
- Add priority levels for handlers
- Implement error handling in handlers
- Add event replay feature
- Add event history/audit log

---

### 📝 Task 3: Generic Priority Queue with Binary Heap (Hard)

**🎯 Goal:**
Implement a generic priority queue using a binary heap for efficient operations.

**Requirements:**

**1. Generic Priority Queue:**
- Support any comparable priority type
- Implement min-heap or max-heap
- Thread-safe operations

**2. Core Operations:**
- `Push(item T, priority P)` - O(log n)
- `Pop() (T, error)` - O(log n)
- `Peek() (T, error)` - O(1)
- `Size() int` - O(1)
- `IsEmpty() bool` - O(1)

**3. E-Commerce Use Cases:**
- Order processing by urgency
- Customer support tickets by priority
- Shipping queue by delivery date

**Complete Template:**

```go
package main

import (
    "container/heap"
    "errors"
    "fmt"
    "sync"
)

// Priority constraint
type Priority interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 |
        ~float32 | ~float64
}

// Item in priority queue
type PQItem[T any, P Priority] struct {
    Value    T
    Priority P
    index    int  // for heap operations
}

// Internal heap implementation
type pqHeap[T any, P Priority] struct {
    items   []*PQItem[T, P]
    compare func(P, P) bool  // comparator function
}

// Implement heap.Interface
func (h *pqHeap[T, P]) Len() int {
    return len(h.items)
}

func (h *pqHeap[T, P]) Less(i, j int) bool {
    // TODO: Use comparator to determine order
    return h.compare(h.items[i].Priority, h.items[j].Priority)
}

func (h *pqHeap[T, P]) Swap(i, j int) {
    // TODO: Swap items and update their indices
    h.items[i], h.items[j] = h.items[j], h.items[i]
    h.items[i].index = i
    h.items[j].index = j
}

func (h *pqHeap[T, P]) Push(x any) {
    // TODO: Add item to heap
    item := x.(*PQItem[T, P])
    item.index = len(h.items)
    h.items = append(h.items, item)
}

func (h *pqHeap[T, P]) Pop() any {
    // TODO: Remove and return last item
    old := h.items
    n := len(old)
    item := old[n-1]
    item.index = -1
    h.items = old[0 : n-1]
    return item
}

// Priority Queue
type PriorityQueue[T any, P Priority] struct {
    heap *pqHeap[T, P]
    mu   sync.RWMutex
}

// NewMinPQ - Create min priority queue (lower value = higher priority)
func NewMinPQ[T any, P Priority]() *PriorityQueue[T, P] {
    pq := &PriorityQueue[T, P]{
        heap: &pqHeap[T, P]{
            items:   []*PQItem[T, P]{},
            compare: func(a, b P) bool { return a < b },
        },
    }
    heap.Init(pq.heap)
    return pq
}

// NewMaxPQ - Create max priority queue (higher value = higher priority)
func NewMaxPQ[T any, P Priority]() *PriorityQueue[T, P] {
    pq := &PriorityQueue[T, P]{
        heap: &pqHeap[T, P]{
            items:   []*PQItem[T, P]{},
            compare: func(a, b P) bool { return a > b },
        },
    }
    heap.Init(pq.heap)
    return pq
}

func (pq *PriorityQueue[T, P]) Push(value T, priority P) {
    // TODO: Lock mutex
    // TODO: Create item and push to heap
    // TODO: Unlock mutex
}

func (pq *PriorityQueue[T, P]) Pop() (T, error) {
    // TODO: Lock mutex
    // TODO: Check if empty
    // TODO: Pop from heap
    // TODO: Return value
    var zero T
    return zero, errors.New("queue is empty")
}

func (pq *PriorityQueue[T, P]) Peek() (T, error) {
    // TODO: Lock for reading
    // TODO: Return first item without removing
    var zero T
    return zero, errors.New("queue is empty")
}

func (pq *PriorityQueue[T, P]) Size() int {
    pq.mu.RLock()
    defer pq.mu.RUnlock()
    return pq.heap.Len()
}

func (pq *PriorityQueue[T, P]) IsEmpty() bool {
    return pq.Size() == 0
}

// ========================================
// E-Commerce Examples
// ========================================

type Order struct {
    ID       int
    Customer string
    Total    float64
    Urgent   bool
}

type SupportTicket struct {
    ID       int
    Customer string
    Issue    string
}

func main() {
    // Example 1: Order Processing Queue
    fmt.Println("=== Order Processing Queue (Max Heap) ===")
    orderQueue := NewMaxPQ[Order, int]()
    
    // Add orders (higher priority = more urgent)
    orders := []struct {
        order    Order
        priority int
    }{
        {Order{1001, "Alice", 199.99, false}, 1},
        {Order{1002, "Bob", 999.99, true}, 10},    // Urgent!
        {Order{1003, "Charlie", 49.99, false}, 1},
        {Order{1004, "Diana", 299.99, true}, 10},  // Urgent!
        {Order{1005, "Eve", 599.99, false}, 5},    // Medium
    }
    
    for _, o := range orders {
        orderQueue.Push(o.order, o.priority)
    }
    
    fmt.Printf("Orders in queue: %d\n\n", orderQueue.Size())
    
    // Process orders (urgent first)
    for !orderQueue.IsEmpty() {
        order, _ := orderQueue.Pop()
        urgentStr := ""
        if order.Urgent {
            urgentStr = " [URGENT]"
        }
        fmt.Printf("Processing Order #%d: %s - $%.2f%s\n",
            order.ID, order.Customer, order.Total, urgentStr)
    }
    
    // Example 2: Support Ticket Queue
    fmt.Println("\n=== Support Ticket Queue (Min Heap) ===")
    ticketQueue := NewMinPQ[SupportTicket, int]()  // Lower = higher priority
    
    tickets := []struct {
        ticket   SupportTicket
        priority int
    }{
        {SupportTicket{1, "Alice", "Login issue"}, 3},
        {SupportTicket{2, "Bob", "Payment failed"}, 1},     // Critical!
        {SupportTicket{3, "Charlie", "General question"}, 5},
        {SupportTicket{4, "Diana", "Order not received"}, 2}, // High
        {SupportTicket{5, "Eve", "Feature request"}, 4},
    }
    
    for _, t := range tickets {
        ticketQueue.Push(t.ticket, t.priority)
    }
    
    fmt.Printf("Tickets in queue: %d\n\n", ticketQueue.Size())
    
    // Process tickets (critical first)
    priorityNames := map[int]string{
        1: "CRITICAL",
        2: "HIGH",
        3: "MEDIUM",
        4: "LOW",
        5: "VERY LOW",
    }
    
    for !ticketQueue.IsEmpty() {
        ticket, _ := ticketQueue.Pop()
        // Note: In real implementation, store priority with item
        fmt.Printf("Handling Ticket #%d [%s]: %s - %s\n",
            ticket.ID, "Priority", ticket.Customer, ticket.Issue)
    }
    
    // Example 3: Peek without removing
    fmt.Println("\n=== Peek Example ===")
    taskQueue := NewMaxPQ[string, int]()
    taskQueue.Push("Low priority task", 1)
    taskQueue.Push("High priority task", 10)
    taskQueue.Push("Medium priority task", 5)
    
    // Peek at highest priority
    if task, err := taskQueue.Peek(); err == nil {
        fmt.Printf("Next task to process: %s\n", task)
    }
    fmt.Printf("Queue size: %d (unchanged after peek)\n", taskQueue.Size())
    
    // Now actually process
    task, _ := taskQueue.Pop()
    fmt.Printf("Processing: %s\n", task)
    fmt.Printf("Queue size after pop: %d\n", taskQueue.Size())
}
```

**Success Criteria:**
1. ✅ All heap operations implemented correctly
2. ✅ Both min and max heaps work
3. ✅ Thread-safe with mutex
4. ✅ Generic over both item and priority types
5. ✅ Efficient O(log n) push/pop operations
6. ✅ Peek doesn't modify the queue
7. ✅ All e-commerce examples work

**Bonus Challenges:**
1. Add `Update(item T, newPriority P)` method
2. Add `Remove(item T)` method
3. Add `Contains(item T) bool` method
4. Implement custom comparator function
5. Add bulk operations (`PushAll`, `PopN`)
6. Add statistics (average priority, etc.)
7. Implement time-based priority (deadline support)

**Hints:**
- Use `container/heap` package from standard library
- Store index in items for O(1) lookups
- Comparator function determines min vs max heap
- Test with both numeric and custom priority types
- Remember: heap indices change after push/pop!
- Lock mutex for all operations that modify state

---

## 🎓 Evaluation Rubric

### Task 1: Generic Cache with TTL - 30 points
- ✅ Basic operations (Set, Get, Delete, Clear) - 10 pts
- ✅ TTL expiration logic works correctly - 8 pts
- ✅ CleanExpired method - 5 pts
- ✅ Thread safety with mutex - 5 pts
- ✅ Edge cases handled (expired items, empty cache) - 2 pts

### Task 2: Generic Event Bus - 35 points
- ✅ Subscribe/Unsubscribe functionality - 10 pts
- ✅ Publish to all subscribers correctly - 10 pts
- ✅ Thread safety with mutex - 8 pts
- ✅ Multiple event types work independently - 5 pts
- ✅ Async handling (bonus) - 2 pts

### Task 3: Generic Priority Queue - 35 points
- ✅ Heap interface implemented correctly - 10 pts
- ✅ Push/Pop operations work - 8 pts
- ✅ Both min and max heaps work - 7 pts
- ✅ Thread safety with mutex - 5 pts
- ✅ Generic over both value and priority - 3 pts
- ✅ E-commerce examples run successfully - 2 pts

**Total: 100 points**

---

## 🎁 Wrapping Up Chapter 8

### ✅ What You Learned:

**1. Generics Fundamentals:**
- Type parameters and constraints
- Generic functions and types
- Type inference mechanism
- When Go infers types automatically

**2. Constraints System:**
- Built-in: `any` and `comparable`
- Custom constraints with interfaces
- Union types with `|` operator
- Type terms with `~` operator
- The `constraints` package

**3. Generic Data Structures:**
- Stack, Queue, Linked List
- Binary Search Tree
- Result/Option types
- Generic sets and caches
- Priority queues

**4. Idiomatic Go Generics:**
- When to use generics (data structures, algorithms)
- When NOT to use generics (different behavior per type)
- Best practices and naming conventions
- Balancing generics with interfaces

**5. Standard Library Integration:**
- `slices` package (Sort, Contains, Max, Min, etc.)
- `maps` package (Clone, Equal, Copy, etc.)
- `cmp` package (Compare, Or)
- Future generic APIs coming to Go

### 🎯 Key Takeaways:

1. **Generics reduce code duplication** - Write once, use with many types
2. **Compile-time type safety** - No runtime overhead or type assertions
3. **Use constraints wisely** - Keep them simple and necessary
4. **Standard library adoption** - More generic functions being added
5. **Balance is key** - Sometimes interfaces are still better

### 📚 Best Practices Summary:

**DO ✅:**
- Use generics for collections and data structures
- Use generics for algorithms (sort, filter, map, reduce)
- Keep type parameter names descriptive (T, K, V, E)
- Leverage type inference when possible
- Use `constraints.Ordered` for comparisons
- Make generic types thread-safe when needed

**DON'T ❌:**
- Use generics when behavior differs significantly per type
- Over-engineer simple code with unnecessary generics
- Create overly complex constraints
- Replace all interfaces with generics
- Use type switches on generic type parameters
- Forget about the zero value problem

### 🚀 Real-World Applications:

**Perfect for Generics:**
- ✅ Data structures (List, Stack, Queue, Tree, Graph)
- ✅ Algorithms (Sort, Search, Filter, Map, Reduce)
- ✅ Caches and object pools
- ✅ Result/Option/Either types
- ✅ Event buses and pub/sub systems
- ✅ Generic middleware patterns

**Better with Interfaces:**
- ✅ HTTP handlers
- ✅ Database operations
- ✅ I/O operations
- ✅ Plugin systems
- ✅ When behavior varies by type

### 📊 Generics Timeline:

```
2022 (Go 1.18) ➜ Generics introduced!
2022 (Go 1.19) ➜ Bug fixes and improvements
2023 (Go 1.20) ➜ slices and maps packages
2023 (Go 1.21) ➜ cmp package, clearer errors
2024 (Go 1.22+) ➜ Wider stdlib adoption
Future         ➜ More generic APIs coming
```

### 💡 Final Tips for Mastery:

1. **Practice, practice, practice** - Build your own generic types
2. **Study the standard library** - See how experts use generics
3. **Read the docs** - `golang.org/doc/tutorial/generics`
4. **Start simple** - Don't over-complicate
5. **Benchmark** - Generics don't automatically mean faster
6. **Stay updated** - New generic features being added

### 🌟 What's Next:

Now that you've mastered generics, you're ready for:
- **Concurrency** - Goroutines and channels
- **Advanced Patterns** - Combining generics with concurrency
- **Performance** - Profiling and optimization
- **Production Systems** - Building scalable applications

### 📖 Recommended Reading:

- Go Generics Tutorial: https://go.dev/doc/tutorial/generics
- Type Parameters Proposal: https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md
- When To Use Generics: https://go.dev/blog/when-generics
- Constraints Package: https://pkg.go.dev/golang.org/x/exp/constraints

### 🎊 Congratulations!

You've successfully completed Chapter 8 on Go Generics! You now have:

✅ Deep understanding of type parameters  
✅ Mastery of constraints and type terms  
✅ Ability to build generic data structures  
✅ Knowledge of when (and when not) to use generics  
✅ Experience with real-world applications  
✅ Three completed practice tasks  

**Keep building amazing type-safe Go applications! 🚀**

---

**End of Chapter 8: Generics**
