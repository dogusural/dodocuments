# Copy Elision in action

```
#include <iostream>
#include <string>

using namespace std;

class Dodo
{
public:
    Dodo()
    {
        cout << "Default Constructor for " << this << std::endl;
    }
    Dodo(const Dodo &)
    {
        cout << "Copy Constructor for " << this << std::endl;
    }
    Dodo &operator=(const Dodo &)
    {
        cout << "Copy assignment Constructor for " << this << std::endl;
        return *this;
    }
    Dodo(Dodo &&)
    {
        cout << "Move Constructor for " << this << std::endl;
    }
    Dodo operator=(const Dodo &&)
    {
        cout << "Move Assignment Operator for " << this << std::endl;
        return *this;
    }
    ~Dodo()
    {
        cout << "Destructor for " << this << std::endl;
    }

private:
};

void CE(Dodo ref)
{
    Dodo dodo = std::move(ref);
}

Dodo RVO()
{
    return Dodo{};
}

Dodo NRVO()
{
    Dodo dodo{};
    return dodo;
}

int main()
{

    cout << "=================================" << std::endl;
    {
        Dodo ldodo{}; // Case 1 : a local variable with automatic lifetime is passed by value to a function resulting in unnecessary copy. Constructor calling sequence: 1) Default Constructor for 0x7ffdac766746
                      //                                                                                                                                                 2) Copy Constructor for 0x7ffdac766747
                      //                                                                                                                                                 3) Move Constructor for 0x7ffdac766727
        CE(ldodo);
    }
    cout << "=================================" << std::endl;
    {
        CE(Dodo{}); // Case 1 : a temporary variable is passed by value to a function resulting in "Copy Elision" to avoid unnecessary copy. Constructor calling sequence: 1) Default Constructor for 0x7ffdac766747
                    //                                                                                                                                                     2) Move Constructor for 0x7ffdac766727
                    // This behaivor is mandated by compilers since c++17
    }
    cout << "=================================" << std::endl;
    {
        Dodo ldodo = RVO(); // Case 1 : a temporary variable is returned by value from a function resulting in "Return Value Optimization" to avoid unnecessary copy. Constructor calling sequence: 1) Default Constructor for 0x7ffdac766747
                            // This behaivor is mandated by compilers since c++17
    }
    cout << "=================================" << std::endl;

    {
        Dodo ldodo = NRVO(); // Case 1 : a temporary variable is returned by value from a function resulting in "Named Return Value Optimization" to avoid unnecessary copy. Constructor calling sequence: 1) Default Constructor for 0x7ffdac766747
                             // This behaivor is not mandated by compilers and it is still optional, try changing the optimizations levels to see different effects. On g++ (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0 even with the optimization level 0 (-O0). The Copy elision is performed 

    }
    cout << "=================================" << std::endl;
}
```

