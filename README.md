# Effective-CPP
## ***Item2: Prefer consts, enums, and inlines to #defines***
<font size="3">
->Prefer the compiler to the preproces-sor.

```cpp
#define ASPECT_RATIO 1.653
// the symbolic name ASPECT_RATIO may never be seen by compilers;the name ASPECT_RATIO may not be in the symbol table

// replace the macro with a constant
const double AspectRatio = 1.653;
```
### constant pointers
```cpp
const char * const authorName = "Scott Meyers";
```
### class-specific constants
```cpp
class GamePlayer {
private:
    static const int NumTurns = 5; // constant declaration
    int scores[NumTurns]; // use of constant
};
const int GamePlayer::NumTurns; // definition of NumTurns

//the values of an enumerated type can be used where ints are expected
class GamePlayer {
private:
    enum { NumTurns = 5 }; // “the enum hack” — makes 
                           // NumTurns a symbolic name for 5
    int scores[NumTurns]; // fine
};
```
&emsp;&emsp;in-class initialization is allowed only for integral types and only for constants.
```cpp
class CostEstimate {
private:
    static const double FudgeFactor; // declaration of static class constant; goes in header file
};
const double CostEstimate::FudgeFactor = 1.35; //definition of static class constant; goes in impl. file
```
### drawbacks of macro
```cpp
#define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))
int a = 5, b = 0;
CALL_WITH_MAX(++a, b); //a is incremented twice
CALL_WITH_MAX(++a, b+10); //a is incremented once

// replace with template inline function
template<typename T>
inline void callWithMax(const T &a, const T &b)
{
  f(a > b ? a : b);
}
```
### <font color="#dd0000">**Things to Remember**</font>

✦For simple constants, prefer const objects or enums to #defines.

✦For function-like macros, prefer inline functions to #defines.
  
## ***Item3: Use const whenever possible.***

&emsp;&emsp;specify a semantic constraint — a particular object should not be modified and compilers will enforce that constraint.

### constant pointers
```cpp
int a = 1,b = 2;
// 指针常量
int* const p = &a; // const pointer,non-const data
*p = 3; // success  
p = &b; // fail

//常量指针
const int* p1 = &a; // non-const pointer, const data, = int const* p1
*p1 = 3;//fail
p1 = &b;//success

const int* const p2 = &a;  // const pointer, const data
*p2 = 3;//fail
p2 = &b;//fail
```
### detect usage errors
```cpp
class UserInt {
	int num;
public:
	UserInt(int n):num(n) {}
	UserInt operator* (const UserInt& a) const {
		return UserInt(num * a.num);
	}
};

int main() {
    UserInt a(2), b(3), c(4);
	a * b = c; // will compile success, but it is an incorrect assignment
    return 0;
}
```
&emsp;&emsp;Declaring operator*’s return value const prevents it.
```cpp
class UserInt {
	...
public:
	const UserInt operator* (const UserInt& a) const;
};

int main() {
    UserInt a(2), b(3), c(4);
	a * b = c; // will compile failed
    return 0;
}
```
### const Member Functions
```cpp
class TextBlock {
	string text;
public:
	TextBlock(string tb):text(tb) {}
	const char& operator[](size_t position) const { 
		return text[position];
	} 
	char& operator[](size_t position) {
		return text[position];
	}
	void print() {
		cout << text << endl;
	}
};

int main() {
	TextBlock tb("Hello");
	cout << tb[0];  // call non-const TextBlock::operator[], read ok
	tb[0] = 'h';  // ok, writing a non-const variable
	tb.print();  // change success, will print "hello"
	const TextBlock ctb("World");
	cout << ctb[0];  // call const TextBlock::operator[], read ok
	ctb[0] = 'w'; // failed, can not write a const variable

    return 0;
}
```
&emsp;&emsp;suppose we have a TextBlock-like class that stores its data as a char* instead of a string, then we create a constant object with a particular value and invoke only const member functionson it, yet we still change its value! 
```cpp
class CTextBlock {
	char* pText;
public:
	CTextBlock(char tb[]): pText(tb) {}
	char& operator[](size_t position) const {
		return pText[position];
	}
	void print() const {
		cout << *pText << endl;
	}
};

int main() {
	char tb[] = "Hello";
	const CTextBlock cctb(tb);
	cctb.print(); // print "H"

	char* pc = &cctb[0];
	*pc = 'J';   // will change cctb variable
	cctb.print(); // print "J"
	return 0;
}
```
&emsp;&emsp;A member function is const if and only if it doesn’t modify any of the object’s data members (excluding those that are static)
```cpp
class CTextBlock {
    char* pText;
    size_t textLength;
    bool lengthisValid;
public:
    CTextBlock(char tb[]): pText(tb), textLength(0), lengthisValid(false) {}

    size_t length() const {
        if (!lengthisValid) {
            textLength = strlen(pText); //error: assignment of member ‘CTextBlock::textLength’ in read-only object
            lengthisValid = true;  //error: assignment of member ‘CTextBlock::lengthisValid in read-only object
        }
        return textLength;
    }
};
```
&emsp;&emsp;So a const member function isn’t allowed to modify any of the non-static data members of the object on which it is invoked.

