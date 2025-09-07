---
alwaysApply: false
---

# Swift Rules and Best Practices

As an AI assistant working with Swift code, I must follow these rules and best practices to write correct, up to date, bug free, secure, performant, fully functional and working code.

## Naming Conventions

### General Naming Rules

- Use clear and descriptive names, following [API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/#naming)
- Use camelCase for variables, functions and methods
- Use UpperCamelCase (PascalCase) for classes, structs, enums, typealiases and protocols
- Words and phrases should not be abbreviated, except for commonly accepted ones
- Avoid unnecessary words or redundancy in names

### Specific Naming Patterns

- Boolean variables, functions and methods should start with `is`, `has`, `should`, etc.
- End protocol names with `Type` word

```swift
// GOOD
protocol FooType { ... }

// BAD
protocol Foo { ... }
```

- End enum names with `Kind` word (except for enums that are used as namespaces, such as `Constants`)

```swift
// GOOD
enum FooKind { ... }

// BAD
enum Foo { ... }
```

- Avoid underscores except for version numbers in enum cases and unit-test function names

```swift
// GOOD
struct User {
    let name: String
    let email: String
}

// BAD
struct User {
    let _name: String
    let _email: String
}

// EXCEPTION 1
enum VersionKind {
    /// v3.0
    case v3_0
    /// v3.1
    case v3_1
}

// EXCEPTION 2
func test_onInit_whenVersionIsNew_shouldSendRequest() { ... }
```

- Use type postfix for async or reactive variables in mixed sync and async (or reactive) scopes

```swift
// Mixed sync and async scope
final class Repository {
    var hasError: Bool { ... }
    var hasErrorPublisher: AnyPublisher<Bool, Never> { ... }
}
```

### UI Naming Patterns

- End names of [NSObject-inherited](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CocoaFundamentals/Art/uikit_classes.jpg) types and variables with the type name

```swift
// GOOD
final class FooViewController: UIViewController {
    var imageSizeLayoutConstraint: NSLayoutConstraint?
    let titleLabel = UILabel()
    let iconImageView = UIImageView()
    let contentTapGestureRecognizer = UITapGestureRecognizer()
}

// BAD
final class FooController: UIViewController {
    var imageSizeConstraint: NSLayoutConstraint?
    let title = UILabel()
    let iconImage = UIImageView()
    let contentTapRecognizer = UITapGestureRecognizer()
}
```

- End names of SwiftUI View protocol-conforming types and variables with the type name

```swift
// GOOD
struct WelcomeView: View {
    var body: some View {
        VStack {
            headerView
            inputView
        }
    }

    private var headerView: some View { .. }
    private var inputView: some View { .. }
}

// BAD
struct Welcome: View {
    var body: some View {
        VStack {
            header
            input
        }
    }

    private var header: some View { .. }
    private var input: some View { .. }
}
```

## Files Structure

- One file should contain only one type (class, struct or enum), except for very small or related types
- The filename should match the primary type
  - Single type file: Use the type name (Foo.swift contains Foo class or struct)
  - View controller: Use abbreviated form (FooVC.swift contains FooViewController class)
  - View model: Use abbreviated form (FooVM.swift contains FooViewModel class or struct)
  - Extension with protocol conformance: Use the type name and protocol name, combined with the plus character (Foo+BarType.swift)
  - Extension with single method: Use the type name and method name (in UpperCamelCase), combined with the plus character (Foo+BarMethod.swift)
  - Multiple extensions for one type or multiple methods in one extension: Use the type name and general description (in UpperCamelCase), combined with the plus character (Foo+GeneralizedDescription.swift)
- Group related files into directories that represent single components, features or modules

## Code Formatting

### Column Limit and Indentation

- Use column limit to 100 characters (except for long constants, auto-generated code, lines impossible to break meaningfully)
- Use 4 spaces for indentation (not tabs)
- Separate logical blocks of code with a blank line

### Braces

- Open braces on same line, close on new line, except for empty body, simple closures, getters and setters in protocol declaration and protocol conformance

### Closures

- Use shorthand argument names for simple closures
- Use trailing closure syntax for single closure argument
- Avoid trailing closure with multiple closure arguments
- Use key path for simple property access

### Comments

- Use `//` for regular comments (always followed by a space)
- Use `///` for documentation comments
- Use `// MARK: -` to name semantic code blocks 
- Use `// MARK: -` to name each extension except confirmation of empty protocols
- Use `// TODO:` for temporary disabled, unclear or uncomplete code blocks
- Mark complex and non-obvious code with comments
- Don't put a blank line between a comment and a line of code

## Code Organization

### Imports

- Import whole modules instead of individual declarations

```swift
// GOOD
import Foundation

// BAD
import Foundation.UUID
```

- Import only required modules (don't import Foundation if importing UIKit)
- Arrange imports alphabetically

### Constants

- Organize local constants in private `Constants` enum at top of file, after imports
- Group related constants in nested enums
- Omit simple literals used only once in a file with clear context

### Extensions

- Use extensions for protocol conformance
- Use extensions to organize functions into meaningful groups or by access modifiers

### Free Functions

- Don't create free functions outside of extension or enum
- Use extension and static methods inside it to create free functions for classes, structs, enums and protocols
- Use enum and static methods inside it to create namespace and free functions for general use
- Use `Self` for static method calls within classes, structs, enums and protocols

## Control Flow Formatting

### If Statements

- Write single condition with simple return (no value) on one line

```swift
// GOOD
if error != nil { return }

// BAD
if error != nil { 
    return 
}
```

- Put return value on new line for single condition with return value

```swift
// GOOD
if let error { 
    return error
}

// BAD
if let error { return error }
```

- Place each complex condition on separate line, aligned with first condition

```swift
// GOOD
if let name = repository.getName(),
   let email = repository.getEmail() {
    ...
}

// BAD
if let name = repository.getName(),
    let email = repository.getEmail() {
    ...
}

// EXCEPTION: IF IT FITS THE COLUMN LIMIT
if let name = repository.getName(), let email = repository.getEmail() {
    ...
}
```

- Split multiple alternatives for early exit into separate if statements

```swift
// GOOD
if x > y {
    return "x"
}

if y > z {
    return "y"
}

return "z"

// BAD
if x > y {
    return "x"
} else if y > z {
    return "y"
} else {
    return "z"
}
```

- Combine nested if statements into single if when possible

```swift
// GOOD
if isA, isB, isC {
    ...
}

// BAD
if isA {
    if isB {
        if isC {
            ...   
        }
    }
}
```

### Guard Statements

- Write condition with simple return (no value) on one line

```swift
// GOOD
guard error == nil else { return }

// BAD
guard error == nil else { 
    return 
}
```

- Put return value on new line for condition with return value

```swift
// GOOD
guard let error else { 
    return nil
}

// BAD
guard let error { return nil }
```

- Place multiple conditions on separate lines with else aligned with guard

```swift
// GOOD
guard let self,
      let data,
      let context
else { 
    return nil
}

// BAD 1
guard let self,
      let data,
      let context else { 
    return nil
}

// BAD 2
guard 
    let self,
    let data,
    let context 
else { 
    return nil
}

// BAD 3
guard 
    let self,
    let data,
    let context else { 
    return nil
}
```

- Combine multiple guards into single guard when possible

```swift
// GOOD
guard isA, isB else { return }

// BAD
guard isA else { return }
guard isB else { return }
```

### Switch Statements

- Use Swift style indentation for cases

```swift
// GOOD
switch fruitKind {
    case .apple: 
        ...
    case .orange:
        ...
    case .pear:
        ...
}

// BAD
switch fruitKind {
case .apple: 
    ...
case .orange:
    ...
case .pear:
    ...
}
```

- The body of the switch case should be on a separate line

```swift
// GOOD
switch fruitKind {
    case .apple: 
        ...
    case .orange:
        ...
    case .pear:
        ...
}

// BAD
switch fruitKind {
    case .apple: ...
    case .orange: ...
    case .pear: ...
}
```

- Omit return keyword when possible
- Move let/var out of case when extracting associated values

```swift
// GOOD
switch resultKind {
    case let .success(data):
        ...
}

// BAD
switch resultKind {
    case .success(let data):
        ...
}
```

### Logical Operators

- If there are too many conditions, place each condition on separate line with operator at beginning

```swift
// GOOD
let isValid = isCondition1 
    && isCondition2 
    && isCondition3 
    && isCondition4

// BAD
let isValid = isCondition1 &&
    isCondition2 &&
    isCondition3 &&
    isCondition4
```

- Use commas instead of && in if/guard statements

```swift
// GOOD
if isA, isB, isC { ... }

// BAD
if isA && isB && isC { ... }
```

### Ternary Operators

- Simple expressions should be written on a single line

```swift
// GOOD
let result = condition ? resultIfTrue : resultIfFalse

// BAD
let result = condition 
    ? resultIfTrue 
    : resultIfFalse
```

- Large expressions should be written on a multiple lines

```swift
// GOOD
let result = veryLargeCondition 
    ? veryLargeResultIfTrue 
    : veryLargeResultIfFalse

// BAD
let result = veryLargeCondition ? veryLargeResultIfTrue : veryLargeResultIfFalse
```

## Language Guidelines

### Optionals

- Use optional binding with `if let` or `guard let` to safely unwrap
- Use optional chaining (`?.`) to safely access properties and methods
- Use nil coalescing operator (`??`) to provide default values
- Use force unwrap (`!`) only for IBOutlets, constant URLs, mock objects and tests

### Classes and Structs

- Use structs instead of classes where possible, following Swift and Apple guidlines
- Use `self.` only where required
- Use the `final` keyword to mark classes that are not inherited
- Use the `final` keyword to mark methods of parent class that are not overriden

### Protocols

- Favor protocol composition over inheritance
- Design dependencies based on protocols, not actual types

### Type Inference and Annotation

- Use type inference whenever possible

```swift
// GOOD
let count = 10
let message = "Hello, World!"

// BAD
let count: Int = 10
let message: String = "Hello, World!"
```

- Use type annotation to define empty collections

```swift
// GOOD
var array: [Int] = []
var dictionary: [String: Int] = [:]

// BAD
var array = [Int]()
var dictionary = [String: Int]()
```

### Error Handling

- Use Swift's error handling with `throws`, `do-catch`
- Create custom error types using enums that conform to `Error` protocol
- Handle errors appropriately at each level

## Memory Management

### General Memory Management Rules

- Use `weak` references for delegates and parent-child relationships
- Use `unowned` only when reference lifetime is guaranteed
- Use **Decision Tree: When to Use [weak self] in Closure**  to check the correct use of weak self in closure
- Use **Decision Tree: Check for Memory Leak in Nested Closure** to check implicit retain cycle in nested closure

### Decision Tree: When to Use [weak self] in Closure

```
START: Analyzing closure for potential memory leaks
├── Does the closure capture 'self'?
│   ├── NO → No risk of memory leak from 'self'
│   └── YES → Continue to next step
│
├── What type of closure is this?
│   ├── Non-escaping (runs immediately, not retained)
│   │   ├── Do you care about delayed deallocation?
│   │   │   ├── NO → [weak self] NOT NEEDED
│   │   │   └── YES → [weak self] RECOMMENDED
│   │   └── Examples: .map(), .filter(), .forEach()
│   │
│   └── Escaping (retained/executed later)
│       └── Proceed to retention analysis
│
├── Is the closure stored anywhere (property, completion handler, timer, etc.)?
│   ├── NO → Check for delayed deallocation scenarios:
│   │   ├── Long timeout (URLSession)?
│   │   ├── Expensive serial work?
│   │   ├── Thread blocking (DispatchSemaphore)?
│   │   ├── Delayed execution (asyncAfter)?
│   │   └── If YES to any → [weak self] RECOMMENDED
│   │       If NO to all → [weak self] NOT NEEDED
│   │
│   └── YES → Where is it stored?
│       ├── In a property of the same object → RETAIN CYCLE!
│       │   └── [weak self] REQUIRED
│       ├── In another object → Analyze reference relationships
│       └── Passed to another closure → Analyze full chain
│
├── Special cases:
│   ├── Grand Central Dispatch (DispatchQueue.async/sync)
│   │   ├── Executes immediately → [weak self] NOT NEEDED
│   │   └── DispatchWorkItem stored → [weak self] REQUIRED
│   │
│   ├── UIView.animate
│   │   ├── Regular calls → [weak self] NOT NEEDED
│   │   └── Animation stored in a property → [weak self] REQUIRED
│   │
│   ├── Timers
│   │   ├── Repeating timer → [weak self] REQUIRED
│   │   └── Single-fire timer → Depends on context
│   │
│   ├── Function stored as property (closure = self.method)
│   │   └── [weak self] REQUIRED
│   │
│   └── Nested closures
│       └── See specific subtree below
│
└── Alternatives to [weak self]:
    ├── Capture specific properties: let view = self.view
    ├── Build a context tuple: let context = (view: self.view, data: self.data)
    └── Use other architectural patterns as needed
```

### Decision Tree: Check for Memory Leak in Nested Closure

```
Nested closures: Detecting implicit retain cycles
├── Does the outer closure use [weak self]?
│   ├── NO → Potential outer-level retain cycle
│   └── YES → Continue
│
├── Does it use guard let self in the outer closure?
│   ├── NO → Always use self?., safe pattern
│   └── YES → Strong reference is created, check inner closures
│
├── Does the inner closure capture 'self'?
│   ├── NO → Safe
│   └── YES → IMPLICIT MEMORY LEAK
│
└── Fix strategies:
    ├── Option 1: Use optional chaining (self?.method())
    ├── Option 2: Add [weak self] to the inner closure
    └── Option 3: Use guard let in the inner closure as needed
```

## Unit Testing

- Use Swift Testing framework
- Write unit tests for all business logic and models
- Structure unit test functions using Giwen When Then (GWT) principle
- Name unit tests using one of this templates: 
  - `test_<property or method to test>_on<condition>_should<expected result>`
  - `test_<property or method to test>_when<condition>_should<expected result>`

```swift
// EXAMPLE
import Testing

struct CalculatorTests {
    @Test 
    func test_sum_whenGivenTwoPositiveNumbers_shouldReturnCorrectSum() {
        // Given
        let a = 10.5
        let b = 2.012
        let sut = Calculator()  // sut (subject under test)

        // When
        let result = sut.sum(a, b)

        // Then
        #expect(result == 12.512)
    }
}
```

## Libraries

- Use native Apple libraries and frameworks whenever possible
- Use Swift Package Manager (SPM) for integrating dependencies, except for those dependencies that cannot be integrated using SPM

## Architecture

- Follow SOLID, DRY, KISS, YAGNI principles
- Use MVVM design pattern unless otherwise stated
- For large projects, consider modularizing the app using Swift Packages