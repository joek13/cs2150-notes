# AdvancedCpp
# Notes 10.29.2020 - notes

## C++ Inheritance
Specifies hierarchical relationships between types

```cpp
class Name {
	public:
		Name(void) : myName("") {}
		~Name(void) {}
		void SetName(string theName) {
			myName = theName;
		}
		void print(void) {
			cout << myName << endl;
		}

	private:
		string myName;
}

class Contact : public Name {
	public:
		Contact(void) {
			myAddress = ""
		}
		~Contact(void) {}
		void SetAddress(string theAddress) {
			myAddress = theAddress;
		}
		void print(void) {
			Name::print();
			cout << myAddress << endl;
		}

	private:
		string myAddress;
}
```

Constructing a subclass:
- Superclass constructor called first
- Then subclass constructor

Destructing a subclass:
- Subclass destructor called first
- Then superclass destructor

Constructors are *top-down*, destructors are *bottom-up*

### Visibility of inheritance

i.e.,
```
class Contact : public Name { /* … */ } // public could be any visibility modifier
```
If we had declared Contact as a `protected` subclass of `Name`, then all of the `public` members of `Name` would become `protected` members in `Contact`

Useful for *encapsulating* functionality from superclasses. Maybe the set of functionality for a subclass needs to be limited

### Problems with single inheritance
`void insert(const Comparable &x, BinaryNode * & t)`
- What if the parameter `x`, the element to be inserted, is a non-primitive type
  - Sphere (which should inherit from Shape)
  - BigInteger (which could inherit from BigNumber)
  - etc., etc.,
- What if it also needs to be Serializable?

How we would save this in Java (single inheritance):
- Sphere extends Shape, implements Comparable

C++ Doesn't Have Interfaces.
C++ Has Multiple Inheritance.

```
class Sphere : public Shape, public Comparable, public Serializable {
  // …
}
```

## Static vs. Dynamic Dispatch
- Static dispatch
  - Decision on which member function to invoke made using compile-time type of an object
- Dynamic dispatch
  - Decision on which member function to invoke made using runtime type of an object

```cpp
class Person {
	public:
		// ...
		void print(ostream &out = cout);
		// ...
};

class Student : public Person {
	public:
		// ...
		void print(ostream &out = cout);
		// ...
};
```

## Static Dispatch examples
```cpp
Person *p;
if(user_input)
  p = new Student();
else
  p = new Person();
p->print(); // which method needs to be invoked? Impossible to determine at compile-time.
```
In the name of **Ruthless Efficiency**, C++ will *always* prefer static dispatch. So, in the above example, `Person::print` will *always* be called, because the pointer is of type `Person`.

```
Student s(123456789, "Jane", 4.0);
Person &p = s;
s.print(); // Student::print()
p.print(); // Person::print()
```
Different from Java: notice that the actual type of the object / reference dictates which method gets called

## Dynamic dispatch
Uses `virutal` keyword

```cpp
class Person {
  public:
    virtual void print(ostream &out = cout);
};
```
Marking methods as `virtual` enables dynamic dispatch

### Dynamic dispatch overhead
- Incurs runtime overhead
  - Program must maintain type information in addition to pointers
  - Compiler must generate code to determine which member function to invoke

Dynamic dispatch is the *norm* in languages like Java

*N.B.*: base class destructor should probably be virtual

### Dynamic dispatch internals - virtual method tables
Each object contains a pointer to the virtual method table
Virtual method table contains addresses of the methods
- Any virtual method must follow pointer to the object (first pointer dereference)
- Follow the virtual method table pointer (second pointer dereferences)
- Lookup the method pointer
- Jump to that method (third pointer dereference)

Each instance of a class contains a VMT.
VMT pointers are implicitly updated every time your class is constructed

Constructing `Person` sets the VMT for `print` to some address
Constructing `Student` first calls `Person()`, then updates the VMT again to some new address

## Abstract classes
```cpp
class shape { // convention: lowercase for abstract classes
  public:
    virtual void draw() = 0;
};
```
Must be virtual!
`draw()` is called a "pure virtual function/method"
- Has no body definition in the .cpp file
- And thus this class can not be instantiated
Subclasses would define their own method bodies for `draw`

# Problems with Inheritance
### Replicated and shared inheritance

```
gp_list_node   person   gp_list_node
        \     /      \    /
        student     professor
          \            /
           student_prof
```
`student_prof` should only have one `name`. Its inheritance from `person` should be *shared*
`student_prof` probably wants *two* `next` fields, since it belongs to two lists (student list and professor list). Its inheritance from `gp_list_node` should be *replicated*

In C++, replicated is the default. Shared inheritance can be specified by using the keyword `virtual`

### Types of multiple inheritance
1. Shared
2. Replicated
3. Non-replicated
  - Does not allow common ancestors, categorically
4. Mix-in
  - "Fake" multiple inheritance using interfaces or traits

## C++11 Standard
### Move constructors
