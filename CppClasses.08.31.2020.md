# C++ Classes
# Notes 08.31.2020 - notes

[Lecture video](https://uva.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=7d93c3ee-2998-48df-8caf-ac2400db4067)

## Classes (and how we split things into files)
- Unlike Java, in C++, we split interface and implementation
	- Interface (.h files) -> define method prototypes, class members
	- Implementation (.cpp files) -> define method bodies, etc.
- C++'s "oddball syntax"
```cpp
int main() {
	IntCell m1; // calls the default constructor
	IntCell m2(37); // calls constructor
	m1 = m2; // classes are "like" value types; they have copy semantics
}
```

## Sample header

`IntCell.h`

```cpp
#ifndef INTCELL_H // include guard
#define INTCELL_H 
class IntCell { // classes do not have visibility in C++ (no such thing as a private class)
	public: // define public members
		IntCell(int initialValue = 0); // define default constructor
		// constructors:
		// - share name with class
		// - have no return type
		// - can have default values for their parameters (as above)

		int getValue() const; // const functions don't mutate the object they're called on
		void setValue(int val); // this function does mutate its object

	private: // define private members
		int storedValue;
		int max(int m) const;
}; // note the semicolon at the end of the class
#endif
```

- "Every header file that you create, in your life, EVER" needs the include guard using `#ifndef, #define, and #endif`

## Sample .cpp
```cpp
#include "IntCell.h"
using namespace std;

IntCell::IntCell(int initialValue) : storedValue(initialValue) {
}

int IntCell::getValue() const {
	return storedValue;
}

void IntCell::setValue(int val) {
	storedValue = val;
}

int IntCell::max(int m) const {
	return (m > storedValue) ? m : storedValue;
}
```
Notes:
- Field initialization syntax is useful for const fields (const fields are set at initialization and then cannot be changed)

## 'Rational example' on separation from implementation & interface
- Create .h file with class definition
- Create .cpp file with member function implementations
- Create .cpp file containing main()

## E.g., 'Rational.h'
`Rational.h`
```cpp
#ifndef RATIONAL_H
#define RATIONAL_H

class Rational {
	public:
		Rational();
		~Rational();
		Rational(int numerator, int denominator);
		void print() const;
		Ratioanal times(Rational b) const;
		Rational plus(Rational b) const;
		Rational reciprocal() const;
		Rational divides(Rational b) const;
	private:
		int num, den;
		static int gcd(Rational a, Rational b);
}

#endif RATIONAL_H
```
`Rational.cpp`
```cpp
#include "Rational.h"
#include <iostream>
using namespace std;

// default to 0/1
Rational::Rational() : num(0), den(1) {}

void Rational::print() const {
	if(den == 1) {
		cout << num << endl;
	} else {
		cout << num << "/" << den << endl;
}

Rational Rational::times(Rational b) const {
	return Rational(num * b.num, den * b.den);
}

Rational::Rational(int numerator, int denominator) {
	if (denominator == 0)
		cout << "Denominator is zero." << endl;
	int g = gcd(numerator, denominator);
	num = numerator / g;
	den = denominator / g;
}
```
