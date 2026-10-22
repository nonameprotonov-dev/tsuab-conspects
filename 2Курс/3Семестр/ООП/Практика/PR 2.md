---
tags:
  - OOP
  - ThirdSemester
---
```cpp
#include <iostream>
#include <string>
using namespace std;

class Tovar {
private:
    string name;
    double mass;
    double height;
    double width;

public:
    void set(const string& n, double m, double h, double w);
    void displayInfo() const;
};

// Реализация методов вне класса
void Tovar::set(const string& n, double m, double h, double w) {
    name = n;
    mass = m;
    height = h;
    width = w;
}

void Tovar::displayInfo() const {
    cout << "Наименование: " << name << "\n"
         << "Масса:        " << mass << " кг\n"
         << "Высота:       " << height << " см\n"
         << "Ширина:       " << width << " см\n\n";
}

int main() {

    Tovar t1, t2, t3, t4;

    t1.set("Книга",   0.5,  20.0,  15.0);
    t2.set("Монитор", 4.2,  35.0,  60.0);
    t3.set("Стол",    12.5, 75.0, 120.0);
    t4.set("Телефон", 0.2,  15.0,   7.0);

    cout << "=== Информация о четырёх товарах ===\n\n";
    t1.displayInfo();
    t2.displayInfo();
    t3.displayInfo();
    t4.displayInfo();

    return 0;
}
```
