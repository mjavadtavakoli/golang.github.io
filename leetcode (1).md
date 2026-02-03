# LeetCode Exercises for Go Topics

## Task 1: Calculate Sum of Numbers (Easy) ⭐

### Description
Write a function that takes a slice of integers (`[]int`) and returns the sum of all numbers.

### Requirements
- Use **Predeclared Types** (like `int`, `int64`)
- Use appropriate **Declarations** (`var`, `:=`)
- Use **Control Structures** (like `for` loop)
- The function should properly implement **Functions in Go**

### Examples
```go
// Input: []int{1, 2, 3, 4, 5}
// Output: 15

// Input: []int{-1, 0, 1}
// Output: 0

// Input: []int{}
// Output: 0
```

### Required Function
```go
func SumNumbers(numbers []int) int {
    // Write your code here
}
```

### Learning Points
- Using predeclared data types (`int`)
- Using variable declarations (`var` or `:=`)
- Using `for` loop to iterate over slice
- Implementing function with parameters and return value

---

## Task 2: Inventory Management System (Medium) ⭐⭐

### Description
Implement an inventory management system. You must use **Composite Types** (struct) and write functions to add, remove, and check product inventory.

### Requirements
- Use **Composite Types** (struct) to define products
- Use **Control Structures** (if/else, switch) to check conditions
- Use **Functions in Go** with different parameters
- Properly use **Blocks** and **Scopes**

### Product Structure
```go
type Product struct {
    ID       int
    Name     string
    Quantity int
    Price    float64
}
```

### Required Functions
```go
// Add product to inventory
func AddProduct(inventory map[int]Product, product Product) error {
    // Write your code here
    // If product already exists, increase the quantity
    // If product is new, add it
}

// Remove product from inventory
func RemoveProduct(inventory map[int]Product, productID int) error {
    // Write your code here
    // If product doesn't exist, return an error
}

// Check product stock
func CheckStock(inventory map[int]Product, productID int) (int, bool) {
    // Write your code here
    // First value: product quantity
    // Second value: whether product exists or not
}

// Calculate total inventory value
func CalculateTotalValue(inventory map[int]Product) float64 {
    // Write your code here
    // Sum of (Quantity * Price) for all products
}
```

### Usage Example
```go
inventory := make(map[int]Product)

// Add products
product1 := Product{ID: 1, Name: "Laptop", Quantity: 10, Price: 1500.0}
AddProduct(inventory, product1)

product2 := Product{ID: 2, Name: "Mouse", Quantity: 50, Price: 25.0}
AddProduct(inventory, product2)

// Check stock
quantity, exists := CheckStock(inventory, 1)
// quantity = 10, exists = true

// Calculate total value
totalValue := CalculateTotalValue(inventory)
// totalValue = (10 * 1500.0) + (50 * 25.0) = 16250.0
```

### Learning Points
- Using struct as Composite Type
- Using map for data storage
- Using control structures (if/else) to check conditions
- Using multiple return values in functions
- Managing scope and blocks in functions

---

## Task 3: Order Processing System with Discounts (Hard) ⭐⭐⭐

### Description
Implement a complex system for processing orders with different discount rules. This task requires advanced use of all topics.

### Requirements
- Use **Composite Types** (struct, slice, map)
- Use complex **Control Structures** (nested if/else, switch, for range)
- Use **Functions** of different types (variadic, higher-order, closures)
- Use **Blocks** and **Scopes** to manage variables
- Be aware of **Shadowing** and use it correctly

### Required Structures
```go
type OrderItem struct {
    ProductID int
    Quantity  int
    Price     float64
}

type Order struct {
    ID        int
    Items     []OrderItem
    Discount  float64 // Discount percentage (0-100)
    Total     float64
    Status    string // "pending", "processing", "completed", "cancelled"
}

type DiscountRule struct {
    MinAmount      float64
    DiscountPercent float64
    Description    string
}
```

