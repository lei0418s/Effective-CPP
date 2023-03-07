<a id="rule"></a>

# C Secure Coding Rules
[Rule 01. Preprocessor](#rule01)

[Rule 02. Declarations and Initialization (DCL)](#rule02)

[Rule 03. Expressions (EXP)](#rule03)

[Rule 04. Integers (INT)](#rule04)

[Rule 05. Floating Point (FLP)](#rule05)

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
<td> Description </td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/EXP47-C.+Do+not+call+va_arg+with+an+argument+of+the+incorrect+type" target="_blank">EXP47-C. Do not call va_arg with an argument of the incorrect type</a> </td>
<td> Description </td>
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
<td> Rule </td> <td> Description </td><td> Noncompliant Code Example </td> <td> Compliant Solution </td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP30-C.+Do+not+use+floating-point+variables+as+loop+counters" target="_blank">FLP30-C. Do not use floating-point variables as loop counters</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP32-C.+Prevent+or+detect+domain+and+range+errors+in+math+functions" target="_blank">FLP32-C. Prevent or detect domain and range errors in math functions</a> </td>
<td>A domain error occurs if an input argument is outside the domain over which the mathematical function is defined.</td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP34-C.+Ensure+that+floating-point+conversions+are+within+range+of+the+new+type" target="_blank">FLP34-C. Ensure that floating-point conversions are within range of the new type</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP36-C.+Preserve+precision+when+converting+integral+values+to+floating-point+type" target="_blank">FLP36-C. Preserve precision when converting integral values to floating-point type</a> </td>
<td></td>
<td></td>
<td></td>
</tr>

<tr>
<td> <a href= "https://wiki.sei.cmu.edu/confluence/display/c/FLP37-C.+Do+not+use+object+representations+to+compare+floating-point+values" target="_blank">FLP37-C. Do not use object representations to compare floating-point values</a> </td>
<td></td>
<td></td>
<td></td>
</tr>
</table>

[Back to top](#rule)