&emsp;&emsp;But if the implementation of length() insists on bitwise constness. What to do?
Use mutable：mutable frees non-static data members from the constraints of bitwise constness.
```cpp
class CTextBlock {
    ...
    mutable size_t textLength;
    mutable bool lengthisValid;
public:
    ...
    void printLen() const {
        cout << "lengthisValid = " << lengthisValid << endl;
        cout << "textLength = " << textLength << endl;
        return;
    }
};
int main() {
    char tb[] = "Hello";
	const CTextBlock ctb(tb);
    ctb.printLen(); //lengthisValid = 0  textLength = 0
	size_t curLen = ctb.length(); 
	ctb.printLen(); //lengthisValid = 1 textLength = 5
    return 0;
}
```
### Avoiding Duplication in const and Non-const Member Functions
&emsp;&emsp;In the first example in "const Member Functions", suppose that operator[] in TextBlock not only returned a reference to the appropriate character, it also performed several actions. Putting all this in both the const and the non-const
operator[] functions yields code duplication, attendant compilation time, maintenance...
```cpp
class TextBlock {
	...
public:
	const char& operator[](size_t position) const { 
		...//do bounds checking
		...//log access data
		...//verify data integrity
		return text[position];
	} 
	char& operator[](size_t position) {
		...//do bounds checking
		...//log access data
		...//verify data integrity
		return text[position];
	}
};
```
&emsp;&emsp;What you really want to do is implement operator[] functionality once and use it twice. That is, you want to have one version of operator[] call the other one. And that brings us to casting away constness.
```cpp
class TextBlock {
	string text;
public:
	TextBlock(string tb):text(tb) {}
	const char& operator[](size_t position) const { 
		return text[position];
	} 
	char& operator[](size_t position) {
		//use a static_cast: from a nonconst object to a const one
		//use a const_cast: removes const
		return const_cast<char&>(static_cast<const TextBlock&>(*this)[position]);
	}
	void print() const {
		cout << text << endl;
	}
};
int main() {
	TextBlock tb("Hello");
	tb[0] = 'h';  // non-const operator[] call const operator[]. It's OK, change success, will print "hello"
	tb.print();  // change success, will print "hello"
    return 0;
}
```
<font color="#dd0000">**Things to Remember**</font>

✦ Declaring something const helps compilers detect usage errors. const can be applied to objects at any scope, to function parameters and return types, and to member functions as a whole.

✦ Compilers enforce bitwise constness, but you should program using logical constness.

✦ When const and non-const member functions have essentially identical implementations, code duplication can be avoided by having the non-const version call the const version.

## ***Item 4: Make sure that objects are initialized before they’re used.***

### built-in types initialization
&emsp;&emsp;In C++, built-in types are inherited from C, and C has no initial value. When defining a variable, it must be initialized, or its initial value will generally be junk and lead to undefined behavior.

&emsp;&emsp;In general, if you’re in the C part of C++ (see Item 1) and initialization would probably incur a runtime cost, it’s not guaranteed to take place. If you cross into the non-C parts of C++, things sometimes change. This explains why an array (from the C part of C++) isn’t necessarily guaranteed to have its contents initialized, but a vector (from the STL part of C++) is.
```cpp
int arr[2];
vector<int> vec(2);
cout << arr[0] << ' ' << arr[1] << endl;  //no initial value, output two random value
cout << vec[0] << ',' << vec[1] << endl;  //initialized, output 0,0
```

### Class initialization
&emsp;&emsp;The responsibility for initialization falls on constructors. Make sure that each constructor initializes every member of the object. It is important not to confuse assignment with initialization.
```cpp
class ABEntry {
public:
    ABEntry(const string &name, const string &address,const list<PhoneNumber> &phones);
private:
    string theName;
    string theAddress;
    list<PhoneNumber> thePhones;
    int numTimesConsulted;	//built-in types
};

ABEntry::ABEntry(const string &name, const string &address,const list<PhoneNumber> &phones) {
	theName = name;       //these are all assignments
    theAddress = address; //not initializations
    thePhones = phones;
    numTimesConsulted = 0;
}

//use the member initialization list instead of assignments
ABEntry::ABEntry(const string &name, const string &address,const list<PhoneNumber> &phones):
				theName(name),
                theAddress(address),
                thePhones(phones),
                numTimesConsulted(0) //these are now all initializations
{
    //the ctor body is now empty
}
```
### the order in which an object’s data is initialized
&emsp;&emsp;base classes are initialized before derived classes (see also Item 12), and within a class, data members are initialized in the order in which they are declared.