### Required Functions
```go
// Calculate order subtotal without discount
func CalculateSubtotal(order Order) float64 {
    // Write your code here
    // Sum of (Quantity * Price) for all items
}

// Apply discount based on rules
func ApplyDiscountRules(order Order, rules []DiscountRule) Order {
    // Write your code here
    // Check if order total meets minimum for each rule
    // Apply the maximum possible discount
    // Update order.Discount and order.Total
}

// Process multiple orders simultaneously
func ProcessOrders(orders []Order, rules []DiscountRule) []Order {
    // Write your code here
    // For each order:
    //   1. Change status to "processing"
    //   2. Apply discount
    //   3. Calculate final total
    //   4. Change status to "completed"
    // If order total is less than 0, set status to "cancelled"
}

// Filter orders by status
func FilterOrdersByStatus(orders []Order, status string) []Order {
    // Write your code here
    // Return only orders with the specified status
}

// Calculate order statistics
func CalculateOrderStatistics(orders []Order) map[string]interface{} {
    // Write your code here
    // Return a map containing:
    //   - "total_orders": total number of orders
    //   - "total_revenue": total revenue
    //   - "average_order_value": average order value
    //   - "completed_orders": number of completed orders
    //   - "total_discount": total discounts applied
}
```

### Usage Example
```go
// Define discount rules
rules := []DiscountRule{
    {MinAmount: 100.0, DiscountPercent: 5.0, Description: "5% off for orders over $100"},
    {MinAmount: 500.0, DiscountPercent: 10.0, Description: "10% off for orders over $500"},
    {MinAmount: 1000.0, DiscountPercent: 15.0, Description: "15% off for orders over $1000"},
}

// Create orders
orders := []Order{
    {
        ID: 1,
        Items: []OrderItem{
            {ProductID: 1, Quantity: 2, Price: 50.0},
            {ProductID: 2, Quantity: 1, Price: 30.0},
        },
        Status: "pending",
    },
    {
        ID: 2,
        Items: []OrderItem{
            {ProductID: 3, Quantity: 10, Price: 100.0},
        },
        Status: "pending",
    },
}

// Process orders
processedOrders := ProcessOrders(orders, rules)

// Filter completed orders
completedOrders := FilterOrdersByStatus(processedOrders, "completed")

// Calculate statistics
stats := CalculateOrderStatistics(processedOrders)
```

### Learning Points
- Advanced use of struct and slice
- Using nested control structures
- Using variadic functions or functions with complex parameters
- Managing scope and blocks in nested functions
- Awareness of variable shadowing
- Using map to return structured data
- Managing state in functions

---

## Solution Guide

### Task 1 (Easy)
- Use a variable to store the sum
- Iterate over the slice with a `for` loop
- Add each element's value to the sum

### Task 2 (Medium)
- Use `map[int]Product` to store inventory
- In `AddProduct`, first check if product exists
- Use `if/else` to check conditions
- Use multiple return values in `CheckStock`

### Task 3 (Hard)
- In `CalculateSubtotal`, use `for range` to iterate over items
- In `ApplyDiscountRules`, use nested loops and conditions
- In `ProcessOrders`, use `for range` and nested function calls
- Be careful with variable shadowing (e.g., if you define a variable with the same name in an inner block)
- Use type assertion for `map[string]interface{}`

---

## Important Notes for Students

1. **Predeclared Types**: Use Go's predeclared data types (`int`, `float64`, `string`, `bool`)
2. **Declarations**: Know the difference between `var` and `:=` and use each appropriately
3. **Composite Types**: Properly use struct, slice, map
4. **Control Structures**: Properly use if/else, switch, for
5. **Functions**: Properly manage parameters, return values, and function scope
6. **Blocks & Scopes**: Be careful about which scope variables are accessible in
7. **Shadowing**: If you define a variable with the same name in an inner block, the outer variable will be shadowed
