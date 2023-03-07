## ***Scoped enumerations (enum classes)***
### Two primary differences from Unscoped enumerations
1. strongly typed (they won’t implicitly convert to integers) 
2. strongly scoped (the enumerators are only placed into the scope region of the enumeration)
### Scoped enumerations define their own scope regions
```cpp
#include <iostream>
int main()
{
    enum class Color // "enum class" defines this as a scoped enum rather than an unscoped enum
    {
        red, // red is considered part of Color's scope region
        blue,
    };

    std::cout << red << '\n';        // compile error: red not defined in this scope region
    std::cout << Color::red << '\n'; // compile error: std::cout doesn't know how to print this (will not implicitly convert to int)

    Color color { Color::blue }; // okay

    return 0;
}
```
### Scoped enumerations don’t implicitly convert to integers
```cpp
#include <iostream>
int main()
{
    enum class Color
    {
        red,
        blue,
    };

    Color shirt { Color::red };

    if (shirt == Color::red) // this Color to Color comparison is okay
        std::cout << "The shirt is red!\n";
    else if (shirt == Color::blue)
        std::cout << "The shirt is blue!\n";
    std::cout << shirt << '\n'; // won't work, because there's no implicit conversion to int
    std::cout << static_cast<int>(shirt) << '\n'; // will print 0
    return 0;
}
```
static_cast an integer to a scoped enumerator.
```cpp
#include <iostream>

enum class Pet
{
    cat, // assigned 0
    dog, // assigned 1
    pig, // assigned 2
    whale, // assigned 3
};

int main()
{
    std::cout << "Enter a pet (0=cat, 1=dog, 2=pig, 3=whale): ";

    int input{};
    std::cin >> input; // input an integer

    Pet pet{ static_cast<Pet>(input) }; // static_cast our integer to a Pet

    return 0;
}
```
As of C++17, you can initialize a scoped enumeration using an integral value without the static_cast (and unlike an unscoped enumeration, you don’t need to specify a base).

<font color="#dd0000">**Best practice**</font>

Favor scoped enumerations over unscoped enumerations unless there’s a compelling reason to do otherwise.

### std::underlying_type_t
```cpp
#include <iostream>

enum class Animals
{
    chicken, // 0
    dog, // 1
    cat, // 2
    elephant, // 3
    duck, // 4
    snake, // 5

    maxAnimals,
};

// Overload the unary + operator to convert Animals to the underlying type
// adapted from https://stackoverflow.com/a/42198760, thanks to Pixelchemist for the idea
constexpr auto operator+(Animals a) noexcept
{
    return static_cast<std::underlying_type_t<Animals>>(a);
}

int main()
{
    std::cout << +Animals::elephant << '\n'; // convert Animals::elephant to an integer using unary operator+

    return 0;
}
```