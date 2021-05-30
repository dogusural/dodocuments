# Difference in usage between Copy Constructor and Copy Assignment Operator

```
#include <iostream>

using namespace std;

class test_class {
public:
    test_class() {
        printf("constructor called\n");
    };
    test_class(const test_class& other) {
        printf("copy constructor called\n");
    };
    test_class& operator=(const test_class& other) {
        printf("copy assignment operator called\n");
        return *this;
    };
    test_class(test_class&& s)
    {
        printf("move constructor called\n");
    };
    void foo(test_class bar)
    {

    }

    test_class bar()
    {
        test_class b;
        return b;
    }
};



int main()
{
    {
        test_class b; //constructor
        test_class c; //constructor
        b = c; //copy assignment
        c = test_class(b); //copy constructor, then copy assignment
    }

    cout<<"--------------------------------------------------------"<<endl;

    {
        test_class* b = new test_class(); //constructor called
        test_class* c; //nothing is called
        c = b; //still nothing is called
        c = new test_class(*b); //copy constructor is called
    }

    cout<<"--------------------------------------------------------"<<endl;
    {
        test_class b; //constructor
        b.foo(b); //copy constructor called
        test_class a = b.bar(); // constructor called because of Copy elision , Omits copy and move (since C++11) constructors, resulting in zero-copy pass-by-value semantics.

    }

    return 0;
}
```