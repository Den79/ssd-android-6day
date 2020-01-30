# Android Development: A Brief Introduction to Kotlin

1. [ What is Kotlin? ](#1)
2. [ Kotlin Playground ](#2)
4. [ Kotlin Types ](#4)
5. [ Constants and Variables ](#5)
6. [ Nullable Types ](#6)
7. [ Collections ](#7)
8. [ Control Flow ](#8)
9. [ Functions ](#9)
10. [ Classes and Objects ](#10)
11. [ Interfaces and Extensions ](#11)
12. [ Additional Resources ](#12)
13. [ In-class Assignment ](#13)


<a name="1"></a>
## 1) What is Kotlin?

[Kotlin](https://kotlinlang.org/) is a powerful, concise, statically-typed, general-purpose programming language with type inference. It is designed to interoperate with Java, and depends on the Java Class Library in the JVM version of its standard library. Kotlin is officially supported by Google and is quickly eclipsing Java as the industry-standard programming language for Android development.


<a name="2"></a>
## 2) Kotlin Playground

https://play.kotlinlang.org/


<a name="3"></a>
## 3) Kotlin Types

### Basic Types

#### Numbers

##### Integers

```kotlin
val imAnInteger = 1 // Int
val imALong = 999999999999999999 // Long
val imAnotherLong = 1L // Long
val thisisOneByte: Byte = 1 // Byte
```

##### Floating Point
```kotlin
val pi = 3.14 // Double
val e = 2.7182818284 // Double
val eFloat = 2.7182818284f // Float, actual value is 2.7182817
```

#### Characters
```kotlin
val aChar = 'a'
```

#### Booleans

```kotlin
var aBoolean = false
aBoolean = true

if (aBoolean) {
    println("It's true")
}
```

#### Strings
```kotlin
val myString = "Hello World!"

println(myString)
```


<a name="5"></a>
## 5) Constants and Variables

In Kotlin we use `val` to denote an immutable (or unchangeable value) and `var` to denote a variable (ie. a variable can have it's value reassigned).

```kotlin
val constant = "Immutable string"

constant = "This won't work XD" // Val cannot be reassigned

var variable = "Mutable string"

variable = "This will be stored in variable"
```

<a name="6"></a>
## 6) Nullable Types

A common issue across many programming languages (including Java) occurs when attempting to access a member of a null reference - this will result in a NullPointerException. Kotlin's type system distinguishes between references that can hold null and those that can't.

#### Not-nullable Type

```kotlin
// A regular variable of type String cannot hold null
var message: String
message = null // does not compile
```

#### Nullable Type

```kotlin
// To declare a nullable variable, we need to include ?
var message: String?
message = null // compiles
```

### Checking for null

If `message` is immutable (ie. declared with `val`), then we can do the following:

```kotlin
val message: String? = "hai thar"

// The compiler tracks the information about the null check,and allows the.
if (message != null && message.length > 0) {
    print(message)
}
```

#### Safe Call Operator
Alternatively, you can use the safe call operator: `?.` to check for null.

```kotlin
var message: String? = null

// message?.length returns message.length if message is not null, and null otherwise
println(message?.length)
```

#### Chaining

Let's look at an example to understand chaining:

Sarah is an employee at a medium-sized technology company called Goggle. Inside Goggle, she belongs to a department as an Engineering Manager and has multiple teams that she manages. If we want to find out how many teams she manages, we could use chaining:

```kotlin
sarah?.department?.teams?.count
```

Chaining allows us to ensure that we are safely accessing nullable references, while keeping our code readable and succinct. 

#### The Elvis Operator

The elvis operator `?:` allows us to check for null, and provide a backup option in the event that the reference we checked does turn out to be null

```kotlin
val messageToPrint = message ?: "This is a default message"

// If message is null, then "This is a default message" will be passed to print()
print(messageToPrint)
```

#### The !! Operator

Warning: be careful with this!

Sometimes there may be a reason to throw a NotNullException on a nullable reference.

The `!!` operator converts any value to a non-null type and, when accessed, throws an exception if the value is null.

```kotlin
// Will throw an exception if message is null
val messageToPrint = message!!
```

<a name="7"></a>
## 7) Collections

### Arrays

#### Invariance

Arrays in Kotlin are invariant - Kotlin does not let us assign an `Array<String>` to an `Array<Any>`.

#### Mutability

`Array<T>` is mutable, but an array's size is defined upon initialization and is never changed.

```kotlin
val cats = arrayOf("Bobo", "Fluffy", "Shadow")
cats[0] = "Cookie"
    
cats.forEach { print(it + " ") }

// Prints:
// > Cookie Fluffy Shadow
```

### List

`List<T>` is immutable. It stores elements in a specified order and provides indexed access to them.

Lists can have duplicates (including nulls).

```kotlin
fun main() {    
    val cat1 = Cat("Bobo")
    val cat2 = Cat("Fluffy")
    val cat3 = Cat("Cookie")
    
    val cats = listOf(cat1, cat2, cat3)
    cats.forEach { print(it.name + " ") }
}

class Cat(val name: String)

// Prints:
// > Bobo Fluffy Cookie
```

`MutableList<T>` is a `List` with list-specific write operations, for example, to add or remove an element at a specific position.

A list doesn't have a predefined size; a list's size can be changed as a result of write operations: adding, updating, or removing elements.

```kotlin
fun main() {    
    val cat1 = Cat("Bobo")
    val cat2 = Cat("Fluffy")
    val cat3 = Cat("Cookie")
    
    val mutableCats = mutableListOf(cat1, cat2, cat3)
    
    mutableCats.add(Cat("Shadow"))
    mutableCats.forEach { print(it.name + " ") }
}

class Cat(val name: String)

// Prints:
// > Bobo Fluffy Cookie Shadow 
```

### Set

`Set<T>` stores unique elements (including null) but their order is generally undefined.

```kotlin
fun main() {    
    val cat1 = Cat("Bobo")
    val cat2 = Cat("Fluffy")
    val cat3 = Cat("Cookie")
    val cat4 = cat3 // duplicate reference
    
    val cats = setOf(cat1, cat2, cat3, cat4)
    cats.forEach { print(it.name + " ") }
}

class Cat(val name: String)

// Prints:
// > Bobo Fluffy Cookie
```

`MutableSet<T>` is a Set with write operations.


### Map

`Map<K, V>` stores key-value pairs where keys are unique, but different keys can be paired with equal values.

```kotlin
fun main() {    
    val cat1 = Cat("Bobo")
    val cat2 = Cat("Fluffy")
    val cat3 = Cat("Cookie")
    val cat4 = cat3 // duplicate reference
    
    val cats = mapOf(0 to cat1, 1 to cat2, 2 to cat3, 3 to cat4)
    cats.forEach { print(it.value.name + " ") }
}

class Cat(val name: String)

// Prints:
// > Bobo Fluffy Cookie Cookie 
```

`MutableMap<K, V>` is a `Map` with write operations, for example, you can add a new key-value pair or update the value associated with the given key.

<a name="8"></a>
## 8) Contol Flow

### if

```kotlin
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```

```kotlin
val max = if (a > b) a else b
```

### when

`when` replaces the switch operator of C-like languages. In the simplest form it looks like this

```kotlin
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { // Note the block
        print("x is neither 1 nor 2")
    }
}
```

### Loops

#### for

```kotlin
for (item in collection) print(item)
```

```kotlin
cats.forEach { print(it.name) }
```

```kotlin
for (i in 1..3) {
    println(i)
}
for (i in 6 downTo 0 step 2) {
    println(i)
}
```

#### while

```kotlin
while (foodCount > 0) {
    foodCount--
}

do {
    val cats = retrieveCats()
} while (cats != null) // cats is accessible here
```

<a name="9"></a>
## 9) Functions

### Function Declarations

```kotlin
fun doubleCats(catCount: Int): Int {
    return 2 * catCount
}
```

### Higher-Order Functions

A higher-order function is a function that takes functions as parameters, or returns a function.

**Lambdas and Anonymous Functions** are functions that are not declared, but are passed as an expression.

##### Lambdas

```kotlin
val sum = { x, y -> x + y }
```

##### it

`it` is the implicit name of a single parameter

```kotlin
cats.forEach { println(it.name) }
```

##### Anonymous Functions

```kotlin
fun(x: Int, y: Int): Int = x + y
```

##### Closures

A lambda expression or anonymous function (as well as a local function and an object expression) can access its closure, i.e. the variables declared in the outer scope. The variables captured in the closure can be modified in the lambda:

```kotlin
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it
}
print(sum)
```

<a name="10"></a>
## 10) Classes and Objects

```kotlin
class Cat1 public constructor (val name: String)

class Cat2 constructor (val name: String)

// If there are no annotations or visibility modifiers we can omit the
// constructor keyword
class Cat3 (val name: String)

```

### Data Classes

We frequently create classes whose main purpose is to hold data. In such a class some standard functionality and utility functions are often mechanically derivable from the data. In Kotlin, this is called a data class and is marked with the data keyword.

```kotlin
data class Cat(val name: String)

val cat = Cat("Fluffy")
```

### Sealed Classes

Sealed classes are used for representing restricted class hierarchies, when a value can have one of the types from a limited set, but cannot have any other type. They are, in a sense, an extension of enum classes: the set of values for an enum type is also restricted, but each enum constant exists only as a single instance, whereas a subclass of a sealed class can have multiple instances which can contain state.

```kotlin
sealed class PetFood
data class DryFood(val name: String): PetFood()
data class CannedFood(val name: String): PetFood()
object FreshFood: PetFood()

...

fun printFood(food: PetFood) {
    when(food) {
        is DryFood -> println("Dry Food")
        is CannedFood -> println("Canned Food")
        is FreshFood -> println("Fresh Food")
    }
}
```

### Inheritance

All classes in Kotlin inherit from `Any`, which provides 3 out-of-the-box methods: `equals()`, `hashCode()` and `toString()`.

To create a superclass use the open keyword:

```kotlin
open class Pet(val petName: String) {
    open fun feed() {
        println("Feeding pet...")
    }
}

class Cat(val catName: String): Pet(catName) {
    override fun feed() {
        println("Feeding pet fish...")
    }
}
```

### Abstract Classes

A class and its members may be declared abstract. An abstract member does not have an implementation in its class. We do not need to annotate an abstract class with `open`.

```kotlin
abstract class Pet(val petName: String) {
	abstract fun feed()
}

class Cat(val catName: String): Pet(catName) {
    override fun feed() {
        println("Feeding pet fish...")
    }
}
```

### Companion Objects

An object declaration inside a class can be marked with the companion keyword.

Members of the companion object can be called by using simply the class name as the qualifier:



```kotlin
class Cat{
    companion object {
        fun create(): Cat = Cat()
    }
}

val cat = Cat.create()
```

<a name="11"></a>
## 11) Interfaces and Extensions

### Interfaces

Interfaces in Kotlin can contain method declarations, as well as method implementations. What makes them different from abstract classes is that interfaces cannot store state.

```kotlin
interface Pet {
    // Interfaces can have properties but these need to be abstract or 
    // to provide accessor implementations.
    val name: String

    fun description() {
        print("My pet's name is: $name")
    }
    
    fun feed()
}

class Cat(name: String): Pet {
    override val name: String = name
    
    fun feed() {
        println("Your cat $name has been fed")
    }
}

...

val cat = Cat("Fluffy")

println(cat.name)
cat.feed()
cat.description()

// Prints:
// > Fluffy
// > Your cat Fluffy has been fed
// > Your pet's name is Fluffy
```

### Extensions

Kotlin provides the ability to extend a class with new functionality without having to inherit from the class. Extensions allow you to write new functions for a class. This is helpful when, for example, you want to add a function to one of the classes from a third-party library, but don't have access to the souce code.

Extension functions are called in the usual way as if they were methods of the original class.  

```kotlin
fun main() { 
    val cat = Cat("Fluffy")
    cat.play()
}

class Cat(val name: String)

// To declare an extension function, we need to prefix its name with the type being extended
fun Cat.play() {
    val toys = listOf("ball", "mouse", "laser", "bell")
    
    val selectedToy = toys.random()
    println("Your cat is playing with $selectedToy")
}

```

<a name="12"></a>
## 12) Additional Resources

1. [Kotlin Playground](https://play.kotlinlang.org/)
2. [Kotlin Docs](https://kotlinlang.org/docs/reference/)
3. [Kotlin Koans](https://kotlinlang.org/docs/tutorials/koans.html)
4. [Kotlin Style Guide](https://developer.android.com/kotlin/style-guide)

<a name="13"></a>
## 13) In Class Assignment

Create an employee management system in your playground that does the following:

1) An Employee class, where each employee has an id, a name, an email address (an optional as not every employee has one), an employee type (ie. "contract", or "full time" etc.) and an employee status (ie. "active" or "inactive")

2) An EmployeeManager class, that can do the following:
    * Maintain a record of employees (can add new employees, and remove old ones)
    * Get the name of an employee, given an id
    * Get the email address of an employee, given an id
    * Get the employee type of an employee, given an id
    * Get the employee status of an employee, given an id
    * Print a list of the names of all employees

3) Test your implementation by: 
    * adding at least 5 Employees to your EmployeeManager
    * print a list of all employee names
    * call each EmployeeManager "get method" at least once
    * remove 3 Employees from the EmployeeManager
    * print a new list of all employee names

### Assignment Learning Outcomes

* Student can apply the correct Kotlin syntax
* Student can apply the correct Kotlin style (proper indentation etc.)
* Student can apply Kotlin types correctly
* Student code can fulfill the above outlined expectations (ie. your code works)
