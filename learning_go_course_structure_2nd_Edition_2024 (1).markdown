# 🎉 Learning Go – Course Structure (2nd Edition, 2024) 🚀

![Go Gopher Mascot](https://blog.golang.org/gopher/gopher.png)

## 🌟 Preface: Welcome to Your Go Adventure!

![Adventure Map](https://images.unsplash.com/photo-1506744038136-46273834b3fb?auto=format&fit=crop&w=600&q=80)

> *Every journey starts with a single step. Let's Go!* 🚀

## 📘 Table of Contents

1. **🚦 Setting Up Your Go Environment**
2. **🔤 Predeclared Types and Declarations**
3. **🧩 Composite Types**


4. **🔄 Blocks, Shadows, and Control Structures**
5. **🛠️ Functions**

---

## 🚦 Chapter 1: Setting Up Your Go Environment

### 📚 Table of Contents for This Chapter
1. **Introduction: Why Go?**
2. **Section 1: Installation and Setup**
3. **Section 2: Go Tooling Essentials**
4. **Section 3: Your First Go Program**
5. **Section 4: Go Modules Deep Dive**
6. **Section 5: Development Tools**
7. **Exercises and Challenges**

---

## 🎯 Introduction: Why Go?

### Go Toolchain Overview
```mermaid
flowchart LR
    A["Your Code (main.go)"] --> B["go build"]
    B --> C["Go Compiler"]
    C --> D["Binary Executable"]
    D --> E["Run Program"]
```

**What Makes Go Special?**

Go (or Golang) is a modern programming language created by Google in 2007 and released as open-source in 2009. It was designed by Robert Griesemer, Rob Pike, and Ken Thompson to solve real-world problems at scale.

**Real-World Success Stories:**
- 🚗 **Uber**: Uses Go for their geofence microservices (handling millions of requests per second)
- 📦 **Docker**: The entire Docker platform is written in Go
- ☁️ **Kubernetes**: The world's most popular container orchestration platform runs on Go
- 🎵 **SoundCloud**: Migrated to Go for better performance and reliability
- 📺 **Netflix**: Uses Go for server optimization

**Why Choose Go?**

| Feature | Benefit | Real-World Impact |
|---------|---------|-------------------|
| **Simple Syntax** | Easy to learn, read, and maintain | New team members become productive in weeks, not months |
| **Fast Compilation** | Build times in seconds | Rapid development and deployment cycles |
| **Built-in Concurrency** | Goroutines and channels | Handle thousands of concurrent connections easily |
| **Static Typing** | Catch errors at compile time | Fewer bugs in production |
| **Cross-Platform** | Compile for any OS | One codebase runs everywhere |
| **Standard Library** | Rich, well-documented packages | Less dependency on third-party libraries |

**Simple Example - Go vs Other Languages:**

**Python:**
```python
# Python: Dynamic typing, slower execution
def greet(name):
    return f"Hello {name}!"

print(greet("Alice"))  # No compile-time type checking
```

**Java:**
```java
// Java: Verbose, requires compilation setup
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello Alice!");
    }
}
```

**Go:**
```go
// Go: Simple, fast, statically typed
package main

import "fmt"

func main() {
    name := "Alice"
    fmt.Printf("Hello %s!\n", name)
}
```

> **Key Takeaway:** Go gives you the simplicity of Python with the performance and safety of compiled languages like C++ and Java!

---

## 📖 Section 1: Installation and Setup

### 1.1 Installing Go - Step by Step 🛠️

**Three Ways to Install Go:**

#### Method 1: Official Installer (Recommended for Beginners)

**For Windows:**
1. Visit [https://go.dev/dl/](https://go.dev/dl/)
2. Download `go1.23.x.windows-amd64.msi` (latest version)
3. Run the installer (double-click the `.msi` file)
4. Follow the installation wizard (use default settings)
5. Open Command Prompt and verify:
   ```cmd
   go version
   ```

**For macOS:**
1. Visit [https://go.dev/dl/](https://go.dev/dl/)
2. Download `go1.23.x.darwin-amd64.pkg` (for Intel) or `go1.23.x.darwin-arm64.pkg` (for Apple Silicon M1/M2)
3. Double-click the `.pkg` file
4. Follow the installation wizard
5. Open Terminal and verify:
   ```bash
   go version
   ```

**For Linux:**
```bash
# Download Go (replace version with latest)
wget https://go.dev/dl/go1.23.0.linux-amd64.tar.gz

# Remove old Go installation (if exists)
sudo rm -rf /usr/local/go

# Extract to /usr/local
sudo tar -C /usr/local -xzf go1.23.0.linux-amd64.tar.gz

# Add Go to PATH (add to ~/.bashrc or ~/.zshrc)
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc

# Verify installation
go version
```

#### Method 2: Using Package Managers

**macOS (Homebrew):**
```bash
brew install go
go version
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install golang-go
go version
```

**Linux (Fedora):**
```bash
sudo dnf install golang
go version
```

#### Method 3: Using Version Managers (For Advanced Users)

**Using GVM (Go Version Manager):**
```bash
# Install GVM
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)

# Install Go
gvm install go1.23.0
gvm use go1.23.0 --default
```

### 1.2 Understanding Go Environment Variables 🔧

After installation, Go uses several important environment variables:

| Variable | Purpose | Default Value | Example |
|----------|---------|---------------|---------|
| `GOROOT` | Where Go is installed | `/usr/local/go` | Where the Go compiler lives |
| `GOPATH` | Your Go workspace | `$HOME/go` | Where your projects and dependencies go |
| `GOBIN` | Where `go install` puts binaries | `$GOPATH/bin` | Compiled executables location |
| `GOPROXY` | Module proxy server | `https://proxy.golang.org` | For downloading dependencies |
| `GOMODCACHE` | Module cache | `$GOPATH/pkg/mod` | Downloaded modules storage |

**Check Your Environment:**
```bash
# View all Go environment variables
go env

# View specific variable
go env GOPATH
go env GOROOT
```

**Example Output:**
```bash
$ go version
go version go1.23.0 darwin/arm64

$ go env GOPATH
/Users/amir/go

$ go env GOROOT
/usr/local/go
```

### 1.3 Setting Up Your Workspace 📁

**Recommended Project Structure:**
```
$HOME/
├── go/                          # GOPATH
│   ├── bin/                     # Compiled binaries
│   ├── pkg/                     # Compiled package objects
│   └── src/                     # Source code (deprecated with modules)
│
└── projects/                    # Your actual projects (can be anywhere)
    ├── ecommerce-app/
    │   ├── go.mod
    │   ├── go.sum
    │   ├── main.go
    │   ├── handlers/
    │   ├── models/
    │   └── utils/
    │
    └── learning-go/
        ├── go.mod
        ├── main.go
        └── exercises/
```

**Create Your First Workspace:**
```bash
# Create a directory for your Go projects
mkdir -p ~/projects/learning-go
cd ~/projects/learning-go

# Initialize a Go module (we'll explain this soon!)
go mod init github.com/yourusername/learning-go

# Create your first Go file
touch main.go
```

> **Pro Tip:** With Go modules (introduced in Go 1.11+), you can create Go projects anywhere on your system—not just in `$GOPATH/src`!

> **Try it Yourself!** Open your terminal and follow the installation steps for your operating system. Then run `go version` to verify everything works!

### 1.4 Troubleshooting Your Go Installation 🕵️‍♂️

**Common Issues and Solutions:**

#### Issue 1: "command not found: go" 🚫

**Problem:** Terminal doesn't recognize the `go` command.

**Diagnosis:**
```bash
# Check if Go is in your PATH
which go         # macOS/Linux
where go         # Windows

# Check PATH variable
echo $PATH       # macOS/Linux
echo %PATH%      # Windows
```

**Solution for macOS/Linux:**
```bash
# Add to ~/.bashrc or ~/.zshrc
export PATH=$PATH:/usr/local/go/bin
export PATH=$PATH:$HOME/go/bin

# Reload configuration
source ~/.bashrc    # or source ~/.zshrc
```

**Solution for Windows:**
1. Search for "Environment Variables" in Start Menu
2. Click "Edit the system environment variables"
3. Click "Environment Variables" button
4. Under "System variables", find "Path"
5. Add `C:\Go\bin` to the Path
6. Restart Command Prompt

#### Issue 2: Permission Denied Errors 🔒

**Problem:** Cannot write to `/usr/local/go` or `$GOPATH`.

**Solution (Linux/macOS):**
```bash
# Fix GOPATH permissions
mkdir -p $HOME/go
chmod -R 755 $HOME/go

# If you need to reinstall Go with sudo
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go*.tar.gz
```

**Solution (Windows):**
- Run installer as Administrator
- Or install to a user directory instead

#### Issue 3: Wrong Go Version 🔄

**Problem:** Need specific Go version for a project.

**Check your version:**
```bash
go version
```

**Solution - Update Go:**
```bash
# macOS (Homebrew)
brew upgrade go

# Linux
# Download new version and reinstall
wget https://go.dev/dl/go1.23.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.23.0.linux-amd64.tar.gz
```

#### Issue 4: "go.mod file not found" ❌

**Problem:** Trying to run Go code without a module.

**Solution:**
```bash
# Initialize a Go module first
go mod init myproject

# Or use go run with a filename
go run main.go
```

#### Issue 5: Module Download Issues 🌐

**Problem:** Cannot download dependencies.

**Diagnosis:**
```bash
# Check proxy settings
go env GOPROXY

# Check if you can reach the proxy
curl https://proxy.golang.org
```

**Solution:**
```bash
# Try different proxy
go env -w GOPROXY=https://goproxy.io,direct

# Or use direct mode (slower)
go env -w GOPROXY=direct

# Clear module cache and retry
go clean -modcache
go mod download
```

### 1.5 Verification Checklist ✅

**Run these commands to verify everything works:**

```bash
# 1. Check Go version
go version
# Expected: go version go1.23.0 darwin/arm64 (or similar)

# 2. Check Go location
which go           # macOS/Linux
where go           # Windows
# Expected: /usr/local/go/bin/go (or similar)

# 3. Check environment
go env GOROOT
go env GOPATH
go env GOMODCACHE
# All should show valid paths

# 4. Create a test project
mkdir -p ~/test-go && cd ~/test-go
go mod init test
echo 'package main
import "fmt"
func main() { fmt.Println("Go works!") }' > main.go
go run main.go
# Expected output: Go works!

# 5. Clean up
cd ~ && rm -rf ~/test-go
```

**If all commands work correctly: Congratulations! 🎉 Your Go installation is perfect!**

> **Need More Help?** Visit these resources:
> - 📖 Official Go Documentation: [https://go.dev/doc/](https://go.dev/doc/)
> - 💬 Go Forum: [https://forum.golangbridge.org/](https://forum.golangbridge.org/)
> - 🐛 Stack Overflow: [https://stackoverflow.com/questions/tagged/go](https://stackoverflow.com/questions/tagged/go)
> - 💬 Go Slack: [https://gophers.slack.com/](https://gophers.slack.com/)

---

## 📖 Section 2: Go Tooling Essentials

### 2.1 Essential Go Commands 🧰

Go comes with a powerful CLI toolchain. Let's explore each command with practical examples:

#### Command 1: `go run` - Run Without Building 🏃

**Purpose:** Compile and execute Go code in one step (perfect for development).

**Syntax:**
```bash
go run [filename.go]
go run .              # Run package in current directory
```

**Example:**
```go
// hello.go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

```bash
go run hello.go
# Output: Hello, Go!
```

**Real-World Use Case:**
```bash
# Quick testing during development
go run main.go

# Run with command-line arguments
go run main.go --config=dev.yaml --port=8080
```

#### Command 2: `go build` - Compile to Binary 🏗️

**Purpose:** Compile Go code into an executable binary.

**Syntax:**
```bash
go build                    # Build current package
go build -o mybinary       # Specify output name
go build ./cmd/myapp       # Build specific package
```

**Example:**
```bash
# Build the current package
go build

# Build with custom name
go build -o myapp

# Run the binary
./myapp
```

**Cross-Platform Compilation:**
```bash
# Build for Linux (from macOS/Windows)
GOOS=linux GOARCH=amd64 go build -o myapp-linux

# Build for Windows (from macOS/Linux)
GOOS=windows GOARCH=amd64 go build -o myapp.exe

# Build for macOS (from Linux/Windows)
GOOS=darwin GOARCH=amd64 go build -o myapp-mac

# Build for ARM (Raspberry Pi)
GOOS=linux GOARCH=arm go build -o myapp-arm
```

**Build with Optimization:**
```bash
# Reduce binary size
go build -ldflags="-s -w" -o myapp

# Add version information
go build -ldflags="-X main.version=1.0.0" -o myapp
```

#### Command 3: `go fmt` - Format Code 🎨

**Purpose:** Automatically format Go source code to match Go standards.

**Syntax:**
```bash
go fmt                      # Format current package
go fmt ./...               # Format all packages recursively
go fmt filename.go         # Format specific file
```

**Before `go fmt`:**
```go
package main
import "fmt"
func main(){
fmt.Println("Messy code")
}
```

**After `go fmt`:**
```go
package main

import "fmt"

func main() {
    fmt.Println("Clean code")
}
```

**Pro Tips:**
```bash
# Format all Go files in project
go fmt ./...

# Check if formatting is needed (CI/CD)
test -z $(go fmt ./...)
```

#### Command 4: `go vet` - Static Analysis 🔍

**Purpose:** Detect suspicious code that might be bugs.

**Syntax:**
```bash
go vet                      # Analyze current package
go vet ./...               # Analyze all packages
```

**Example - What `go vet` Catches:**

```go
// Bad: Printf with wrong arguments
fmt.Printf("%d", "string")  // vet catches: wrong type

// Bad: Unreachable code
func example() int {
    return 1
    fmt.Println("Never runs")  // vet catches: unreachable code
}

// Bad: Suspicious loop
for i, v := range slice {
    go func() {
        fmt.Println(i, v)  // vet catches: loop variable captured by closure
    }()
}
```

**Run `go vet`:**
```bash
$ go vet main.go
# main.go:10: Printf format %d has arg "string" of wrong type string
# main.go:15: unreachable code
# main.go:22: loop variable v captured by func literal
```

#### Command 5: `go test` - Run Tests 🧪

**Purpose:** Run unit tests and benchmarks.

**Syntax:**
```bash
go test                     # Test current package
go test ./...              # Test all packages
go test -v                 # Verbose output
go test -cover             # Show code coverage
go test -bench=.           # Run benchmarks
```

**Example Test File (main_test.go):**
```go
package main

import "testing"

func TestAdd(t *testing.T) {
    result := Add(2, 3)
    expected := 5
    
    if result != expected {
        t.Errorf("Add(2, 3) = %d; want %d", result, expected)
    }
}

func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}
```

**Run Tests:**
```bash
# Run all tests
go test ./...

# Verbose output
go test -v

# With coverage
go test -cover

# Coverage report
go test -coverprofile=coverage.out
go tool cover -html=coverage.out
```

#### Command 6: `go mod` - Module Management 📦

**Purpose:** Manage project dependencies.

**Syntax:**
```bash
go mod init [module-path]   # Initialize new module
go mod tidy                 # Add missing, remove unused deps
go mod download             # Download dependencies
go mod verify               # Verify dependencies
go mod vendor               # Copy dependencies to vendor/
```

**Example Workflow:**
```bash
# 1. Create new project
mkdir myproject && cd myproject

# 2. Initialize module
go mod init github.com/username/myproject

# 3. Add code that imports external package
cat > main.go << 'EOF'
package main

import (
    "fmt"
    "github.com/gin-gonic/gin"
)

func main() {
    fmt.Println("Hello!")
}
EOF

# 4. Download dependencies
go mod tidy

# 5. Verify everything
go mod verify
```

#### Command 7: `go install` - Install Binary 📥

**Purpose:** Compile and install binary to `$GOPATH/bin`.

**Syntax:**
```bash
go install                  # Install current package
go install ./cmd/myapp     # Install specific package
go install github.com/user/tool@latest  # Install from GitHub
```

**Example:**
```bash
# Install popular Go tools
go install golang.org/x/tools/cmd/goimports@latest
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest

# Now you can use them
goimports -w .
golangci-lint run
```

#### Command 8: `go get` - Download Dependencies 📥

**Purpose:** Download and install packages.

**Syntax:**
```bash
go get package@version      # Get specific version
go get -u                   # Update dependencies
```

**Example:**
```bash
# Get specific package
go get github.com/gin-gonic/gin

# Get specific version
go get github.com/gin-gonic/gin@v1.9.0

# Update all dependencies
go get -u ./...
```

### 2.2 Workflow: Putting It All Together 🔄

**Daily Development Workflow:**

```bash
# 1. Format code before committing
go fmt ./...

# 2. Check for issues
go vet ./...

# 3. Run tests
go test ./...

# 4. Build for testing
go build

# 5. Run the binary
./myapp

# 6. Clean up module dependencies
go mod tidy
```

**Pre-Commit Checklist Script:**
```bash
#!/bin/bash
# save as: pre-commit.sh

echo "🎨 Formatting code..."
go fmt ./...

echo "🔍 Running go vet..."
go vet ./...

echo "🧪 Running tests..."
go test ./...

echo "📦 Tidying modules..."
go mod tidy

echo "✅ All checks passed!"
```

**Make it executable:**
```bash
chmod +x pre-commit.sh
./pre-commit.sh
```

### 2.3 Advanced Commands for Power Users 💪

**Performance Profiling:**
```bash
# CPU profiling
go test -cpuprofile=cpu.prof
go tool pprof cpu.prof

# Memory profiling
go test -memprofile=mem.prof
go tool pprof mem.prof

# Benchmark with profiling
go test -bench=. -cpuprofile=cpu.prof
```

**Code Generation:**
```bash
# Generate code from templates
go generate ./...

# Common use with stringer
//go:generate stringer -type=Status
```

**Module Graph:**
```bash
# View dependency graph
go mod graph

# Why is this dependency included?
go mod why github.com/some/package
```

> **Pro Teacher Note:** Create an alias for common commands:
> ```bash
> # Add to ~/.bashrc or ~/.zshrc
> alias gofmt='go fmt ./...'
> alias govet='go vet ./...'
> alias gotest='go test -v ./...'
> alias gocheck='go fmt ./... && go vet ./... && go test ./...'
> ```

---

## 📖 Section 3: Your First Go Program

### 3.1 Hello, World! - Your First Go Program ✨

Let's write the traditional "Hello, World!" program and understand every part of it.

**Step 1: Create Project Directory**
```bash
mkdir -p ~/projects/hello-go
cd ~/projects/hello-go
```

**Step 2: Initialize Go Module**
```bash
go mod init hello
```

**Step 3: Create main.go**
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

**Step 4: Run Your Program**
```bash
go run main.go
```

**Expected Output:**
```
Hello, World!
```

### 3.2 Understanding Every Line 🔍

Let's break down the "Hello, World!" program line by line:

```go
package main  // 1. Package declaration
```
- Every Go file starts with a `package` declaration
- `package main` indicates this is an executable program (not a library)
- Package name should match the directory name (except for `main`)

```go
import "fmt"  // 2. Import statement
```
- `import` brings in other packages
- `fmt` (format) is a standard library package for formatted I/O
- Standard library packages don't need a full path

```go
func main() {  // 3. Main function
```
- `func` keyword declares a function
- `main()` is the entry point of the program
- Every executable Go program must have a `main()` function in `package main`

```go
    fmt.Println("Hello, World!")  // 4. Function call
```
- `fmt.Println` prints text followed by a newline
- String literals use double quotes `""`
- The function comes from the `fmt` package we imported

```go
}  // 5. Closing brace
```
- Closes the `main()` function
- Go uses curly braces `{}` for blocks

### 3.3 Variations and Experiments 🧪

**Example 1: Multiple Print Statements**
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
    fmt.Println("Welcome to Go!")
    fmt.Println("Let's learn together!")
}
```

**Output:**
```
Hello, World!
Welcome to Go!
Let's learn together!
```

**Example 2: Using Printf for Formatting**
```go
package main

import "fmt"

func main() {
    name := "Alice"
    age := 25
    
    fmt.Printf("Hello, %s!\n", name)
    fmt.Printf("You are %d years old.\n", age)
    fmt.Printf("Nice to meet you, %s (%d)!\n", name, age)
}
```

**Output:**
```
Hello, Alice!
You are 25 years old.
Nice to meet you, Alice (25)!
```

**Example 3: Variables and User Input**
```go
package main

import "fmt"

func main() {
    var name string
    
    fmt.Print("Enter your name: ")
    fmt.Scanln(&name)
    
    fmt.Printf("Hello, %s! Welcome to Go!\n", name)
}
```

**Example 4: E-Commerce Welcome Message**
```go
package main

import "fmt"

func main() {
    storeName := "TechShop"
    productCount := 150
    discount := 20.5
    
    fmt.Println("=" * 50)
    fmt.Printf("🛍️  Welcome to %s!\n", storeName)
    fmt.Println("=" * 50)
    fmt.Printf("📦 We have %d products available\n", productCount)
    fmt.Printf("🎉 Special discount: %.1f%% off on all items!\n", discount)
    fmt.Println("\nHappy shopping! 🎁")
}
```

### 3.4 Common Mistakes and How to Fix Them 🐛

**Mistake 1: Wrong Package Name**
```go
// ❌ Wrong
package hello  // Should be 'main' for executable

func main() {
    fmt.Println("Hello!")
}
```

**Error:**
```
go run: cannot run non-main package
```

**Fix:**
```go
// ✅ Correct
package main

func main() {
    fmt.Println("Hello!")
}
```

**Mistake 2: Missing Import**
```go
// ❌ Wrong
package main

func main() {
    fmt.Println("Hello!")  // fmt is not imported
}
```

**Error:**
```
undefined: fmt
```

**Fix:**
```go
// ✅ Correct
package main

import "fmt"

func main() {
    fmt.Println("Hello!")
}
```

**Mistake 3: Wrong Function Name**
```go
// ❌ Wrong
package main

import "fmt"

func hello() {  // Should be 'main'
    fmt.Println("Hello!")
}
```

**Error:**
```
runtime.main_main·f: function main is undeclared in the main package
```

**Fix:**
```go
// ✅ Correct
package main

import "fmt"

func main() {
    fmt.Println("Hello!")
}
```

**Mistake 4: Opening Brace on New Line**
```go
// ❌ Wrong (syntax error in Go)
package main

import "fmt"

func main()
{  // Opening brace must be on the same line
    fmt.Println("Hello!")
}
```

**Error:**
```
syntax error: unexpected semicolon or newline before {
```

**Fix:**
```go
// ✅ Correct
package main

import "fmt"

func main() {
    fmt.Println("Hello!")
}
```

### 3.5 Building and Running 🏗️

**Method 1: go run (Quick Testing)**
```bash
go run main.go
```
- Compiles and runs in one step
- Doesn't create a binary file
- Perfect for development and testing

**Method 2: go build (Create Binary)**
```bash
# Build with default name (directory name)
go build

# Build with custom name
go build -o myapp

# Run the binary
./myapp        # macOS/Linux
myapp.exe      # Windows
```

**Method 3: go install (Install to GOPATH/bin)**
```bash
go install
# Binary is now in $GOPATH/bin
# Can run from anywhere if $GOPATH/bin is in PATH
```

### 3.6 Interactive Exercise 🎯

**Challenge: Create a Personal Greeting Program**

Requirements:
1. Ask for user's name
2. Ask for user's favorite programming language
3. Display a personalized welcome message

**Template:**
```go
package main

import "fmt"

func main() {
    // Your code here
    // 1. Declare variables for name and language
    // 2. Use fmt.Print() to ask questions
    // 3. Use fmt.Scanln() to read input
    // 4. Print personalized message
}
```

**Solution:**
```go
package main

import "fmt"

func main() {
    var name string
    var language string
    
    fmt.Print("What's your name? ")
    fmt.Scanln(&name)
    
    fmt.Print("What's your favorite language? ")
    fmt.Scanln(&language)
    
    fmt.Println("\n" + "=" * 50)
    fmt.Printf("🎉 Hello, %s!\n", name)
    fmt.Printf("👨‍💻 You love %s? That's awesome!\n", language)
    fmt.Println("🚀 Welcome to the Go community!")
    fmt.Println("=" * 50)
}
```

> **Try it Yourself!** Create different versions:
> - Add more questions (age, city, hobby)
> - Calculate something (years of experience, birth year)
> - Add colors using ANSI codes

---

## 📖 Section 4: Go Modules Deep Dive

### 4.1 What Are Go Modules? 📦

**Go Modules** are Go's built-in dependency management system (introduced in Go 1.11+). They solve the problem of versioning and dependency management that plagued earlier Go versions.

**Before Modules (Pre-Go 1.11) - The Old Way:**
- All code had to be in `$GOPATH/src`
- No version control for dependencies
- Different projects couldn't use different versions of the same package
- Reproducible builds were difficult

**With Modules (Go 1.11+) - The Modern Way:**
- ✅ Create projects anywhere on your system
- ✅ Explicit version control for each dependency
- ✅ Reproducible builds across environments
- ✅ Multiple versions of the same package in different projects

**Comparison with Other Languages:**

| Language | Dependency File | Lock File | Package Manager |
|----------|----------------|-----------|-----------------|
| Go | `go.mod` | `go.sum` | Built-in (`go mod`) |
| JavaScript | `package.json` | `package-lock.json` | npm/yarn |
| Python | `requirements.txt` | `Pipfile.lock` | pip/pipenv |
| Rust | `Cargo.toml` | `Cargo.lock` | cargo |
| Ruby | `Gemfile` | `Gemfile.lock` | bundler |

### 4.2 Creating Your First Module 🎯

**Step-by-Step Guide:**

```bash
# 1. Create project directory
mkdir ~/projects/ecommerce-app
cd ~/projects/ecommerce-app

# 2. Initialize Go module
go mod init github.com/yourusername/ecommerce-app

# 3. View the created go.mod file
cat go.mod
```

**Output:**
```
module github.com/yourusername/ecommerce-app

go 1.23
```

**Naming Convention for Modules:**
```bash
# For GitHub projects
go mod init github.com/username/projectname

# For GitLab projects
go mod init gitlab.com/username/projectname

# For private/local projects (any valid path)
go mod init mycompany.com/team/projectname
go mod init example.com/myproject
go mod init myproject  # Simplest form for local-only projects
```

### 4.3 Understanding go.mod File 📄

**Basic go.mod Structure:**
```go
module github.com/yourusername/ecommerce-app

go 1.23

require (
    github.com/gin-gonic/gin v1.9.1
    github.com/lib/pq v1.10.9
)

replace github.com/old/package => github.com/new/package v1.2.3

exclude github.com/broken/package v1.0.0
```

**Line-by-Line Explanation:**

**1. module directive:**
```go
module github.com/yourusername/ecommerce-app
```
- Declares your module's import path
- Used by other packages to import your code
- Must be unique if you plan to publish

**2. go directive:**
```go
go 1.23
```
- Minimum Go version required to build this module
- Ensures compatibility with language features

**3. require directive:**
```go
require (
    github.com/gin-gonic/gin v1.9.1
    github.com/lib/pq v1.10.9  // indirect
)
```
- Lists direct dependencies and their versions
- `// indirect` means it's a transitive dependency
- Uses semantic versioning (SemVer)

**4. replace directive (optional):**
```go
replace github.com/old/package => github.com/new/package v1.2.3
replace github.com/some/package => ../local/path
```
- Redirect one module to another
- Useful for:
  - Testing local changes
  - Using a fork
  - Working with private repositories

**5. exclude directive (optional):**
```go
exclude github.com/broken/package v1.0.0
```
- Prevent specific versions from being used
- Useful when a version has critical bugs

### 4.4 Understanding go.sum File 🔒

When you download dependencies, Go creates a `go.sum` file:

```
github.com/gin-gonic/gin v1.9.1 h1:4idEAncQnU5cB7BeOkPtxjfCSye0AAm1R0RVIqJ+Jmg=
github.com/gin-gonic/gin v1.9.1/go.mod h1:hPrL7YrpYKXt5YId3A/Tnip5kqbEAP+KLuI3SUcPTeU=
```

**What is go.sum?**
- **Cryptographic checksums** of module versions
- Ensures **integrity** and **authenticity** of downloads
- Prevents **tampering** with dependencies
- Should be **committed to version control**

**Analogy:**
- `go.mod` = Your shopping list
- `go.sum` = Receipt with verification codes

### 4.5 Working with Dependencies 📥

**Adding Dependencies - Automatic:**
```go
// main.go
package main

import (
    "fmt"
    "github.com/gin-gonic/gin"  // Just import it!
)

func main() {
    r := gin.Default()
    r.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{"message": "Hello!"})
    })
    r.Run()
}
```

```bash
# Go automatically downloads dependencies
go run main.go
# or
go mod tidy  # Downloads and cleans up dependencies
```

**Adding Dependencies - Manual:**
```bash
# Add specific version
go get github.com/gin-gonic/gin@v1.9.1

# Add latest version
go get github.com/gin-gonic/gin@latest

# Add specific commit
go get github.com/gin-gonic/gin@abc1234

# Add specific branch
go get github.com/gin-gonic/gin@master
```

**Updating Dependencies:**
```bash
# Update specific package
go get -u github.com/gin-gonic/gin

# Update all dependencies
go get -u ./...

# Update to latest minor/patch version
go get -u=patch ./...
```

**Removing Dependencies:**
```bash
# Remove unused dependencies automatically
go mod tidy

# Remove specific package (manually edit go.mod, then:)
go mod tidy
```

**Viewing Dependencies:**
```bash
# List all dependencies
go list -m all

# Show dependency graph
go mod graph

# Explain why a package is needed
go mod why github.com/some/package

# Download dependencies without building
go mod download
```

### 4.6 Common Module Operations 🔧

**Operation 1: Clean Up Dependencies**
```bash
# Remove unused dependencies and add missing ones
go mod tidy
```

**Operation 2: Verify Dependencies**
```bash
# Check if dependencies have been modified
go mod verify
```

**Operation 3: Vendor Dependencies**
```bash
# Copy all dependencies to vendor/ directory
go mod vendor

# Build using vendor directory
go build -mod=vendor
```

**Operation 4: View Dependency Updates**
```bash
# Check which dependencies have updates
go list -u -m all
```

### 4.7 Practical Example: E-Commerce Project 🛍️

Let's create a real project with multiple dependencies:

```bash
# 1. Create project
mkdir ecommerce-api
cd ecommerce-api

# 2. Initialize module
go mod init github.com/yourusername/ecommerce-api

# 3. Create main.go
cat > main.go << 'EOF'
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/lib/pq"
    _ "github.com/lib/pq"  // PostgreSQL driver
)

func main() {
    r := gin.Default()
    
    // Products endpoint
    r.GET("/products", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "products": []gin.H{
                {"id": 1, "name": "Laptop", "price": 999.99},
                {"id": 2, "name": "Mouse", "price": 29.99},
            },
        })
    })
    
    // Orders endpoint
    r.GET("/orders", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "orders": []gin.H{
                {"id": 1001, "status": "shipped"},
                {"id": 1002, "status": "pending"},
            },
        })
    })
    
    r.Run(":8080")
}
EOF

# 4. Download dependencies
go mod tidy

# 5. View go.mod
cat go.mod
```

**Resulting go.mod:**
```go
module github.com/yourusername/ecommerce-api

go 1.23

require (
    github.com/gin-gonic/gin v1.9.1
    github.com/lib/pq v1.10.9
)

require (
    github.com/bytedance/sonic v1.9.1 // indirect
    github.com/chenzhuoyu/base64x v0.0.0-20221115062448-fe3a3abad311 // indirect
    // ... more indirect dependencies
)
```

**Run the project:**
```bash
go run main.go
# Server runs on http://localhost:8080
```

### 4.8 Module Best Practices ✅

**1. Always Run `go mod tidy`:**
```bash
# Before committing code
go mod tidy
```

**2. Commit go.mod and go.sum:**
```bash
git add go.mod go.sum
git commit -m "Update dependencies"
```

**3. Use Specific Versions:**
```go
// ✅ Good: Explicit version
require github.com/gin-gonic/gin v1.9.1

// ❌ Bad: Avoid using @latest in production
go get github.com/gin-gonic/gin@latest
```

**4. Review Dependency Updates:**
```bash
# Check what's being updated
go list -u -m all

# Update carefully, test thoroughly
go get -u github.com/gin-gonic/gin
go test ./...
```

**5. Use replace for Local Development:**
```go
// Temporarily use local version for testing
replace github.com/yourusername/shared => ../shared
```

> **Real-World Analogy:** Think of `go.mod` as your project's passport:
> - **Module name** = Your identity
> - **Go version** = Citizenship requirements  
> - **Dependencies** = Travel visa stamps
> - **go.sum** = Security verification codes

---

## 📖 Section 5: Development Tools

### 7. Core Commands: go build, go fmt, and go vet 🏗️
| Command       | Function                                              |
|---------------|-------------------------------------------------------|
| go build      | Compile the current module and dependencies           |
| go fmt ./...  | Format all Go files in the directory and subdirectories |
| go vet ./...  | Analyze all packages for suspicious code              |

**Example:**
```bash
go fmt ./...
go build
go vet ./...
```

### 5.1 Code Editors and IDEs 🖥️

#### Option 1: Visual Studio Code (VSCode) - Recommended for Beginners ⭐

**Why VSCode?**
- ✅ Free and open-source
- ✅ Lightweight and fast
- ✅ Excellent Go extension
- ✅ Cross-platform
- ✅ Integrated terminal
- ✅ Git integration

**Installation:**
1. Download from [https://code.visualstudio.com/](https://code.visualstudio.com/)
2. Install the Go extension:
   - Open VSCode
   - Press `Ctrl+Shift+X` (or `Cmd+Shift+X` on macOS)
   - Search for "Go"
   - Install the official Go extension by the Go Team at Google

**Essential VSCode Extensions for Go:**
```bash
# Go extension (must-have)
- Name: Go
- ID: golang.go
- Features: IntelliSense, debugging, testing, formatting

# Additional helpful extensions
- Name: Error Lens
- ID: usernamehw.errorlens
- Shows errors inline

- Name: Better Comments
- ID: aaron-bond.better-comments
- Colorful comment highlighting

- Name: GitLens
- ID: eamodio.gitlens
- Advanced Git integration
```

**Essential Settings for Go in VSCode:**
```json
// settings.json
{
    // Format on save
    "editor.formatOnSave": true,
    
    // Use goimports instead of gofmt
    "[go]": {
        "editor.defaultFormatter": "golang.go",
        "editor.codeActionsOnSave": {
            "source.organizeImports": true
        }
    },
    
    // Go-specific settings
    "go.useLanguageServer": true,
    "go.lintTool": "golangci-lint",
    "go.lintOnSave": "workspace",
    "go.vetOnSave": "workspace",
    "go.buildOnSave": "workspace",
    "go.testOnSave": false,
    "go.coverOnSave": false,
    
    // IntelliSense
    "go.autocompleteUnimportedPackages": true,
    "go.gotoSymbol.includeImports": true
}
```

**Useful VSCode Shortcuts for Go:**
| Action | macOS | Windows/Linux |
|--------|-------|---------------|
| Go to Definition | `Cmd+Click` | `Ctrl+Click` |
| Format Document | `Shift+Option+F` | `Shift+Alt+F` |
| Quick Fix | `Cmd+.` | `Ctrl+.` |
| Run Tests | `Cmd+Shift+P` → "Go: Test Package" | `Ctrl+Shift+P` → "Go: Test Package" |
| Toggle Terminal | `Ctrl+\`` | `Ctrl+\`` |
| Find All References | `Shift+F12` | `Shift+F12` |

#### Option 2: GoLand - Professional IDE 💼

**Why GoLand?**
- ✅ Most powerful Go IDE
- ✅ Advanced refactoring tools
- ✅ Excellent debugger
- ✅ Built-in database tools
- ✅ Smart code completion
- ❌ Paid (free for students)

**Installation:**
1. Download from [https://www.jetbrains.com/go/](https://www.jetbrains.com/go/)
2. 30-day free trial
3. Free for students and open-source developers

**GoLand Features:**
- Intelligent code completion
- On-the-fly error detection
- Advanced debugging (with conditional breakpoints)
- Integrated test runner with coverage
- Database tools
- Docker and Kubernetes integration

#### Option 3: Other Editors

**Vim/Neovim with vim-go:**
```bash
# Install vim-go plugin
# Add to .vimrc:
Plug 'fatih/vim-go', { 'do': ':GoUpdateBinaries' }
```

**Emacs with go-mode:**
```elisp
;; Add to init.el:
(use-package go-mode)
```

**Sublime Text with GoSublime:**
- Package Control → Install Package → GoSublime

### 5.2 Online Tools 🌐

#### Go Playground - Instant Code Testing ⚡

**URL:** [https://go.dev/play/](https://go.dev/play/)

**Features:**
- ✅ No installation needed
- ✅ Run Go code instantly
- ✅ Share code with others (generates URL)
- ✅ Import most standard library packages
- ✅ Great for learning and quick tests

**Limitations:**
- ❌ No file system access
- ❌ No network access
- ❌ Limited execution time
- ❌ Cannot import external packages

**Example Use Cases:**
```go
// Test a quick algorithm
package main

import "fmt"

func main() {
    // Test code here
    result := sum(1, 2, 3, 4, 5)
    fmt.Println(result)
}

func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}
```

**Share Your Code:**
- Click "Share" button
- Get a unique URL like: `https://go.dev/play/p/XYZ123`
- Share with others for help or collaboration

#### Other Online Go Tools:

**1. Go by Example**
- URL: [https://gobyexample.com/](https://gobyexample.com/)
- Learn Go with annotated example programs

**2. Go Tour**
- URL: [https://go.dev/tour/](https://go.dev/tour/)
- Interactive tutorial for learning Go

**3. Repl.it**
- URL: [https://replit.com/](https://replit.com/)
- Full IDE in browser with file system

### 5.3 Essential Development Tools 🛠️

#### Tool 1: golangci-lint (Code Linter) 🔍

**Installation:**
```bash
# macOS/Linux
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest

# Verify installation
golangci-lint version
```

**Usage:**
```bash
# Run all linters
golangci-lint run

# Run with specific linters
golangci-lint run --enable=gofmt,golint,govet

# Auto-fix issues
golangci-lint run --fix
```

**Configuration (.golangci.yml):**
```yaml
linters:
  enable:
    - gofmt
    - golint
    - govet
    - errcheck
    - staticcheck
    - unused
    - gosimple
    - structcheck
    - varcheck
    - ineffassign
    - deadcode

linters-settings:
  gofmt:
    simplify: true
```

#### Tool 2: goimports (Import Management) 📦

**Installation:**
```bash
go install golang.org/x/tools/cmd/goimports@latest
```

**Usage:**
```bash
# Format file and organize imports
goimports -w main.go

# Format all Go files
goimports -w .

# Show diff without writing
goimports -d main.go
```

#### Tool 3: gopls (Language Server) 🧠

**What is gopls?**
- Official Go language server
- Powers IDE features (autocomplete, go to definition, etc.)
- Usually installed automatically by editors

**Manual Installation:**
```bash
go install golang.org/x/tools/gopls@latest
```

#### Tool 4: delve (Debugger) 🐛

**Installation:**
```bash
go install github.com/go-delve/delve/cmd/dlv@latest
```

**Usage:**
```bash
# Debug a program
dlv debug main.go

# Debug with arguments
dlv debug main.go -- --port=8080

# Attach to running process
dlv attach <pid>
```

**Common Commands:**
```
(dlv) break main.main    # Set breakpoint
(dlv) continue           # Continue execution
(dlv) next               # Next line
(dlv) step               # Step into function
(dlv) print myVar        # Print variable value
(dlv) exit               # Exit debugger
```

### 5.4 Build Automation with Makefiles 🏗️

**Why Makefiles?**
- Automate repetitive tasks
- Standardize workflows across team
- Document common commands

**Example Makefile:**
```makefile
# Makefile for Go project

.PHONY: help build run test clean lint fmt vet

# Default target
help:
	@echo "Available commands:"
	@echo "  make build    - Build the application"
	@echo "  make run      - Run the application"
	@echo "  make test     - Run tests"
	@echo "  make clean    - Remove build artifacts"
	@echo "  make lint     - Run linter"
	@echo "  make fmt      - Format code"
	@echo "  make vet      - Run go vet"
	@echo "  make all      - Run fmt, vet, lint, test, build"

# Build binary
build:
	@echo "Building..."
	go build -o bin/myapp main.go

# Run application
run:
	@echo "Running..."
	go run main.go

# Run tests
test:
	@echo "Running tests..."
	go test -v ./...

# Run tests with coverage
test-coverage:
	@echo "Running tests with coverage..."
	go test -v -coverprofile=coverage.out ./...
	go tool cover -html=coverage.out

# Clean build artifacts
clean:
	@echo "Cleaning..."
	rm -rf bin/
	go clean

# Run linter
lint:
	@echo "Running linter..."
	golangci-lint run

# Format code
fmt:
	@echo "Formatting code..."
	go fmt ./...
	goimports -w .

# Run go vet
vet:
	@echo "Running go vet..."
	go vet ./...

# Run all checks
all: fmt vet lint test build
	@echo "All checks passed!"

# Install dependencies
deps:
	@echo "Installing dependencies..."
	go mod download
	go mod tidy

# Update dependencies
update-deps:
	@echo "Updating dependencies..."
	go get -u ./...
	go mod tidy
```

**Usage:**
```bash
# Show available commands
make help

# Format, lint, test, and build
make all

# Just build
make build

# Run tests with coverage
make test-coverage
```

### 5.5 Recommended Workflow Setup 🔄

**Daily Development Workflow:**

```bash
# 1. Morning: Update dependencies
make deps

# 2. Write code in VSCode
# ... coding ...

# 3. Before commit: Run checks
make fmt      # Format code
make vet      # Check for issues
make lint     # Run linter
make test     # Run tests

# 4. Build and run
make build
make run

# 5. Commit changes
git add .
git commit -m "Add feature X"
git push
```

**Git Pre-Commit Hook:**
```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running pre-commit checks..."

# Format code
echo "1. Formatting code..."
go fmt ./...

# Run go vet
echo "2. Running go vet..."
go vet ./...

# Run tests
echo "3. Running tests..."
go test ./...

if [ $? -ne 0 ]; then
    echo "❌ Tests failed. Commit aborted."
    exit 1
fi

echo "✅ All checks passed!"
exit 0
```

**Make it executable:**
```bash
chmod +x .git/hooks/pre-commit
```

> **Pro Tip:** Set up your editor, install essential tools, and create a Makefile for every project. This will save you hours of manual work!

### 5.6 The Go Compatibility Promise 🤝

**What is the Go Compatibility Promise?**

Go 1 was released in March 2012 with a promise: **Code that compiles under Go 1 will continue to compile and run correctly under future Go 1.x releases.**

**What This Means:**
- ✅ Your code won't break when you update Go
- ✅ Dependencies remain stable
- ✅ Long-term maintenance is easier
- ✅ Production code is safe to upgrade

**Example:**
```go
// This code from 2012 still works in Go 1.23 (2024)
package main

import "fmt"

func main() {
    fmt.Println("Go 1 compatibility rocks!")
}
```

**What's Covered:**
- Language syntax
- Standard library APIs
- Package import paths
- Build behavior

**What's NOT Covered:**
- Bug fixes (bugs will be fixed)
- Security issues (will be patched)
- Performance improvements
- New features (opt-in)

**Real-World Impact:**
```go
// Code written in 2012
// Still compiles and runs in 2024!
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, World!")
    })
    http.ListenAndServe(":8080", nil)
}
```

### 5.7 Staying Up-to-Date 🔄

**Checking for Updates:**
```bash
# Check current version
go version

# Check for available updates
# Visit: https://go.dev/dl/
```

**Updating Go:**
```bash
# macOS (Homebrew)
brew upgrade go

# Linux (manual)
wget https://go.dev/dl/go1.23.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.23.0.linux-amd64.tar.gz

# Verify
go version
```

**Updating Dependencies:**
```bash
# Update all dependencies to latest
go get -u ./...
go mod tidy

# Update specific package
go get -u github.com/gin-gonic/gin

# Check for available updates
go list -u -m all
```

**Go Release Cycle:**
- New Go version every **6 months** (February and August)
- Each version supported for **1 year** (until 2 versions later)
- Security patches for supported versions

**Stay Informed:**
- 📰 Go Blog: [https://go.dev/blog/](https://go.dev/blog/)
- 📧 Go Newsletter: [https://golangweekly.com/](https://golangweekly.com/)
- 🐦 Twitter: [@golang](https://twitter.com/golang)
- 💬 Reddit: [r/golang](https://reddit.com/r/golang)

---

## 🏋️ Section 6: Exercises and Challenges

### Exercise 1: Installation and Setup ⚙️

**Simple Level:**
1. Install Go on your system and verify with `go version`
2. Check your `GOPATH` and `GOROOT` environment variables
3. Create a "Hello, World!" program and run it with `go run`

**Solution:**
```bash
# 1. Verify installation
go version
# Output: go version go1.23.0 darwin/arm64

# 2. Check environment
go env GOPATH
go env GOROOT

# 3. Create and run Hello World
cat > hello.go << 'EOF'
package main
import "fmt"
func main() {
    fmt.Println("Hello, World!")
}
EOF

go run hello.go
```

### Exercise 2: Go Commands Mastery 🎯

**Medium Level:**

**Task 1:** Create a Go program that has a formatting issue and fix it:
```go
// messy.go (Before)
package main
import "fmt"
func main(){
fmt.Println("Messy code")
}
```

**Solution:**
```bash
# Format the code
go fmt messy.go

# Result:
package main

import "fmt"

func main() {
    fmt.Println("Clean code")
}
```

**Task 2:** Create a program with a bug that `go vet` can catch:
```go
// buggy.go
package main

import "fmt"

func main() {
    fmt.Printf("%d", "string")  // Wrong type
}
```

**Solution:**
```bash
go vet buggy.go
# Output: Printf format %d has arg "string" of wrong type string
```

### Exercise 3: Module Management 📦

**Medium Level:**

Create a project with external dependencies:

```bash
# 1. Create project
mkdir movie-api && cd movie-api

# 2. Initialize module
go mod init github.com/yourusername/movie-api

# 3. Create main.go with dependency
cat > main.go << 'EOF'
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

type Movie struct {
    ID    int    `json:"id"`
    Title string `json:"title"`
    Year  int    `json:"year"`
}

func main() {
    r := gin.Default()
    
    movies := []Movie{
        {1, "The Matrix", 1999},
        {2, "Inception", 2010},
        {3, "Interstellar", 2014},
    }
    
    r.GET("/movies", func(c *gin.Context) {
        c.JSON(http.StatusOK, movies)
    })
    
    r.Run(":8080")
}
EOF

# 4. Download dependencies
go mod tidy

# 5. Verify module
cat go.mod

# 6. Run the API
go run main.go
```

**Tasks:**
1. What version of Gin was downloaded?
2. How many total dependencies (including indirect)?
3. Update Gin to the latest version
4. Create a vendor directory

**Solution:**
```bash
# 1. Check Gin version
grep gin go.mod

# 2. Count dependencies
go list -m all | wc -l

# 3. Update Gin
go get -u github.com/gin-gonic/gin
go mod tidy

# 4. Create vendor directory
go mod vendor
ls vendor/
```

### Exercise 4: Build and Cross-Compile 🏗️

**Medium Level:**

Create a simple CLI tool and build it for multiple platforms:

```go
// cli.go
package main

import (
    "fmt"
    "os"
    "runtime"
)

func main() {
    if len(os.Args) < 2 {
        fmt.Println("Usage: cli <name>")
        return
    }
    
    name := os.Args[1]
    fmt.Printf("Hello, %s!\n", name)
    fmt.Printf("Running on: %s/%s\n", runtime.GOOS, runtime.GOARCH)
}
```

**Tasks:**
1. Build for your current platform
2. Build for Linux
3. Build for Windows
4. Build for macOS (Intel and ARM)

**Solution:**
```bash
# 1. Build for current platform
go build -o cli cli.go

# 2. Build for Linux
GOOS=linux GOARCH=amd64 go build -o cli-linux cli.go

# 3. Build for Windows
GOOS=windows GOARCH=amd64 go build -o cli.exe cli.go

# 4. Build for macOS
GOOS=darwin GOARCH=amd64 go build -o cli-mac-intel cli.go
GOOS=darwin GOARCH=arm64 go build -o cli-mac-m1 cli.go

# Verify
ls -lh cli*
```

### Exercise 5: Create a Makefile 📋

**Hard Level:**

Create a complete Makefile for a Go project with all common commands:

**Solution:**
```makefile
# Makefile for Go project

.PHONY: help build run test clean lint fmt vet docker

# Variables
APP_NAME=myapp
VERSION=1.0.0
BUILD_DIR=bin
DOCKER_IMAGE=$(APP_NAME):$(VERSION)

# Default target
help:
	@echo "Available commands:"
	@echo "  make build         - Build the application"
	@echo "  make run           - Run the application"
	@echo "  make test          - Run tests"
	@echo "  make test-coverage - Run tests with coverage"
	@echo "  make clean         - Remove build artifacts"
	@echo "  make lint          - Run linter"
	@echo "  make fmt           - Format code"
	@echo "  make vet           - Run go vet"
	@echo "  make all           - Run fmt, vet, lint, test, build"
	@echo "  make docker        - Build Docker image"

# Build binary
build:
	@echo "Building $(APP_NAME) v$(VERSION)..."
	@mkdir -p $(BUILD_DIR)
	go build -ldflags "-X main.version=$(VERSION)" -o $(BUILD_DIR)/$(APP_NAME) .

# Run application
run:
	go run main.go

# Run tests
test:
	@echo "Running tests..."
	go test -v ./...

# Run tests with coverage
test-coverage:
	@echo "Running tests with coverage..."
	go test -v -coverprofile=coverage.out ./...
	go tool cover -html=coverage.out -o coverage.html
	@echo "Coverage report: coverage.html"

# Clean build artifacts
clean:
	@echo "Cleaning..."
	rm -rf $(BUILD_DIR)
	rm -f coverage.out coverage.html
	go clean

# Run linter
lint:
	@echo "Running linter..."
	golangci-lint run

# Format code
fmt:
	@echo "Formatting code..."
	go fmt ./...
	goimports -w .

# Run go vet
vet:
	@echo "Running go vet..."
	go vet ./...

# Run all checks
all: fmt vet lint test build
	@echo "✅ All checks passed!"

# Build Docker image
docker:
	@echo "Building Docker image..."
	docker build -t $(DOCKER_IMAGE) .

# Install dependencies
deps:
	@echo "Installing dependencies..."
	go mod download
	go mod tidy
```

### Challenge: Complete Project Setup 🎯

**Hard Level:**

Create a complete Go project with proper structure, dependencies, tests, and tooling:

**Requirements:**
1. E-commerce order management system
2. RESTful API with Gin
3. CRUD operations for orders
4. Unit tests with >80% coverage
5. Makefile for automation
6. README with documentation
7. Docker support

**Solution Structure:**
```
ecommerce-orders/
├── main.go
├── go.mod
├── go.sum
├── Makefile
├── README.md
├── Dockerfile
├── .gitignore
├── handlers/
│   ├── orders.go
│   └── orders_test.go
├── models/
│   └── order.go
└── utils/
    └── validator.go
```

**Step-by-Step Solution:**

```bash
# 1. Create project structure
mkdir -p ecommerce-orders/{handlers,models,utils}
cd ecommerce-orders

# 2. Initialize module
go mod init github.com/yourusername/ecommerce-orders

# 3. Create main.go (see full code below)
# 4. Create handlers/orders.go
# 5. Create models/order.go
# 6. Create tests
# 7. Create Makefile
# 8. Create README.md
# 9. Create Dockerfile

# 10. Run the project
make all
make run
```

This is a comprehensive project that incorporates everything learned in Chapter 1!

### Summary of Exercises:
- ✅ **Exercise 1**: Basic installation (Simple)
- ✅ **Exercise 2**: Go commands (`fmt`, `vet`) (Medium)
- ✅ **Exercise 3**: Module management (Medium)
- ✅ **Exercise 4**: Cross-compilation (Medium)
- ✅ **Exercise 5**: Makefile creation (Hard)
- ✅ **Challenge**: Complete project (Hard)

---

## 🎁 Chapter 1 Summary

### ✅ What You Learned:

**1. Installation and Setup:**
- How to install Go on any platform
- Environment variables (`GOPATH`, `GOROOT`)
- Troubleshooting common issues
- Setting up your workspace

**2. Go Tooling:**
- Essential commands (`build`, `run`, `fmt`, `vet`, `test`)
- Cross-platform compilation
- Performance profiling
- Code generation

**3. Go Modules:**
- Creating and managing modules
- Understanding `go.mod` and `go.sum`
- Working with dependencies
- Versioning and updates

**4. Development Environment:**
- VSCode setup with Go extension
- GoLand IDE features
- Online tools (Go Playground)
- Essential development tools

**5. Best Practices:**
- Using Makefiles for automation
- Pre-commit hooks
- Code quality tools (linters)
- Workflow optimization

### 🎯 Key Takeaways:

1. **Go is simple yet powerful** - Easy syntax, fast compilation, built-in concurrency
2. **Modules make dependency management easy** - No more `$GOPATH` headaches
3. **Built-in tools are your friends** - `go fmt`, `go vet`, `go test` keep code clean
4. **Compatibility promise means stability** - Your code won't break on updates
5. **Great tooling ecosystem** - VSCode, GoLand, linters, debuggers

### 📚 Next Steps:

Now that your environment is set up, you're ready to dive into Go fundamentals:
- Chapter 2: Predeclared Types and Declarations
- Chapter 3: Composite Types  
- Chapter 4: Control Structures
- Chapter 5: Functions

**Ready to Go? Let's continue! 🚀**

---

## 🔤 Chapter 2: Predeclared Types and Declarations

![Go Types](https://golang.org/doc/gopher/gophercolor.png)

### 📚 Table of Contents for This Chapter
1. **Introduction: Understanding Types**
2. **Section 1: The Predeclared Types**
3. **Section 2: Zero Values and Literals**
4. **Section 3: Variables and Constants**
5. **Section 4: Type Conversion and Safety**
6. **Section 5: Naming Conventions**
7. **Exercises and Challenges**

---

## 🎯 Introduction: Understanding Types

### What is a Type? 🤔

A **type** defines:
- What **values** a variable can hold
- What **operations** you can perform on it
- How much **memory** it occupies
- How it's **represented** internally

**Real-World Analogy:**
Think of types like containers:
- 🥛 **Glass** → holds liquids (water, juice, milk)
- 📦 **Box** → holds solid items (books, toys, clothes)
- 💾 **USB Drive** → holds digital data (files, photos, videos)

Each container has specific rules about what it can hold!

### Why Types Matter in Go 📊

**Go is Statically Typed:**
- Types are checked at **compile time**
- Errors caught **before** running
- **No runtime** type surprises
- **Better performance** (no type checks at runtime)

**Comparison with Other Languages:**

| Language | Typing | When Errors Are Caught | Example |
|----------|--------|------------------------|---------|
| **Go** | Static | Compile time | `var age int = "25"` → Compile error ✅ |
| **Python** | Dynamic | Runtime | `age = "25"; age + 5` → Runtime error ❌ |
| **JavaScript** | Dynamic | Runtime (or never!) | `"5" + 5` → `"55"` 🤔 |
| **Java** | Static | Compile time | `int age = "25";` → Compile error ✅ |

### Go Type System Overview

```mermaid
mindmap
  root((Go Types))
    Basic Types
      Booleans
        bool
      Numeric
        Integers
          int, int8, int16, int32, int64
          uint, uint8, uint16, uint32, uint64
        Floating-point
          float32, float64
        Complex
          complex64, complex128
      Strings
        string
      Rune & Byte
        rune
        byte
    Composite Types
      Arrays
        Fixed size
      Slices
        Dynamic arrays
      Maps
        Key-value pairs
      Structs
        Custom types
    Special Types
      Pointers
      Functions
      Interfaces
      Channels
```

---

## 📖 Section 1: The Predeclared Types

### 1.1 Overview of Predeclared Types 🏷️

Go comes with **built-in types** (no import needed):

**Basic Types:**
| Category | Types | Purpose | Example Use |
|----------|-------|---------|-------------|
| **Boolean** | `bool` | True/False values | Order status (paid/unpaid) |
| **Integer** | `int`, `int8`, `int16`, `int32`, `int64` | Whole numbers | Product quantity, User ID |
| **Unsigned Int** | `uint`, `uint8`, `uint16`, `uint32`, `uint64` | Positive numbers only | Age, Count |
| **Float** | `float32`, `float64` | Decimal numbers | Price, Weight, Temperature |
| **Complex** | `complex64`, `complex128` | Complex numbers | Scientific calculations |
| **String** | `string` | Text | Customer name, Address |
| **Byte** | `byte` (alias for `uint8`) | Raw data | File contents, Binary data |
| **Rune** | `rune` (alias for `int32`) | Unicode character | Individual characters |

**Composite Types:**
- `array` - Fixed-size collection
- `slice` - Dynamic collection
- `map` - Key-value pairs
- `struct` - Custom data structure

**Special Types:**
- `interface{}` - Any type (now `any` in Go 1.18+)
- `error` - Error handling
- `chan` - Channels for concurrency

### 1.2 Boolean Type (bool) 🔘

**Definition:**
- Only two values: `true` or `false`
- Memory size: 1 byte

**Real-World Examples:**
```go
package main

import "fmt"

func main() {
    // E-commerce order system
    var isOrderPaid bool = true
    var isShipped bool = false
    var hasDiscount bool = true
    var isInStock bool = true
    
    fmt.Printf("Order paid: %t\n", isOrderPaid)
    fmt.Printf("Order shipped: %t\n", isShipped)
    fmt.Printf("Has discount: %t\n", hasDiscount)
    fmt.Printf("In stock: %t\n", isInStock)
    
    // Boolean operations
    canShip := isOrderPaid && isInStock
    needsAction := !isShipped && isOrderPaid
    
    fmt.Printf("\nCan ship: %t\n", canShip)
    fmt.Printf("Needs action: %t\n", needsAction)
}
```

**Boolean Operators:**
```go
// Logical AND (&&)
result := true && true   // true
result = true && false   // false

// Logical OR (||)
result = true || false   // true
result = false || false  // false

// Logical NOT (!)
result = !true           // false
result = !false          // true

// Comparison operators return bool
isEqual := 5 == 5        // true
isGreater := 10 > 5      // true
isLess := 3 < 2          // false
```

**Practical Example: Order Validation**
```go
func canProcessOrder(isPaid bool, isInStock bool, isValidAddress bool) bool {
    return isPaid && isInStock && isValidAddress
}

func main() {
    order1 := canProcessOrder(true, true, true)   // true
    order2 := canProcessOrder(true, false, true)  // false
    order3 := canProcessOrder(false, true, true)  // false
    
    fmt.Println("Order 1 can be processed:", order1)
    fmt.Println("Order 2 can be processed:", order2)
    fmt.Println("Order 3 can be processed:", order3)
}
```

### 1.3 Integer Types 🔢

**Signed Integers (can be negative):**
```go
var age int8 = -128 to 127              // 1 byte
var temperature int16 = -32768 to 32767 // 2 bytes
var population int32 = -2B to 2B        // 4 bytes
var nationalDebt int64 = -9Q to 9Q      // 8 bytes
var count int = platform-dependent      // 4 or 8 bytes
```

**Unsigned Integers (positive only):**
```go
var level uint8 = 0 to 255               // 1 byte (also called byte)
var port uint16 = 0 to 65535             // 2 bytes
var ip uint32 = 0 to 4B                  // 4 bytes
var bigNumber uint64 = 0 to 18Q          // 8 bytes
var index uint = platform-dependent      // 4 or 8 bytes
```

**When to Use Each Type:**

| Type | Use Case | Example |
|------|----------|---------|
| `int` | **Default choice** for integers | User ID, Product ID, Quantity |
| `int64` | Large numbers, timestamps | Unix timestamp, Large calculations |
| `int32` | Specific size requirements | API compatibility |
| `int8`/`int16` | **Memory-critical** applications | Large arrays, IoT devices |
| `uint` | Always positive | Array indices, Counts |
| `uint64` | Large positive numbers | File sizes, Memory addresses |

**Practical Examples:**
```go
package main

import (
    "fmt"
    "math"
)

func main() {
    // E-commerce system
    var orderID int = 1001
    var quantity int = 5
    var productID uint = 4294967295  // Large ID
    
    // Age (always positive)
    var age uint8 = 25
    
    // Price in cents (avoid float for money)
    var priceInCents int64 = 9999  // $99.99
    
    // Demonstrating limits
    fmt.Printf("int8 max: %d\n", math.MaxInt8)
    fmt.Printf("int16 max: %d\n", math.MaxInt16)
    fmt.Printf("int32 max: %d\n", math.MaxInt32)
    fmt.Printf("int64 max: %d\n", math.MaxInt64)
    fmt.Printf("uint8 max: %d\n", math.MaxUint8)
    fmt.Printf("uint16 max: %d\n", math.MaxUint16)
    fmt.Printf("uint32 max: %d\n", math.MaxUint32)
    fmt.Printf("uint64 max: %d\n", uint64(math.MaxUint64))
    
    // Arithmetic operations
    total := quantity * 10
    discount := total - 5
    fmt.Printf("\nOrder #%d: %d items = $%d (after $%d discount)\n", 
        orderID, quantity, total, discount)
}
```

**Common Mistakes:**
```go
// ❌ Wrong: Overflow
var small int8 = 127
small = small + 1  // Overflow! Wraps to -128

// ✅ Correct: Use appropriate size
var normal int = 127
normal = normal + 1  // 128 (no overflow)

// ❌ Wrong: Mixing signed and unsigned
var signed int = -5
var unsigned uint = 10
// result := signed + unsigned  // Compile error!

// ✅ Correct: Convert explicitly
result := signed + int(unsigned)
```

> **Key Takeaway:** Every value in Go has a type. Types keep your code safe and predictable!

### 1.4 Floating-Point Types 🔢💧

**Float Types:**
```go
float32  // 32 bits, ~7 decimal digits precision
float64  // 64 bits, ~15 decimal digits precision (RECOMMENDED)
```

**When to Use:**
- **float64**: Default for decimal numbers (more accurate)
- **float32**: When memory is critical (e.g., graphics, large datasets)

**Real-World Examples:**
```go
package main

import "fmt"

func main() {
    // E-commerce prices
    var productPrice float64 = 29.99
    var shippingCost float64 = 5.99
    var taxRate float64 = 0.09  // 9%
    
    // Calculate total
    subtotal := productPrice + shippingCost
    tax := subtotal * taxRate
    total := subtotal + tax
    
    fmt.Printf("Product: $%.2f\n", productPrice)
    fmt.Printf("Shipping: $%.2f\n", shippingCost)
    fmt.Printf("Subtotal: $%.2f\n", subtotal)
    fmt.Printf("Tax (9%%): $%.2f\n", tax)
    fmt.Printf("Total: $%.2f\n", total)
    
    // Precision demonstration
    var f32 float32 = 0.1 + 0.2
    var f64 float64 = 0.1 + 0.2
    
    fmt.Printf("\nPrecision:\n")
    fmt.Printf("float32: %.20f\n", f32)
    fmt.Printf("float64: %.20f\n", f64)
}
```


### 1.5 String Type 📝

**What is a String?**
- Sequence of bytes (UTF-8 encoded)
- **Immutable** (cannot be changed after creation)
- Can be empty `""`

**String Basics:**
```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    // String declaration
    var customerName string = "Alice"
    productName := "Laptop"
    empty := ""
    
    // String properties
    fmt.Printf("Name: %s\n", customerName)
    fmt.Printf("Length: %d bytes\n", len(customerName))
    
    // String concatenation
    fullName := "John" + " " + "Doe"
    message := fmt.Sprintf("Hello, %s!", customerName)
    
    // String operations
    upper := strings.ToUpper(productName)    // "LAPTOP"
    lower := strings.ToLower(productName)    // "laptop"
    contains := strings.Contains(productName, "top")  // true
    
    fmt.Println(upper, lower, contains)
}
```

**Multi-line Strings:**
```go
// Using backticks (raw strings)
message := `Dear Customer,

Thank you for your order!

Order Details:
- Product: Laptop
- Price: $999.99
- Status: Shipped

Best regards,
The Team`

fmt.Println(message)
```

**String Iteration:**
```go
// Iterate by bytes
name := "Hello"
for i := 0; i < len(name); i++ {
    fmt.Printf("%c ", name[i])  // H e l l o
}

// Iterate by runes (characters) - RECOMMENDED for Unicode
text := "Hello 世界"
for index, runeValue := range text {
    fmt.Printf("%d: %c\n", index, runeValue)
}
```

### 1.6 Rune and Byte Types 🔤

**byte (uint8):**
- Represents a single byte (0-255)
- Used for raw binary data

**rune (int32):**
- Represents a Unicode code point
- Used for individual characters

**Understanding UTF-8:**
```go
package main

import "fmt"

func main() {
    // English text (1 byte per character)
    english := "Hello"
    fmt.Printf("'%s' length: %d bytes\n", english, len(english))  // 5 bytes
    
    // Persian text (2 bytes per character)
    persian := "سلام"
    fmt.Printf("'%s' length: %d bytes\n", persian, len(persian))  // 8 bytes
    
    // Count actual characters (runes)
    runeCount := 0
    for range persian {
        runeCount++
    }
    fmt.Printf("'%s' has %d characters\n", persian, runeCount)  // 4 characters
    
    // Converting string to []rune and []byte
    text := "Go 👍"
    runes := []rune(text)
    bytes := []byte(text)
    
    fmt.Printf("\nText: %s\n", text)
    fmt.Printf("Runes: %v (length: %d)\n", runes, len(runes))  // 4 runes
    fmt.Printf("Bytes: %v (length: %d)\n", bytes, len(bytes))   // 7 bytes
}
```

**Practical Example:**
```go
func processCustomerName(name string) {
    // Convert to runes to handle international names correctly
    runes := []rune(name)
    
    fmt.Printf("Name: %s\n", name)
    fmt.Printf("Byte length: %d\n", len(name))
    fmt.Printf("Character count: %d\n", len(runes))
    
    // Process each character
    for i, r := range runes {
        fmt.Printf("Character %d: %c (Unicode: U+%04X)\n", i+1, r, r)
    }
}

func main() {
    processCustomerName("Alice")
    fmt.Println()
    processCustomerName("محمد")  // Arabic/Persian name
}
```

---

## 📖 Section 2: Zero Values and Literals

### 2.1 Understanding Zero Values 🧊

**What is a Zero Value?**
When you declare a variable without initializing it, Go automatically assigns a **zero value** based on its type.

**Zero Values by Type:**
| Type | Zero Value | Description |
|------|------------|-------------|
| `bool` | `false` | Not true |
| `int`, `int8`, `int16`, `int32`, `int64` | `0` | Zero number |
| `uint`, `uint8`, `uint16`, `uint32`, `uint64` | `0` | Zero |
| `float32`, `float64` | `0.0` | Zero decimal |
| `string` | `""` | Empty string |
| `pointer` | `nil` | Points to nothing |
| `slice` | `nil` | No elements |
| `map` | `nil` | No key-value pairs |
| `channel` | `nil` | No channel |
| `interface` | `nil` | No value |
| `function` | `nil` | No function |

**Examples:**
```go
package main

import "fmt"

func main() {
    // Declare variables without initialization
    var isActive bool
    var count int
    var price float64
    var name string
    var data []int
    var config map[string]string
    
    // Print zero values
    fmt.Printf("bool: %t\n", isActive)        // false
    fmt.Printf("int: %d\n", count)            // 0
    fmt.Printf("float64: %f\n", price)        // 0.000000
    fmt.Printf("string: '%s'\n", name)        // ''
    fmt.Printf("slice: %v (nil: %t)\n", data, data == nil)
    fmt.Printf("map: %v (nil: %t)\n", config, config == nil)
}
```

**Why Zero Values Are Important:**
```go
// ✅ Safe to use immediately (no "undefined" errors like JavaScript)
var counter int
counter++  // Works! counter is 0, becomes 1

var message string
message = message + "Hello"  // Works! "" + "Hello" = "Hello"

// ⚠️ But be careful with nil types
var users []string
// users[0] = "Alice"  // Runtime panic! slice is nil

// ✅ Initialize first
users = []string{}
users = append(users, "Alice")  // Works!
```

**Practical Example: Order Struct**
```go
type Order struct {
    ID        int
    Customer  string
    Total     float64
    IsPaid    bool
    Items     []string
}

func main() {
    // Zero values for all fields
    var order Order
    
    fmt.Printf("Order: %+v\n", order)
    // Output: Order: {ID:0 Customer: Total:0 IsPaid:false Items:[]}
    
    // Safe to check and use
    if !order.IsPaid {
        fmt.Println("Order not paid yet")
    }
    
    if order.Total == 0 {
        fmt.Println("Order total is zero")
    }
}
```

### 2.2 Literals 📄

**What is a Literal?**
A **literal** is a fixed value written directly in your code.

**Integer Literals:**
```go
// Decimal (base 10)
var dec int = 42

// Binary (base 2) - prefix: 0b
var bin int = 0b101010  // 42

// Octal (base 8) - prefix: 0o
var oct int = 0o52  // 42

// Hexadecimal (base 16) - prefix: 0x
var hex int = 0x2A  // 42

// Underscore for readability
var large int = 1_000_000  // 1 million
var hex2 int = 0xFF_FF_FF  // 16777215
```

**Floating-Point Literals:**
```go
var f1 float64 = 3.14
var f2 float64 = .5        // 0.5
var f3 float64 = 2.        // 2.0
var f4 float64 = 1.5e3     // 1500.0 (scientific notation)
var f5 float64 = 2.5e-2    // 0.025
```

**String Literals:**
```go
// Interpreted strings (double quotes)
str1 := "Hello, World!"
str2 := "Line 1\nLine 2\tTabbed"  // Escape sequences work

// Raw strings (backticks) - no escape sequences
str3 := `C:\Users\Documents\file.txt`  // Backslashes are literal
str4 := `Line 1
Line 2
Line 3`  // Actual newlines
```

**Rune Literals:**
```go
var char1 rune = 'A'           // English character
var char2 rune = '世'          // Chinese character
var char3 rune = '\n'          // Newline
var char4 rune = '\u0041'      // Unicode: 'A'
var char5 rune = '\U00000041'  // Unicode: 'A'
```

**Boolean Literals:**
```go
var isActive bool = true
var isDeleted bool = false
```

---

## 📖 Section 3: Variables and Constants

### 3.1 Declaring Variables 📝

**Four Ways to Declare Variables in Go:**

**Method 1: var with type and value**
```go
var name string = "Alice"
var age int = 25
var price float64 = 99.99
var isActive bool = true
```

**Method 2: var with type (zero value)**
```go
var name string      // ""
var age int          // 0
var price float64    // 0.0
var isActive bool    // false
```

**Method 3: var without type (type inference)**
```go
var name = "Alice"        // string (inferred)
var age = 25              // int (inferred)
var price = 99.99         // float64 (inferred)
var isActive = true       // bool (inferred)
```

**Method 4: Short declaration `:=` (inside functions only)**
```go
name := "Alice"           // string
age := 25                 // int
price := 99.99            // float64
isActive := true          // bool
```

**When to Use Each Method:**

| Method | Use When | Scope |
|--------|----------|-------|
| `var name type = value` | Need to be explicit about type | Package or function |
| `var name type` | Want zero value | Package or function |
| `var name = value` | Type is obvious from value | Package or function |
| `name := value` | **Most common**, quick declarations | **Function only** |

**Practical Example:**
```go
package main

import "fmt"

// Package-level variables (must use var)
var companyName string = "TechShop"
var version = "1.0.0"
var maxConnections int

func main() {
    // Function-level variables (can use :=)
    customerName := "Alice"
    orderID := 1001
    totalAmount := 299.99
    isPaid := true
    
    fmt.Printf("Company: %s (v%s)\n", companyName, version)
    fmt.Printf("Order #%d for %s: $%.2f (Paid: %t)\n", 
        orderID, customerName, totalAmount, isPaid)
}
```

**Multiple Variable Declaration:**
```go
// Multiple variables of same type
var x, y, z int

// Multiple variables with values
var a, b, c = 1, 2, 3

// Multiple variables with different types
var (
    name   string = "Alice"
    age    int    = 25
    height float64 = 5.6
)

// Short declaration
x, y, z := 1, 2, 3
name, age := "Bob", 30
```

### 3.2 Short Declaration `:=` vs `var` ⚡

**When to Use `:=` (Preferred):**
```go
func processOrder() {
    // ✅ Use := for local variables
    orderID := 1001
    customerName := "Alice"
    totalAmount := 99.99
    
    // ✅ Use := when type is obvious
    items := []string{"Laptop", "Mouse"}
    prices := map[string]float64{"laptop": 999.99}
}
```

**When to Use `var`:**
```go
// ✅ Package-level variables (must use var)
var globalConfig string

// ✅ When you want zero value
func example() {
    var counter int  // 0 (explicit intent to start at zero)
    
    // ✅ When type isn't obvious
    var timeout time.Duration = 5 * time.Second
    
    // ✅ When declaring but assigning later
    var result string
    if condition {
        result = "success"
    } else {
        result = "failure"
    }
}
```

**Common Mistake:**
```go
// ❌ Can't use := at package level
package main

// orderCount := 0  // Syntax error!

// ✅ Use var at package level
var orderCount = 0

func main() {
    // ✅ := works here (inside function)
    customerName := "Alice"
}
```

### 3.3 Constants with `const` 🔒

**What is a Constant?**
- Value that **never changes** during program execution
- Must be assigned at **compile time**
- Cannot be modified after declaration

**Declaring Constants:**
```go
const pi = 3.14159
const companyName = "TechShop"
const maxRetries = 3
const isDebugMode = false

// Typed constants
const timeout int64 = 30
const taxRate float64 = 0.09

// Multiple constants
const (
    StatusPending   = "pending"
    StatusShipped   = "shipped"
    StatusDelivered = "delivered"
)
```

**Real-World E-Commerce Example:**
```go
package main

import "fmt"

// Application constants
const (
    AppName    = "E-Commerce Platform"
    AppVersion = "2.1.0"
    APITimeout = 30  // seconds
)

// Order status constants
const (
    OrderPending   = "pending"
    OrderConfirmed = "confirmed"
    OrderShipped   = "shipped"
    OrderDelivered = "delivered"
    OrderCancelled = "cancelled"
)

// Pricing constants
const (
    TaxRate            = 0.09   // 9%
    FreeShippingMin    = 100.0  // $100
    StandardShipping   = 5.99
    ExpressShipping    = 15.99
)

func calculateTotal(subtotal float64) float64 {
    tax := subtotal * TaxRate
    
    var shipping float64
    if subtotal >= FreeShippingMin {
        shipping = 0.0
    } else {
        shipping = StandardShipping
    }
    
    return subtotal + tax + shipping
}

func main() {
    fmt.Printf("%s v%s\n", AppName, AppVersion)
    
    orderAmount := 95.00
    total := calculateTotal(orderAmount)
    
    fmt.Printf("Subtotal: $%.2f\n", orderAmount)
    fmt.Printf("Tax (%.0f%%): $%.2f\n", TaxRate*100, orderAmount*TaxRate)
    fmt.Printf("Shipping: $%.2f\n", StandardShipping)
    fmt.Printf("Total: $%.2f\n", total)
}
```

### 3.4 Typed vs Untyped Constants 🎯

**Untyped Constants (Flexible):**
```go
const maxItems = 100  // Untyped integer
const taxRate = 0.09  // Untyped float

func example() {
    var count1 int32 = maxItems   // Works: adapts to int32
    var count2 int64 = maxItems   // Works: adapts to int64
    var count3 int = maxItems     // Works: adapts to int
    
    var rate1 float32 = taxRate   // Works: adapts to float32
    var rate2 float64 = taxRate   // Works: adapts to float64
}
```

**Typed Constants (Strict):**
```go
const maxItems int = 100         // Typed as int
const taxRate float64 = 0.09    // Typed as float64

func example() {
    var count1 int32 = maxItems    // ❌ Error! Can't assign int to int32
    var count2 int = maxItems      // ✅ Works (same type)
    
    var rate1 float32 = taxRate    // ❌ Error! Can't assign float64 to float32
    var rate2 float64 = taxRate    // ✅ Works (same type)
}
```

**Best Practice - Use Untyped:**
```go
// ✅ Recommended: Untyped (more flexible)
const (
    maxRetries = 3
    timeout = 30
    taxRate = 0.09
)

// ⚠️ Use typed only when necessary
const timeout int64 = 30  // When you need specific type
```

### 3.5 iota - Auto-Incrementing Constants 🔢

**What is iota?**
- Special constant generator
- Starts at `0` and increments by `1`
- Resets in each `const` block

**Basic Usage:**
```go
const (
    Sunday    = iota  // 0
    Monday            // 1
    Tuesday           // 2
    Wednesday         // 3
    Thursday          // 4
    Friday            // 5
    Saturday          // 6
)
```

**E-Commerce Order Status:**
```go
const (
    StatusPending = iota  // 0
    StatusConfirmed       // 1
    StatusProcessing      // 2
    StatusShipped         // 3
    StatusDelivered       // 4
    StatusCancelled       // 5
    StatusRefunded        // 6
)

func getStatusName(status int) string {
    names := []string{
        "Pending",
        "Confirmed",
        "Processing",
        "Shipped",
        "Delivered",
        "Cancelled",
        "Refunded",
    }
    if status >= 0 && status < len(names) {
        return names[status]
    }
    return "Unknown"
}
```

**Advanced iota Patterns:**
```go
// Skip values
const (
    _ = iota  // Skip 0
    Small     // 1
    Medium    // 2
    Large     // 3
)

// Multiply by values
const (
    KB = 1 << (10 * iota)  // 1 << 0 = 1
    MB                      // 1 << 10 = 1024
    GB                      // 1 << 20 = 1048576
    TB                      // 1 << 30 = 1073741824
)

// Bit flags
const (
    Read = 1 << iota   // 1 (binary: 001)
    Write              // 2 (binary: 010)
    Execute            // 4 (binary: 100)
)

// Can combine flags
permission := Read | Write  // 3 (binary: 011)
```

---

## 📖 Section 4: Type Conversion and Safety

### 4.1 Explicit Type Conversion 🔄

**Go requires explicit type conversion** - no automatic conversion!

**Basic Conversions:**
```go
// int to float
var quantity int = 5
var price float64 = 19.99
total := float64(quantity) * price  // Must convert int to float64

// float to int (truncates decimal part)
var price float64 = 29.99
var priceInt int = int(price)  // 29 (decimal lost!)

// int to string (NOT the number as text!)
var num int = 65
str := string(num)  // "A" (ASCII character, not "65"!)

// Correct: int to string representation
import "strconv"
str := strconv.Itoa(65)  // "65"

// string to int
str := "123"
num, err := strconv.Atoi(str)  // 123
if err != nil {
    // Handle error
}
```

**E-Commerce Example:**
```go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    // Product pricing
    unitPrice := 29.99
    quantity := 3
    
    // Calculate total
    total := unitPrice * float64(quantity)  // Explicit conversion
    
    fmt.Printf("Unit price: $%.2f\n", unitPrice)
    fmt.Printf("Quantity: %d\n", quantity)
    fmt.Printf("Total: $%.2f\n", total)
    
    // Convert to cents (avoid float precision issues)
    totalCents := int(total * 100)
    fmt.Printf("Total in cents: %d¢\n", totalCents)
    
    // Convert order ID to string
    orderID := 1001
    orderIDStr := strconv.Itoa(orderID)
    confirmationCode := "ORD-" + orderIDStr
    fmt.Printf("Confirmation: %s\n", confirmationCode)
}
```

**Common Conversion Functions:**
```go
import "strconv"

// String to numbers
i, err := strconv.Atoi("123")           // string to int
i64, err := strconv.ParseInt("123", 10, 64)  // string to int64
f, err := strconv.ParseFloat("3.14", 64)     // string to float64
b, err := strconv.ParseBool("true")          // string to bool

// Numbers to string
s := strconv.Itoa(123)                  // int to string
s := strconv.FormatInt(123, 10)        // int64 to string
s := strconv.FormatFloat(3.14, 'f', 2, 64)  // float to string
s := strconv.FormatBool(true)          // bool to string
```

### 4.2 Type Safety in Go 🛡️

**Why Type Safety Matters:**
```go
// ❌ This won't compile in Go (good thing!)
var quantity int = 5
var price float64 = 19.99
// total := quantity * price  // Compile error!

// ✅ Must be explicit
total := float64(quantity) * price  // Works!
```

**Comparison with Other Languages:**
```javascript
// JavaScript (dangerous implicit conversion)
console.log("5" + 5);      // "55" (string concatenation)
console.log("5" - 5);      // 0 (numeric subtraction)
console.log("5" * "2");    // 10 (both converted to numbers!)
console.log([] + []);      // "" (empty string)
console.log({} + []);      // "[object Object]"
```

```python
# Python (some implicit conversion)
result = 5 + 5.0  # 10.0 (int to float, automatic)
result = "5" + "5"  # "55" (string concatenation)
# result = "5" + 5  # TypeError at runtime!
```

```go
// Go (explicit everything!)
var a int = 5
var b float64 = 5.0
// result := a + b  // Compile error!
result := float64(a) + b  // ✅ Explicit

var s string = "5"
var n int = 5
// result := s + n  // Compile error!
result := s + strconv.Itoa(n)  // ✅ "55"
```

### 4.3 Practical Conversion Examples 💼

**Example 1: Money Handling (Avoid Float Precision Issues)**
```go
package main

import "fmt"

// Store money as cents (integers)
type Money int64

func NewMoney(dollars float64) Money {
    return Money(dollars * 100)
}

func (m Money) Dollars() float64 {
    return float64(m) / 100
}

func (m Money) String() string {
    return fmt.Sprintf("$%.2f", m.Dollars())
}

func main() {
    price := NewMoney(29.99)
    quantity := 3
    total := Money(int64(price) * int64(quantity))
    
    fmt.Printf("Price: %s\n", price)
    fmt.Printf("Quantity: %d\n", quantity)
    fmt.Printf("Total: %s\n", total)
}
```

**Example 2: Temperature Conversion**
```go
func celsiusToFahrenheit(c float64) float64 {
    return c*9/5 + 32
}

func fahrenheitToCelsius(f float64) float64 {
    return (f - 32) * 5 / 9
}

func main() {
    celsius := 25.0
    fahrenheit := celsiusToFahrenheit(celsius)
    
    fmt.Printf("%.1f°C = %.1f°F\n", celsius, fahrenheit)
    // Output: 25.0°C = 77.0°F
}
```

### 4.4 Unused Variables - Go's Strict Rule 🚫

**Go's Rule:** If you declare a variable and don't use it, your code won't compile!

**Why This Rule Exists:**
- Prevents dead code
- Keeps codebase clean
- Catches potential bugs (typos in variable names)
- Forces developers to think about every variable

**Examples:**
```go
// ❌ This won't compile
func example() {
    unused := "Hello"  // Declared but never used
    // Compile error: unused declared but not used
}

// ✅ This compiles (variable is used)
func example() {
    message := "Hello"
    fmt.Println(message)  // Used!
}
```

**Ignoring Values with `_` (Blank Identifier):**
```go
// Ignore return values you don't need
value, _ := someFunction()  // Ignore second return value
_, err := someFunction()    // Ignore first return value

// Ignore loop variables
for _, item := range items {  // Ignore index
    fmt.Println(item)
}

for i := range items {  // Ignore value (just need index)
    fmt.Println(i)
}

// Ignore all values (just loop)
for range items {  // Just iterate, ignore both
    fmt.Println("Item exists")
}
```

**Real-World Example:**
```go
package main

import (
    "fmt"
    "os"
)

func processOrder(orderID int) (string, error) {
    if orderID <= 0 {
        return "", fmt.Errorf("invalid order ID")
    }
    return "Order processed", nil
}

func main() {
    // ✅ Use both return values
    message, err := processOrder(1001)
    if err != nil {
        fmt.Println("Error:", err)
        os.Exit(1)
    }
    fmt.Println(message)
    
    // ✅ Ignore error if you're sure it won't fail
    message, _ = processOrder(1002)
    fmt.Println(message)
    
    // ❌ This won't compile
    // result, err := processOrder(1003)
    // // 'result' and 'err' declared but not used!
}
```

---

## 📖 Section 5: Naming Conventions

### 5.1 Variable and Constant Naming Rules 📛

**Go Naming Rules (Must Follow):**
1. Names must start with a **letter** (a-z, A-Z) or **underscore** (_)
2. Followed by **letters**, **digits** (0-9), or **underscores**
3. Names are **case-sensitive** (`name` ≠ `Name`)
4. Cannot use **Go keywords** (`func`, `var`, `if`, `for`, etc.)

**Valid Names:**
```go
name          // ✅
customerName  // ✅
customer_name // ✅
_internal     // ✅
price2023     // ✅
MAX_RETRIES   // ✅
```

**Invalid Names:**
```go
2fast         // ❌ Starts with digit
user-name     // ❌ Hyphen not allowed
var           // ❌ Go keyword
customer name // ❌ Space not allowed
```

### 5.2 Go Naming Conventions (Best Practices) ⭐

**1. camelCase for Variables and Functions (Unexported):**
```go
// ✅ Good: camelCase for local/unexported
var orderCount int
var totalAmount float64
var customerName string

func calculateTotal() {}
func processOrder() {}
```

**2. PascalCase for Exported Names:**
```go
// ✅ Exported (visible outside package)
package order

type Order struct {
    ID     int     // Exported field
    Amount float64 // Exported field
}

func ProcessOrder() {}  // Exported function
var MaxRetries = 3      // Exported variable
```

**3. Short Names for Short Scopes:**
```go
// ✅ Good: Short names in small scopes
func sum(nums []int) int {
    s := 0  // Short scope, short name
    for _, n := range nums {
        s += n
    }
    return s
}

// ✅ Good: Longer names for larger scopes
var customerDatabase *Database
var authenticationService *Service
```

**4. Acronyms Stay Uppercase:**
```go
// ✅ Correct
var userID int       // Not userId
var apiURL string    // Not apiUrl
var httpServer       // Not httpServer
var maxHTTPRequests  // Not maxHttpRequests

// For exported names
type APIClient struct {}  // Not ApiClient
func ServeHTTP() {}       // Not ServeHttp
```

**5. Avoid Underscores in Names (Go Style):**
```go
// ⚠️ Acceptable but not idiomatic
var user_name string
var total_amount float64

// ✅ Preferred: camelCase
var userName string
var totalAmount float64

// ✅ Exception: Constants can use underscores
const MAX_RETRY_COUNT = 3
const DEFAULT_TIMEOUT = 30
```

### 5.3 Package Naming 📦

**Rules:**
- **Lowercase only** (no capitals, no underscores, no dashes)
- **Short and descriptive**
- **Singular form** preferred
- **Match directory name**

```go
// ✅ Good package names
package order
package payment
package customer
package http
package json
package strconv

// ❌ Bad package names
package Order          // No capitals!
package order_utils    // No underscores!
package order-helper   // No dashes!
package utils          // Too generic!
package common         // Too generic!
```

**Multi-Word Packages:**
```go
// ❌ Don't use separators
package order_processor
package orderProcessor

// ✅ Combine into one word
package orderprocessor
package paymentgateway
```

### 5.4 Complete Naming Example 📚

```go
// Package name: lowercase, single word
package ecommerce

// Exported constants: UPPER_SNAKE_CASE or PascalCase
const (
    MAX_CART_ITEMS = 50
    DEFAULT_CURRENCY = "USD"
    FreeShippingThreshold = 100.0
)

// Unexported constants: camelCase
const (
    defaultTaxRate = 0.09
    maxRetries = 3
)

// Exported type: PascalCase
type Order struct {
    ID         int        // Exported field: PascalCase
    CustomerID string     // Acronyms stay uppercase
    Items      []Item     // Exported field
    totalAmount float64   // Unexported field: camelCase
    isPaid     bool       // Unexported field
}

// Exported function: PascalCase
func CalculateTotal(order *Order) float64 {
    // Local variables: camelCase, short names
    tax := order.totalAmount * defaultTaxRate
    shipping := calculateShipping(order)
    
    return order.totalAmount + tax + shipping
}

// Unexported function: camelCase
func calculateShipping(order *Order) float64 {
    if order.totalAmount >= FreeShippingThreshold {
        return 0.0
    }
    return 5.99
}

// Method on type
func (o *Order) IsEligibleForFreeShipping() bool {
    return o.totalAmount >= FreeShippingThreshold
}
```

### 5.5 Common Naming Patterns 🎯

**Getters (No "Get" prefix):**
```go
type Order struct {
    total float64
}

// ❌ Don't use "Get" prefix
func (o *Order) GetTotal() float64 {
    return o.total
}

// ✅ Just use the name
func (o *Order) Total() float64 {
    return o.total
}
```

**Setters (Use "Set" prefix):**
```go
// ✅ Setters can use "Set" prefix
func (o *Order) SetTotal(amount float64) {
    o.total = amount
}
```

**Boolean Fields/Functions:**
```go
// ✅ Start with "is", "has", "can", "should"
var isActive bool
var hasDiscount bool
var canShip bool

func (o *Order) IsValid() bool {}
func (o *Order) HasItems() bool {}
func (o *Order) CanBeCancelled() bool {}
```

**Interfaces (often end with "er"):**
```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type Stringer interface {
    String() string
}

// Your custom interfaces
type OrderProcessor interface {
    Process(order *Order) error
}

type PaymentHandler interface {
    Handle(payment *Payment) error
}
```

---

## 🏋️ Section 6: Exercises and Challenges

### Exercise 1: Type Basics 📝

**Simple Level:**

1. Declare variables for an e-commerce product:
```go
// Your code here:
// - Product name (string)
// - Price (float64)
// - Quantity in stock (int)
// - Is available (bool)
```

**Solution:**
```go
package main

import "fmt"

func main() {
    var productName string = "Laptop"
    var price float64 = 999.99
    var quantityInStock int = 15
    var isAvailable bool = true
    
    fmt.Printf("Product: %s\n", productName)
    fmt.Printf("Price: $%.2f\n", price)
    fmt.Printf("Stock: %d units\n", quantityInStock)
    fmt.Printf("Available: %t\n", isAvailable)
}
```

### Exercise 2: Zero Values 🧊

**Simple Level:**

What will this code print?
```go
package main

import "fmt"

func main() {
    var count int
    var price float64
    var name string
    var isActive bool
    
    fmt.Println(count, price, name, isActive)
}
```

**Answer:** `0 0  false` (int=0, float64=0.0, string="", bool=false)

### Exercise 3: Type Conversion 🔄

**Medium Level:**

Fix this code:
```go
package main

import "fmt"

func main() {
    var quantity int = 5
    var unitPrice float64 = 29.99
    
    // ❌ This won't compile
    total := quantity * unitPrice
    
    fmt.Printf("Total: $%.2f\n", total)
}
```

**Solution:**
```go
package main

import "fmt"

func main() {
    var quantity int = 5
    var unitPrice float64 = 29.99
    
    // ✅ Convert quantity to float64
    total := float64(quantity) * unitPrice
    
    fmt.Printf("Total: $%.2f\n", total)
}
```

### Exercise 4: Constants and iota 🔢

**Medium Level:**

Create order status constants using `iota`:
```go
package main

import "fmt"

// Your code here:
// Define order statuses: Pending, Confirmed, Shipped, Delivered, Cancelled

func getStatusName(status int) string {
    // Your code here
}

func main() {
    // Test your constants
}
```

**Solution:**
```go
package main

import "fmt"

const (
    StatusPending = iota
    StatusConfirmed
    StatusShipped
    StatusDelivered
    StatusCancelled
)

func getStatusName(status int) string {
    names := []string{"Pending", "Confirmed", "Shipped", "Delivered", "Cancelled"}
    if status >= 0 && status < len(names) {
        return names[status]
    }
    return "Unknown"
}

func main() {
    fmt.Println("Order status:", getStatusName(StatusShipped))
    fmt.Println("Status value:", StatusShipped)
}
```

### Challenge 1: Temperature Converter 🌡️

**Medium Level:**

Create a temperature converter with proper type handling:

**Requirements:**
- Function `celsiusToFahrenheit(c float64) float64`
- Function `fahrenheitToCelsius(f float64) float64`
- Function `celsiusToKelvin(c float64) float64`
- Main function to test conversions

**Solution:**
```go
package main

import "fmt"

func celsiusToFahrenheit(c float64) float64 {
    return c*9/5 + 32
}

func fahrenheitToCelsius(f float64) float64 {
    return (f - 32) * 5 / 9
}

func celsiusToKelvin(c float64) float64 {
    return c + 273.15
}

func main() {
    celsius := 25.0
    
    fahrenheit := celsiusToFahrenheit(celsius)
    kelvin := celsiusToKelvin(celsius)
    
    fmt.Printf("%.1f°C = %.1f°F = %.2fK\n", celsius, fahrenheit, kelvin)
    
    // Test reverse conversion
    backToCelsius := fahrenheitToCelsius(fahrenheit)
    fmt.Printf("%.1f°F = %.1f°C\n", fahrenheit, backToCelsius)
}
```

### Challenge 2: Money Calculator 💰

**Hard Level:**

Create a money type that avoids floating-point precision issues:

**Requirements:**
- Store money as cents (integer)
- Functions: Add, Subtract, Multiply
- Format as currency string
- Handle negative amounts

**Solution:**
```go
package main

import (
    "fmt"
)

type Money int64

// Constructor
func NewMoney(dollars float64) Money {
    return Money(dollars * 100)
}

func (m Money) Dollars() float64 {
    return float64(m) / 100
}

func (m Money) String() string {
    if m < 0 {
        return fmt.Sprintf("-$%.2f", float64(-m)/100)
    }
    return fmt.Sprintf("$%.2f", float64(m)/100)
}

func (m Money) Add(other Money) Money {
    return m + other
}

func (m Money) Subtract(other Money) Money {
    return m - other
}

func (m Money) Multiply(factor int) Money {
    return m * Money(factor)
}

func main() {
    price := NewMoney(29.99)
    quantity := 3
    
    total := price.Multiply(quantity)
    discount := NewMoney(10.00)
    final := total.Subtract(discount)
    
    fmt.Printf("Unit price: %s\n", price)
    fmt.Printf("Quantity: %d\n", quantity)
    fmt.Printf("Subtotal: %s\n", total)
    fmt.Printf("Discount: %s\n", discount)
    fmt.Printf("Final: %s\n", final)
}
```

### Challenge 3: LeetCode - Valid Palindrome Number 🔍

**Medium Level:**

Determine if an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

**Example 1:**
```
Input: x = 121
Output: true
Explanation: 121 reads as 121 from left to right and from right to left.
```

**Example 2:**
```
Input: x = -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

**Example 3:**
```
Input: x = 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```

**Solution:**
```go
package main

import (
    "fmt"
    "strconv"
)

// Method 1: Convert to string
func isPalindrome(x int) bool {
    if x < 0 {
        return false
    }
    
    s := strconv.Itoa(x)
    left, right := 0, len(s)-1
    
    for left < right {
        if s[left] != s[right] {
            return false
        }
        left++
        right--
    }
    
    return true
}

// Method 2: Reverse number (without string conversion)
func isPalindromeNumeric(x int) bool {
    if x < 0 || (x%10 == 0 && x != 0) {
        return false
    }
    
    reversed := 0
    original := x
    
    for x > 0 {
        digit := x % 10
        reversed = reversed*10 + digit
        x /= 10
    }
    
    return original == reversed
}

func main() {
    testCases := []int{121, -121, 10, 0, 12321, 12345}
    
    for _, tc := range testCases {
        result := isPalindrome(tc)
        fmt.Printf("isPalindrome(%d) = %t\n", tc, result)
    }
}
```

---

## 🎁 Chapter 2 Summary

### ✅ What You Learned:

**1. Type System:**
- Boolean, Integer, Float, String, Rune, Byte types
- When to use each type
- Memory sizes and ranges

**2. Zero Values:**
- Every type has a default zero value
- Prevents undefined behavior
- Safe to use immediately

**3. Literals:**
- Integer literals (decimal, binary, octal, hex)
- Float literals (decimal, scientific)
- String literals (interpreted vs raw)
- Rune and boolean literals

**4. Variables:**
- Four ways to declare (`var`, `:=`)
- When to use each method
- Multiple declarations
- Unused variable rules

**5. Constants:**
- Typed vs untyped
- `iota` for auto-incrementing
- Best practices for constants

**6. Type Conversion:**
- Explicit conversion required
- No automatic type conversion
- `strconv` package for string conversions
- Type safety benefits

**7. Naming Conventions:**
- camelCase vs PascalCase
- Exported vs unexported
- Package naming
- Common patterns

### 🎯 Key Takeaways:

1. **Go is statically and strongly typed** - catches errors at compile time
2. **Explicit is better than implicit** - all conversions must be explicit
3. **Zero values prevent bugs** - no "undefined" values
4. **Naming matters** - follow Go conventions for readable code
5. **Type safety is your friend** - prevents entire classes of bugs

### 📚 Next Chapter:

Ready for **Chapter 3: Composite Types**? We'll learn about:
- Arrays and Slices
- Maps
- Structs
- Bytes and Runes in depth

**Let's continue! 🚀**

---

### 5. Numeric Types 🔢
Go provides several numeric types with different ranges and memory sizes:

**Signed Integers (can be negative or positive):**
- `int8`: -128 to 127
- `int16`: -32,768 to 32,767
- `int32`: -2,147,483,648 to 2,147,483,647
- `int64`: -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807
- `int`: Platform-dependent (32 or 64 bits, same as `int32` or `int64`)

**Unsigned Integers (only positive values):**
- `uint8` (also called `byte`): 0 to 255
- `uint16`: 0 to 65,535
- `uint32`: 0 to 4,294,967,295
- `uint64`: 0 to 18,446,744,073,709,551,615
- `uint`: Platform-dependent (32 or 64 bits, same as `uint32` or `uint64`)

**Floating-Point Numbers (decimal numbers):**
- `float32`: Approximately ±3.4 × 10³⁸ with about 7 decimal digits of precision
- `float64`: Approximately ±1.7 × 10³⁰⁸ with about 15-17 decimal digits of precision

**Choosing the Right Type:**
Select based on:
- **Range needed:** Will your numbers exceed the type's limits?
- **Memory size:** Smaller types use less memory (useful for large arrays)
- **API/database compatibility:** Some APIs require specific types

**Example:**
```go
var productPrice float64 = 29.99
var quantity int = 3
total := productPrice * float64(quantity)
```

### 6. A Taste of Strings and Runes 🍬
- Strings are UTF-8 encoded.
- A `rune` is an alias for `int32`, representing Unicode characters.

**UTF-8 Encoding Details:**
UTF-8 is a variable-length encoding, meaning different characters use different numbers of bytes:
- **English characters (ASCII):** 1 byte per character (e.g., 'A', 'z', '0', '!')
- **Persian/Arabic characters:** 2 bytes per character (e.g., 'س', 'م', 'ا', 'ل')
- **Some special characters:** 3 or 4 bytes (e.g., emojis, certain symbols)

This means a string like `"Hello"` uses 5 bytes, while `"سلام"` uses 8 bytes (4 characters × 2 bytes each). When you iterate over a string with `range`, Go automatically handles UTF-8 decoding, giving you one rune (character) at a time, regardless of how many bytes it occupies.

**Example:**
```go
var status string = "Delivered"
for _, r := range status {
    fmt.Printf("%c\n", r) // prints each character
}
```

> **Real-World Analogy:** Think of a string as a necklace, and each rune is a bead!

### 7. Explicit Type Conversion 🔄
Go requires explicit conversions.

```go
var quantity int = 3
var unitPrice float64 = 19.5
total := float64(quantity) * unitPrice
```

### 8. Literals Are Untyped 🕵️
Literals like `42` or `"Hi"` are untyped until assigned. This means they don't have a fixed type until Go determines what type they should be based on context.

```go
var x = 10     // x is int
const y = 3.14 // y is untyped float until used
```

**Real-World Example:**
Untyped literals are flexible and can adapt to different numeric types as needed:

```go
// Tax rate as an untyped constant
const taxRate = 0.09  // untyped float

var orderTotal int = 10000        // in cents (100.00 dollars)
var discountPercent float64 = 15.5

// The untyped literal 0.09 can be used with both int and float64
var taxAmount1 int = int(float64(orderTotal) * taxRate)        // taxRate adapts to float64
var taxAmount2 float64 = float64(orderTotal) * taxRate          // taxRate adapts to float64
var finalPrice float64 = 199.99 * (1 + taxRate)                // taxRate works with float64 literal

fmt.Printf("Tax: $%.2f\n", float64(taxAmount1)/100)
fmt.Printf("Final price with tax: $%.2f\n", finalPrice)
```

In this example, `taxRate` is an untyped constant that can be used with `int`, `float64`, or any other numeric type without explicit conversion of the constant itself. This flexibility makes untyped literals powerful for mathematical calculations where you need the same value in different contexts.

### 9. var Versus := ⚡
- Use `var` for package-level or explicit type declarations.
- Use `:=` for short local declarations.

**Example:**
```go
var vendorName string = "PizzaCo"
orderID := 12345
```

### 10. Using const 🏷️
Constants are fixed at compile time.

```go
const currency = "USD"
```

### 11. Typed and Untyped Constants 🧑‍🏫
```go
const a = 10        // untyped
const b int = 10    // typed
```
Typed constants prevent unwanted assignments.

### 12. Unused Variables 🚫
Go disallows unused variables.

```go
var unused string // compile-time error
```
Use `_` to ignore values:
```go
_, err := someFunc()
```

> **Watch Out!** Go is strict about unused variables. It keeps your code clean!

### 13. Naming Variables and Constants 🏷️
Go has clear naming conventions that make code more readable and maintainable.

**1. Be Descriptive:**
Use clear, meaningful names that explain what the variable represents.

```go
// ❌ Bad: Abbreviated and unclear
var ta float64
var qty int
var custID string

// ✅ Good: Descriptive and self-documenting
var totalAmount float64
var quantity int
var customerID string
```

**2. camelCase for Variables and Unexported Names:**
Variables, functions, and types that start with a lowercase letter are **unexported** (private to the package).

```go
package order

// Unexported variables (only accessible within this package)
var orderCount int
var defaultTaxRate float64 = 0.09

// Unexported function (only accessible within this package)
func calculateSubtotal(items []Item) float64 {
    var subtotal float64
    for _, item := range items {
        subtotal += item.Price
    }
    return subtotal
}
```

**3. PascalCase for Exported Names:**
Names that start with an uppercase letter are **exported** (public, accessible from other packages).

```go
package order

// Exported type (can be used by other packages)
type Order struct {
    ID     int
    Amount float64
}

// Exported function (can be called from other packages)
func CalculateTotal(order Order) float64 {
    return order.Amount * (1 + DefaultTaxRate)
}

// Exported constant (can be used by other packages)
const DefaultTaxRate = 0.09
```

**4. Constants: camelCase or UPPER_SNAKE_CASE:**
Constants can use either style, but `UPPER_SNAKE_CASE` is preferred for acronyms and public constants.

```go
// camelCase for simple constants
const maxRetries = 3
const defaultTimeout = 30

// UPPER_SNAKE_CASE for exported constants and acronyms
const MAX_ORDER_ITEMS = 100
const API_BASE_URL = "https://api.example.com"
const HTTP_STATUS_OK = 200

// Mixed: camelCase for private, UPPER_SNAKE_CASE for public
const maxConnections = 10        // unexported
const MAX_CONNECTIONS = 10       // exported (if you want it public)
```

**5. Package Names:**
Package names in Go follow specific rules and conventions:

- **Lowercase only:** Package names must be lowercase
- **No underscores or dashes:** Use single words or compound words without separators
- **Short and descriptive:** Keep names concise but meaningful
- **Singular form:** Prefer singular nouns (e.g., `order` not `orders`)
- **Match directory name:** Package name should match the directory name
- **Avoid generic names:** Don't use names like `util`, `common`, `misc`

```go
// ❌ Bad: Package naming mistakes
package Order          // uppercase not allowed
package order_utils    // underscores not allowed
package order-helper   // dashes not allowed
package utils          // too generic
package common         // too generic

// ✅ Good: Proper package names
package order          // lowercase, singular, descriptive
package payment        // clear purpose
package shipping       // specific functionality
package validator      // describes what it does
```

**Package Names with Multiple Words:**
When a package name consists of multiple words, combine them into a single lowercase word without any separators (no underscores, dashes, or spaces).

```go
// ❌ Bad: Using separators
package order_processor    // underscores not allowed
package order-processor    // dashes not allowed
package order processor   // spaces not allowed

// ✅ Good: Combined into single word
package orderprocessor    // two words combined
package paymentgateway    // two words combined
package userauthenticator // three words combined
```

**Package Name Examples:**
```go
// Simple, single-word packages
package http
package json
package time
package order
package payment

// Two-word packages (combined)
package httputil          // http + util
package jsonrpc           // json + rpc
package timezone          // time + zone
package orderprocessor    // order + processor
package paymentgateway    // payment + gateway
package userauthenticator // user + authenticator
package databasemanager   // database + manager

// Three or more words (all combined)
package orderprocessingservice  // order + processing + service
package userauthentication      // user + authentication
package paymentgatewayhandler   // payment + gateway + handler
```

**Real-World Examples from Go Standard Library:**
```go
// Standard library examples
package httputil      // http utilities
package jsonrpc       // JSON-RPC protocol
package timezone      // time zone handling
package filepath      // file path operations
package strconv       // string conversion
package syscall       // system calls
```

**Package name matches directory structure:**
```go
// Directory: github.com/user/project/order
// Package:   package order

// Directory: github.com/user/project/orderprocessor
// Package:   package orderprocessor

// Directory: github.com/user/project/payment/processor
// Package:   package processor  (not "paymentprocessor" - the directory already indicates it's in payment/)
```

**Important Notes:**
- The package name is what you use when importing: `import "github.com/user/project/orderprocessor"` → use as `orderprocessor.ProcessOrder()`
- Package name should reflect what the package provides, not where it comes from
- **For multi-word names:** Simply concatenate all words together in lowercase: `order` + `processor` = `orderprocessor`
- Keep it readable: If the combined name becomes too long or hard to read, consider if you can split it into separate packages or use a shorter, more descriptive single word

**Complete Example:**
```go
package ecommerce

// Exported constants (UPPER_SNAKE_CASE)
const (
    MAX_CART_ITEMS = 50
    DEFAULT_CURRENCY = "USD"
    FREE_SHIPPING_THRESHOLD = 100.00
)

// Unexported constants (camelCase)
const (
    defaultTaxRate = 0.09
    maxRetryAttempts = 3
)

// Exported type
type Cart struct {
    Items      []Item
    CustomerID string
}

// Unexported variable
var totalOrders int

// Exported function
func CalculateShipping(cart Cart) float64 {
    if cart.Total() >= FREE_SHIPPING_THRESHOLD {
        return 0.0
    }
    return calculateStandardShipping(cart) // calls unexported function
}

// Unexported function
func calculateStandardShipping(cart Cart) float64 {
    return 5.99 // standard shipping fee
}
```

### 14. Wrapping Up 🎁
You've learned:
- Go's type system foundation
- Value storage and initialization
- Naming and declaration best practices

---

## 📝 Exam Time! Test Your Knowledge 🧠

### Simple Level
1. What is the zero value of a string?
2. What keyword defines a constant?
3. What happens if you declare a variable but do not use it?

### Medium Level
1. Write a Go snippet to calculate the total cost of an order with `quantity` and `unitPrice`.
2. What's the difference between:
   ```go
   const a = 5
   const b int = 5
   ```
3. Why does Go not allow implicit type conversion?

### Hard Level
1. Write a function `GetOrderSummary` that takes:
   - `customerName` (string)
   - `orderID` (int)
   - `isPaid` (bool)
   Returns a formatted string like: "Order 123 by Amir - Paid ✅"
2. Convert the untyped constant:
   ```go
   const rate = 1.8
   var tempCelsius int = 30
   ```
   Multiply `tempCelsius` by `rate` to return the Fahrenheit equivalent.
3. Define a function that returns a rune from a string and validates it's alphabetic using range comparisons.

---

## 🧩 Chapter 3: Composite Types

![Composite Types](https://images.unsplash.com/photo-1464983953574-0892a716854b?auto=format&fit=crop&w=600&q=80)

### 📚 Table of Contents for This Chapter
1. **Introduction: What are Composite Types?**
2. **Section 1: Arrays - The Foundation**
3. **Section 2: Slices - Dynamic Arrays**
4. **Section 3: Maps - Key-Value Storage**
5. **Section 4: Structs - Custom Types**
6. **Section 5: Strings, Runes, and Bytes**
7. **Exercises and Challenges**

---

## 🎯 Introduction: What are Composite Types?

### Understanding Composite Types

**Basic Types** (Chapter 2) hold single values:
```go
var age int = 25           // Single number
var name string = "Alice"  // Single text value
var price float64 = 99.99  // Single decimal
```

**Composite Types** (Chapter 3) hold multiple values:
```go
var prices [3]float64            // Array: 3 numbers
var names []string               // Slice: dynamic list of texts
var userAges map[string]int      // Map: name → age pairs
var product struct { name string; price float64 }  // Struct: grouped data
```

### Composite Types Overview

```mermaid
mindmap
  root((Composite Types))
    Arrays
      Fixed size
      Rarely used directly
      [3]int
    Slices
      Dynamic size
      Most commonly used
      []int
    Maps
      Key-value pairs
      map[K]V
    Structs
      Custom data structures
      type Person struct
    Strings
      Immutable byte sequence
      string
```

**Comparison Table:**

| Type | Size | Use Case | Example |
|------|------|----------|---------|
| **Array** | Fixed | Exact number of items | `[3]string{"A", "B", "C"}` |
| **Slice** | Dynamic | Lists that grow/shrink | `[]int{1, 2, 3}` |
| **Map** | Dynamic | Key-value lookups | `map[string]int{"age": 25}` |
| **Struct** | Fixed fields | Related data together | `type User struct { Name string }` |

---

## 📖 Section 1: Arrays - The Foundation

### 1.1 Understanding Arrays 🧱

**What is an Array?**
- Fixed-size collection of elements
- All elements must be the **same type**
- Size is **part of the type** (can't be changed)
- Stored contiguously in memory

**Array Declaration:**
```go
// Method 1: Declare with size
var prices [3]float64

// Method 2: Declare and initialize
var products [3]string = [3]string{"Laptop", "Mouse", "Keyboard"}

// Method 3: Short declaration
names := [2]string{"Alice", "Bob"}

// Method 4: Let compiler count (...)
numbers := [...]int{1, 2, 3, 4, 5}  // Size is 5
```

**Why Arrays Are Rarely Used:**
```go
package main

import "fmt"

func main() {
    // ❌ Problem 1: Fixed size
    prices := [3]float64{19.99, 29.99, 39.99}
    // Can't add a 4th price!
    // prices[3] = 49.99  // Runtime panic!
    
    // ❌ Problem 2: Different sizes = different types
    var prices3 [3]float64
    var prices5 [5]float64
    // prices3 = prices5  // Compile error! Different types!
    
    // ❌ Problem 3: Can't pass to functions easily
    // processPrices(prices3)  // Works
    // processPrices(prices5)  // Doesn't work! Different type!
    
    fmt.Println(prices)
}
```

### 1.2 Array Operations 🔧

**Accessing Elements:**
```go
prices := [3]float64{19.99, 29.99, 39.99}

// Read
first := prices[0]   // 19.99
second := prices[1]  // 29.99

// Write
prices[0] = 24.99    // Change first element

// Length
length := len(prices)  // 3

// Iterate
for i := 0; i < len(prices); i++ {
    fmt.Printf("Price[%d] = $%.2f\n", i, prices[i])
}

// Iterate with range
for index, price := range prices {
    fmt.Printf("Price[%d] = $%.2f\n", index, price)
}
```

**Array Initialization Patterns:**
```go
// All zeros (zero value)
var nums [5]int  // [0, 0, 0, 0, 0]

// Partial initialization
nums := [5]int{1, 2}  // [1, 2, 0, 0, 0]

// Specific indices
nums := [5]int{0: 10, 2: 20, 4: 30}  // [10, 0, 20, 0, 30]

// Two-dimensional array
matrix := [2][3]int{
    {1, 2, 3},
    {4, 5, 6},
}
```

### 1.3 When to Use Arrays ✅

**Rare but Valid Use Cases:**

1. **Exact number of elements needed:**
```go
// RGB color (always 3 values)
var color [3]uint8 = [3]uint8{255, 128, 0}

// 3D coordinates (always x, y, z)
type Point3D [3]float64

// Days of week (always 7)
var weekdays [7]string = [7]string{
    "Monday", "Tuesday", "Wednesday", "Thursday",
    "Friday", "Saturday", "Sunday",
}
```

2. **Performance-critical code (avoid allocations):**
```go
// Fixed buffer for performance
var buffer [1024]byte
```

3. **Embedding in structs:**
```go
type Config struct {
    Name     string
    Servers  [3]string  // Always expect 3 servers
    Timeouts [5]int     // 5 different timeouts
}
```

**Most of the time, use slices instead!**

---

## 📖 Section 2: Slices - Dynamic Arrays

### 2.1 Understanding Slices 🍰

**What is a Slice?**
- **Dynamic** array (can grow/shrink)
- Most commonly used collection type
- Reference to an underlying array
- Has length (`len`) and capacity (`cap`)

**Slice Declaration:**
```go
// Method 1: Literal (most common)
names := []string{"Alice", "Bob", "Charlie"}

// Method 2: make() with length
numbers := make([]int, 5)  // [0, 0, 0, 0, 0]

// Method 3: make() with length and capacity
numbers := make([]int, 5, 10)  // len=5, cap=10

// Method 4: From array
array := [5]int{1, 2, 3, 4, 5}
slice := array[1:4]  // [2, 3, 4]

// Method 5: var (zero value = nil)
var items []string  // nil slice
```

### 2.2 Slice Internals 🔍

**How Slices Work:**
```
Slice Structure:
┌─────────┬─────────┬──────────┐
│ Pointer │ Length  │ Capacity │
│   *arr  │   len   │   cap    │
└────┬────┴─────────┴──────────┘
     │
     ▼
Underlying Array:
┌───┬───┬───┬───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │ 5 │ 0 │ 0 │ 0 │
└───┴───┴───┴───┴───┴───┴───┴───┘
```

**Length vs Capacity:**
```go
slice := make([]int, 3, 5)

fmt.Println(len(slice))  // 3 (current number of elements)
fmt.Println(cap(slice))  // 5 (capacity before reallocation)

// Visual:
// [0, 0, 0, _, _]
//  ←─ len ─→
//  ←──── cap ────→
```

### 2.3 Slice Operations 🔧

**Appending Elements:**
```go
// Start with empty slice
items := []string{}

// Append one element
items = append(items, "Apple")  // ["Apple"]

// Append multiple elements
items = append(items, "Banana", "Cherry")  // ["Apple", "Banana", "Cherry"]

// Append another slice
moreItems := []string{"Date", "Elderberry"}
items = append(items, moreItems...)  // Use ... to unpack
```

**E-Commerce Shopping Cart Example:**
```go
package main

import "fmt"

type Product struct {
    Name  string
    Price float64
}

func main() {
    // Shopping cart (empty at start)
    cart := []Product{}
    
    // Add items
    cart = append(cart, Product{"Laptop", 999.99})
    cart = append(cart, Product{"Mouse", 29.99})
    cart = append(cart, Product{"Keyboard", 79.99})
    
    // Calculate total
    var total float64
    for _, item := range cart {
        total += item.Price
    }
    
    fmt.Printf("Cart has %d items\n", len(cart))
    fmt.Printf("Total: $%.2f\n", total)
    
    // Print each item
    for i, item := range cart {
        fmt.Printf("%d. %s - $%.2f\n", i+1, item.Name, item.Price)
    }
}
```

**Slicing (Creating Sub-Slices):**
```go
numbers := []int{0, 1, 2, 3, 4, 5}

// Syntax: slice[start:end]  (end is exclusive)
subset1 := numbers[1:4]   // [1, 2, 3]
subset2 := numbers[:3]    // [0, 1, 2] (from start)
subset3 := numbers[3:]    // [3, 4, 5] (to end)
subset4 := numbers[:]     // [0, 1, 2, 3, 4, 5] (entire slice)

// With capacity
subset5 := numbers[1:3:5]  // [start:end:cap]
```

**Practical Example:**
```go
// Order processing: first 10 orders
allOrders := []int{1001, 1002, 1003, /* ... */ 1100}
firstTen := allOrders[:10]

// Pagination: show page 2 (items 10-19)
page := 2
pageSize := 10
start := (page - 1) * pageSize
end := start + pageSize
pageOrders := allOrders[start:end]
```

### 2.4 make() for Pre-allocation 🏗️

**Why Use make()?**
- Pre-allocate memory for better performance
- Avoid multiple allocations when appending

**Syntax:**
```go
// make([]Type, length, capacity)
slice := make([]int, length, capacity)

// If capacity omitted, it equals length
slice := make([]int, length)
```

**Examples:**
```go
// Create slice with 5 zeros, capacity 5
nums := make([]int, 5)  // [0, 0, 0, 0, 0]

// Create empty slice with capacity 100
inventory := make([]string, 0, 100)  // []
// Can append up to 100 items without reallocation!

// Add items
for i := 0; i < 50; i++ {
    inventory = append(inventory, fmt.Sprintf("Item%d", i))
}
// No reallocations happened!
```

**Performance Comparison:**
```go
// ❌ Bad: Multiple reallocations
func badAppend() []int {
    var nums []int  // cap = 0
    for i := 0; i < 10000; i++ {
        nums = append(nums, i)  // Reallocates many times!
    }
    return nums
}

// ✅ Good: Pre-allocate
func goodAppend() []int {
    nums := make([]int, 0, 10000)  // Pre-allocate capacity
    for i := 0; i < 10000; i++ {
        nums = append(nums, i)  // No reallocations!
    }
    return nums
}
```

### 2.5 Copying Slices 📝

**Why Copy?**
- Slices are **references** to underlying arrays
- Modifying one slice can affect another
- Use `copy()` to create independent slices

**The copy() Function:**
```go
// Syntax: copy(destination, source)
numCopied := copy(dst, src)

// Example
src := []int{1, 2, 3, 4, 5}
dst := make([]int, len(src))
copy(dst, src)

fmt.Println(dst)  // [1, 2, 3, 4, 5]

// Now independent
dst[0] = 999
fmt.Println(src)  // [1, 2, 3, 4, 5] (unchanged)
fmt.Println(dst)  // [999, 2, 3, 4, 5]
```

**Common Patterns:**
```go
// Pattern 1: Copy entire slice
src := []int{1, 2, 3, 4, 5}
dst := make([]int, len(src))
copy(dst, src)

// Pattern 2: Copy with append (alternative)
dst := append([]int{}, src...)

// Pattern 3: Partial copy
src := []int{1, 2, 3, 4, 5}
dst := make([]int, 3)
n := copy(dst, src)  // Copies first 3 elements
fmt.Println(n)       // 3 (number of elements copied)
fmt.Println(dst)     // [1, 2, 3]
```

**Real-World Example: Backup Cart:**
```go
type Product struct {
    Name  string
    Price float64
}

func main() {
    cart := []Product{
        {"Laptop", 999.99},
        {"Mouse", 29.99},
    }
    
    // Create backup before modification
    backup := make([]Product, len(cart))
    copy(backup, cart)
    
    // Modify cart
    cart[0].Price = 899.99
    
    // Original backup unchanged
    fmt.Printf("Cart price: %.2f\n", cart[0].Price)      // 899.99
    fmt.Printf("Backup price: %.2f\n", backup[0].Price)  // 999.99
}
```

### 2.6 Clearing Slices 🧹

**Two Ways to Clear:**

**Method 1: Set to nil (fully clear, release memory):**
```go
orders := []int{1, 2, 3, 4, 5}
orders = nil

fmt.Println(orders)       // []
fmt.Println(len(orders))  // 0
fmt.Println(cap(orders))  // 0
fmt.Println(orders == nil)  // true
```

**Method 2: Reslice to [:0] (keep capacity, clear length):**
```go
orders := make([]int, 0, 10)
orders = append(orders, 1, 2, 3, 4, 5)

fmt.Println(len(orders), cap(orders))  // 5, 10

// Clear but keep capacity
orders = orders[:0]

fmt.Println(len(orders), cap(orders))  // 0, 10
fmt.Println(orders == nil)  // false (not nil!)

// Can reuse without reallocation
orders = append(orders, 6, 7, 8)
fmt.Println(len(orders), cap(orders))  // 3, 10
```

**When to Use Each:**
```go
// ✅ Use nil: When done with slice permanently
func processOrders(orders []int) {
    // ... process ...
    orders = nil  // Release memory
}

// ✅ Use [:0]: When reusing slice repeatedly
func batchProcess() {
    buffer := make([]int, 0, 1000)
    
    for batch := range batches {
        buffer = buffer[:0]  // Clear but keep capacity
        
        // Reuse buffer
        for _, item := range batch {
            buffer = append(buffer, process(item))
        }
        
        send(buffer)
    }
}
```

### 2.7 Removing Elements from Slices ✂️

**Remove Element at Index:**
```go
// Method 1: Preserve order
func remove(slice []int, index int) []int {
    return append(slice[:index], slice[index+1:]...)
}

// Example
numbers := []int{1, 2, 3, 4, 5}
numbers = remove(numbers, 2)  // Remove index 2 (value 3)
fmt.Println(numbers)  // [1, 2, 4, 5]

// Method 2: Fast (doesn't preserve order)
func removeFast(slice []int, index int) []int {
    slice[index] = slice[len(slice)-1]  // Copy last to index
    return slice[:len(slice)-1]          // Remove last
}

numbers := []int{1, 2, 3, 4, 5}
numbers = removeFast(numbers, 2)  // Remove index 2
fmt.Println(numbers)  // [1, 2, 5, 4] (order changed but faster!)
```

**E-Commerce Example: Remove from Cart:**
```go
type CartItem struct {
    ProductID int
    Name      string
    Price     float64
}

func removeFromCart(cart []CartItem, productID int) []CartItem {
    for i, item := range cart {
        if item.ProductID == productID {
            // Found it, remove
            return append(cart[:i], cart[i+1:]...)
        }
    }
    return cart  // Not found
}

func main() {
    cart := []CartItem{
        {1001, "Laptop", 999.99},
        {1002, "Mouse", 29.99},
        {1003, "Keyboard", 79.99},
    }
    
    cart = removeFromCart(cart, 1002)  // Remove mouse
    
    for _, item := range cart {
        fmt.Printf("%s: $%.2f\n", item.Name, item.Price)
    }
}
```

### 2.8 Array/Slice Conversion 🔄

**Array to Slice:**
```go
// Method 1: Slice operator [:]
arr := [5]int{1, 2, 3, 4, 5}
slice := arr[:]  // Converts to slice

// Method 2: Partial slice
slice := arr[1:4]  // [2, 3, 4]

// ⚠️ Warning: Slice shares underlying array
arr[0] = 999
fmt.Println(slice)  // [999, 2, 3, 4, 5] (affected!)
```

**Slice to Array (Go 1.17+):**
```go
slice := []int{1, 2, 3}

// Convert to array
arr := [3]int(slice)  // Copies to array

// Slice and array are independent
slice[0] = 999
fmt.Println(arr)    // [1, 2, 3] (unchanged)
fmt.Println(slice)  // [999, 2, 3]
```

**Legacy: Slice to Array (Before Go 1.17):**
```go
slice := []int{1, 2, 3}
var arr [3]int
copy(arr[:], slice)  // Copy slice to array

fmt.Println(arr)  // [1, 2, 3]
```

---

## 📖 Section 3: Maps - Key-Value Storage

### 5.1 Quick Recap: Strings, Runes, and Bytes 🧵

(Detailed coverage in Chapter 2, Section 1.5 and 1.6)

**Key Points:**
- **String**: Immutable sequence of bytes (UTF-8 encoded)
- **Rune**: Unicode code point (int32 alias)
- **Byte**: Raw byte (uint8 alias)

**Conversions:**
```go
text := "Hello 世界"

// String to []rune (characters)
runes := []rune(text)
fmt.Println(len(runes))  // 8 characters

// String to []byte (bytes)
bytes := []byte(text)
fmt.Println(len(bytes))  // 14 bytes

// Back to string
s1 := string(runes)
s2 := string(bytes)
```

**Practical Example: Validating Product Name:**
```go
func validateProductName(name string) bool {
    runes := []rune(name)
    
    // Check length (in characters, not bytes)
    if len(runes) < 3 || len(runes) > 50 {
        return false
    }
    
    // Check each character
    for _, r := range runes {
        if !unicode.IsLetter(r) && !unicode.IsSpace(r) {
            return false
        }
    }
    
    return true
}
```

---

## 🏋️ Section 6: Exercises and Challenges

### Exercise 1: Slice Basics 📝

**Simple Level:**

Create a slice of product names and perform operations:
```go
// Your tasks:
// 1. Create slice: ["Laptop", "Mouse", "Keyboard"]
// 2. Add "Monitor" to the slice
// 3. Print the length
// 4. Print each item with index
```

**Solution:**
```go
package main

import "fmt"

func main() {
    // 1. Create slice
    products := []string{"Laptop", "Mouse", "Keyboard"}
    
    // 2. Add "Monitor"
    products = append(products, "Monitor")
    
    // 3. Print length
    fmt.Printf("Total products: %d\n", len(products))
    
    // 4. Print each item
    for i, product := range products {
        fmt.Printf("%d. %s\n", i+1, product)
    }
}
```

### Exercise 2: Map Operations 🗺️

**Medium Level:**

Create an inventory system:
```go
// Requirements:
// 1. Create map[string]int for product stock
// 2. Add 3 products with quantities
// 3. Decrease stock when selling
// 4. Check if product exists before selling
// 5. Print inventory
```

**Solution:**
```go
package main

import "fmt"

func main() {
    // 1. Create inventory map
    inventory := map[string]int{
        "Laptop":   10,
        "Mouse":    50,
        "Keyboard": 25,
    }
    
    // 2. Function to sell product
    sellProduct := func(name string, qty int) bool {
        if stock, exists := inventory[name]; exists && stock >= qty {
            inventory[name] -= qty
            fmt.Printf("Sold %d %s(s)\n", qty, name)
            return true
        }
        fmt.Printf("Cannot sell %d %s(s) - insufficient stock\n", qty, name)
        return false
    }
    
    // 3. Sell products
    sellProduct("Laptop", 2)   // Success
    sellProduct("Mouse", 5)    // Success
    sellProduct("Monitor", 1)  // Fail (doesn't exist)
    sellProduct("Laptop", 20)  // Fail (insufficient)
    
    // 4. Print final inventory
    fmt.Println("\nFinal Inventory:")
    for product, stock := range inventory {
        fmt.Printf("  %s: %d units\n", product, stock)
    }
}
```

### Exercise 3: Struct Design 🏗️

**Medium Level:**

Design an Order struct for e-commerce:
```go
// Requirements:
// 1. Order has: ID, CustomerID, Items (slice of OrderItem), Total
// 2. OrderItem has: ProductID, Name, Price, Quantity
// 3. Method to calculate total
// 4. Method to add item
// 5. Method to print order summary
```

**Solution:**
```go
package main

import "fmt"

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
}

func (o *Order) AddItem(item OrderItem) {
    o.Items = append(o.Items, item)
}

func (o Order) CalculateTotal() float64 {
    var total float64
    for _, item := range o.Items {
        total += item.Price * float64(item.Quantity)
    }
    return total
}

func (o Order) PrintSummary() {
    fmt.Printf("Order #%d for Customer %s\n", o.ID, o.CustomerID)
    fmt.Println("Items:")
    for i, item := range o.Items {
        subtotal := item.Price * float64(item.Quantity)
        fmt.Printf("  %d. %s x%d @ $%.2f = $%.2f\n",
            i+1, item.Name, item.Quantity, item.Price, subtotal)
    }
    fmt.Printf("Total: $%.2f\n", o.CalculateTotal())
}

func main() {
    order := Order{
        ID:         1001,
        CustomerID: "cust-123",
    }
    
    order.AddItem(OrderItem{1001, "Laptop", 999.99, 1})
    order.AddItem(OrderItem{1002, "Mouse", 29.99, 2})
    order.AddItem(OrderItem{1003, "Keyboard", 79.99, 1})
    
    order.PrintSummary()
}
```

### Challenge 1: LeetCode - Group Anagrams 🔤

**Medium Level:**

Given an array of strings, group anagrams together.

**Example:**
```
Input: ["eat", "tea", "tan", "ate", "nat", "bat"]
Output: [["eat","tea","ate"],["tan","nat"],["bat"]]
```

**Solution:**
```go
package main

import (
    "fmt"
    "sort"
    "strings"
)

func groupAnagrams(strs []string) [][]string {
    groups := make(map[string][]string)
    
    for _, str := range strs {
        // Sort the string to use as key
        sorted := sortString(str)
        groups[sorted] = append(groups[sorted], str)
    }
    
    // Convert map to slice
    result := [][]string{}
    for _, group := range groups {
        result = append(result, group)
    }
    
    return result
}

func sortString(s string) string {
    runes := []rune(s)
    sort.Slice(runes, func(i, j int) bool {
        return runes[i] < runes[j]
    })
    return string(runes)
}

func main() {
    strs := []string{"eat", "tea", "tan", "ate", "nat", "bat"}
    result := groupAnagrams(strs)
    fmt.Println(result)
}
```

### Challenge 2: Product Search System 🔍

**Hard Level:**

Build a product search system with:
- Add products
- Search by name (case-insensitive)
- Filter by price range
- Sort results

**Solution:**
```go
package main

import (
    "fmt"
    "sort"
    "strings"
)

type Product struct {
    ID    int
    Name  string
    Price float64
    Stock int
}

type ProductStore struct {
    products map[int]Product
    nextID   int
}

func NewProductStore() *ProductStore {
    return &ProductStore{
        products: make(map[int]Product),
        nextID:   1001,
    }
}

func (ps *ProductStore) Add(name string, price float64, stock int) int {
    id := ps.nextID
    ps.products[id] = Product{id, name, price, stock}
    ps.nextID++
    return id
}

func (ps *ProductStore) SearchByName(query string) []Product {
    query = strings.ToLower(query)
    results := []Product{}
    
    for _, product := range ps.products {
        if strings.Contains(strings.ToLower(product.Name), query) {
            results = append(results, product)
        }
    }
    
    return results
}

func (ps *ProductStore) FilterByPriceRange(min, max float64) []Product {
    results := []Product{}
    
    for _, product := range ps.products {
        if product.Price >= min && product.Price <= max {
            results = append(results, product)
        }
    }
    
    return results
}

func SortByPrice(products []Product) {
    sort.Slice(products, func(i, j int) bool {
        return products[i].Price < products[j].Price
    })
}

func main() {
    store := NewProductStore()
    
    store.Add("Gaming Laptop", 1299.99, 5)
    store.Add("Office Laptop", 799.99, 10)
    store.Add("Wireless Mouse", 29.99, 50)
    store.Add("Mechanical Keyboard", 89.99, 20)
    store.Add("USB Mouse", 19.99, 100)
    
    // Search
    results := store.SearchByName("laptop")
    fmt.Println("Search 'laptop':")
    for _, p := range results {
        fmt.Printf("  %s - $%.2f\n", p.Name, p.Price)
    }
    
    // Filter
    results = store.FilterByPriceRange(20, 100)
    SortByPrice(results)
    fmt.Println("\nPrice range $20-$100:")
    for _, p := range results {
        fmt.Printf("  %s - $%.2f\n", p.Name, p.Price)
    }
}
```

---

## 🎁 Chapter 3 Summary

### ✅ What You Learned:

**1. Arrays:**
- Fixed-size collections
- Rarely used directly
- Size is part of type

**2. Slices:**
- Dynamic arrays (most used!)
- `len()` and `cap()`
- `append()`, `make()`, `copy()`
- Slicing operations
- Remove elements patterns

**3. Maps:**
- Key-value pairs
- Fast lookups
- "comma ok" idiom
- Iteration (unordered!)
- Maps as sets
- Common patterns

**4. Structs:**
- Custom data types
- Named fields
- Methods
- Embedding
- Struct tags
- Comparison rules

**5. Strings, Runes, Bytes:**
- String = bytes
- Rune = character
- UTF-8 encoding
- Conversions

### 🎯 Key Takeaways:

1. **Slices > Arrays** - Always prefer slices for collections
2. **Maps are fast** - O(1) lookups, perfect for caching/indexing
3. **Structs group data** - Create meaningful types
4. **Methods add behavior** - Make structs act like objects
5. **Unicode matters** - Use runes for character operations

### 📚 Next Chapter:

Ready for **Chapter 4: Control Structures**? We'll master:
- if, for, switch statements
- Loops and iteration
- Labels and break/continue
- Error handling patterns

**Let's continue! 🚀**

### 3.1 Understanding Maps 🗺️

**What is a Map?**
- Unordered collection of **key-value pairs**
- Like a dictionary or hash table
- Fast lookups by key (O(1) average)
- Keys must be **comparable** types

**Map Declaration:**
```go
// Method 1: Literal (most common)
ages := map[string]int{
    "Alice": 25,
    "Bob":   30,
    "Carol": 28,
}

// Method 2: make()
ages := make(map[string]int)

// Method 3: make() with capacity hint
ages := make(map[string]int, 100)

// Method 4: var (zero value = nil)
var ages map[string]int  // nil map (can't write to it!)
```

**Map Key Types (Must Be Comparable):**
```go
// ✅ Valid key types
map[string]int          // String keys
map[int]string          // Integer keys
map[float64]bool        // Float keys (careful with precision!)
map[bool]string         // Boolean keys
map[[3]int]string       // Array keys
map[struct{x, y int}]bool  // Struct keys (if all fields comparable)

// ❌ Invalid key types
map[[]int]string        // ❌ Slice (not comparable)
map[map[string]int]bool // ❌ Map (not comparable)
map[func()]string       // ❌ Function (not comparable)
```

### 3.2 Map Operations 🔧

**Reading and Writing:**
```go
// Create map
stock := map[string]int{
    "Apple":  100,
    "Banana": 50,
}

// Read value
appleStock := stock["Apple"]  // 100

// Write/Update value
stock["Orange"] = 30     // Add new key
stock["Apple"] = 120     // Update existing key

// Read non-existent key (returns zero value)
missing := stock["Grape"]  // 0 (zero value for int)
```

**The "comma ok" Idiom (Check if Key Exists):**
```go
stock := map[string]int{
    "Apple":  100,
    "Banana": 0,    // Explicitly set to 0
}

// Without "comma ok" - ambiguous
qty := stock["Banana"]  // 0 (exists with value 0)
missing := stock["Grape"]  // 0 (doesn't exist)
// Can't tell the difference!

// ✅ With "comma ok" - clear
qty, ok := stock["Banana"]
if ok {
    fmt.Println("Banana stock:", qty)  // Found: 0
}

qty, ok = stock["Grape"]
if !ok {
    fmt.Println("Grape not found")  // Not found
}

// ✅ Short form
if qty, ok := stock["Apple"]; ok {
    fmt.Printf("Apple stock: %d\n", qty)
}
```

**E-Commerce Inventory Example:**
```go
package main

import "fmt"

type Product struct {
    Name  string
    Price float64
    Stock int
}

func main() {
    // Product catalog
    inventory := map[int]Product{
        1001: {"Laptop", 999.99, 10},
        1002: {"Mouse", 29.99, 50},
        1003: {"Keyboard", 79.99, 25},
    }
    
    // Add new product
    inventory[1004] = Product{"Monitor", 299.99, 15}
    
    // Update stock
    if product, exists := inventory[1002]; exists {
        product.Stock -= 1  // Sold one mouse
        inventory[1002] = product
    }
    
    // Check product availability
    productID := 1001
    if product, exists := inventory[productID]; exists {
        if product.Stock > 0 {
            fmt.Printf("%s is in stock (%d units)\n", product.Name, product.Stock)
        } else {
            fmt.Printf("%s is out of stock\n", product.Name)
        }
    } else {
        fmt.Println("Product not found")
    }
}
```

### 3.3 Deleting from Maps ❌

**Delete a Key:**
```go
stock := map[string]int{
    "Apple":  100,
    "Banana": 50,
    "Orange": 30,
}

// Delete a key
delete(stock, "Banana")

// Delete non-existent key (safe, no error)
delete(stock, "Grape")  // No effect

fmt.Println(stock)  // map[Apple:100 Orange:30]
```

**Clear Entire Map:**
```go
// Method 1: Set to nil (release memory)
stock = nil
fmt.Println(stock == nil)  // true

// Method 2: Create new empty map (keeps map allocated)
stock = map[string]int{}
fmt.Println(stock == nil)  // false

// Method 3: Loop and delete (if you need to keep the map)
for key := range stock {
    delete(stock, key)
}

// Method 4 (Go 1.21+): clear() built-in
clear(stock)  // New in Go 1.21
```

### 3.4 Iterating Over Maps 🔄

**Basic Iteration:**
```go
prices := map[string]float64{
    "Laptop":   999.99,
    "Mouse":    29.99,
    "Keyboard": 79.99,
}

// Iterate over key-value pairs
for product, price := range prices {
    fmt.Printf("%s: $%.2f\n", product, price)
}

// Iterate over keys only
for product := range prices {
    fmt.Println(product)
}

// Iterate over values only (use _ for key)
for _, price := range prices {
    fmt.Printf("$%.2f\n", price)
}
```

**⚠️ Important: Map Iteration Order is Random!**
```go
// Order changes every time you run!
ages := map[string]int{"Alice": 25, "Bob": 30, "Carol": 28}

for name, age := range ages {
    fmt.Printf("%s: %d\n", name, age)
}
// Output could be:
// Bob: 30
// Carol: 28
// Alice: 25
// OR
// Alice: 25
// Carol: 28
// Bob: 30
// OR any other order!
```

**Sorted Iteration:**
```go
import "sort"

ages := map[string]int{
    "Alice": 25,
    "Bob":   30,
    "Carol": 28,
}

// Get keys and sort them
keys := make([]string, 0, len(ages))
for key := range ages {
    keys = append(keys, key)
}
sort.Strings(keys)

// Iterate in sorted order
for _, key := range keys {
    fmt.Printf("%s: %d\n", key, ages[key])
}
// Output (always same order):
// Alice: 25
// Bob: 30
// Carol: 28
```

### 3.5 Maps as Sets 🎯

**What is a Set?**
- Collection of **unique** elements
- No duplicates
- Fast membership testing

**Implementing Sets with Maps:**
```go
// Use map[Type]struct{} for memory efficiency
// struct{} takes 0 bytes!

// Create set
seen := map[string]struct{}{}

// Add elements
seen["order-123"] = struct{}{}
seen["order-456"] = struct{}{}
seen["order-123"] = struct{}{}  // Duplicate, no effect

// Check membership
if _, exists := seen["order-123"]; exists {
    fmt.Println("Already processed")
}

// Remove element
delete(seen, "order-123")

// Count elements
count := len(seen)

// Iterate
for orderID := range seen {
    fmt.Println(orderID)
}
```

**Practical Example: Unique Visitors:**
```go
package main

import "fmt"

func countUniqueVisitors(visitors []string) int {
    unique := map[string]struct{}{}
    
    for _, visitor := range visitors {
        unique[visitor] = struct{}{}
    }
    
    return len(unique)
}

func main() {
    visitors := []string{
        "alice@example.com",
        "bob@example.com",
        "alice@example.com",  // Duplicate
        "carol@example.com",
        "bob@example.com",    // Duplicate
    }
    
    count := countUniqueVisitors(visitors)
    fmt.Printf("Unique visitors: %d\n", count)  // 3
}
```

### 3.6 Common Map Patterns 💡

**Pattern 1: Counting Occurrences**
```go
func countWords(words []string) map[string]int {
    counts := make(map[string]int)
    
    for _, word := range words {
        counts[word]++  // Auto-initializes to 0 if not exists
    }
    
    return counts
}

// Example
words := []string{"apple", "banana", "apple", "cherry", "banana", "apple"}
counts := countWords(words)
// map[apple:3 banana:2 cherry:1]
```

**Pattern 2: Grouping**
```go
type Order struct {
    ID         int
    CustomerID string
    Amount     float64
}

func groupOrdersByCustomer(orders []Order) map[string][]Order {
    grouped := make(map[string][]Order)
    
    for _, order := range orders {
        grouped[order.CustomerID] = append(grouped[order.CustomerID], order)
    }
    
    return grouped
}

// Example
orders := []Order{
    {1001, "customer-1", 99.99},
    {1002, "customer-2", 149.99},
    {1003, "customer-1", 49.99},
}

byCustomer := groupOrdersByCustomer(orders)
// map[customer-1:[{1001 customer-1 99.99} {1003 customer-1 49.99}] 
//     customer-2:[{1002 customer-2 149.99}]]
```

**Pattern 3: Index/Lookup Table**
```go
type Product struct {
    ID    int
    Name  string
    Price float64
}

// Build index for fast lookups
products := []Product{
    {1001, "Laptop", 999.99},
    {1002, "Mouse", 29.99},
}

// Create lookup map
productIndex := make(map[int]Product)
for _, p := range products {
    productIndex[p.ID] = p
}

// Fast lookup by ID
if product, exists := productIndex[1001]; exists {
    fmt.Println(product.Name)  // "Laptop"
}
```

### 3.7 Map Limitations ⚠️

**Cannot Compare Maps:**
```go
map1 := map[string]int{"a": 1, "b": 2}
map2 := map[string]int{"a": 1, "b": 2}

// ❌ Compile error
// if map1 == map2 { }

// ✅ Only nil check allowed
if map1 == nil {
    fmt.Println("map is nil")
}

// ✅ Manual comparison
func mapsEqual(m1, m2 map[string]int) bool {
    if len(m1) != len(m2) {
        return false
    }
    for k, v1 := range m1 {
        if v2, ok := m2[k]; !ok || v1 != v2 {
            return false
        }
    }
    return true
}
```

**Maps Are Not Thread-Safe:**
```go
// ❌ Dangerous: Concurrent access without synchronization
m := make(map[string]int)

go func() {
    m["key"] = 1  // Write
}()

go func() {
    _ = m["key"]  // Read
}()
// Can cause runtime panic!

// ✅ Solution 1: Use sync.Mutex
var mu sync.Mutex
mu.Lock()
m["key"] = 1
mu.Unlock()

// ✅ Solution 2: Use sync.Map (built-in concurrent map)
import "sync"
var m sync.Map
m.Store("key", 1)
val, ok := m.Load("key")
```

---

## 📖 Section 4: Structs - Custom Types

### 4.1 Understanding Structs 🏗️

**What is a Struct?**
- Collection of **named fields**
- Create custom data types
- Group related data together
- Like a class (without methods initially)

**Basic Struct Declaration:**
```go
// Define struct type
type Product struct {
    ID    int
    Name  string
    Price float64
    Stock int
}

// Create instances
var laptop Product

laptop2 := Product{
    ID:    1001,
    Name:  "Laptop",
    Price: 999.99,
    Stock: 10,
}

// Short form (positional)
laptop3 := Product{1001, "Laptop", 999.99, 10}
```

### 4.2 Working with Structs 🔧

**Creating Struct Instances:**
```go
type Order struct {
    ID         int
    CustomerID string
    Total      float64
    IsPaid     bool
}

// Method 1: Zero values
var order1 Order
// Order{ID:0, CustomerID:"", Total:0, IsPaid:false}

// Method 2: Named fields (recommended)
order2 := Order{
    ID:         1001,
    CustomerID: "cust-123",
    Total:      299.99,
    IsPaid:     true,
}

// Method 3: Partial initialization
order3 := Order{
    ID:    1002,
    Total: 149.99,
    // Other fields get zero values
}

// Method 4: Positional (not recommended)
order4 := Order{1003, "cust-456", 199.99, false}
```

**Accessing Fields:**
```go
product := Product{
    ID:    1001,
    Name:  "Laptop",
    Price: 999.99,
}

// Read fields
name := product.Name
price := product.Price

// Write fields
product.Stock = 5
product.Price = 899.99

// Print struct
fmt.Printf("%+v\n", product)
// Output: {ID:1001 Name:Laptop Price:899.99 Stock:5}
```

### 4.3 Anonymous Structs 🎭

**When to Use Anonymous Structs:**
- One-time use (no reuse needed)
- JSON marshaling/unmarshaling
- Temporary data grouping

**Examples:**
```go
// Method 1: Declare and initialize
config := struct {
    Host string
    Port int
}{
    Host: "localhost",
    Port: 8080,
}

// Method 2: Use in function
func printPoint(p struct{ X, Y int }) {
    fmt.Printf("Point: (%d, %d)\n", p.X, p.Y)
}

printPoint(struct{ X, Y int }{10, 20})

// Method 3: JSON response
response := struct {
    Success bool   `json:"success"`
    Message string `json:"message"`
    Data    []int  `json:"data"`
}{
    Success: true,
    Message: "OK",
    Data:    []int{1, 2, 3},
}

json.Marshal(response)
```

**E-Commerce API Response:**
```go
package main

import (
    "encoding/json"
    "fmt"
)

func getOrderResponse(orderID int) []byte {
    response := struct {
        OrderID   int     `json:"order_id"`
        Status    string  `json:"status"`
        Total     float64 `json:"total"`
        Items     int     `json:"items"`
    }{
        OrderID: orderID,
        Status:  "shipped",
        Total:   299.99,
        Items:   3,
    }
    
    data, _ := json.Marshal(response)
    return data
}

func main() {
    jsonData := getOrderResponse(1001)
    fmt.Println(string(jsonData))
    // {"order_id":1001,"status":"shipped","total":299.99,"items":3}
}
```

### 4.4 Nested Structs 🪆

**Embedding Structs:**
```go
type Address struct {
    Street  string
    City    string
    ZipCode string
    Country string
}

type Customer struct {
    ID      int
    Name    string
    Email   string
    Address Address  // Nested struct
}

// Create customer
customer := Customer{
    ID:    1001,
    Name:  "Alice",
    Email: "alice@example.com",
    Address: Address{
        Street:  "123 Main St",
        City:    "New York",
        ZipCode: "10001",
        Country: "USA",
    },
}

// Access nested fields
fmt.Println(customer.Address.City)  // "New York"
customer.Address.ZipCode = "10002"  // Update nested field
```

**Anonymous Fields (Embedding):**
```go
type Person struct {
    Name string
    Age  int
}

type Employee struct {
    Person      // Anonymous field (embedding)
    EmployeeID  int
    Department  string
}

// Create employee
emp := Employee{
    Person: Person{
        Name: "Bob",
        Age:  30,
    },
    EmployeeID: 5001,
    Department: "IT",
}

// Can access embedded fields directly!
fmt.Println(emp.Name)  // "Bob" (no need for emp.Person.Name)
fmt.Println(emp.Age)   // 30
fmt.Println(emp.EmployeeID)  // 5001
```

### 4.5 Struct Tags 🏷️

**What are Struct Tags?**
- Metadata attached to struct fields
- Used by libraries (JSON, XML, database ORMs)
- Format: `key:"value"`

**Common Uses:**
```go
type Product struct {
    ID        int     `json:"id" db:"product_id"`
    Name      string  `json:"name" db:"product_name"`
    Price     float64 `json:"price" db:"price"`
    CreatedAt string  `json:"created_at,omitempty" db:"created_at"`
    Password  string  `json:"-"`  // Never export to JSON
}

// JSON marshaling
product := Product{
    ID:    1001,
    Name:  "Laptop",
    Price: 999.99,
}

jsonData, _ := json.Marshal(product)
fmt.Println(string(jsonData))
// {"id":1001,"name":"Laptop","price":999.99}
```

**Common JSON Tags:**
```go
type User struct {
    ID        int    `json:"id"`                    // Rename field
    Name      string `json:"name,omitempty"`        // Omit if empty
    Password  string `json:"-"`                     // Never export
    Email     string `json:"email_address"`         // Custom name
    IsActive  bool   `json:"is_active,string"`      // Convert to string
}
```

### 4.6 Comparing Structs ⚖️

**Structs are Comparable IF:**
- All fields are comparable types
- No slices, maps, or functions

**Examples:**
```go
// ✅ Comparable struct
type Point struct {
    X int
    Y int
}

p1 := Point{10, 20}
p2 := Point{10, 20}
p3 := Point{15, 25}

fmt.Println(p1 == p2)  // true
fmt.Println(p1 == p3)  // false

// ❌ Non-comparable struct
type Order struct {
    ID    int
    Items []string  // Slice makes it non-comparable
}

o1 := Order{1001, []string{"A", "B"}}
o2 := Order{1001, []string{"A", "B"}}
// fmt.Println(o1 == o2)  // Compile error!

// ✅ Manual comparison
func compareOrders(o1, o2 Order) bool {
    if o1.ID != o2.ID {
        return false
    }
    if len(o1.Items) != len(o2.Items) {
        return false
    }
    for i := range o1.Items {
        if o1.Items[i] != o2.Items[i] {
            return false
        }
    }
    return true
}
```

### 4.7 Struct Methods 🎯

**Adding Behavior to Structs:**
```go
type Rectangle struct {
    Width  float64
    Height float64
}

// Method with value receiver
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Method with pointer receiver (can modify)
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

// Usage
rect := Rectangle{Width: 10, Height: 5}

area := rect.Area()  // 50.0

rect.Scale(2)  // Doubles the size
fmt.Println(rect.Width, rect.Height)  // 20, 10
```

**E-Commerce Example:**
```go
type ShoppingCart struct {
    Items []CartItem
}

type CartItem struct {
    ProductID int
    Name      string
    Price     float64
    Quantity  int
}

// Method: Calculate total
func (c ShoppingCart) Total() float64 {
    var total float64
    for _, item := range c.Items {
        total += item.Price * float64(item.Quantity)
    }
    return total
}

// Method: Add item
func (c *ShoppingCart) AddItem(item CartItem) {
    c.Items = append(c.Items, item)
}

// Method: Remove item by ID
func (c *ShoppingCart) RemoveItem(productID int) {
    for i, item := range c.Items {
        if item.ProductID == productID {
            c.Items = append(c.Items[:i], c.Items[i+1:]...)
            return
        }
    }
}

// Usage
func main() {
    cart := ShoppingCart{}
    
    cart.AddItem(CartItem{1001, "Laptop", 999.99, 1})
    cart.AddItem(CartItem{1002, "Mouse", 29.99, 2})
    
    fmt.Printf("Total: $%.2f\n", cart.Total())  // $1059.97
    
    cart.RemoveItem(1002)
    fmt.Printf("Total: $%.2f\n", cart.Total())  // $999.99
}
```

---

## 📖 Section 5: Strings, Runes, and Bytes (Recap)

### Exercises 🏋️
1. Create a slice of order IDs and append new ones dynamically.
2. Write a `map[string]int` to manage product stock levels. Decrease stock when an order is placed.
3. Define a struct `Order` with fields: `ID`, `UserID`, `Items []string`. Instantiate and print it.

**4. LeetCode Challenge: Contains Duplicate (Easy) 🔍**

Given an integer slice `nums`, return `true` if any value appears at least twice in the slice, and return `false` if every element is distinct.

**Example 1:**
```go
Input: nums = []int{1, 2, 3, 1}
Output: true
Explanation: The number 1 appears twice.
```

**Example 2:**
```go
Input: nums = []int{1, 2, 3, 4}
Output: false
Explanation: All elements are distinct.
```

**Example 3:**
```go
Input: nums = []int{1, 1, 1, 3, 3, 4, 3, 2, 4, 2}
Output: true
```

**Hint:** Use a `map[int]bool` or `map[int]struct{}` to track which numbers you've seen. Iterate through the slice and check if each number exists in the map.

**Solution Template:**
```go
func containsDuplicate(nums []int) bool {
    // Your code here
    // Hint: Create a map to track seen numbers
    // Loop through nums and check if number exists in map
    // If exists, return true
    // If not, add to map
    // If loop completes, return false
}
```

**Test Your Solution:**
```go
func main() {
    testCases := [][]int{
        {1, 2, 3, 1},
        {1, 2, 3, 4},
        {1, 1, 1, 3, 3, 4, 3, 2, 4, 2},
        {},
        {1},
    }
    
    for _, nums := range testCases {
        result := containsDuplicate(nums)
        fmt.Printf("nums = %v, result = %v\n", nums, result)
    }
}
```

**Expected Output:**
```
nums = [1 2 3 1], result = true
nums = [1 2 3 4], result = false
nums = [1 1 1 3 3 4 3 2 4 2], result = true
nums = [], result = false
nums = [1], result = false
```

**5. LeetCode Challenge: Two Sum - E-Commerce Edition 🛒**

You're building a feature for an e-commerce site where customers want to find two products whose prices add up to exactly their budget. Given a slice of product prices `prices` and a target budget `target`, return the indices of the two products whose prices sum to `target`. You may assume that each input has exactly one solution, and you may not use the same product twice.

**Example 1:**
```go
Input: prices = []int{2, 7, 11, 15}, target = 9
Output: []int{0, 1}
Explanation: Because prices[0] + prices[1] == 9, we return [0, 1].
```

**Example 2:**
```go
Input: prices = []int{3, 2, 4}, target = 6
Output: []int{1, 2}
Explanation: Because prices[1] + prices[2] == 6, we return [1, 2].
```

**Example 3:**
```go
Input: prices = []int{3, 3}, target = 6
Output: []int{0, 1}
```

**E-Commerce Context:**
```go
// Real-world scenario: Customer has $50 budget
// Product prices: [15, 25, 30, 35, 20]
// Find two products that cost exactly $50 together
prices := []int{15, 25, 30, 35, 20}
target := 50
result := twoSum(prices, target)
// Output: [1, 4] (products at index 1 and 4: $25 + $20 = $45... wait, let's use better example)

// Better example:
prices := []int{15, 25, 30, 35, 20}
target := 45
result := twoSum(prices, target)
// Output: [1, 4] (products at index 1 and 4: $25 + $20 = $45)
```

**Hint:** 
- Use a `map[int]int` where the key is the price and the value is the index.
- As you iterate through the slice, for each price, calculate `complement = target - currentPrice`.
- Check if the complement exists in the map.
- If it exists, return `[map[complement], currentIndex]`.
- If not, add the current price and its index to the map.

**Algorithm Approach:**
```
1. Create an empty map: priceMap := make(map[int]int)
2. Loop through prices with index i:
   - Calculate complement = target - prices[i]
   - Check if complement exists in priceMap
   - If yes: return [priceMap[complement], i]
   - If no: store priceMap[prices[i]] = i
3. Return empty slice if no solution (shouldn't happen per problem statement)
```

**Solution Template:**
```go
func twoSum(prices []int, target int) []int {
    // Your code here
    // Hint: Create a map[int]int to store price -> index
    // Loop through prices with index
    // For each price, calculate complement = target - price
    // Check if complement exists in map
    // If exists, return [map[complement], currentIndex]
    // If not, add current price and index to map
}
```

**Test Your Solution:**
```go
func main() {
    testCases := []struct {
        prices []int
        target int
        expected []int
    }{
        {[]int{2, 7, 11, 15}, 9, []int{0, 1}},
        {[]int{3, 2, 4}, 6, []int{1, 2}},
        {[]int{3, 3}, 6, []int{0, 1}},
        {[]int{15, 25, 30, 35, 20}, 45, []int{1, 4}},
    }
    
    for i, tc := range testCases {
        result := twoSum(tc.prices, tc.target)
        fmt.Printf("Test %d: prices = %v, target = %d\n", i+1, tc.prices, tc.target)
        fmt.Printf("  Expected: %v, Got: %v\n", tc.expected, result)
        
        // Verify the result
        if len(result) == 2 {
            sum := tc.prices[result[0]] + tc.prices[result[1]]
            fmt.Printf("  Verification: prices[%d] + prices[%d] = %d + %d = %d (target: %d)\n",
                result[0], result[1], tc.prices[result[0]], tc.prices[result[1]], sum, tc.target)
        }
        fmt.Println()
    }
}
```

**Expected Output:**
```
Test 1: prices = [2 7 11 15], target = 9
  Expected: [0 1], Got: [0 1]
  Verification: prices[0] + prices[1] = 2 + 7 = 9 (target: 9)

Test 2: prices = [3 2 4], target = 6
  Expected: [1 2], Got: [1 2]
  Verification: prices[1] + prices[2] = 2 + 4 = 6 (target: 6)

Test 3: prices = [3 3], target = 6
  Expected: [0 1], Got: [0 1]
  Verification: prices[0] + prices[1] = 3 + 3 = 6 (target: 6)

Test 4: prices = [15 25 30 35 20], target = 45
  Expected: [1 4], Got: [1 4]
  Verification: prices[1] + prices[4] = 25 + 20 = 45 (target: 45)
```

**Time Complexity:** O(n) - Single pass through the slice  
**Space Complexity:** O(n) - Map storage for at most n elements

### Wrapping Up 🎁
You now understand:
- Arrays (used rarely)
- Slices (flexible and common)
- Maps (key-value pairs)
- Structs (user-defined records)
- Bytes and runes for string manipulation

---

## 📝 Exams: Challenge Yourself! 💪

### Simple
1. What is the difference between an array and a slice?
2. How do you check if a key exists in a map?
3. What happens when you call `delete(myMap, "key")`?

### Medium
1. Write a function `AddProduct` that adds a product to a stock map.
2. What's the output of:
   ```go
   nums := []int{1, 2, 3}
   copy(nums[1:], nums[:2])
   fmt.Println(nums)
   ```
3. Why are slices preferred over arrays in Go?

### Hard
1. Create a struct `Order` with:
   - `OrderID` int
   - `Items` []string
   - `Total` float64
   Write a function `PrintSummary(order Order)` that prints: "Order #101 with 3 items totaling $45.99"
2. Implement a map-based set of `orderIDs` (strings) with `AddID`, `HasID`, and `RemoveID` functions.
3. Convert a UTF-8 encoded string (e.g., "سلام") to a rune slice, count the number of letters, and print each letter with its index.

---

## 🔄 Chapter 4: Blocks, Shadows, and Control Structures

![Control Flow](https://images.unsplash.com/photo-1503676382389-4809596d5290?auto=format&fit=crop&w=600&q=80)

### 📚 Table of Contents for This Chapter
1. **Introduction: Control Flow**
2. **Section 1: Blocks and Scope**
3. **Section 2: if Statements**
4. **Section 3: for Loops**
5. **Section 4: switch Statements**
6. **Section 5: Advanced Control Flow**
7. **Exercises and Challenges**

---

## 🎯 Introduction: Control Flow

### Understanding Control Flow

**What is Control Flow?**
- Order in which code executes
- Decisions (if/switch)
- Repetition (for loops)
- Jumps (break/continue/goto)

```mermaid
flowchart TD
    Start((Start)) --> Input[Get Order]
    Input --> CheckPaid{Is Paid?}
    CheckPaid -->|Yes| CheckStock{In Stock?}
    CheckPaid -->|No| Error1[Payment Required]
    CheckStock -->|Yes| Ship[Ship Order]
    CheckStock -->|No| Error2[Out of Stock]
    Ship --> End((End))
    Error1 --> End
    Error2 --> End
```

**Control Structures in Go:**

| Structure | Purpose | Example |
|-----------|---------|---------|
| `if/else` | Conditional execution | `if isPaid { ship() }` |
| `for` | Loops and iteration | `for i := 0; i < 10; i++` |
| `switch` | Multiple conditions | `switch status { case "paid": ... }` |
| `break` | Exit loop | `break` |
| `continue` | Skip iteration | `continue` |
| `goto` | Jump to label | `goto retry` (rarely used) |

---

## 📖 Section 1: Blocks and Scope

### 1.1 Understanding Blocks 🧱

**What is a Block?**
- Code enclosed in curly braces `{}`
- Creates a new scope
- Variables declared inside are local

**Types of Blocks:**
```go
package main

import "fmt"

// 1. Package block (entire file)

// 2. Function block
func example() {
    // 3. Explicit block
    {
        message := "Inside block"
        fmt.Println(message)
    }
    // fmt.Println(message)  // Error: message not defined
    
    // 4. if block
    if true {
        x := 10
        fmt.Println(x)
    }
    // fmt.Println(x)  // Error: x not defined
    
    // 5. for block
    for i := 0; i < 5; i++ {
        y := i * 2
        fmt.Println(y)
    }
    // fmt.Println(i)  // Error: i not defined
    // fmt.Println(y)  // Error: y not defined
}
```

### 1.2 Variable Scope 🔍

**Scope Levels:**
```go
package main

import "fmt"

// Package-level scope (visible everywhere in package)
var globalConfig = "production"

func main() {
    // Function-level scope
    localVar := "main function"
    
    // Block-level scope
    {
        blockVar := "inside block"
        fmt.Println(localVar)   // ✅ Can access outer scope
        fmt.Println(blockVar)   // ✅ Can access current scope
    }
    
    // fmt.Println(blockVar)  // ❌ Error: out of scope
    
    if true {
        ifVar := "inside if"
        fmt.Println(localVar)  // ✅ Can access outer scope
    }
    
    // fmt.Println(ifVar)  // ❌ Error: out of scope
}
```

**Practical Example: E-Commerce Order Processing:**
```go
func processOrder(orderID int) error {
    // Function scope
    order, err := fetchOrder(orderID)
    if err != nil {
        return err
    }
    
    // Validate payment (block scope)
    {
        paymentStatus := checkPayment(order)
        if paymentStatus != "paid" {
            return fmt.Errorf("payment not completed")
        }
        // paymentStatus only exists in this block
    }
    
    // Check inventory (block scope)
    {
        inStock := checkInventory(order.Items)
        if !inStock {
            return fmt.Errorf("items out of stock")
        }
        // inStock only exists in this block
    }
    
    // Ship order
    return shipOrder(order)
}
```

### 1.3 Shadowing Variables 🕶️

**What is Shadowing?**
- Declaring a variable with the same name in a narrower scope
- Inner variable "shadows" (hides) outer variable
- Can lead to bugs if not careful!

**Examples:**
```go
package main

import "fmt"

func main() {
    x := 5
    fmt.Println("Outer x:", x)  // 5
    
    {
        x := 10  // Shadows outer x
        fmt.Println("Inner x:", x)  // 10
    }
    
    fmt.Println("Outer x again:", x)  // 5 (unchanged!)
}
```

**Common Shadowing Bug:**
```go
// ❌ Bug: Shadowing error variable
func loadConfig(filename string) error {
    config, err := readFile(filename)
    if err != nil {
        return err
    }
    
    // Oops! Shadows err with :=
    if config.NeedsValidation {
        config, err := validateConfig(config)  // Creates new err!
        if err != nil {
            return err
        }
        // config here is the local one
    }
    
    // Original err might not be what you expect
    return err
}

// ✅ Fix: Use = instead of :=
func loadConfigFixed(filename string) error {
    config, err := readFile(filename)
    if err != nil {
        return err
    }
    
    if config.NeedsValidation {
        config, err = validateConfig(config)  // Reuses err!
        if err != nil {
            return err
        }
    }
    
    return err
}
```

**E-Commerce Example:**
```go
func calculateTotal(orderID int) (float64, error) {
    // Outer scope
    subtotal := 0.0
    taxRate := 0.09
    
    // Get order
    order, err := fetchOrder(orderID)
    if err != nil {
        return 0, err
    }
    
    // Calculate subtotal
    for _, item := range order.Items {
        subtotal += item.Price * float64(item.Quantity)
    }
    
    // Apply discount (shadowing subtotal - bug!)
    if order.HasDiscount {
        discount := 0.10
        subtotal := subtotal * (1 - discount)  // ❌ Shadows outer subtotal!
        fmt.Printf("Discounted: $%.2f\n", subtotal)
    }
    
    // Original subtotal used here (discount not applied!)
    tax := subtotal * taxRate
    total := subtotal + tax
    
    return total, nil
}

// ✅ Fixed version
func calculateTotalFixed(orderID int) (float64, error) {
    subtotal := 0.0
    taxRate := 0.09
    
    order, err := fetchOrder(orderID)
    if err != nil {
        return 0, err
    }
    
    for _, item := range order.Items {
        subtotal += item.Price * float64(item.Quantity)
    }
    
    // Apply discount (no shadowing)
    if order.HasDiscount {
        discount := 0.10
        subtotal = subtotal * (1 - discount)  // ✅ Modifies outer subtotal
        fmt.Printf("Discounted: $%.2f\n", subtotal)
    }
    
    tax := subtotal * taxRate
    total := subtotal + tax
    
    return total, nil
}
```

**Best Practices:**
```go
// ⚠️ Avoid shadowing when possible
func example1() {
    x := 5
    {
        x := 10  // Shadowing (confusing)
        fmt.Println(x)
    }
}

// ✅ Use different names
func example2() {
    outerX := 5
    {
        innerX := 10  // Clear and explicit
        fmt.Println(innerX)
    }
}

// ⚠️ Watch out for := in if statements
func example3(ok bool) error {
    err := errors.New("default error")
    
    if ok {
        value, err := someFunction()  // Shadows err!
        fmt.Println(value)
    }
    
    return err  // Might not be the err you expect
}

// ✅ Declare variables before if
func example4(ok bool) error {
    var err error
    
    if ok {
        var value string
        value, err = someFunction()  // Reuses err
        fmt.Println(value)
    }
    
    return err  // Correct err
}
```

> **Key Takeaway:** Blocks create scope boundaries. Be careful with shadowing - it can hide bugs!

---

## 📖 Section 2: if Statements

### 2.1 Basic if Statements ❓

**Syntax:**
```go
// Simple if
if condition {
    // code
}

// if-else
if condition {
    // code when true
} else {
    // code when false
}

// if-else if-else
if condition1 {
    // code1
} else if condition2 {
    // code2
} else {
    // code3
}
```

**Key Rules:**
- No parentheses around condition (unlike C/Java)
- Curly braces `{}` are **required** (even for one line)
- Opening brace `{` must be on same line as `if`

**Examples:**
```go
// ✅ Correct
if x > 10 {
    fmt.Println("Large")
}

// ❌ Wrong: Missing braces
if x > 10
    fmt.Println("Large")

// ❌ Wrong: Brace on new line
if x > 10
{
    fmt.Println("Large")
}

// ❌ Wrong: Unnecessary parentheses (works but not idiomatic)
if (x > 10) {
    fmt.Println("Large")
}
```

**E-Commerce Example:**
```go
func processOrder(order Order) string {
    if order.Total < 0 {
        return "Invalid order amount"
    }
    
    if order.IsPaid {
        if order.IsShipped {
            return "Order delivered"
        } else {
            return "Order being prepared"
        }
    } else {
        return "Waiting for payment"
    }
}
```

### 2.2 if with Short Statement 🎯

**Syntax:**
```go
if statement; condition {
    // code
}
```

**Purpose:**
- Declare variable scoped to if block
- Keep code concise
- Prevent variable pollution

**Examples:**
```go
// Example 1: Inline assignment
if discount := calculateDiscount(order); discount > 0 {
    fmt.Printf("Discount: $%.2f\n", discount)
} else {
    fmt.Println("No discount available")
}
// discount is NOT accessible here

// Example 2: Error handling
if err := validateOrder(order); err != nil {
    return err
}
// err is NOT accessible here

// Example 3: Map lookup
if value, ok := inventory["laptop"]; ok {
    fmt.Printf("Stock: %d\n", value)
} else {
    fmt.Println("Product not found")
}
```

**Practical E-Commerce Examples:**
```go
// Check inventory before processing
func canFulfillOrder(productID int, quantity int) bool {
    if stock, exists := inventory[productID]; exists {
        return stock >= quantity
    }
    return false
}

// Validate and process payment
func processPayment(orderID int, amount float64) error {
    if order, err := fetchOrder(orderID); err != nil {
        return fmt.Errorf("order not found: %w", err)
    } else if order.IsPaid {
        return fmt.Errorf("order already paid")
    } else if amount != order.Total {
        return fmt.Errorf("amount mismatch: expected %.2f, got %.2f", 
            order.Total, amount)
    }
    
    // Process payment...
    return nil
}

// Apply discount based on rules
func calculateFinalPrice(basePrice float64, customerType string) float64 {
    if discount := getDiscount(customerType); discount > 0 {
        discountedPrice := basePrice * (1 - discount)
        if discountedPrice < basePrice * 0.5 {
            // Max 50% discount
            return basePrice * 0.5
        }
        return discountedPrice
    }
    return basePrice
}
```

---

## 📖 Section 3: for Loops

### for – The Only Loop in Go 🔁
Four forms:
1. **Complete for loop**:
   ```go
   for i := 0; i < 5; i++ {
       fmt.Println(i)
   }
   ```
2. **Condition-only**:
   ```go
   i := 0
   for i < 5 {
       fmt.Println(i)
       i++
   }
   ```
3. **Infinite loop**:
   ```go
   for {
       fmt.Println("Retrying...")
       time.Sleep(1 * time.Second)
   }
   ```
4. **for-range loop**:
   ```go
   items := []string{"Milk", "Bread", "Eggs"}
   for i, item := range items {
       fmt.Printf("%d: %s\n", i, item)
   }
   ```

### break and continue ⏭️
- `break`: Exits the loop.
- `continue`: Skips to the next iteration.

```go
for _, item := range items {
    if item == "Bread" {
        continue // skip Bread
    }
    fmt.Println(item)
}
```

### Labeling for Loops 🏷️
Useful in nested loops to control specific loops.

```go
outer:
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if i == j {
            continue outer
        }
        fmt.Println(i, j)
    }
}
```

### switch and Blank Switches 🔀
```go
status := "delivered"
switch status {
case "pending":
    fmt.Println("Wait for confirmation")
case "delivered":
    fmt.Println("Send thank you SMS")
default:
    fmt.Println("Unknown status")
}
```
**Blank switch**:
```go
switch {
case time.Now().Hour() < 12:
    fmt.Println("Good morning")
case time.Now().Hour() < 18:
    fmt.Println("Good afternoon")
default:
    fmt.Println("Good evening")
}
```

**Real-World E-Commerce Examples 🛒**

**Example 1: Order Status Processing (Regular Switch)**
```go
package main

import "fmt"

type Order struct {
    ID     int
    Status string
    Total  float64
}

func ProcessOrderStatus(order Order) {
    switch order.Status {
    case "pending":
        fmt.Printf("Order #%d is pending payment. Waiting for confirmation.\n", order.ID)
        // Send payment reminder email
        
    case "confirmed":
        fmt.Printf("Order #%d is confirmed. Preparing for shipment.\n", order.ID)
        // Update inventory, create shipping label
        
    case "processing":
        fmt.Printf("Order #%d is being processed. Items are being packed.\n", order.ID)
        // Notify warehouse
        
    case "shipped":
        fmt.Printf("Order #%d has been shipped. Tracking number sent.\n", order.ID)
        // Send tracking email to customer
        
    case "delivered":
        fmt.Printf("Order #%d has been delivered. Sending thank you SMS.\n", order.ID)
        // Request review, update customer loyalty points
        
    case "cancelled":
        fmt.Printf("Order #%d has been cancelled. Processing refund.\n", order.ID)
        // Refund payment, restore inventory
        
    case "refunded":
        fmt.Printf("Order #%d refund has been processed.\n", order.ID)
        // Close order, update accounting
        
    default:
        fmt.Printf("Order #%d has unknown status: %s\n", order.ID, order.Status)
        // Log error, notify admin
    }
}

func main() {
    orders := []Order{
        {ID: 1001, Status: "pending", Total: 99.99},
        {ID: 1002, Status: "delivered", Total: 149.50},
        {ID: 1003, Status: "shipped", Total: 79.99},
    }
    
    for _, order := range orders {
        ProcessOrderStatus(order)
    }
}
```

**Example 2: Shipping Cost Calculation (Blank Switch)**
```go
package main

import (
    "fmt"
    "time"
)

type ShippingRequest struct {
    Weight      float64 // in kg
    Distance    float64 // in km
    IsExpress   bool
    OrderValue  float64
    OrderDate   time.Time
}

func CalculateShippingCost(req ShippingRequest) float64 {
    var baseCost float64
    
    // Blank switch: multiple conditions determine shipping cost
    switch {
    // Free shipping for orders over $100
    case req.OrderValue >= 100.00:
        return 0.00
    
    // Express shipping: fixed rate + weight-based
    case req.IsExpress:
        baseCost = 15.00
        if req.Weight > 5.0 {
            baseCost += (req.Weight - 5.0) * 2.0
        }
        return baseCost
    
    // Heavy items (>10kg): special handling
    case req.Weight > 10.0:
        return 25.00 + (req.Weight-10.0)*1.5
    
    // Long distance (>500km): distance-based pricing
    case req.Distance > 500.0:
        return 12.00 + (req.Distance-500.0)*0.02
    
    // Standard shipping: weight-based
    case req.Weight <= 2.0:
        return 5.00
    case req.Weight <= 5.0:
        return 8.00
    case req.Weight <= 10.0:
        return 12.00
    
    // Default: standard rate
    default:
        return 10.00
    }
}

func main() {
    now := time.Now()
    
    requests := []ShippingRequest{
        {Weight: 1.5, Distance: 200, IsExpress: false, OrderValue: 150.00, OrderDate: now},
        {Weight: 8.0, Distance: 300, IsExpress: true, OrderValue: 75.00, OrderDate: now},
        {Weight: 12.0, Distance: 100, IsExpress: false, OrderValue: 50.00, OrderDate: now},
        {Weight: 3.0, Distance: 600, IsExpress: false, OrderValue: 45.00, OrderDate: now},
    }
    
    for i, req := range requests {
        cost := CalculateShippingCost(req)
        fmt.Printf("Request %d: Weight=%.1fkg, Distance=%.0fkm, Express=%v, OrderValue=$%.2f\n", 
            i+1, req.Weight, req.Distance, req.IsExpress, req.OrderValue)
        fmt.Printf("  Shipping Cost: $%.2f\n\n", cost)
    }
}
```

**Example 3: Payment Method Processing (Switch with Multiple Values)**
```go
package main

import "fmt"

type Payment struct {
    OrderID int
    Method  string
    Amount  float64
}

func ProcessPayment(payment Payment) string {
    switch payment.Method {
    case "credit_card", "debit_card":
        return fmt.Sprintf("Processing %s payment of $%.2f for order #%d", 
            payment.Method, payment.Amount, payment.OrderID)
    
    case "paypal", "stripe":
        return fmt.Sprintf("Redirecting to %s gateway for order #%d", 
            payment.Method, payment.OrderID)
    
    case "bank_transfer", "wire_transfer":
        return fmt.Sprintf("Bank transfer initiated. Instructions sent for order #%d", 
            payment.OrderID)
    
    case "cash_on_delivery", "cod":
        return fmt.Sprintf("Order #%d will be paid on delivery. Amount: $%.2f", 
            payment.OrderID, payment.Amount)
    
    case "gift_card", "store_credit":
        return fmt.Sprintf("Applying %s balance to order #%d", 
            payment.Method, payment.OrderID)
    
    default:
        return fmt.Sprintf("Unsupported payment method: %s for order #%d", 
            payment.Method, payment.OrderID)
    }
}

func main() {
    payments := []Payment{
        {OrderID: 1001, Method: "credit_card", Amount: 99.99},
        {OrderID: 1002, Method: "paypal", Amount: 149.50},
        {OrderID: 1003, Method: "cash_on_delivery", Amount: 79.99},
        {OrderID: 1004, Method: "gift_card", Amount: 50.00},
    }
    
    for _, payment := range payments {
        fmt.Println(ProcessPayment(payment))
    }
}
```

### Choosing Between if and switch 🤔

**When to Use `switch`:**
- ✅ Checking a **single variable** against **multiple specific values**
- ✅ Handling **discrete options** (like enums, status codes, payment methods)
- ✅ Code is **more readable** when you have 3+ options
- ✅ Each case handles a **distinct value**

**When to Use `if`:**
- ✅ **Complex conditions** with multiple variables
- ✅ **Range checks** (e.g., `if price > 100 && price < 200`)
- ✅ **Boolean logic** with AND/OR operators
- ✅ **Nested conditions** that don't fit switch patterns

**E-Commerce Examples:**

**✅ Use `switch` - Single Value Check:**
```go
// Good: Checking order status (single variable, multiple discrete values)
func HandleOrderStatus(status string) {
    switch status {
    case "pending":
        sendPaymentReminder()
    case "confirmed":
        prepareShipment()
    case "shipped":
        sendTrackingEmail()
    case "delivered":
        requestReview()
    case "cancelled":
        processRefund()
    default:
        logUnknownStatus(status)
    }
}

// Good: Payment method (discrete options)
func ProcessPayment(method string) {
    switch method {
    case "credit_card", "debit_card":
        processCardPayment()
    case "paypal":
        redirectToPayPal()
    case "bank_transfer":
        generateBankDetails()
    default:
        rejectPayment()
    }
}
```

**✅ Use `if` - Complex Conditions:**
```go
// Good: Multiple variables and range checks
func CalculateDiscount(customerType string, orderValue float64, isVIP bool, orderCount int) float64 {
    var discount float64
    
    // Complex conditions with multiple variables
    if customerType == "premium" && orderValue > 200.00 && isVIP {
        discount = 0.20 // 20% discount
    } else if orderValue >= 100.00 && orderValue < 200.00 {
        discount = 0.10 // 10% discount
    } else if orderCount > 10 && orderValue > 50.00 {
        discount = 0.15 // 15% loyalty discount
    } else if orderValue > 500.00 {
        discount = 0.25 // 25% bulk discount
    }
    
    return discount
}

// Good: Range checks and boolean logic
func IsEligibleForFreeShipping(orderValue float64, weight float64, isExpress bool) bool {
    if orderValue >= 100.00 && weight <= 5.0 && !isExpress {
        return true
    }
    if orderValue >= 200.00 && weight <= 10.0 {
        return true
    }
    return false
}

// Good: Complex nested conditions
func ValidateOrder(order Order) error {
    if order.Total <= 0 {
        return fmt.Errorf("order total must be positive")
    }
    
    if len(order.Items) == 0 {
        return fmt.Errorf("order must have at least one item")
    }
    
    if order.CustomerID == "" && order.GuestEmail == "" {
        return fmt.Errorf("order must have customer ID or guest email")
    }
    
    if order.ShippingAddress.Country == "" || order.ShippingAddress.City == "" {
        return fmt.Errorf("shipping address is incomplete")
    }
    
    return nil
}
```

**❌ Avoid `switch` for Complex Conditions:**
```go
// Bad: Too complex for switch - multiple variables and ranges
func BadExample(orderValue float64, weight float64, isVIP bool) {
    switch { // Blank switch, but conditions are too complex
    case orderValue > 100 && weight < 5 && isVIP:
        // This is better as if-else
    }
}

// Better: Use if-else instead
func GoodExample(orderValue float64, weight float64, isVIP bool) {
    if orderValue > 100 && weight < 5 && isVIP {
        applyVIPDiscount()
    } else if orderValue > 100 && weight < 5 {
        applyStandardDiscount()
    }
}
```

**❌ Avoid `if` for Many Discrete Values:**
```go
// Bad: Repetitive if-else chain
func BadStatusHandler(status string) {
    if status == "pending" {
        handlePending()
    } else if status == "confirmed" {
        handleConfirmed()
    } else if status == "shipped" {
        handleShipped()
    } else if status == "delivered" {
        handleDelivered()
    } else if status == "cancelled" {
        handleCancelled()
    } else {
        handleUnknown()
    }
}

// Better: Use switch for clarity
func GoodStatusHandler(status string) {
    switch status {
    case "pending":
        handlePending()
    case "confirmed":
        handleConfirmed()
    case "shipped":
        handleShipped()
    case "delivered":
        handleDelivered()
    case "cancelled":
        handleCancelled()
    default:
        handleUnknown()
    }
}
```

**Quick Decision Guide:**
- **3+ specific values** → Use `switch`
- **Multiple variables/conditions** → Use `if`
- **Range checks** → Use `if`
- **Boolean logic (&&, ||)** → Use `if`
- **Single variable, discrete values** → Use `switch`

### goto 🚦
Rarely used; jumps to a labeled statement.

```go
func retryOperation() {
    count := 0
retry:
    count++
    if count < 3 {
        fmt.Println("Retrying...")
        goto retry
    }
}
```

### Exercises 🏋️
1. Write a `for` loop to retry an operation 3 times. Use `continue` if a retry fails, and `break` if it succeeds.
2. Create a `switch` to respond to order states: "placed", "shipped", "delivered", "canceled".
3. Create a nested loop comparing two slices, using labels to skip conflicting pairs.

**4. LeetCode Challenge: FizzBuzz - E-Commerce Edition 🎯**

Implement a function that processes order numbers and applies special rules based on divisibility. This is a classic control flow problem that uses `if`, `switch`, and `for` loops.

Given an integer `n`, return a slice of strings where:
- `result[i] == "Fizz"` if `i` is divisible by 3
- `result[i] == "Buzz"` if `i` is divisible by 5
- `result[i] == "FizzBuzz"` if `i` is divisible by both 3 and 5
- `result[i] == str(i)` otherwise (convert number to string)

**E-Commerce Context:**
In an e-commerce system, you might use this pattern to:
- Apply special promotions to orders divisible by certain numbers
- Categorize orders for batch processing
- Create visual indicators for order groups

**Example 1:**
```go
Input: n = 3
Output: []string{"1", "2", "Fizz"}
```

**Example 2:**
```go
Input: n = 5
Output: []string{"1", "2", "Fizz", "4", "Buzz"}
```

**Example 3:**
```go
Input: n = 15
Output: []string{"1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz", "11", "Fizz", "13", "14", "FizzBuzz"}
```

**Hint:** 
- Use a `for` loop from 1 to n
- Use `if-else` statements to check divisibility
- Use `strconv.Itoa()` to convert integers to strings
- Check for divisibility by both 3 and 5 first (FizzBuzz), then individual checks

**Solution Template (Using if-else):**
```go
import "strconv"

func fizzBuzz(n int) []string {
    result := make([]string, 0, n)
    
    // Your code here
    // Loop from 1 to n
    // For each number i:
    //   - If divisible by both 3 and 5: append "FizzBuzz"
    //   - Else if divisible by 3: append "Fizz"
    //   - Else if divisible by 5: append "Buzz"
    //   - Else: append strconv.Itoa(i)
    
    return result
}
```

**Alternative Solution (Using switch):**
```go
import "strconv"

func fizzBuzzSwitch(n int) []string {
    result := make([]string, 0, n)
    
    for i := 1; i <= n; i++ {
        switch {
        case i%3 == 0 && i%5 == 0:
            result = append(result, "FizzBuzz")
        case i%3 == 0:
            result = append(result, "Fizz")
        case i%5 == 0:
            result = append(result, "Buzz")
        default:
            result = append(result, strconv.Itoa(i))
        }
    }
    
    return result
}
```

**Test Your Solution:**
```go
import (
    "fmt"
    "strconv"
)

func main() {
    testCases := []int{3, 5, 15, 1, 20}
    
    for _, n := range testCases {
        result := fizzBuzz(n)
        fmt.Printf("n = %d\n", n)
        fmt.Printf("Output: %v\n", result)
        fmt.Println()
    }
}
```

**Expected Output:**
```
n = 3
Output: [1 2 Fizz]

n = 5
Output: [1 2 Fizz 4 Buzz]

n = 15
Output: [1 2 Fizz 4 Buzz Fizz 7 8 Fizz Buzz 11 Fizz 13 14 FizzBuzz]

n = 1
Output: [1]

n = 20
Output: [1 2 Fizz 4 Buzz Fizz 7 8 Fizz Buzz 11 Fizz 13 14 FizzBuzz 16 17 Fizz 19 Buzz]
```

**5. LeetCode Challenge: Valid Parentheses - Order Validation 🛒**

Given a string `s` containing only the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid. An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

**E-Commerce Context:**
In e-commerce systems, this pattern is used for:
- Validating nested order structures
- Checking configuration file syntax
- Validating JSON-like data structures
- Ensuring proper nesting of HTML/XML tags

**Example 1:**
```go
Input: s = "()"
Output: true
```

**Example 2:**
```go
Input: s = "()[]{}"
Output: true
```

**Example 3:**
```go
Input: s = "(]"
Output: false
```

**Example 4:**
```go
Input: s = "([)]"
Output: false
```

**Example 5:**
```go
Input: s = "{[]}"
Output: true
```

**Hint:**
- Use a `slice` as a stack to track opening brackets
- Use a `map` to store matching pairs: `map[rune]rune{')': '(', '}': '{', ']': '['}`
- Iterate through the string using `for-range` (to get runes)
- If it's an opening bracket, push to stack
- If it's a closing bracket, check if stack is empty or if top doesn't match
- At the end, stack should be empty

**Algorithm Approach:**
```
1. Create a map for matching pairs: closing -> opening
2. Create an empty slice as stack
3. Loop through each character in string:
   - If character is opening bracket: append to stack
   - If character is closing bracket:
     - If stack is empty: return false
     - If top of stack doesn't match: return false
     - Otherwise: pop from stack (remove last element)
4. Return true if stack is empty, false otherwise
```

**Solution Template:**
```go
func isValid(s string) bool {
    // Your code here
    // Hint: Create a map[rune]rune for matching pairs
    // Create a slice as stack: stack := []rune{}
    // Loop through string using for-range
    // Use switch or if to check bracket type
    // For opening brackets: append to stack
    // For closing brackets: check stack and pop if valid
    // Return true only if stack is empty at the end
}
```

**Test Your Solution:**
```go
func main() {
    testCases := []struct {
        input    string
        expected bool
    }{
        {"()", true},
        {"()[]{}", true},
        {"(]", false},
        {"([)]", false},
        {"{[]}", true},
        {"", true},
        {"(", false},
        {")", false},
    }
    
    for i, tc := range testCases {
        result := isValid(tc.input)
        status := "✅"
        if result != tc.expected {
            status = "❌"
        }
        fmt.Printf("Test %d %s: input = \"%s\", expected = %v, got = %v\n",
            i+1, status, tc.input, tc.expected, result)
    }
}
```

**Expected Output:**
```
Test 1 ✅: input = "()", expected = true, got = true
Test 2 ✅: input = "()[]{}", expected = true, got = true
Test 3 ✅: input = "(]", expected = false, got = false
Test 4 ✅: input = "([)]", expected = false, got = false
Test 5 ✅: input = "{[]}", expected = true, got = true
Test 6 ✅: input = "", expected = true, got = true
Test 7 ✅: input = "(", expected = false, got = false
Test 8 ✅: input = ")", expected = false, got = false
```

**Key Concepts Used:**
- `for-range` loop to iterate through string (gets runes)
- `switch` or `if-else` for conditional logic
- `slice` as a stack data structure
- `map` for O(1) lookup of matching pairs
- `append` and slice operations for stack push/pop

**Time Complexity:** O(n) - Single pass through the string  
**Space Complexity:** O(n) - Stack storage in worst case

### Wrapping Up 🎁
You've learned:
- Scope and shadowing
- All `for` loop variants
- `if`, `switch`, `goto`
- Best practices for control flow

---

## 📝 Exams: Show Off Your Skills! 🏆

### Simple
1. What happens when a variable is shadowed in an inner block?
2. What's the difference between `break` and `continue`?
3. Which loop is valid in Go?
   - A. while
   - B. for
   - C. do-while

### Medium
1. Write a `for-range` loop that prints product names from a slice.
2. Use a `switch` statement to handle order statuses: "pending", "delivered", and "canceled".
3. Explain what this code prints:
   ```go
   x := 10
   if x := 20; x > 10 {
       fmt.Println(x)
   }
   fmt.Println(x)
   ```

### Hard
1. Write a function `RetryAction` that attempts a task 3 times using `for`, `break`, and `continue`. Simulate failures.
2. Create a `for` loop with a label that breaks out when two elements from two slices match.
3. Implement a function that reads an int and uses a blank switch to check if it's negative, zero, even, or odd.

---

## 🛠️ Chapter 5: Functions in Go

![Functions](https://images.unsplash.com/photo-1519125323398-675f0ddb6308?auto=format&fit=crop&w=600&q=80)

### 📚 Table of Contents for This Chapter
1. **Introduction: What is a Function?**
2. **Section 1: Basic Function Principles**
3. **Section 2: Types of Parameters**
4. **Section 3: Return Values**
5. **Section 4: Advanced Concepts**
6. **Section 5: Call by Value**
7. **Exercises and Challenges**

---

## 🎯 Introduction: What is a Function?

A **function** is a reusable block of code that performs a specific task. Think of it as a recipe that you can use over and over again!

**Simple Example from Daily Life:**
```
Coffee Making Recipe:
1. Boil water
2. Add coffee
3. Stir
4. Serve
```

You can use this recipe every day! Functions in programming work the same way.

**Simple Example in Go:**
```go
// This is a simple function that greets someone
func greet(name string) {
    fmt.Printf("Hello %s! Welcome\n", name)
}

func main() {
    greet("Alice")  // We call the function
    greet("Bob")
}
```

**Output:**
```
Hello Alice! Welcome
Hello Bob! Welcome
```

### 🔄 How Functions Work

```mermaid
sequenceDiagram
    participant Caller as Caller
    participant Function as Function
    Caller->>Function: Call with arguments
    Function->>Function: Process
    Function-->>Caller: Return result
```

**Function Call Steps:**
1. **Send Arguments**: We pass data to the function
2. **Execute Function**: The function does its work
3. **Return Result**: The function returns the result
4. **Continue Program**: The program continues with the result

**Practical Example:**
```go
// Calculate total order price
func calculateTotal(price float64, quantity int) float64 {
    total := price * float64(quantity)
    return total  // Returns the result
}

func main() {
    // Step 1: Call function with arguments
    result := calculateTotal(29.99, 3)
    
    // Step 2: Use the result
    fmt.Printf("Total price: $%.2f\n", result)
}
```

---

## 📖 Section 1: Basic Function Principles

### 1.1 Declaring and Calling Functions

**General Structure of a Function:**
```go
func functionName(parameter1 type1, parameter2 type2) returnType {
    // function body
    return returnValue
}
```

**Simple Example for Beginners:**
```go
package main

import "fmt"

// Function 1: No parameters, no return value
func sayHello() {
    fmt.Println("Hello! Welcome to our online store")
}

// Function 2: With one parameter
func greetCustomer(name string) {
    fmt.Printf("Hello %s! Welcome\n", name)
}

// Function 3: With parameters and return value
func calculatePrice(unitPrice float64, quantity int) float64 {
    fp  
    total := unitPrice * float64(quantity)
    return total
}

func main() {
    // Calling functions
    sayHello()
    
    greetCustomer("Alice")
    greetCustomer("Bob")
    
    price := calculatePrice(29.99, 3)
    fmt.Printf("Total price: $%.2f\n", price)
}
```

**Output:**
```
Hello! Welcome to our online store
Hello Alice! Welcome
Hello Bob! Welcome
Total price: $89.97
```

**Important Notes:**
- ✅ Function names should be clear and descriptive
- ✅ Parameters must have types
- ✅ Return type comes after the parameters
- ✅ If function doesn't return anything, don't write a return type

### 1.2 Parameters and Arguments

**Difference between Parameter and Argument:**
- **Parameter**: Variable used in function definition
- **Argument**: Value sent when calling the function

**Example:**
```go
// price and quantity are parameters
func calculateTotal(price float64, quantity int) float64 {
    return price * float64(quantity)
}

func main() {
    // 29.99 and 3 are arguments
    total := calculateTotal(29.99, 3)
    fmt.Println(total)
}
```

**Practical Example: E-commerce System**
```go
package main

import "fmt"

// Calculate price of an item
func calculateItemPrice(unitPrice float64, quantity int) float64 {
    return unitPrice * float64(quantity)
}

// Calculate discount
func applyDiscount(price float64, discountPercent float64) float64 {
    discount := price * (discountPercent / 100)
    return price - discount
}

// Calculate tax
func addTax(price float64, taxRate float64) float64 {
    tax := price * (taxRate / 100)
    return price + tax
}

func main() {
    // Unit price of product
    unitPrice := 100.0
    quantity := 2
    
    // Calculate total price
    subtotal := calculateItemPrice(unitPrice, quantity)
    fmt.Printf("Price before discount: $%.2f\n", subtotal)
    
    // Apply 10% discount
    discountedPrice := applyDiscount(subtotal, 10)
    fmt.Printf("Price after discount: $%.2f\n", discountedPrice)
    
    // Add 9% tax
    finalPrice := addTax(discountedPrice, 9)
    fmt.Printf("Final price: $%.2f\n", finalPrice)
}
```

**Output:**
```
Price before discount: $200.00
Price after discount: $180.00
Final price: $196.20
```

### 1.3 Return Values

**Functions can:**
- Return nothing
- Return one value
- Return multiple values

**Example 1: No Return Value**
```go
func printOrderInfo(orderID int, customerName string) {
    fmt.Printf("Order #%d for %s\n", orderID, customerName)
}

func main() {
    printOrderInfo(1001, "Alice")
}
```

**Example 2: With One Return Value**
```go
func calculateTotal(price float64, quantity int) float64 {
    return price * float64(quantity)
}

func main() {
    total := calculateTotal(29.99, 3)
    fmt.Printf("Total: $%.2f\n", total)
}
```

**Example 3: With Multiple Return Values**
```go
func divideAndRemainder(a int, b int) (int, int) {
    quotient := a / b
    remainder := a % b
    return quotient, remainder
}

func main() {
    q, r := divideAndRemainder(17, 5)
    fmt.Printf("Quotient: %d, Remainder: %d\n", q, r)
    // Output: Quotient: 3, Remainder: 2
}
```

---

## 📦 Section 2: Types of Parameters

### 2.1 Regular Parameters

Parameters where the number and type are fixed.

**Example:**
```go
func calculateShipping(weight float64, distance int, isExpress bool) float64 {
    baseCost := 5.0
    
    // Cost based on weight
    if weight > 5.0 {
        baseCost += (weight - 5.0) * 2.0
    }
    
    // Cost based on distance
    if distance > 100 {
        baseCost += float64(distance-100) * 0.1
    }
    
    // Express shipping cost
    if isExpress {
        baseCost += 10.0
    }
    
    return baseCost
}

func main() {
    cost1 := calculateShipping(3.0, 50, false)
    fmt.Printf("Regular shipping cost: $%.2f\n", cost1)
    
    cost2 := calculateShipping(8.0, 200, true)
    fmt.Printf("Express shipping cost: $%.2f\n", cost2)
}
```

### 2.2 Variadic Parameters

Sometimes we don't know how many arguments we'll send. We use `...` for this.

**Simple Example:**
```go
// This function can receive zero, one, or multiple numbers
func sum(numbers ...int) int {
    total := 0
    for _, num := range numbers {
        total += num
    }
    return total
}

func main() {
    result1 := sum()           // 0 arguments
    result2 := sum(5)          // 1 argument
    result3 := sum(1, 2, 3)    // 3 arguments
    result4 := sum(10, 20, 30, 40) // 4 arguments
    
    fmt.Println(result1, result2, result3, result4)
    // Output: 0 5 6 100
}
```

**Practical Example: Calculating Shopping Cart Total**
```go
package main

import "fmt"

// Calculate total price of multiple products
func calculateCartTotal(prices ...float64) float64 {
    total := 0.0
    for i, price := range prices {
        fmt.Printf("Product %d: $%.2f\n", i+1, price)
        total += price
    }
    return total
}

func main() {
    // Shopping cart with 3 products
    total1 := calculateCartTotal(29.99, 15.50, 45.00)
    fmt.Printf("Cart total: $%.2f\n\n", total1)
    
    // Shopping cart with 5 products
    total2 := calculateCartTotal(10.00, 20.00, 30.00, 40.00, 50.00)
    fmt.Printf("Cart total: $%.2f\n", total2)
}
```

**Output:**
```
Product 1: $29.99
Product 2: $15.50
Product 3: $45.00
Cart total: $90.49

Product 1: $10.00
Product 2: $20.00
Product 3: $30.00
Product 4: $40.00
Product 5: $50.00
Cart total: $150.00
```

**Important Notes:**
- ✅ Variadic parameter must be the last parameter
- ✅ Inside the function, variadic parameter is a slice
- ✅ You can send zero arguments

### 2.3 Optional Parameters with Struct

Go doesn't have optional parameters, but we can simulate them with structs.

**Simple Example:**
```go
package main

import "fmt"

// Structure for email settings
type EmailOptions struct {
    To      string // Required
    Subject string // Required
    Body    string // Required
    CC      string // Optional (can be left empty)
}

// Send email
func sendEmail(opts EmailOptions) {
    fmt.Printf("📧 Sending email to: %s\n", opts.To)
    fmt.Printf("   Subject: %s\n", opts.Subject)
    
    if opts.CC != "" {
        fmt.Printf("   CC: %s\n", opts.CC)
    }
    
    fmt.Printf("   Body: %s\n\n", opts.Body)
}

func main() {
    // Simple email (without CC)
    sendEmail(EmailOptions{
        To:      "customer@example.com",
        Subject: "Order Confirmation",
        Body:    "Your order has been confirmed",
    })
    
    // Email with CC
    sendEmail(EmailOptions{
        To:      "customer@example.com",
        Subject: "Order Shipped",
        Body:    "Your order has been shipped",
        CC:      "support@example.com",
    })
}
```

**Output:**
```
📧 Sending email to: customer@example.com
   Subject: Order Confirmation
   Body: Your order has been confirmed

📧 Sending email to: customer@example.com
   Subject: Order Shipped
   CC: support@example.com
   Body: Your order has been shipped
```

**Advantages:**
- ✅ Code becomes clearer and more readable
- ✅ You can omit optional parameters
- ✅ Easy to add new parameters

---

## 🎁 Section 3: Return Values

### 3.1 Single Return Value

**Simple Example:**
```go
func calculateTotal(price float64, quantity int) float64 {
    return price * float64(quantity)
}

func main() {
    total := calculateTotal(29.99, 3)
    fmt.Printf("Total: $%.2f\n", total)
}
```

### 3.2 Multiple Return Values

Go can return multiple values. This is very useful for error handling.

**Simple Example:**
```go
func divide(a float64, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero is not allowed")
    }
    return a / b, nil
}

func main() {
    result, err := divide(10, 2)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Printf("Result: %.2f\n", result)
    }
    
    result2, err2 := divide(10, 0)
    if err2 != nil {
        fmt.Println("Error:", err2)
    }
}
```

**Output:**
```
Result: 5.00
Error: division by zero is not allowed
```

**Practical Example: Payment System**
```go
package main

import (
    "fmt"
    "errors"
)

// Process payment
func processPayment(amount float64, paymentMethod string) (string, error) {
    // Validate amount
    if amount <= 0 {
        return "", errors.New("amount must be greater than zero")
    }
    
    // Validate payment method
    if paymentMethod == "" {
        return "", errors.New("payment method must be specified")
    }
    
    // Simulate payment processing
    transactionID := fmt.Sprintf("TXN-%d", 1000+int(amount))
    return transactionID, nil
}

func main() {
    // Successful payment
    transactionID, err := processPayment(99.99, "credit card")
    if err != nil {
        fmt.Printf("❌ Payment error: %v\n", err)
    } else {
        fmt.Printf("✅ Payment successful! Transaction ID: %s\n", transactionID)
    }
    
    // Failed payment (invalid amount)
    _, err = processPayment(-10, "credit card")
    if err != nil {
        fmt.Printf("❌ Payment error: %v\n", err)
    }
}
```

**Output:**
```
✅ Payment successful! Transaction ID: TXN-1099
❌ Payment error: amount must be greater than zero
```

**Common Pattern in Go:**
```go
// Error is always the last return value
result, err := someFunction()
if err != nil {
    // Handle error
    return err
}
// Use result
```

### 3.3 Named Return Values

You can name return values. This makes code clearer.

**Example:**
```go
func calculateOrderSummary(orderID int, items []string, prices []float64) (
    totalItems int,
    subtotal float64,
    tax float64,
    total float64,
) {
    totalItems = len(items)
    
    // Calculate subtotal
    for _, price := range prices {
        subtotal += price
    }
    
    // Calculate tax (9%)
    tax = subtotal * 0.09
    
    // Calculate total
    total = subtotal + tax
    
    return // Automatically returns named values
}

func main() {
    items := []string{"Laptop", "Mouse", "Keyboard"}
    prices := []float64{999.99, 29.99, 79.99}
    
    totalItems, subtotal, tax, total := calculateOrderSummary(1001, items, prices)
    
    fmt.Printf("Order Summary #1001:\n")
    fmt.Printf("  Total items: %d\n", totalItems)
    fmt.Printf("  Subtotal: $%.2f\n", subtotal)
    fmt.Printf("  Tax: $%.2f\n", tax)
    fmt.Printf("  Total: $%.2f\n", total)
}
```

**Output:**
```
Order Summary #1001:
  Total items: 3
  Subtotal: $1109.97
  Tax: $99.90
  Total: $1209.87
```

**Important Note:** It's better to explicitly return values:
```go
// ✅ Good
return totalItems, subtotal, tax, total

// ⚠️ Acceptable but less clear
return
```

### 3.4 Ignoring Return Values

Sometimes we only need some return values. We use `_` for this.

**Example:**
```go
func getOrderInfo(orderID int) (string, float64, error) {
    return "Laptop", 999.99, nil
}

func main() {
    // Only need product name and error
    productName, _, err := getOrderInfo(1001)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Printf("Product: %s\n", productName)
    }
    
    // Only need error
    _, _, err = getOrderInfo(1002)
    if err != nil {
        fmt.Println("Error:", err)
    }
}
```

**Important Note:** Always check errors! Ignoring errors is usually wrong.

---

## 🚀 Section 4: Advanced Concepts

### 4.1 Functions as Values

In Go, functions can be used like any other value:
- Assigned to variables
- Passed to other functions
- Returned from functions

**Simple Example:**
```go
package main

import "fmt"

func main() {
    // Assign function to variable
    calculateTax := func(price float64) float64 {
        return price * 0.09 // 9% tax
    }
    
    price := 100.0
    tax := calculateTax(price)
    fmt.Printf("Price: $%.2f, Tax: $%.2f\n", price, tax)
    
    // Change function
    calculateTax = func(price float64) float64 {
        return price * 0.15 // 15% tax
    }
    
    tax = calculateTax(price)
    fmt.Printf("Price: $%.2f, Tax: $%.2f\n", price, tax)
}
```

**Output:**
```
Price: $100.00, Tax: $9.00
Price: $100.00, Tax: $15.00
```

### 4.2 Anonymous Functions

Functions that don't have names and can be executed immediately.

**Simple Example:**
```go
package main

import "fmt"

func main() {
    // Anonymous function assigned to variable
    greet := func(name string) {
        fmt.Printf("Hello %s!\n", name)
    }
    
    greet("Alice")
    greet("Bob")
    
    // Anonymous function executed immediately
    func() {
        fmt.Println("This function executed immediately!")
    }()
    
    // Anonymous function with parameter executed immediately
    func(orderID int) {
        fmt.Printf("Processing order #%d\n", orderID)
    }(1001)
}
```

**Output:**
```
Hello Alice!
Hello Bob!
This function executed immediately!
Processing order #1001
```

### 4.3 Closure

A closure is a function that has access to variables in its surrounding scope.

**Simple Example: Order ID Generator**
```go
package main

import "fmt"

// This function returns another function
func createOrderIDGenerator() func() int {
    nextID := 1000 // This variable is "captured" by the closure
    
    return func() int {
        nextID++ // Has access to nextID variable
        return nextID
    }
}

func main() {
    // Create a generator
    generateID := createOrderIDGenerator()
    
    fmt.Println(generateID()) // 1001
    fmt.Println(generateID()) // 1002
    fmt.Println(generateID()) // 1003
    
    // New generator (separate counter)
    generateID2 := createOrderIDGenerator()
    fmt.Println(generateID2()) // 1001 (starts from beginning)
}
```

**Output:**
```
1001
1002
1003
1001
```

**Practical Example: Discount System**
```go
package main

import "fmt"

func createDiscountCalculator(discountPercent float64) func(float64) float64 {
    return func(price float64) float64 {
        discount := price * (discountPercent / 100)
        return price - discount
    }
}

func main() {
    // Create 10% discount calculator
    apply10PercentDiscount := createDiscountCalculator(10)
    
    // Create 20% discount calculator
    apply20PercentDiscount := createDiscountCalculator(20)
    
    price := 100.0
    
    fmt.Printf("Original price: $%.2f\n", price)
    fmt.Printf("After 10%% discount: $%.2f\n", apply10PercentDiscount(price))
    fmt.Printf("After 20%% discount: $%.2f\n", apply20PercentDiscount(price))
}
```

**Output:**
```
Original price: $100.00
After 10% discount: $90.00
After 20% discount: $80.00
```

### 4.4 Higher-Order Functions

Functions that take other functions as parameters or return functions.

**Simple Example: Filtering Orders**
```go
package main

import "fmt"

type Order struct {
    ID     int
    Amount float64
}

// This is a higher-order function
// Takes a slice of orders and a filter function
func filterOrders(orders []Order, shouldInclude func(Order) bool) []Order {
    result := []Order{}
    for _, order := range orders {
        if shouldInclude(order) {
            result = append(result, order)
        }
    }
    return result
}

func main() {
    orders := []Order{
        {1001, 50.0},
        {1002, 150.0},
        {1003, 75.0},
        {1004, 200.0},
    }
    
    // Filter orders above $100
    highValueOrders := filterOrders(orders, func(o Order) bool {
        return o.Amount >= 100.0
    })
    
    fmt.Println("High-value orders:")
    for _, order := range highValueOrders {
        fmt.Printf("  Order #%d: $%.2f\n", order.ID, order.Amount)
    }
}
```

**Output:**
```
High-value orders:
  Order #1002: $150.00
  Order #1004: $200.00
```

### 4.5 defer (Deferred Execution)

`defer` postpones execution of a function until the current function ends.

**Simple Example:**
```go
package main

import "fmt"

func processOrder(orderID int) {
    fmt.Printf("Starting to process order #%d\n", orderID)
    
    defer fmt.Printf("Finished processing order #%d\n", orderID)
    
    fmt.Println("Processing...")
    fmt.Println("Checking inventory...")
    fmt.Println("Processing payment...")
}

func main() {
    processOrder(1001)
}
```

**Output:**
```
Starting to process order #1001
Processing...
Checking inventory...
Processing payment...
Finished processing order #1001
```

**Note:** Deferred functions execute in reverse order (last defer executes first).

**Practical Example: Closing Files or Database Connections**
```go
package main

import "fmt"

type Database struct {
    isOpen bool
}

func (db *Database) Open() {
    db.isOpen = true
    fmt.Println("🔓 Database connection opened")
}

func (db *Database) Close() {
    db.isOpen = false
    fmt.Println("🔒 Database connection closed")
}

func saveOrder(orderID int) {
    db := &Database{}
    db.Open()
    
    defer db.Close() // Always closes, even if error occurs
    
    fmt.Printf("💾 Saving order #%d to database\n", orderID)
    // If an error occurs, defer still executes
}

func main() {
    saveOrder(1001)
}
```

**Output:**
```
🔓 Database connection opened
💾 Saving order #1001 to database
🔒 Database connection closed
```

---

## 📦 Section 5: Call by Value

In Go, when we pass a value to a function, a copy of that value is created. Changes to the copy don't affect the original value.

**Simple Example:**
```go
package main

import "fmt"

func increment(number int) {
    number++ // Only changes the copy
    fmt.Printf("Inside function: %d\n", number)
}

func main() {
    x := 10
    fmt.Printf("Before call: %d\n", x)
    
    increment(x)
    
    fmt.Printf("After call: %d\n", x) // x hasn't changed
}
```

**Output:**
```
Before call: 10
Inside function: 11
After call: 10
```

**To change the original value, we use a pointer:**
```go
package main

import "fmt"

func incrementByPointer(number *int) {
    *number++ // Changes the original value
    fmt.Printf("Inside function: %d\n", *number)
}

func main() {
    x := 10
    fmt.Printf("Before call: %d\n", x)
    
    incrementByPointer(&x) // We send the address of x
    
    fmt.Printf("After call: %d\n", x) // x has changed
}
```

**Output:**
```
Before call: 10
Inside function: 11
After call: 11
```

**Practical Example: Updating Order Status**
```go
package main

import "fmt"

type Order struct {
    ID     int
    Status string
    Total  float64
}

// This function only changes the copy of the struct
func updateStatusByValue(order Order, newStatus string) {
    order.Status = newStatus
    fmt.Printf("Inside function: Status = %s\n", order.Status)
}

// This function changes the original struct
func updateStatusByPointer(order *Order, newStatus string) {
    order.Status = newStatus
    fmt.Printf("Inside function: Status = %s\n", order.Status)
}

func main() {
    order := Order{ID: 1001, Status: "pending", Total: 99.99}
    
    fmt.Printf("Before call: %s\n", order.Status)
    updateStatusByValue(order, "shipped")
    fmt.Printf("After call (by value): %s\n\n", order.Status)
    
    fmt.Printf("Before call: %s\n", order.Status)
    updateStatusByPointer(&order, "delivered")
    fmt.Printf("After call (by pointer): %s\n", order.Status)
}
```

**Output:**
```
Before call: pending
Inside function: Status = shipped
After call (by value): pending

Before call: pending
Inside function: Status = delivered
After call (by pointer): delivered
```

---

## 🏋️ Exercises and Challenges

Before diving deep into functions, let's practice with some exercises that will help you understand function concepts:

**1. Basic Function Understanding**
Write a function `greetCustomer(name string)` that prints a welcome message for an e-commerce customer.

**2. Function with Return Value**
Create a function `calculateItemPrice(unitPrice float64, quantity int) float64` that calculates the total price for multiple items.

**3. Multiple Return Values**
Write a function `getOrderInfo(orderID int) (string, float64, error)` that returns:
- Product name (string)
- Price (float64)
- Error (if orderID is invalid)

### Challenge 1: Reverse String 🔄

**Problem:** Write a function that reverses a string. This is useful for generating order confirmation codes or processing customer input.

**Example 1:**
```go
Input: "ORDER123"
Output: "321REDRO"
```

**Example 2:**
```go
Input: "CUSTOMER"
Output: "RETSUOMC"
```

**Hints:**
- Convert string to slice of runes: `[]rune(s)`
- Swap characters from both ends
- Convert back to string: `string(runes)`

**Solution Template:**
```go
func reverseString(s string) string {
    // Your code here
    // Convert string to []rune
    // Loop and swap characters
    // Convert back to string
    // Return reversed string
}
```

**Complete Solution:**
```go
package main

import "fmt"

func reverseString(s string) string {
    // Convert string to slice of runes
    runes := []rune(s)
    
    // Swap characters from both ends
    for i, j := 0, len(runes)-1; i < j; i, j = i+1, j-1 {
        runes[i], runes[j] = runes[j], runes[i]
    }
    
    // Convert back to string
    return string(runes)
}

func main() {
    testCases := []string{
        "ORDER123",
        "CUSTOMER",
        "A",
        "Hello World",
        "",
        "12345",
    }
    
    for _, s := range testCases {
        result := reverseString(s)
        fmt.Printf("Input: \"%s\" → Output: \"%s\"\n", s, result)
    }
}
```

**Expected Output:**
```
Input: "ORDER123" → Output: "321REDRO"
Input: "CUSTOMER" → Output: "RETSUOMC"
Input: "A" → Output: "A"
Input: "Hello World" → Output: "dlroW olleH"
Input: "" → Output: ""
Input: "12345" → Output: "54321"
```

### Challenge 2: Valid Palindrome 🔍

**Problem:** Check if a string is a palindrome. A palindrome reads the same forwards and backwards. Only consider alphanumeric characters and ignore case.

**Example 1:**
```go
Input: "A man a plan a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome
```

**Example 2:**
```go
Input: "race a car"
Output: false
```

**Hints:**
- Use two pointers (start and end)
- Ignore non-alphanumeric characters
- Compare characters ignoring case

**Complete Solution:**
```go
package main

import (
    "fmt"
    "strings"
    "unicode"
)

func isPalindrome(s string) bool {
    // Convert to lowercase
    s = strings.ToLower(s)
    
    // Use two pointers
    left := 0
    right := len(s) - 1
    
    for left < right {
        // Skip non-alphanumeric characters from left
        for left < right && !unicode.IsLetter(rune(s[left])) && !unicode.IsDigit(rune(s[left])) {
            left++
        }
        
        // Skip non-alphanumeric characters from right
        for left < right && !unicode.IsLetter(rune(s[right])) && !unicode.IsDigit(rune(s[right])) {
            right--
        }
        
        // Compare characters
        if s[left] != s[right] {
            return false
        }
        
        left++
        right--
    }
    
    return true
}

func main() {
    testCases := []struct {
        input    string
        expected bool
    }{
        {"A man a plan a canal: Panama", true},
        {"race a car", false},
        {"Order12321redrO", true},
        {"", true},
        {"a", true},
    }
    
    for i, tc := range testCases {
        result := isPalindrome(tc.input)
        status := "✅"
        if result != tc.expected {
            status = "❌"
        }
        fmt.Printf("Test %d %s: \"%s\" → %v\n", i+1, status, tc.input, result)
    }
}
```

### Challenge 3: Function Composition 🧩

Write several simple functions and then compose them:

```go
// Base functions
func applyDiscount(price float64, percent float64) float64 {
    return price * (1 - percent/100)
}

func addTax(price float64, rate float64) float64 {
    return price * (1 + rate/100)
}

func addShipping(price float64, cost float64) float64 {
    return price + cost
}

// Composite function
func calculateFinalPrice(basePrice float64, operations ...func(float64) float64) float64 {
    result := basePrice
    for _, op := range operations {
        result = op(result)
    }
    return result
}

func main() {
    basePrice := 100.0
    
    finalPrice := calculateFinalPrice(
        basePrice,
        func(p float64) float64 { return applyDiscount(p, 10) }, // 10% discount
        func(p float64) float64 { return addTax(p, 9) },        // 9% tax
        func(p float64) float64 { return addShipping(p, 5.99) }, // $5.99 shipping
    )
    
    fmt.Printf("Final price: $%.2f\n", finalPrice)
    // Calculation: ((100 * 0.9) * 1.09) + 5.99 = 98.09 + 5.99 = 104.08
}
```

### Challenge 4: Complete Order Processing System ⚠️

Write a complete order processing system with error handling:

```go
package main

import (
    "fmt"
    "errors"
)

type Order struct {
    ID     int
    Amount float64
    Items  []string
}

func validateOrder(order Order) error {
    if order.ID <= 0 {
        return errors.New("order ID must be greater than zero")
    }
    
    if order.Amount <= 0 {
        return errors.New("amount must be greater than zero")
    }
    
    if len(order.Items) == 0 {
        return errors.New("order must have at least one item")
    }
    
    return nil
}

func processPayment(order Order) (string, error) {
    if order.Amount > 10000 {
        return "", errors.New("amount exceeds maximum limit")
    }
    
    transactionID := fmt.Sprintf("TXN-%d", order.ID)
    return transactionID, nil
}

func processCompleteOrder(order Order) (string, error) {
    // Validate
    if err := validateOrder(order); err != nil {
        return "", fmt.Errorf("validation failed: %v", err)
    }
    
    // Process payment
    transactionID, err := processPayment(order)
    if err != nil {
        return "", fmt.Errorf("payment failed: %v", err)
    }
    
    return transactionID, nil
}

func main() {
    order := Order{
        ID:     1001,
        Amount: 99.99,
        Items:  []string{"Laptop", "Mouse"},
    }
    
    transactionID, err := processCompleteOrder(order)
    if err != nil {
        fmt.Printf("❌ Error: %v\n", err)
    } else {
        fmt.Printf("✅ Order processed! Transaction ID: %s\n", transactionID)
    }
}
```

### Exercise 1: Simple Functions 📝

**1.1 Greeting Function**
Write a function that takes a customer name and prints a welcome message.

```go
func greetCustomer(name string) {
    // Your code here
}
```

**1.2 Price Calculation**
Write a function that takes unit price and quantity and returns the total price.

```go
func calculateTotal(unitPrice float64, quantity int) float64 {
    // Your code here
}
```

**Solution:**
```go
package main

import "fmt"

func greetCustomer(name string) {
    fmt.Printf("Hello %s! Welcome to our store\n", name)
}

func calculateTotal(unitPrice float64, quantity int) float64 {
    return unitPrice * float64(quantity)
}

func main() {
    greetCustomer("Alice")
    
    total := calculateTotal(29.99, 3)
    fmt.Printf("Total price: $%.2f\n", total)
}
```

### Exercise 2: Multiple Return Values 📝

**2.1 Division and Remainder**
Write a function that takes two numbers and returns quotient and remainder.

```go
func divideAndRemainder(a int, b int) (int, int) {
    // Your code here
}
```

**2.2 Order Information**
Write a function that takes order ID and returns product name, price, and status.

```go
func getOrderInfo(orderID int) (string, float64, string) {
    // Your code here
}
```

**Solution:**
```go
package main

import "fmt"

func divideAndRemainder(a int, b int) (int, int) {
    quotient := a / b
    remainder := a % b
    return quotient, remainder
}

func getOrderInfo(orderID int) (string, float64, string) {
    if orderID == 1001 {
        return "Laptop", 999.99, "Shipped"
    }
    return "Unknown", 0.0, "Not Found"
}

func main() {
    q, r := divideAndRemainder(17, 5)
    fmt.Printf("Quotient: %d, Remainder: %d\n", q, r)
    
    name, price, status := getOrderInfo(1001)
    fmt.Printf("Product: %s, Price: $%.2f, Status: %s\n", name, price, status)
}
```

### Exercise 3: Variadic Parameters 📝

**3.1 Sum of Numbers**
Write a function that takes unlimited numbers and returns their sum.

```go
func sum(numbers ...int) int {
    // Your code here
}
```

**3.2 Average Price Calculation**
Write a function that takes multiple prices and returns their average.

```go
func averagePrice(prices ...float64) float64 {
    // Your code here
}
```

**Solution:**
```go
package main

import "fmt"

func sum(numbers ...int) int {
    total := 0
    for _, num := range numbers {
        total += num
    }
    return total
}

func averagePrice(prices ...float64) float64 {
    if len(prices) == 0 {
        return 0
    }
    
    total := 0.0
    for _, price := range prices {
        total += price
    }
    return total / float64(len(prices))
}

func main() {
    result := sum(1, 2, 3, 4, 5)
    fmt.Printf("Sum: %d\n", result)
    
    avg := averagePrice(10.0, 20.0, 30.0, 40.0)
    fmt.Printf("Average price: $%.2f\n", avg)
}
```

### Exercise 4: Error Handling 📝

**4.1 Order Validation**
Write a function that validates order information and returns an error if invalid.

```go
func validateOrder(orderID int, amount float64) error {
    // Your code here
    // If orderID <= 0, return error
    // If amount <= 0, return error
}
```

**Solution:**
```go
package main

import (
    "fmt"
    "errors"
)

func validateOrder(orderID int, amount float64) error {
    if orderID <= 0 {
        return errors.New("order ID must be greater than zero")
    }
    
    if amount <= 0 {
        return errors.New("amount must be greater than zero")
    }
    
    return nil
}

func main() {
    err := validateOrder(1001, 99.99)
    if err != nil {
        fmt.Printf("❌ Error: %v\n", err)
    } else {
        fmt.Println("✅ Order is valid")
    }
    
    err = validateOrder(-1, 50.0)
    if err != nil {
        fmt.Printf("❌ Error: %v\n", err)
    }
}
```

### Exercise 5: Closure 📝

**5.1 Order ID Generator**
Write a function that returns an order ID generator function.

```go
func createOrderIDGenerator() func() int {
    // Your code here
    // Start from 1000
}
```

**Solution:**
```go
package main

import "fmt"

func createOrderIDGenerator() func() int {
    nextID := 1000
    return func() int {
        nextID++
        return nextID
    }
}

func main() {
    generateID := createOrderIDGenerator()
    
    fmt.Printf("Order #%d\n", generateID())
    fmt.Printf("Order #%d\n", generateID())
    fmt.Printf("Order #%d\n", generateID())
}
```

### Exercise 6: defer 📝

**6.1 Resource Management**
Write a function that uses defer to close database connection.

```go
type Database struct {
    isOpen bool
}

func (db *Database) Open() {
    db.isOpen = true
    fmt.Println("Connection opened")
}

func (db *Database) Close() {
    db.isOpen = false
    fmt.Println("Connection closed")
}

func processOrder(orderID int) {
    // Your code here
    // Use defer to close database
}
```

**Solution:**
```go
package main

import "fmt"

type Database struct {
    isOpen bool
}

func (db *Database) Open() {
    db.isOpen = true
    fmt.Println("🔓 Database connection opened")
}

func (db *Database) Close() {
    db.isOpen = false
    fmt.Println("🔒 Database connection closed")
}

func processOrder(orderID int) {
    db := &Database{}
    db.Open()
    defer db.Close() // Always closes
    
    fmt.Printf("💾 Processing order #%d\n", orderID)
}

func main() {
    processOrder(1001)
}
```

---

## 🎯 Advanced Challenges

### Challenge 1: Reverse String 🔄

---

## 📝 Chapter 5 Summary: Functions

### ✅ What You Learned:

1. **Basics:**
   - Declaring and calling functions
   - Parameters and arguments
   - Return values (single or multiple)

2. **Types of Parameters:**
   - Regular parameters
   - Variadic parameters (`...`)
   - Optional parameters with structs

3. **Advanced Concepts:**
   - Functions as values
   - Anonymous functions
   - Closures
   - Higher-order functions
   - defer for resource management

4. **Call by Value:**
   - Go makes copies by default
   - Use pointers to change original values

### 🎯 Important Notes:

- ✅ Always check errors: `if err != nil { ... }`
- ✅ Use defer for resource management
- ✅ Closures are useful for stateful functions
- ✅ Higher-order functions provide flexibility
- ✅ Use pointers for large structs

### 📚 Next Steps:

Now that you're familiar with functions, you're ready to learn more advanced Go concepts:
- Methods
- Interfaces
- Goroutines and Channels
- Advanced Error Handling

---

## 🎓 Final Exercises

### Beginner Level:
1. Write a function that takes a customer name and prints a welcome message.
2. Write a function that takes two numbers and returns their sum.
3. Write a function that takes a number and returns its square.

### Intermediate Level:
1. Write a function that takes multiple prices and returns their average.
2. Write a function that validates order information and returns an error if invalid.
3. Write a closure that generates order numbers (starting from 1000).

### Advanced Level:
1. Write a complete order processing system with error handling.
2. Write a higher-order function that filters orders.
3. Write a function with defer that manages resources.

---

**Congratulations! You've completed Chapter 5! 🎉**

### Wrapping Up 🎁
You've covered:
- Declaring and using functions
- Closures, `defer`, higher-order functions
- Multiple return values and error handling

---

## 🎓 Learning Go – Course Summary (2nd Edition, 2024)

![Celebrate](https://images.unsplash.com/photo-1465101046530-73398c7f28ca?auto=format&fit=crop&w=600&q=80)

> **Congratulations!** You're now ready to Go! 🚀 Keep experimenting, keep building, and join the Go community. Happy coding! 🎉

### 🎯 Course Objective
This course gives you a hands-on, practical intro to Go, so you can write clean, idiomatic, and production-ready code—with a smile!

### 1. Setting Up Your Go Environment
**Key Concepts:**
- Install Go on any platform.
- Configure `PATH` and troubleshoot issues.
- Use `go build`, `go run`, `go fmt`, `go vet`, `go test`.
- Manage modules with `go.mod`.
- Use tools like VSCode, GoLand, Go Playground.
- Understand the Go compatibility promise.

**Outcome:** You're ready to Go!

### 2. Predeclared Types and Declarations
**Key Concepts:**
- Core types: `int`, `float64`, `bool`, `string`, `interface{}`, `error`.
- Zero values, type literals, untyped constants.
- Explicit type conversion, `var` vs `:=`, `const`.
- Naming conventions and unused variable management.

**Outcome:** You know Go's type system inside and out.

### 3. Composite Types
**Key Concepts:**
- Arrays, slices, maps, structs.
- Slices: `len`, `cap`, `append`, `make`, `copy`.
- Maps: lookup, deletion, `comma ok` idiom.
- Structs: custom types, anonymous structs.
- Bytes and runes for strings.

**Outcome:** You can build and manipulate complex data structures.

### 4. Blocks, Shadows, and Control Structures
**Key Concepts:**
- Blocks for scope boundaries.
- Shadowing variables.
- `if`, `for` (complete, condition-only, infinite, for-range), `switch`, `goto`.
- `break`, `continue`, labeled loops.

**Outcome:** You write clean, readable control flow.

### 5. Functions
**Key Concepts:**
- Declare/call functions, variadic parameters.
- Multiple return values, `defer`, closures.
- Higher-order functions, call-by-value, pointers.
- Structs for named/optional parameters.

**Outcome:** You design modular, testable functions.

### 📚 Course Features
- **Hands-On Examples:** Real-world scenarios (e.g., order processing).
- **Exercises:** Practical tasks to reinforce concepts.
- **Exams:** Simple, Medium, Hard challenges for each chapter.

### ✅ Final Skills Acquired
- Set up and manage Go environments.
- Understand Go's type system and memory model.
- Use composite types effectively.
- Build programs with clean control flow.
- Write modular, reusable functions.
- Apply best practices in naming, formatting, and error handling.
- Develop confidence in reading, writing, and debugging Go programs.