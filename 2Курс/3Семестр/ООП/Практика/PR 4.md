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
    double averageSize;   // среднее значение одного из параметров

public:
    Tovar();                    // конструктор
    ~Tovar();                   // деструктор

    void setFromKeyboard();
    void calculateAverageSize();
    double getAverageSize() const;

    void displayInfo() const;
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

// Конструктор: инициализирует поля значениями по умолчанию
Tovar::Tovar() : name(""), mass(0), height(0), width(0), averageSize(0) {
    cout << "Создан объект Tovar (конструктор)\n";
}

// Деструктор
Tovar::~Tovar() {
    cout << "Удалён объект Tovar: " << name << " (деструктор)\n";
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

// Подсчёт среднего значения массы, высоты и ширины
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
         << "Средний размер:" << averageSize << "\n\n";
}

void Tovar::saveToFile(const string& filename) const {
    ofstream fout(filename, ios_base::app);
    if (!fout) {
        cerr << "Ошибка открытия файла!\n";
        return;
    }
    fout << name << ' ' << mass << ' ' << height << ' ' << width << '\n';
}

void Tovar::printFile(const string& filename) {
    ifstream fin(filename);
    if (!fin) {
        cerr << "Файл не найден!\n";
        return;
    }

    string n;
    double m, h, w;

    while (fin >> n >> m >> h >> w) {
        cout << "Наименование: " << n << "\n"
             << "Масса:        " << m << " кг\n"
             << "Высота:       " << h << " см\n"
             << "Ширина:       " << w << " см\n\n";
    }
    fin.close();
}
```

### main.cpp
```cpp
#include "Tovar.h"
#include <iostream>

using namespace std;

int main() {
    Tovar t;
    t.setFromKeyboard();
    t.calculateAverageSize();   // подсчёт среднего значения
    t.displayInfo();
    t.saveToFile("tovary.txt");

    cout << "Данные добавлены в файл tovary.txt\n";

    cout << "\n=== Содержимое файла tovary.txt ===\n";
    Tovar::printFile("tovary.txt");

    return 0;
}
```
