<a id="rule"></a>

# C Secure Coding Rules
[Rule 01. Preprocessor](#rule01)

[Rule 02. Declarations and Initialization (DCL)](#rule02)

[Rule 03. Expressions (EXP)](#rule03)

[Rule 04. Integers (INT)](#rule04)

[Rule 05. Floating Point (FLP)](#rule05)

[Rule 06. Arrays (ARR)](#rule06)

[Rule 07. Characters and Strings (STR)](#rule07)

[Rule 08. Memory Management (MEM)](#rule08)

[Rule 09. Input Output (FIO)](#rule09)

[Rule 10. Environment (ENV)](#rule10)

[Rule 11. Signals (SIG)](#rule11)

[Rule 12. Error Handling (ERR)](#rule12)

[Rule 14. Concurrency (CON)](#rule14)

[Rule 48. Miscellaneous (MSC)](#rule48)

[Rule 51. Microsoft Windows (WIN)](#rule51)

<a id="rule01"></a>
## Rule 01. Preprocessor (PRE)
<table>
<tr>
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td> <td> Compliant Solution </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/PRE30-C.+Do+not+create+a+universal+character+name+through+concatenation" target="_blank">PRE30-C. Do not create a universal character name through concatenation</a> </td>
<td> If a character sequence that matches the syntax of a universal character name is produced by token concatenation (6.10.3.3), the behavior is undefined. In general, avoid universal character names in identifiers unless absolutely necessary. </td>
<td>

```c
#define assign(uc1, uc2, val) uc1##uc2 = val
void func(void) {
  int \u0401;
  assign(\u04, 01, 4);
}
```
</td>
<td>

```c
#define assign(ucn, val) ucn = val
void func(void) {
  int \u0401;
  assign(\u0401, 4);
}
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/PRE31-C.+Avoid+side+effects+in+arguments+to+unsafe+macros" target="_blank">PRE31-C. Avoid side effects in arguments to unsafe macros</a> </td>
<td> Never invoke an unsafe macro with arguments containing an assignment, increment, decrement, volatile access, input/output, or other expressions with side effects (including function calls, which may cause side effects). it is recommended that the creation of unsafe function-like macros be avoided. (See PRE00-C. Prefer inline or static functions to function-like macros.) </td>
<td>

```c
#define ABS(x) (((x) < 0) ? -(x) : (x))
void func(int n) {
  int m = ABS(++n);
}
```
</td>
<td>

```c

#define ABS(x) (((x) < 0) ? -(x) : (x)) /* UNSAFE */
void func(int n) {
  ++n;
  int m = ABS(n);
}
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/PRE32-C.+Do+not+use+preprocessor+directives+in+invocations+of+function-like+macros" target="_blank">PRE32-C. Do not use preprocessor directives in invocations of function-like macros</a> </td>
<td> The arguments to a macro must not include preprocessor directives, such as #define, #ifdef, and #include. </td>
<td>

```c
char *dest, *src;
/* malloc() destination string */
memcpy(dest, src,
#ifdef PLATFORM1
    12
#else
    24
#endif
);
```
</td>
<td>

```c
char *dest, *src;
/* malloc() destination string */ 
#ifdef PLATFORM1
memcpy(dest, src, 12);
#else
memcpy(dest, src, 24);
#endif
```
</td>
</tr>
</table>

[Back to top](#rule)

<a id="rule02"></a>
## Rule 02. Declarations and Initialization (DCL)
<table>
<tr>
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td> <td> Compliant Solution </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL30-C.+Declare+objects+with+appropriate+storage+durations" target="_blank">DCL30-C. Declare objects with appropriate storage durations</a> </td>
<td> if an object is referred to outside of its lifetime, the behavior is undefined. The value of a pointer becomes indeterminate when the object it points to reaches the end of its lifetime. </td>
<td>  </td>
<td>  </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL31-C.+Declare+identifiers+before+using+them" target="_blank">DCL31-C. Declare identifiers before using them</a> </td>
<td> The C11 Standard requires type specifiers and forbids implicit function declarations. </td>
<td>  </td>
<td>  </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL36-C.+Do+not+declare+an+identifier+with+conflicting+linkage+classifications" target="_blank">DCL36-C. Do not declare an identifier with conflicting linkage classifications</a> </td>
<td> Use of an identifier (within one translation unit) classified as both internally and externally linked is undefined behavior. (See also undefined behavior 8.) A translation unit includes the source file together with its headers and all source files included via the preprocessing directive #include. </td>
<td>  </td>
<td>  </td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL37-C.+Do+not+declare+or+define+a+reserved+identifier" target="_blank">DCL37-C. Do not declare or define a reserved identifier</a> </td>
<td> Reserved identifiers for implementation include:<br>
Begin with __ or _ followed by an uppercase letter are reserved for any use<br>
Begin with _ are reserved for file scope </td>
<td>  </td>
<td>  </td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL38-C.+Use+the+correct+syntax+when+declaring+a+flexible+array+member" target="_blank">DCL38-C. Use the correct syntax when declaring a flexible array member</a> </td>
<td> Structures with a flexible array member can be used to produce code with defined behavior. However, some restrictions apply:<br>
1.The incomplete array type must be the last element within the structure.<br>
2.There cannot be an array of structures that contain a flexible array member.<br>
3.Structures that contain a flexible array member cannot be used as a member of another structure.<br>
4.The structure must contain at least one named member in addition to the flexible array member. </td>
<td> 

```c
struct flexArrayStruct {
  int num;
  int data[1];
};
```  
</td>
<td>

```c
struct flexArrayStruct {
  int num;
  int data[];
};
``` 
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL39-C.+Avoid+information+leakage+when+passing+a+structure+across+a+trust+boundary" target="_blank">DCL39-C. Avoid information leakage when passing a structure across a trust boundary</a> </td>
<td> When passing a pointer to a structure across a trust boundary to a different trusted domain, the programmer must ensure that the padding bytes and bit-field storage unit padding bits of such a structure do not contain sensitive information. </td>
<td>

```c
struct test {
  int a;
  char b;
  int c;
};
```
</td>
<td>

```c
struct test {
  int a;
  char b;
  char padding_1, padding_2, padding_3;
  int c;
};
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL40-C.+Do+not+create+incompatible+declarations+of+the+same+function+or+object" target="_blank">DCL40-C. Do not create incompatible declarations of the same function or object</a> </td>
<td> Two or more incompatible declarations of the same function or object must not appear in the same program because they result in undefined behavior. </td>
<td>

```c
/* In a.c */
extern int i;
/* In b.c */
short i;
```
</td>
<td>

```c
/* In a.c */
extern int i;
/* In b.c */
int i;
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/DCL41-C.+Do+not+declare+variables+inside+a+switch+statement+before+the+first+case+label" target="_blank">DCL41-C. Do not declare variables inside a switch statement before the first case label</a> </td>
<td> If a programmer declares variables, initializes them before the first case statement, and then tries to use them inside any of the case statements, those variables will have scope inside the switch block but will not be initialized and will consequently contain indeterminate values. </td>
<td>

```c
void func(int expr) {
  switch (expr) {
    int i = 4;
  case 0:
    i = 17;
    /* Falls through into default code */
  default:
    printf("%d\n", i);
  }
}
```
</td>
<td>

```c
void func(int expr) {
  int i = 4;
  switch (expr) {
  case 0:
    i = 17;
    /* Falls through into default code */
  default:
    printf("%d\n", i);
  }
}
```
</td>
</tr>
</table>

[Back to top](#rule)

<a id="rule03"></a>
## Rule 03. Expressions (EXP)
<table>
<tr>
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td><td> Compliant Solution </td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP30-C.+Do+not+depend+on+the+order+of+evaluation+for+side+effects" target="_blank">EXP30-C. Do not depend on the order of evaluation for side effects</a></td>
<td> the order of evaluation for function call arguments is unspecified and can happen in any order.<br>1.Function arguments are evaluated in an indefinite order<br>2.Some scenarios for increment/decrement use </td>
<td>

```c
void f(int i) {
  func(i++, i);
}
```
</td>
<td>

```c
void f(int i) {
  int j = i++;
  func(j, i);
}
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP32-C.+Do+not+access+a+volatile+object+through+a+nonvolatile+reference" target="_blank">EXP32-C. Do not access a volatile object through a nonvolatile reference</a> </td>
<td> If an attempt is made to refer to an object defined with a volatile-qualified type through use of an lvalue with non-volatile-qualified type, the behavior is undefined. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP33-C.+Do+not+read+uninitialized+memory" target="_blank">EXP33-C. Do not read uninitialized memory</a> </td>
<td> When local, automatic variables are stored on the program stack, for example, their values default to whichever values are currently stored in stack memory.Additionally, some dynamic memory allocation functions do not initialize the contents of the memory they allocate. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP34-C.+Do+not+dereference+null+pointers" target="_blank">EXP34-C. Do not dereference null pointers</a> </td>
<td> Dereferencing a null pointer is undefined behavior. </td>
<td></td>
<td> ensure that the pointer is not null when used. </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP35-C.+Do+not+modify+objects+with+temporary+lifetime" target="_blank">EXP35-C. Do not modify objects with temporary lifetime</a> </td>
<td> Do not attempt to call a function and then access or modify its array return value in the same statement. Its array has temporary lifetime, and modifying the array is undefined behavior in both C99 and C11. </td>
<td>

```c
struct X { int a[6]; };
struct X addressee(void) {
  struct X result = { { 1, 2, 3, 4, 5, 6 } };
  return result;
}
int main(void) {
  printf("%x", ++(addressee().a[0]));
  return 0;
}
```
</td>
<td>

```c
struct X { int a[6]; };
struct X addressee(void) {
  struct X result = { { 1, 2, 3, 4, 5, 6 } };
  return result;
}
int main(void) {
  struct X my_x = addressee();
  printf("%x", ++(my_x.a[0]));
  return 0;
}
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP36-C.+Do+not+cast+pointers+into+more+strictly+aligned+pointer+types" target="_blank">EXP36-C. Do not cast pointers into more strictly aligned pointer types</a> </td>
<td> Different alignments are possible for different types of objects. If the type-checking system is overridden by an explicit cast or the pointer is converted to a void pointer (void *) and then to a different type, the alignment of an object may be changed. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP37-C.+Call+functions+with+the+correct+number+and+type+of+arguments" target="_blank">EXP37-C. Call functions with the correct number and type of arguments</a> </td>
<td> Do not call a function with the wrong number or type of arguments. Call functions as intended by the API. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP39-C.+Do+not+access+a+variable+through+a+pointer+of+an+incompatible+type" target="_blank">EXP39-C. Do not access a variable through a pointer of an incompatible type</a> </td>
<td> Modifying a variable through a pointer of an incompatible type (other than unsigned char) can lead to unpredictable results. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP40-C.+Do+not+modify+constant+objects" target="_blank">EXP40-C. Do not modify constant objects</a> </td>
<td> If an attempt is made to modify an object defined with a const-qualified type through use of an lvalue with non-const-qualified type, the behavior is undefined. </td>
<td>

```c
const int **ipp;
int *ip;
const int i = 42;
 
ipp = &ip; /* warning, Constraint violation */
*ipp = &i; /* Valid */
*ip = 0;   /* Modifies constant i (was 42) */
```
</td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP42-C.+Do+not+compare+padding+data" target="_blank">EXP42-C. Do not compare padding data</a> </td>
<td> Padding values are unspecified, attempting a byte-by-byte comparison between structures can lead to incorrect results </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP43-C.+Avoid+undefined+behavior+when+using+restrict-qualified+pointers" target="_blank">EXP43-C. Avoid undefined behavior when using restrict-qualified pointers</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP44-C.+Do+not+rely+on+side+effects+in+operands+to+sizeof%2C+_Alignof%2C+or+_Generic" target="_blank">EXP44-C. Do not rely on side effects in operands to sizeof, _Alignof, or _Generic</a> </td>
<td>The sizeof operator yields the size (in bytes) of its operand, which may be an expression or the parenthesized name of a type.  In most cases, the operand is not evaluated. </td>
<td>

```c

int a = 14;
int b = sizeof(a++);
printf("%d, %d\n", a, b); //output 14, 4
```
</td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP45-C.+Do+not+perform+assignments+in+selection+statements" target="_blank">EXP45-C. Do not perform assignments in selection statements</a> </td>
<td> Do not use the assignment operator in the if/while/for... statements because doing so typically indicates programmer error and can result in unexpected behavior. </td>
<td>

```c
if (a = b) {
  /* ... */
}
```
</td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP46-C.+Do+not+use+a+bitwise+operator+with+a+Boolean-like+operand" target="_blank">EXP46-C. Do not use a bitwise operator with a Boolean-like operand</a> </td>
<td> </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP47-C.+Do+not+call+va_arg+with+an+argument+of+the+incorrect+type" target="_blank">EXP47-C. Do not call va_arg with an argument of the incorrect type</a> </td>
<td> </td>
<td></td>
<td></td>
</tr>

</table>

[Back to top](#rule)

<a id="rule04"></a>
## Rule 04. Integers (INT)
<table>
<tr>
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td> <td> Compliant Solution </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/INT30-C.+Ensure+that+unsigned+integer+operations+do+not+wrap" target="_blank">INT30-C. Ensure that unsigned integer operations do not wrap</a> </td>
<td> Unsigned integer operations can wrap if the resulting value cannot be represented by the underlying representation of the integer. </td>
<td>

```c
void func(unsigned int ui_a, unsigned int ui_b) {
  unsigned int usum = ui_a + ui_b;
  /* ... */
}
```
</td>
<td>

```c
void func(unsigned int ui_a, unsigned int ui_b) {
  unsigned int usum;
  if (UINT_MAX - ui_a < ui_b) {
    /* Handle error */
  } else {
    usum = ui_a + ui_b;
  }
}
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/INT31-C.+Ensure+that+integer+conversions+do+not+result+in+lost+or+misinterpreted+data" target="_blank">INT31-C. Ensure that integer conversions do not result in lost or misinterpreted data</a> </td>
<td> Integer conversions, both implicit and explicit (using a cast), must be guaranteed not to result in lost or misinterpreted data. Converting an integer to a smaller type results in truncation of the high-order bits. </td>
<td>Unsigned int to Signed char;  Signed int to Unsigned int</td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/INT32-C.+Ensure+that+operations+on+signed+integers+do+not+result+in+overflow" target="_blank">INT32-C. Ensure that operations on signed integers do not result in overflow</a> </td>
<td>Signed integer overflow is undefined behavior. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/INT33-C.+Ensure+that+division+and+remainder+operations+do+not+result+in+divide-by-zero+errors" target="_blank">INT33-C. Ensure that division and remainder operations do not result in divide-by-zero errors</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/INT34-C.+Do+not+shift+an+expression+by+a+negative+number+of+bits+or+by+greater+than+or+equal+to+the+number+of+bits+that+exist+in+the+operand" target="_blank">INT34-C. Do not shift an expression by a negative number of bits or by greater than or equal to the number of bits that exist in the operand</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/INT35-C.+Use+correct+integer+precisions" target="_blank">INT35-C. Use correct integer precisions</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/INT36-C.+Converting+a+pointer+to+integer+or+integer+to+pointer" target="_blank">INT36-C. Converting a pointer to integer or integer to pointer</a> </td>
<td></td>
<td></td>
<td></td>
</tr>
</table>

[Back to top](#rule)

<a id="rule05"></a>
## Rule 05. Floating Point (FLP) (PRE)
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP30-C.+Do+not+use+floating-point+variables+as+loop+counters" target="_blank">FLP30-C. Do not use floating-point variables as loop counters</a> </td>
<td></td>

</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP32-C.+Prevent+or+detect+domain+and+range+errors+in+math+functions" target="_blank">FLP32-C. Prevent or detect domain and range errors in math functions</a> </td>
<td>A domain error occurs if an input argument is outside the domain over which the mathematical function is defined.</td>

</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP34-C.+Ensure+that+floating-point+conversions+are+within+range+of+the+new+type" target="_blank">FLP34-C. Ensure that floating-point conversions are within range of the new type</a> </td>
<td></td>

</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP36-C.+Preserve+precision+when+converting+integral+values+to+floating-point+type" target="_blank">FLP36-C. Preserve precision when converting integral values to floating-point type</a> </td>
<td></td>

</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP37-C.+Do+not+use+object+representations+to+compare+floating-point+values" target="_blank">FLP37-C. Do not use object representations to compare floating-point values</a> </td>
<td></td>

</tr>
</table>

[Back to top](#rule)

<a id="rule06"></a>
## Rule 06. Arrays (ARR)
<table>
<tr>
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ARR30-C.+Do+not+form+or+use+out-of-bounds+pointers+or+array+subscripts" target="_blank">ARR30-C. Do not form or use out-of-bounds pointers or array subscripts</a> </td>
<td></td>
<td>Out-of-Bounds Pointer, Null Pointer Arithmetic, Using Past-the-End Index</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ARR32-C.+Ensure+size+arguments+for+variable+length+arrays+are+in+a+valid+range" target="_blank">ARR32-C. Ensure size arguments for variable length arrays are in a valid range</a> </td>
<td>ensures the size argument used to allocate vla is in a valid range (between 1 and a programmer-defined maximum); otherwise, it uses an algorithm that relies on dynamic memory allocation. </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ARR36-C.+Do+not+subtract+or+compare+two+pointers+that+do+not+refer+to+the+same+array" target="_blank">ARR36-C. Do not subtract or compare two pointers that do not refer to the same array</a> </td>
<td>When two pointers are subtracted, both must point to elements of the same array object or just one past the last element of the array object; the result is the difference of the subscripts of the two array elements. Otherwise, the operation is undefined behavior.</td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ARR37-C.+Do+not+add+or+subtract+an+integer+to+a+pointer+to+a+non-array+object" target="_blank">ARR37-C. Do not add or subtract an integer to a pointer to a non-array object</a> </td>
<td>Pointer arithmetic must be performed only on pointers that reference elements of array objects.</td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ARR38-C.+Guarantee+that+library+functions+do+not+form+invalid+pointers" target="_blank">ARR38-C. Guarantee that library functions do not form invalid pointers</a> </td>
<td>it is undefined behavior if the "pointer passed to a library function array parameter does not have a value such that all address computations and object accesses are valid."</td>
<td></td>
</tr>
</table>

[Back to top](#rule)

<a id="rule07"></a>
## Rule 07. Characters and Strings (STR)
<table>
<tr>
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td> <td> Compliant Solution </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/STR30-C.+Do+not+attempt+to+modify+string+literals" target="_blank">STR30-C. Do not attempt to modify string literals</a> </td>
<td>Modifying a string literal frequently results in an access violation because string literals are typically stored in read-only memory.</td>
<td>

```c
char *str  = "string literal";
str[0] = 'S';
```
</td>
<td>

```c
char str[]  = "string literal";
str[0] = 'S';
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/STR31-C.+Guarantee+that+storage+for+strings+has+sufficient+space+for+character+data+and+the+null+terminator" target="_blank">STR31-C. Guarantee that storage for strings has sufficient space for character data and the null terminator</a> </td>
<td>Copying data to a buffer that is not large enough to hold that data results in a buffer overflow. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/STR32-C.+Do+not+pass+a+non-null-terminated+character+sequence+to+a+library+function+that+expects+a+string" target="_blank">STR32-C. Do not pass a non-null-terminated character sequence to a library function that expects a string</a> </td>
<td>Passing a character sequence or wide character sequence that is not null-terminated to such a function can result in accessing memory that is outside the bounds of the object. </td>
<td>

```c
char c_str[3] = "abc";
printf("%s\n", c_str);
```
</td>
<td>

```c
char c_str[] = "abc";
printf("%s\n", c_str);
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/STR34-C.+Cast+characters+to+unsigned+char+before+converting+to+larger+integer+sizes" target="_blank">STR34-C. Cast characters to unsigned char before converting to larger integer sizes</a> </td>
<td>Signed character data must be converted to unsigned char before being assigned or converted to a larger signed type.</td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/STR37-C.+Arguments+to+character-handling+functions+must+be+representable+as+an+unsigned+char" target="_blank">STR37-C. Arguments to character-handling functions must be representable as an unsigned char</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/STR38-C.+Do+not+confuse+narrow+and+wide+character+strings+and+functions" target="_blank">STR38-C. Do not confuse narrow and wide character strings and functions</a> </td>
<td>Passing narrow string arguments to wide string functions or wide string arguments to narrow string functions can lead to unexpected and undefined behavior. Scaling problems are likely because of the difference in size between wide and narrow characters. </td>
<td>char/wchar wcsncpy()/strncpy() </td>
<td></td>
</tr>
</table>

[Back to top](#rule)

<a id="rule08"></a>
## Rule 08. Memory Management (MEM)
<table>
<tr>
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td> <td> Compliant Solution </td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MEM30-C.+Do+not+access+freed+memory" target="_blank">MEM30-C. Do not access freed memory</a> </td>
<td>Evaluating a pointer—including dereferencing the pointer, using it as an operand of an arithmetic operation, type casting it, and using it as the right-hand side of an assignment—into memory that has been deallocated by a memory management function is undefined behavior. </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MEM31-C.+Free+dynamically+allocated+memory+when+no+longer+needed" target="_blank">MEM31-C. Free dynamically allocated memory when no longer needed</a> </td>
<td>Before the lifetime of the last pointer that stores the return value of a call to a standard memory allocation function has ended, it must be matched by a call to free() with that pointer value.</td>
<td>malloc();</td>
<td>malloc(); free();</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MEM33-C.++Allocate+and+copy+structures+containing+a+flexible+array+member+dynamically" target="_blank">MEM33-C. Allocate and copy structures containing a flexible array member dynamically</a> </td>
<td>Flexible array structures must:<br>
1.Have dynamic storage duration (be allocated via malloc() or another dynamic allocation function)<br>
2.Be dynamically copied using memcpy() or a similar function and not by assignment<br>
3.When used as an argument to a function, be passed by pointer and not copied by value</td>
<td>

```c
struct flex_array_struct {
  size_t num;
  int data[];
};
struct flex_array_struct flex_struct;
```
</td>
<td>

```c
/* Dynamically allocate memory for the struct */
flex_struct = (struct flex_array_struct *)malloc(sizeof(struct flex_array_struct) + sizeof(int) * array_size);
```
</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MEM34-C.+Only+free+memory+allocated+dynamically" target="_blank">MEM34-C. Only free memory allocated dynamically</a> </td>
<td>Freeing memory that is not allocated dynamically can result in heap corruption and other serious errors. Do not call free() on a pointer other than one returned by a standard memory allocation function, such as malloc(), calloc(), realloc(), or aligned_alloc().</td>
<td></td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MEM35-C.+Allocate+sufficient+memory+for+an+object" target="_blank">MEM35-C. Allocate sufficient memory for an object</a> </td>
<td>The types of integer expressions used as size arguments to malloc(), calloc(), realloc(), or aligned_alloc() must have sufficient range to represent the size of the objects to be stored. If size arguments are incorrect or can be manipulated by an attacker, then a buffer overflow may occur. Incorrect size arguments, inadequate range checking, integer overflow, or truncation can result in the allocation of an inadequately sized buffer.</td>
<td>

```c
struct tm *tmb;
tmb = (struct tm *)malloc(sizeof(tmb)); //sizeof(pointer)
```
</td>
<td>

```c
struct tm *tmb;
tmb = (struct tm *)malloc(sizeof(*tmb)); //sizeof(struct)
```
</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/pages/viewpage.action?pageId=87152255" target="_blank">MEM36-C. Do not modify the alignment of objects by calling realloc()</a> </td>
<td>Do not invoke realloc() to modify the size of allocated objects that have stricter alignment requirements than those guaranteed by malloc(). </td>
<td></td>
<td></td>
</tr>

</table>

[Back to top](#rule)

<a id="rule09"></a>
## Rule 09. Input Output (FIO)
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO30-C.+Exclude+user+input+from+format+strings" target="_blank">Exclude user input from format strings</a> </td>
<td></td>

</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO32-C.+Do+not+perform+operations+on+devices+that+are+only+appropriate+for+files" target="_blank">FIO32-C. Do not perform operations on devices that are only appropriate for files</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO34-C.+Distinguish+between+characters+read+from+a+file+and+EOF+or+WEOF" target="_blank">FIO34-C. Distinguish between characters read from a file and EOF or WEOF</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO37-C.+Do+not+assume+that+fgets%28%29+or+fgetws%28%29+returns+a+nonempty+string+when+successful" target="_blank">FIO37-C. Do not assume that fgets() or fgetws() returns a nonempty string when successful</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO38-C.+Do+not+copy+a+FILE+object" target="_blank">FIO38-C. Do not copy a FILE object</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO39-C.+Do+not+alternately+input+and+output+from+a+stream+without+an+intervening+flush+or+positioning+call" target="_blank">FIO39-C. Do not alternately input and output from a stream without an intervening flush or positioning call</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO40-C.+Reset+strings+on+fgets%28%29++or+fgetws%28%29+failure" target="_blank">FIO40-C. Reset strings on fgets() or fgetws() failure</a> </td>
<td>If either of the C Standard fgets() or fgetws() functions fail, the contents of the array being written is indeterminate.</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO41-C.+Do+not+call+getc%28%29%2C+putc%28%29%2C+getwc%28%29%2C+or+putwc%28%29+with+a+stream+argument+that+has+side+effects" target="_blank">FIO41-C. Do not call getc(), putc(), getwc(), or putwc() with a stream argument that has side effects</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO42-C.+Close+files+when+they+are+no+longer+needed" target="_blank">FIO42-C. Close files when they are no longer needed</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/pages/viewpage.action?pageId=87152071" target="_blank">FIO44-C. Only use values for fsetpos() that are returned from fgetpos()</a> </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/pages/viewpage.action?pageId=87152071" target="_blank">FIO44-C. Only use values for fsetpos() that are returned from fgetpos()</a> </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO45-C.+Avoid+TOCTOU+race+conditions+while+accessing+files" target="_blank">FIO45-C. Avoid TOCTOU race conditions while accessing files</a> </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO46-C.+Do+not+access+a+closed+file" target="_blank">FIO46-C. Do not access a closed file</a> </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FIO47-C.+Use+valid+format+strings" target="_blank">FIO47-C. Use valid format strings</a> </td>
<td></td>
</tr>
</table>

[Back to top](#rule)


<a id="rule10"></a>
## Rule 10. Environment (ENV) 
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ENV30-C.+Do+not+modify+the+object+referenced+by+the+return+value+of+certain+functions" target="_blank">ENV30-C. Do not modify the object referenced by the return value of certain functions</a> </td>
<td>These functions include getenv(), setlocale(), localeconv(), asctime(), and strerror(). In such cases, the function call results must be treated as being const-qualified.</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ENV31-C.+Do+not+rely+on+an+environment+pointer+following+an+operation+that+may+invalidate+it" target="_blank">ENV31-C. Do not rely on an environment pointer following an operation that may invalidate it</a> </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ENV32-C.+All+exit+handlers+must+return+normally" target="_blank">ENV32-C. All exit handlers must return normally</a> </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/pages/viewpage.action?pageId=87152177" target="_blank">ENV33-C. Do not call system()</a> </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ENV34-C.+Do+not+store+pointers+returned+by+certain+functions" target="_blank">ENV34-C. Do not store pointers returned by certain functions</a> </td>
<td></td>
</tr>
</table>

[Back to top](#rule)

<a id="rule11"></a>
## Rule 11. Signals (SIG)
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/SIG30-C.+Call+only+asynchronous-safe+functions+within+signal+handlers" target="_blank">SIG30-C. Call only asynchronous-safe functions within signal handlers</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/SIG31-C.+Do+not+access+shared+objects+in+signal+handlers" target="_blank">SIG31-C. Do not access shared objects in signal handlers</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/SIG34-C.+Do+not+call+signal%28%29+from+within+interruptible+signal+handlers" target="_blank">SIG34-C. Do not call signal() from within interruptible signal handlers</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/SIG35-C.+Do+not+return+from+a+computational+exception+signal+handler" target="_blank">SIG35-C. Do not return from a computational exception signal handler</a>  </td>
<td></td>
</tr>

</table>

[Back to top](#rule)


<a id="rule12"></a>
## Rule 12. Error Handling (ERR)
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ERR30-C.+Take+care+when+reading+errno" target="_blank">ERR30-C. Take care when reading errno</a>  </td>
<td>The value of errno is initialized to zero at program startup, It is meaningful for a program to inspect the contents of errno only after an error might have occurred.</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ERR32-C.+Do+not+rely+on+indeterminate+values+of+errno" target="_blank">ERR32-C. Do not rely on indeterminate values of errno</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ERR33-C.+Detect+and+handle+standard+library+errors" target="_blank">ERR33-C. Detect and handle standard library errors</a>  </td>
<td>Assuming that all calls to such functions will succeed and failing to check the return value for an indication of an error is a dangerous practice that may lead to unexpected or undefined behavior when an error occurs. </td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/ERR34-C.+Detect+errors+when+converting+a+string+to+a+number" target="_blank">ERR34-C. Detect errors when converting a string to a number</a>  </td>
<td></td>
</tr>
</table>

[Back to top](#rule)

<a id="rule14"></a>
## Rule 14. Concurrency (CON)
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>
<tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON30-C.+Clean+up+thread-specific+storage" target="_blank">CON30-C. Clean up thread-specific storage</a>  </td>
<td>The tss_create() function creates a thread-specific storage pointer identified by a key. Threads can allocate thread-specific storage and associate the storage with a key that uniquely identifies the storage by calling the tss_set() function. If not properly freed, this memory may be leaked. Ensure that thread-specific storage is freed.</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON31-C.+Do+not+destroy+a+mutex+while+it+is+locked" target="_blank">CON31-C. Do not destroy a mutex while it is locked</a>  </td>
<td>Don not contains race conditions, do not destroy mutex before it is unlocked. Additionally, and guarantee that lock will be initialized before it is passed to mtx_lock(). </td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON32-C.+Prevent+data+races+when+accessing+bit-fields+from+multiple+threads" target="_blank">CON32-C. Prevent data races when accessing bit-fields from multiple threads</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON33-C.+Avoid+race+conditions+when+using+library+functions" target="_blank">CON33-C. Avoid race conditions when using library functions</a>  </td>
<td>According to the C Standard, the library functions listed in the following table may contain data races when invoked by multiple threads. So, need use the corresponding thread-safe functions if existed. For example, strtok()->strtok_s()</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON34-C.+Declare+objects+shared+between+threads+with+appropriate+storage+durations" target="_blank">CON34-C. Declare objects shared between threads with appropriate storage durations</a>  </td>
<td>Do not access automatic or thread-local objects from a thread other than the one with which the object is associated.</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON35-C.+Avoid+deadlock+by+locking+in+a+predefined+order" target="_blank">CON35-C. Avoid deadlock by locking in a predefined order</a>  </td>
<td>Four conditions are required for deadlock to occur:
1.Mutual exclusion
2.Hold and wait
3.No preemption
4.Circular wait<br>
Deadlock needs all four conditions, so preventing deadlock requires preventing any one of the four conditions. One simple solution is to lock the mutexes in a predefined order, which prevents circular wait.</td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON36-C.+Wrap+functions+that+can+spuriously+wake+up+in+a+loop" target="_blank">CON36-C. Wrap functions that can spuriously wake up in a loop</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON37-C.+Do+not+call+signal%28%29+in+a+multithreaded+program" target="_blank">CON37-C. Do not call signal() in a multithreaded program</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON38-C.+Preserve+thread+safety+and+liveness+when+using+condition+variables" target="_blank">CON38-C. Preserve thread safety and liveness when using condition variables</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON39-C.+Do+not+join+or+detach+a+thread+that+was+previously+joined+or+detached" target="_blank">CON39-C. Do not join or detach a thread that was previously joined or detached</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON40-C.+Do+not+refer+to+an+atomic+variable+twice+in+an+expression" target="_blank">CON40-C. Do not refer to an atomic variable twice in an expression</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON41-C.+Wrap+functions+that+can+fail+spuriously+in+a+loop" target="_blank">CON41-C. Wrap functions that can fail spuriously in a loop</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON42-C.++Don%27t+allow+attackers+to+influence+environment+variables+that+control+concurrency+parameters" target="_blank">CON42-C. Don't allow attackers to influence environment variables that control concurrency parameters</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/CON43-C.+Do+not+allow+data+races+in+multithreaded+code" target="_blank">CON43-C. Do not allow data races in multithreaded code</a>  </td>
<td></td>
</tr>
</table>

[Back to top](#rule)


<a id="rule48"></a>
## Rule 48. Miscellaneous (MSC)
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC30-C.+Do+not+use+the+rand%28%29+function+for+generating+pseudorandom+numbers" target="_blank">MSC30-C. Do not use the rand() function for generating pseudorandom numbers</a>  </td>
<td>The C Standard rand() function makes no guarantees as to the quality of the random sequence produced. The numbers generated by some implementations of rand() have a comparatively short cycle and the numbers can be predictable. Applications that have strong pseudorandom number requirements must use a generator that is known to be sufficient for their needs.</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC32-C.+Properly+seed+pseudorandom+number+generators" target="_blank">MSC32-C. Properly seed pseudorandom number generators</a>  </td>
<td>Calling a PRNG in the same initial state, either without seeding it explicitly or by seeding it with the same value, results in generating the same sequence of random numbers in different runs of the program. The solution is to ensure that the PRNG is always properly seeded. A properly seeded PRNG will generate a different sequence of random numbers each time it is run.</td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC33-C.+Do+not+pass+invalid+data+to+the+asctime%28%29+function" target="_blank">MSC33-C. Do not pass invalid data to the asctime() function</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC37-C.+Ensure+that+control+never+reaches+the+end+of+a+non-void+function" target="_blank">MSC37-C. Ensure that control never reaches the end of a non-void function</a>  </td>
<td>If control reaches the closing curly brace (}) of a non-void function without evaluating a return statement, using the return value of the function call is undefined behavior. </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC38-C.+Do+not+treat+a+predefined+identifier+as+an+object+if+it+might+only+be+implemented+as+a+macro" target="_blank">MSC38-C. Do not treat a predefined identifier as an object if it might only be implemented as a macro</a>  </td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC39-C.+Do+not+call+va_arg%28%29+on+a+va_list+that+has+an+indeterminate+value" target="_blank">MSC39-C. Do not call va_arg() on a va_list that has an indeterminate value</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC40-C.+Do+not+violate+constraints" target="_blank">MSC40-C. Do not violate constraints</a>  </td>
<td></td>
</tr>
<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/MSC41-C.+Never+hard+code+sensitive+information" target="_blank">MSC41-C. Never hard code sensitive information</a>  </td>
<td>Hard coding sensitive information, such as passwords or encryption keys can expose the information to attackers. Anyone who has access to the executable or dynamic library files can examine them for strings or other critical data, revealing the sensitive information. </td>
</tr>
</table>

[Back to top](#rule)

<a id="rule51"></a>
## Rule 51. Microsoft Windows (WIN)
<table>
<tr>
<td> Rule </td> <td> Description </td>
</tr>

<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/WIN30-C.+Properly+pair+allocation+and+deallocation+functions" target="_blank">WIN30-C. Properly pair allocation and deallocation functions</a></td>
<td>malloc()--free()   realloc()--free() ...</td>
</tr>

</table>

[Back to top](#rule)