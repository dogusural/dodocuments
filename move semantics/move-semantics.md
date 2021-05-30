

## Move Semantics

A little example describing basic move semantic principles.

```c++
#include <iostream>
#include <cstring>
#include <vector>

 using std::cout;
 using std::endl;


class name
{
char *m_name;
size_t m_name_size;
public:
name() = delete;
 name(const std::string &string_name)
{
    cout<<"String constructor is called for "<< this<<endl;
    m_name_size = string_name.size();
    m_name = new char[m_name_size];
    memcpy(m_name,string_name.c_str(),m_name_size);
}

 name(const char* char_name)
{
    cout<<"Char* constructor is called for "<< this<<endl;
    m_name_size = strlen(char_name);
    m_name = new char[m_name_size];
    memcpy(m_name,char_name,m_name_size);
}
name& operator= (const name& other)
{
    cout<<"Copy assignment operator is called for "<< this<<endl;
    delete[] (*this).m_name;
    (*this).m_name_size = 0;
    (*this).m_name_size = other.m_name_size;
    (*this).m_name = new char[m_name_size];
    memcpy((*this).m_name,other.m_name,(*this).m_name_size);

    return *this;
} 
name(const name& _name)
{
    cout<<"Copy constructor is called for "<< this<<endl;
    m_name_size = _name.m_name_size;
    m_name = new char[m_name_size];
    memcpy(m_name,_name.m_name,m_name_size);
}

name(name&& _name)
{
    cout<<"Move constructor is called for "<< this<<endl;
    m_name_size = _name.m_name_size;
    m_name = _name.m_name;
    _name.m_name = nullptr;
    _name.m_name_size = 0;
}

~name()
{
    cout<<"Destructor is called for "<< this <<endl;
    delete[] m_name;
}

friend std::ostream& operator<<(std::ostream&, const name&);

};

std::ostream& operator<<(std::ostream& os, const name& n)
{
    os << n.m_name;
    return os;
}

class car
{
name m_name;
public:
 car(const name &_name):m_name{_name}
{
    cout<<"car const name constructor is called.\n";
}
 car(name && _name):m_name{std::move(_name)}
{
    cout<<"name && constructor is called.\n";
}
void print_car_brand() const
{
    cout<<m_name<<endl;
}

car& set_car_brand(const name &_name)
{
    (*this).m_name = _name;
    return *this;
}

};
int main()
{

cout << "========================================"<<endl;


std::vector<name> names;
std::vector<car> cars;
cars.reserve(10);
names.reserve(10);

name bmw{"bmw"};
names.push_back(std::move(bmw));
cout << "========================================"<<endl;
names.push_back(std::string("mercedes"));
cout << "========================================"<<endl;
names.push_back({"audi"});
cout << "========================================"<<endl;
name vw{"volkswagen"};
names.push_back(vw);
cout << "========================================"<<endl;

for(const auto& value: names) {
    std::cout << value << "\n";
}
cout << "========================================"<<endl;
cout << "========================================"<<endl;
cout << "========================================"<<endl;

for(auto& value: names) {
    cars.push_back(std::move(value));
    cout << "========================================"<<endl;
}
cars.at(0).print_car_brand();
cars.at(0) = cars.at(1);
cars.at(0).print_car_brand();
cars.at(1).set_car_brand("mazda");
cars.at(0).print_car_brand();
cars.at(1).print_car_brand();


cout << "========================================"<<endl;

  return 0;
}

```

