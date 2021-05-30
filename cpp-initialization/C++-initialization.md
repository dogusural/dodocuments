## Initialization types

---

#### Aggregate initialization

```
struct Alp {

int a,b,c;

};

Alp a {10,20,30};

Alp b = {10,20,30};

int ax[] {10,20,30};

int ay[] = {10,20,30};
```

---

#### Default Initialization

`int x;` 

* You cant default initialize reference,auto and constant types.
* If you default initialize a primitive type with automatic lifetime , it gets garbage value.
* If you default initialize a primitive type with static lifetime , it gets zero initialized.
* if you default initialize a class type, default constructor is called.
* Default constructor that the compiler writes , default initializes the members. (Garbage for primitive types and default constructor for class types)

---

#### Value initialization

`int x{}; `

* All primitive objects gets zero initialized.
  * `int x{} = int x{0}`
  * `int p*{} = int p*{nullptr}`
  * `int a[10]{}; // all members are zero initialized.`

* For class objects , default constructor is called if it is explicitly declared and if no default constructor is declared(compiler generated compiler is used) then all primitive members get value initialized.

  ```
  class car
  {
  int x,y,z;
  public:
  void print()
  {
      cout<<x<<y<<z<<endl;
  }
  };
  
  int main()
  {
      car m_car{};
      m_car.print(); // prints 000
  
      car m_car;
      m_car.print(); // prints garbage
  }
  ```
  
  ---

#### Direct Initialization

```c++
int x(10); (direct initialization)
int x{10}; brace initialization doesnt allow narrowing conversions unlike paranthesis (direct list initialization)
```

---

#### Copy initialization

`int x = 10`

`myclass x = 23`



---



