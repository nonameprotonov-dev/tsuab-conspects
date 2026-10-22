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
    virtual ~Tovar();                    // виртуальный деструктор

    void setFromKeyboard();
    void calculateAverageSize();

    virtual void displayInfo() const;    // виртуальный метод
    virtual double getAverageSize() const;

    std::string getName()   const { return name; }
    double      getMass()   const { return mass; }
    double      getHeight() const { return height; }
    double      getWidth()  const { return width; }

    void saveToFile(const std::string& filename) const;
    static void printFile(const std::string& filename);
};

#endif // TOVAR_H
```

### Tovar.cpp
```cpp
#include "Tovar.h"
#include <iostream>
#include <fstream>

using namespace std;

Tovar::Tovar() : name(""), mass(0), height(0), width(0), averageSize(0) {
    cout << "Создан объект Tovar (конструктор по умолчанию)\n";
}

Tovar::Tovar(const string& n, double m, double h, double w)
    : name(n), mass(m), height(h), width(w), averageSize(0) {
    calculateAverageSize();
    cout << "Создан объект Tovar (конструктор с параметрами): " << name << "\n";
}

Tovar::~Tovar() {
    cout << "Удалён объект Tovar: " << name << "\n";
}

void Tovar::setFromKeyboard() {
    cin.ignore(10000, '\n');
    cout << "Введите наименование товара: ";
    getline(cin, name);
    cout << "Введите массу (кг): ";
    cin >> mass;
    cout << "Введите высоту (см): ";
    cin >> height;
    cout << "Введите ширину (см): ";
    cin >> width;
}

void Tovar::calculateAverageSize() {
    averageSize = (mass + height + width) / 3.0;
}

double Tovar::getAverageSize() const {
    return averageSize;
}

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
        cout << "Наименование: " << n << "\n"
             << "Масса:        " << m << " кг\n"
             << "Высота:       " << h << " см\n"
             << "Ширина:       " << w << " см\n\n";
    }
    fin.close();
}
```

### ProductBatch.h
```cpp
#ifndef PRODUCTBATCH_H
#define PRODUCTBATCH_H

#include "Tovar.h"

class ProductBatch : public Tovar {
private:
    int quantity;            // количество единиц в партии
    std::string supplier;    // поставщик
    bool hasDiscount;        // наличие скидки

public:
    ProductBatch(const std::string& n, double m, double h, double w,
                 int q, const std::string& sup, bool disc);
    virtual ~ProductBatch();

    // Переопределение методов базового класса
    void displayInfo() const override;
    double getAverageSize() const override;

    // Собственные методы наследника
    double calculateTotalMass() const;   // общая масса партии
    double calculateVolume() const;      // объём партии
    void   applyDiscount(double percent);// применить скидку
    bool   isHeavy() const;              // партия тяжелее 100 кг
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
    : Tovar(n, m, h, w), quantity(q), supplier(sup), hasDiscount(disc) {
    cout << "Создан объект ProductBatch: " << getName() << "\n";
}

ProductBatch::~ProductBatch() {
    cout << "Удалён объект ProductBatch: " << getName() << "\n";
}

void ProductBatch::displayInfo() const {
    Tovar::displayInfo();     // вызов метода родителя
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
```
### main.cpp
```cpp
#include "Tovar.h"
#include "ProductBatch.h"
#include <iostream>

using namespace std;

int main() {
    Tovar t("Книга", 0.5, 20.0, 15.0);
    ProductBatch pb("Монитор", 4.2, 35.0, 60.0, 10, "ООО Техно", false);

    cout << "\n=== Базовый товар ===\n";
    t.displayInfo();

    cout << "=== Партия товара ===\n";
    pb.displayInfo();

    pb.applyDiscount(5);
    cout << "Партия тяжёлая? " << (pb.isHeavy() ? "Да" : "Нет") << "\n";

    return 0;
}
```
