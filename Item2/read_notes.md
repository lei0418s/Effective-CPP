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