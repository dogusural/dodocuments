### Special Member functions implementation in composition

```
#include <iostream>
#include <cstring>
class Member
{
    int m_int;

public:
    Member()
    {
        std::cout << "Member default constructor" << std::endl;
    }

    Member(int _m_int) : m_int{_m_int}
    {
        std::cout << "Member argument constructor" << std::endl;
    }

    Member(const Member &other)
    {
        std::cout << "Member copy constructor" << std::endl;
    }

    Member(Member &&other)
    {
        std::cout << "Member move constructor" << std::endl;
    }

    Member &operator=(const Member &other)
    {
        std::cout << "Member assignment operator" << std::endl;
    }

    Member &operator=(Member &&other)
    {
        std::cout << "Member move assignment operator" << std::endl;
    }
};

class Base
{
    int m_int;
    Member m_member;

public:
    Base()
    {
        std::cout << "Base default constructor" << std::endl;
    }
    Base(int _m_int) : m_int{_m_int}, m_member{_m_int + 1}
    {
        std::cout << "Base argument constructor" << std::endl;
    }
    Base(const Base &other) : m_member{other.m_member}
    {
        std::cout << "Base copy constructor" << std::endl;
    }

    Base(Base &&other) : m_member{std::move(other.m_member)}
    {
        std::cout << "Base move constructor" << std::endl;
    }

    Base &operator=(const Base &other)
    {
        this->m_member = other.m_member;
        std::cout << "Base assignment operator" << std::endl;
    }

    Base &operator=(Base &&other)
    {
        this->m_member = std::move(other.m_member);
        std::cout << "Base move assignment operator" << std::endl;
    }
};

int main()
{
    std::cout << "==========================================" << std::endl;
    Base ma{2};
    std::cout << "==========================================" << std::endl;
    Base mb{ma};
    std::cout << "==========================================" << std::endl;
    Base mc = std::move(mb);
    std::cout << "==========================================" << std::endl;
    mc = std::move(ma);
    std::cout << "==========================================" << std::endl;
    ma = mc;
    std::cout << "==========================================" << std::endl;

    return 0;
}
```