### the order of initialization of non-local static objects
Static objects:
* Local static object
  * a static object inside a function
* Non-local static objects: 
  * global object
  * defines objects in the namespace scope
  * static object declared in class, file scope

```cpp
// a.cpp
class A {
public:
    int a;
};
extern A AA;

// b.cpp
class B {
    B() {
        b = AA.a;
    }
public:
    int b;
}

B BB; // Defining the global variable BB object automatically calls the constructor of class B. The constructor requires the value of member variable a of class AA, but we cannot guarantee that the AA has been initialized.
```
Solution: replace non-local static objects with local static objects.
```cpp
// a.cpp
class A {...};

A& funcA() {      //replace direct accesses to non-local static objects with calls to functions that return references to local static objects
    static A AA; // define and initialize a local static object
    return AA;  //return a reference to it
}

// b.cpp
class B{...};

B::B() {                      
    number = funcA().a;
}

B& funcB() {
    static B BB;
    return BB;
}
```
<font color="#dd0000">**Things to Remember**</font>

✦ Manually initialize objects of built-in type, because C++ only sometimes initializes them itself.

✦ In a constructor, prefer use of the member initialization list to assignment inside the body of the constructor. List data members in
the initialization list in the same order they’re declared in the class.

✦ Avoid initialization order problems across translation units by replacing non-local static objects with local static objects.

## ***Item7: Declare destructors virtual in polymorphic base classes.***

### give the base class a virtual destructor
an example:
```cpp
class TimeKeeper {                             
public:
	TimeKeeper() { cout << "TimeKeeper()" << endl; }
	~TimeKeeper() {cout << "~TimeKeeper()" << endl;}
};

class AtomicClock : public TimeKeeper { 
public:
	AtomicClock() { cout << "AtomicClock()" << endl; }
	~AtomicClock() {cout << "~AtomicClock()" << endl;}
}; 

TimeKeeper* getTimeKeeper() { 
	TimeKeeper* timeKeeper = new AtomicClock; //getTimeKeeper returns a pointer to a derived class object
	return timeKeeper;
}    

int main() {
	TimeKeeper* ptk = getTimeKeeper();
	delete ptk;
}
```
![image_1]

&emsp;&emsp;If a call to getTimeKeeper happened to return
a pointer to an AtomicClock object, the AtomicClock part of the object
(i.e., the data members declared in the AtomicClock class) would probably not be destroyed, nor would the AtomicClock destructor run. This is an excellent way to leak resources, corrupt data structures, and spend a lot of time with a debugger.

&emsp;&emsp;Eliminating the problem is simple: give the base class a virtual destructor. Then deleting a derived class object will do exactly what you want. It will destroy the entire object, including all its derived class parts:
```cpp
class TimeKeeper {                             
public:
	TimeKeeper() { cout << "TimeKeeper()" << endl; }
	virtual ~TimeKeeper() {cout << "~TimeKeeper()" << endl;}
};
```
![image_2]

### virtual functions
&emsp;&emsp;The implementation of virtual functions requires that objects carry information that can be used at runtime to determine which virtual functions should be invoked on the object. This information typically takes the form of a pointer called a vptr (“virtual table pointer”). The
vptr points to an array of function pointers called a vtbl (“virtual table”); each class with virtual functions has an associated vtbl. When a virtual function is invoked on an object, the actual function called is determined by following the object’s vptr to a vtbl and then looking up the appropriate function pointer in the vtbl.
```cpp
class A {                             
public:
	int a;
	virtual void func1() { cout << "A:func()" << endl; }
	virtual ~A(){}
};

class B : public A { 
public:
	int b;
	void func1() { cout << "B:func()" << endl; }
	void func2() {}
	~B(){}
}; 

int main() {
	A* ptk = new B;
	ptk->func1();
	delete ptk;
}
```
The memory layout of object A and B are:

![image_3]

If the class has virtual functions, the size of object will increase, vptr and vtbl need to take up space, so don't declare virtual destructors unnecessarily.

<font color="#dd0000">**Things to Remember**</font>

✦ Polymorphic base classes should declare virtual destructors. If a class has any virtual functions, it should have a virtual destructor.

✦ Classes not designed to be base classes or not designed to be used polymorphically should not declare virtual destructors.





</font> 


[image_1]:data:image/png;base64,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

[image_2]:data:image/png;base64,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

[image_3]:data:image/png;base64,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
