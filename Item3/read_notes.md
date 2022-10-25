## ***Item3: Use const whenever possible.***
<font size="3">
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
