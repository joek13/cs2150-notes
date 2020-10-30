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
