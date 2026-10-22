---
tags:
  - OOP
  - ThirdSemester
---
### Tovar.h
```cpp
#ifndef TOVAR_H
#define TOVAR_H

#include <string>
#include <iostream>

class Tovar {
private:
    std::string name;
    double mass;
    double height;
    double width;
    double averageSize;

public:
    Tovar();
    Tovar(const std::string& n, double m, double h, double w);
    virtual ~Tovar();                          // виртуальный деструктор

    void setFromKeyboard();
    void calculateAverageSize();

    virtual void displayInfo() const;
    virtual double getAverageSize() const;

    std::string getName()   const { return name; }
    double      getMass()   const { return mass; }
    double      getHeight() const { return height; }
    double      getWidth()  const { return width; }

    void saveToFile(const std::string& filename) const;
    static void printFile(const std::string& filename);

    // === Перегрузка операторов как методы класса ===
    Tovar  operator+(const Tovar& other) const;
    Tovar& operator+=(double delta);
    Tovar  operator-(double delta) const;

    // === Перегрузка как дружественные функции ===
    friend bool operator==(const Tovar& a, const Tovar& b);
    friend bool operator> (const Tovar& a, const Tovar& b);
    friend std::ostream& operator<<(std::ostream& os, const Tovar& t);
    friend std::istream& operator>>(std::istream& is, Tovar& t);
};

#endif // TOVAR_H
```

### Tovar.cpp
```cpp
#include "Tovar.h"
#include <fstream>

using namespace std;

Tovar::Tovar() : name(""), mass(0), height(0), width(0), averageSize(0) {}

Tovar::Tovar(const string& n, double m, double h, double w)
    : name(n), mass(m), height(h), width(w), averageSize(0) {
    calculateAverageSize();
}

Tovar::~Tovar() {
    cout << "Удалён объект Tovar: " << name << "\n";
}

void Tovar::setFromKeyboard() {
    cin.ignore(10000, '\n');
    cout << "Наименование: "; getline(cin, name);
    cout << "Масса: ";   cin >> mass;
    cout << "Высота: ";  cin >> height;
    cout << "Ширина: ";  cin >> width;
    calculateAverageSize();
}

void Tovar::calculateAverageSize() {
    averageSize = (mass + height + width) / 3.0;
}

double Tovar::getAverageSize() const { return averageSize; }

void Tovar::displayInfo() const {
    cout << "Наименование:  " << name << "\n"
         << "Масса:         " << mass << " кг\n"
         << "Высота:        " << height << " см\n"
         << "Ширина:        " << width << " см\n"
         << "Средний размер:" << averageSize << "\n";
}

void Tovar::saveToFile(const string& filename) const {
    ofstream fout(filename, ios_base::app);
    if (!fout) { cerr << "Ошибка открытия файла!\n"; return; }
    fout << name << ' ' << mass << ' ' << height << ' ' << width << '\n';
}

void Tovar::printFile(const string& filename) {
    ifstream fin(filename);
    if (!fin) { cerr << "Файл не найден!\n"; return; }
    string n; double m, h, w;
    while (fin >> n >> m >> h >> w) {
        cout << n << " " << m << " " << h << " " << w << "\n";
    }
    fin.close();
}

// === Реализация операторов ===

// Оператор сложения как метод класса
Tovar Tovar::operator+(const Tovar& other) const {
    return Tovar(name + " + " + other.name,
                 mass   + other.mass,
                 height + other.height,
                 width  + other.width);
}

// += как метод класса
Tovar& Tovar::operator+=(double delta) {
    mass += delta;
    calculateAverageSize();
    return *this;
}

// - как метод класса
Tovar Tovar::operator-(double delta) const {
    Tovar result = *this;
    result.mass = (mass - delta > 0) ? mass - delta : 0;
    result.calculateAverageSize();
    return result;
}

// Операторы сравнения — дружественные функции
bool operator==(const Tovar& a, const Tovar& b) { return a.mass == b.mass; }
bool operator> (const Tovar& a, const Tovar& b) { return a.mass >  b.mass; }

// Оператор вывода — дружественная функция
ostream& operator<<(ostream& os, const Tovar& t) {
    os << t.name << " | масса=" << t.mass
       << ", высота=" << t.height
       << ", ширина=" << t.width;
    return os;
}

// Оператор ввода — дружественная функция
istream& operator>>(istream& is, Tovar& t) {
    cout << "Введите наименование, массу, высоту, ширину: ";
    is >> t.name >> t.mass >> t.height >> t.width;
    t.calculateAverageSize();
    return is;
}
```

