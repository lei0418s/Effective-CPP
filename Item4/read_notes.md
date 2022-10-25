## ***Item 4: Make sure that objects are initialized before they’re used.***
<font size="3">

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
