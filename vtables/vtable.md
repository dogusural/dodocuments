## Moving from C to C++

```
struct Foo
{
  char b;
  int a;
}
```

The only real difference between the POD C struct above and C++ class is : C++ member variables are declared private by default.

if we were to print :

```
Foo foo;
std::cout<< sizeof(foo) << std::endl;
```

We would get 8 including the padding, as we also would expect from a C struct. Let's spice things up a little by adding a constructor to our POD struct .

```
struct Foo
{
  Foo():b{'0'},a{0}
  {}
  char b;
  int a;
};
```

## Moving further into C++

Suprisingly, sizeof the new struct is still 8 bytes despite the constructor addition, reason being compiler converts the class functions into free functions by mangling the name and add an implicit first parameter to every function also as known as **this** pointer.

After the name mangling, our constructor roughly becomes

```
xaffdasdFoosadad(Foo *this)
{}
```

Thus turning into a free function which adds no memory overhead to the class it belongs to.

Let's conert our struct to C++ class and add a child class to it.

```
class Foo
{
  char b;
  int a;
  public:
  Foo(char b_ , int a_):b{b_},a{a_}
  {}
  Foo() = default ;

};

class Bar : public Foo
{
  float c;
};
```

Sizes of the each class instances in this case is respectively 8 and 12 as the child class should contain the base class, it's size is sizeof(Foo) + sizeof(float).

Let's add a virtual method to the base class. This is when things will get interesting.

```
class Foo
{
  char b;
  int a;
  public:
  Foo() = default ;
  virtual void foobar()
  {}

}__attribute__((packed));

class Bar : public Foo
{
  float c;
  virtual void foobar() override
  {}
}__attribute__((packed));
```

We turned off memory padding this time to observe the effect of having a virtual base class member effectively.

Sizes of each class Foo is 13 bytes & sizeof class Bar is 17 bytes.

Assuming sizeof (void*) is 8 . To support run-time polymorphism C++ utilizes virtual tables which are basically pointer arrays to the virtual functions in the class. And a virtual table pointer which points to this pointer array. Mechanism is implemented in the following way : In the constructor of each class first thing, the virtual pointer is assigned to the Vtable that class function pointers reside in.

```
#include <iostream>

using std::cout;

class Foo
{
  char b;
  int a;
  public:
  Foo() = default ;
  virtual void foobar()
  {
    cout<< "I am Foo\n";
  }

}__attribute__((packed));

class Bar : public Foo
{
  float c;
  public:
  virtual void foobar() override
  {
    cout<< "I am Bar\n";
  }
}__attribute__((packed));

int main()
{

Bar *bar = new Bar{};
Foo *foo = bar;
bar->foobar();


}
```

Output is : `I am Bar`

Because compiler adds an implicit `void vPtr*` to the member list of the base class. And at the start of each constructor call assign this pointer to the vTable which resides staticly in another region of the memory.

## How vPtr* works

So when a new object of the child class is created, base constructor is invoked first vPtr is assigned to the vTable of the base class. Thereafter child constructor is called, vPtr value is overriden with the value of the child vTable. We can observe this behaviour in the next example.

```
class Foo
{
  char b;
  int a;
  public:
  Foo()
  {
      foobar();
  }
  virtual void foobar()
  {
    cout<< "I am Foo\n";
  }

}__attribute__((packed));

class Bar : public Foo
{
  float c;
  public:
  Bar()
  {
      foobar();
  }
  virtual void foobar() override
  {
    cout<< "I am Bar\n";
  }
}__attribute__((packed));

int main()
{

    Bar *bar = new Bar{};

}
```

Output is :

```
I am Foo
I am Bar
```

Because we are calling the virtual method inside both constructors, the expected behaviour isn't observed.Bar instance is calling the Overriden base method. This is the reason invoking virtual methods inside constructors is not advised.