### ProductBatch.h
```cpp
#ifndef PRODUCTBATCH_H
#define PRODUCTBATCH_H

#include "Tovar.h"

class ProductBatch : public Tovar {
private:
    int quantity;
    std::string supplier;
    bool hasDiscount;

public:
    ProductBatch(const std::string& n, double m, double h, double w,
                 int q, const std::string& sup, bool disc);
    virtual ~ProductBatch();

    // Переопределение виртуальных методов
    void   displayInfo() const override;
    double getAverageSize() const override;

    // Собственные методы наследника
    double calculateTotalMass() const;
    double calculateVolume() const;
    void   applyDiscount(double percent);
    bool   isHeavy() const;

    // Дружественный оператор вывода для наследника
    friend std::ostream& operator<<(std::ostream& os, const ProductBatch& pb);
};

#endif // PRODUCTBATCH_H
```

### ProductBatch.cpp
```cpp
#include "ProductBatch.h"
#include <iostream>

using namespace std;

ProductBatch::ProductBatch(const string& n, double m, double h, double w,
                           int q, const string& sup, bool disc)
    : Tovar(n, m, h, w), quantity(q), supplier(sup), hasDiscount(disc) {}

ProductBatch::~ProductBatch() {
    cout << "Удалён объект ProductBatch: " << getName() << "\n";
}

void ProductBatch::displayInfo() const {
    Tovar::displayInfo();
    cout << "Количество:    " << quantity << " шт.\n"
         << "Поставщик:     " << supplier << "\n"
         << "Скидка:        " << (hasDiscount ? "есть" : "нет") << "\n"
         << "Общая масса:   " << calculateTotalMass() << " кг\n"
         << "Объём партии:  " << calculateVolume() << " см^3\n\n";
}

double ProductBatch::getAverageSize() const {
    if (quantity == 0) return 0.0;
    return calculateTotalMass() / quantity;
}

double ProductBatch::calculateTotalMass() const {
    return getMass() * quantity;
}

double ProductBatch::calculateVolume() const {
    return getHeight() * getWidth() * getMass() * quantity;
}

void ProductBatch::applyDiscount(double percent) {
    hasDiscount = true;
    cout << "Применена скидка " << percent << "% на партию " << getName() << "\n";
}

bool ProductBatch::isHeavy() const {
    return calculateTotalMass() > 100.0;
}

// Оператор вывода для наследника
ostream& operator<<(ostream& os, const ProductBatch& pb) {
    os << static_cast<const Tovar&>(pb)
       << " | кол-во=" << pb.quantity
       << ", поставщик=" << pb.supplier
       << ", скидка=" << (pb.hasDiscount ? "да" : "нет");
    return os;
}
```
### main.cpp
```cpp
#include "Tovar.h"
#include "ProductBatch.h"
#include <iostream>

using namespace std;

// Демонстрация полиморфизма: работаем через указатель на базовый класс
void checkProduct(const Tovar* p) {
    cout << ">>> checkProduct <<<\n";
    cout << "Средний размер: " << p->getAverageSize() << "\n";
    p->displayInfo();   // вызовется метод соответствующего класса
}

int main() {
    cout << "========== ЧАСТЬ 1: ПЕРЕГРУЗКА ОПЕРАТОРОВ ==========\n\n";

    Tovar t1("Книга",   0.5, 20, 15);
    Tovar t2("Монитор", 4.2, 35, 60);
    ProductBatch pb("Стол", 12.5, 75, 120, 5, "ООО Мебель", true);

    // Оператор вывода <<
    cout << "t1: " << t1 << "\n";
    cout << "t2: " << t2 << "\n";
    cout << "pb: " << pb << "\n";

    // Оператор сложения +
    Tovar total = t1 + t2;
    cout << "\nСумма t1 + t2: " << total << "\n";

    // Оператор +=
    t1 += 1.0;
    cout << "После t1 += 1.0: " << t1 << "\n";

    // Оператор -
    Tovar t3 = t2 - 1.0;
    cout << "После t2 - 1.0: " << t3 << "\n";

    // Операторы сравнения == и >
    cout << "\nt1 == t2 ? " << (t1 == t2 ? "Да" : "Нет") << "\n";
    cout << "t1 >  t2 ? " << (t1 >  t2 ? "Да" : "Нет") << "\n";

    // Оператор ввода >>
    Tovar t4;
    cin >> t4;
    cout << "Введено: " << t4 << "\n";

    cout << "\n========== ЧАСТЬ 2: ПОЛИМОРФИЗМ ==========\n\n";

    // Массив указателей на базовый класс
    Tovar* items[3];
    items[0] = new Tovar("Ручка", 0.05, 15, 1);
    items[1] = new ProductBatch("Телевизор", 15.0, 50, 100, 3, "LG", false);
    items[2] = new ProductBatch("Шкаф", 40.0, 200, 80, 2, "IKEA", true);

    for (int i = 0; i < 3; ++i) {
        cout << "--- Объект " << i + 1 << " ---\n";
        cout << *items[i] << "\n";     // оператор <<
        checkProduct(items[i]);        // полиморфный вызов
        cout << "\n";
    }

    // Корректное удаление через виртуальный деструктор
    for (int i = 0; i < 3; ++i) {
        delete items[i];
    }

    return 0;
}
```
