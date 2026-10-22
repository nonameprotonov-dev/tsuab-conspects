---
tags:
  - OOP
  - ThirdSemester
---
## PR3 1
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

public:
    Tovar();                                   // конструктор по умолчанию
    void setFromKeyboard();                    // ввод данных с клавиатуры
    void displayInfo() const;                  // вывод информации
    void saveToFile(const std::string& filename) const; // дозапись в файл
};

#endif // TOVAR_H
```

### Tovar.cpp
```cpp
#include "Tovar.h"
#include <iostream>
#include <fstream>

using namespace std;

Tovar::Tovar() : name(""), mass(0), height(0), width(0) {}

void Tovar::setFromKeyboard() {
    cin.ignore(10000, '\n');   // очистка буфера перед getline

    cout << "Введите наименование товара: ";
    getline(cin, name);

    cout << "Введите массу (кг): ";
    cin >> mass;

    cout << "Введите высоту (см): ";
    cin >> height;

    cout << "Введите ширину (см): ";
    cin >> width;
}

void Tovar::displayInfo() const {
    cout << "Наименование: " << name << "\n"
         << "Масса:        " << mass << " кг\n"
         << "Высота:       " << height << " см\n"
         << "Ширина:       " << width << " см\n\n";
}

void Tovar::saveToFile(const string& filename) const {
    ofstream fout(filename, ios_base::app);   // дозапись в конец файла
    if (!fout) {
        cerr << "Ошибка открытия файла!\n";
        return;
    }
    fout << name << ' ' << mass << ' ' << height << ' ' << width << '\n';
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
    t.displayInfo();
    t.saveToFile("tovary.txt");

    cout << "Данные добавлены в файл tovary.txt\n";
    return 0;
}
```

## PR3 2
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

public:
    Tovar();
    void setFromKeyboard();
    void displayInfo() const;
    void saveToFile(const std::string& filename) const;
    static void printFile(const std::string& filename);   // вывод содержимого файла
};

#endif // TOVAR_H
```

### Tovar.cpp
```cpp
#include "Tovar.h"
#include <iostream>
#include <fstream>

using namespace std;

Tovar::Tovar() : name(""), mass(0), height(0), width(0) {}

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

void Tovar::displayInfo() const {
    cout << "Наименование: " << name << "\n"
         << "Масса:        " << mass << " кг\n"
         << "Высота:       " << height << " см\n"
         << "Ширина:       " << width << " см\n\n";
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
    // Ввод и сохранение нового товара
    Tovar t;
    t.setFromKeyboard();
    t.displayInfo();
    t.saveToFile("tovary.txt");

    cout << "Данные добавлены в файл tovary.txt\n";

    // Чтение всего файла
    cout << "\n=== Содержимое файла tovary.txt ===\n";
    Tovar::printFile("tovary.txt");

    return 0;
}
```
