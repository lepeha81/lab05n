# lab05n
[![Coverage Status](https://coveralls.io/repos/github/lepeha81/lab05n/badge.svg?branch=master)](https://coveralls.io/github/lepeha81/lab05n?branch=master)
# Task 1

Создайте `CMakeList.txt` для библиотеки banking

Подключите библиотеку gtest:

![image](https://github.com/lepeha81/lab05n/blob/main/1.PNG)

![image](https://github.com/lepeha81/lab05n/blob/main/2.PNG)

Создайте CMakeLists.txt для банковских операций:
![image](https://github.com/lepeha81/lab05n/blob/main/3.PNG)

Содержимое файла CMakeLists.txt:

![image](https://github.com/lepeha81/lab05n/blob/main/4.PNG)


```
project(banking_lib)
```

Эта строка определяет имя проекта. Обычно это название папки, содержащей исходный код проекта. Имя проекта нужно для того, чтобы можно было однозначно идентифицировать проект при его сборке, установке и использовании.

```
if (NOT TARGET libbanking)
```

Эта строка проверяет, существует ли уже цель `libbanking`. Если ее еще нет, то будет выполнен блок внутри условия. Это нужно для того, чтобы не создавать цель повторно, если она уже была создана.

```
add_library(libbanking STATIC
    /Account.cpp
    /Transaction.cpp
)
```

Эта строка добавляет цель `libbanking` - это произвольное имя, которое будет использоваться для ссылки на библиотеку в других целях. Задается тип цели `STATIC`, что означает создание статической библиотеки. Далее указывается список исходных файлов, входящих в эту библиотеку. В данном случае это два файла: `Account.cpp` и `Transaction.cpp`.

```
install(TARGETS libbanking
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
)
```

Эта строка добавляет инструкцию установки цели `libbanking`, которая была создана выше. Для этого вызывается команда `install`. С помощью аргумента `TARGETS` указывается цель, которая должна быть установлена. Далее используются два параметра: `ARCHIVE DESTINATION lib` и `LIBRARY DESTINATION lib`. Они указывают, куда будет скопирована установка. Архив будет скопирован в папку `lib`, которая находится в каталоге установки, а точнее - в `/usr/local/lib` на Unix-подобных системах. 

```
include_directories()
```

Эта строка объявляет пути к папкам, содержащим заголовочные файлы, которые должны быть доступны для использования функциям и классам, которые были объявлены в исходных файлах проекта. Пути могут быть указаны в любом количестве внутри круглых скобок.
Создаём CMakeLists.txt для:tests

![image](https://github.com/lepeha81/lab05n/blob/main/5.PNG)

![image](https://github.com/lepeha81/lab05n/blob/main/6.PNG)

Содержимое файла CMakeLists.txt:

![image](https://github.com/lepeha81/lab05n/blob/main/7.PNG)


```
cmake_minimum_required(VERSION 3.4)
```

Эта строка задает минимальную версию CMake, которая требуется для сборки проекта.

```
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

Эти две строки задают стандарт языка C++ для компиляции проекта и устанавливают флаг, который гарантирует, что этот стандарт будет использоваться, если он доступен.

```
option(BUILD_TEST "Build tests" OFF)
```

Эта строка добавляет параметр сборки `BUILD_TEST`, который позволяет включить или выключить сборку тестов проекта. По умолчанию он выключен.

```
project(banking)
```

Эта строка задает имя проекта.

```
add_library(banking STATIC banking/Account.cpp banking/Transaction.cpp)
```

Эта строка создает статическую библиотеку `banking`, которая будет содержать код, написанный в файлах `banking/Account.cpp` и `banking/Transaction.cpp`.

```
target_include_directories(banking PUBLIC banking/)
```

Эта строка указывает, что библиотека `banking` должна включать путь `banking/` в список путей, где будет искаться заголовочные файлы.

```
if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB BANKING_TEST_SOURCES tests/tests.cpp)
  add_executable(check tests/tests.cpp)
  target_link_libraries(check banking gtest_main gmock_main)
  add_test(NAME check COMMAND check)
endif()
```

Этот блок определяет процесс сборки тестов проекта. Если параметр `BUILD_TEST` установлен в значение `ON`, то будут выполнены следующие действия: 

- `enable_testing()` - включает подсистему тестирования CTest. 
- `add_subdirectory(third-party/gtest)` - добавляет каталог с библиотекой Google Test в процесс сборки.
- `file(GLOB BANKING_TEST_SOURCES tests/tests.cpp)` - создает переменную `BANKING_TEST_SOURCES`, содержащую список исходных файлов тестов. В данном случае это файл `tests/tests.cpp`.
- `add_executable(check tests/tests.cpp)` - создает цель с именем `check`, которая будет компилироваться из файла `tests/tests.cpp` и других файлов, необходимых для тестирования. 
- `target_link_libraries(check banking gtest_main gmock_main)` - связывает бинарник тестов с библиотеками Google Test и `banking`.
- `add_test(NAME check COMMAND check)` - добавляет тест с именем `check`.
# Task 2

Создайте модульные тесты на классы `Transaction` и `Account`
- Используйте mock-объекты
- Покрытие кода должно составлять 100%

Сделаем`tests.cpp`:

Содержимое файла tests.cpp:
```
#include "Account.h"
#include "Transaction.h"

#include <gtest/gtest.h>
#include <gmock/gmock.h>

class AccountMock : public Account {
public:
    AccountMock(int id, int balance) : Account(id, balance) {}
    MOCK_CONST_METHOD0(GetBalance, int());
    MOCK_METHOD1(ChangeBalance, void(int diff));
    MOCK_METHOD0(Lock, void());
    MOCK_METHOD0(Unlock, void());
};
class TransactionMock : public Transaction {
public:
    MOCK_METHOD3(Make, bool(Account& from, Account& to, int sum));
};

TEST(Account, Mock) {
    AccountMock acc(1, 100);
    EXPECT_CALL(acc, GetBalance()).Times(1);
    EXPECT_CALL(acc, ChangeBalance(testing::_)).Times(2);
    EXPECT_CALL(acc, Lock()).Times(2);
    EXPECT_CALL(acc, Unlock()).Times(1);
    acc.GetBalance();
    acc.ChangeBalance(100); // throw
    acc.Lock();
    acc.ChangeBalance(100);
    acc.Lock(); // throw
    acc.Unlock();
}

TEST(Account, SimpleTest) {
    Account acc(1, 100);
    EXPECT_EQ(acc.id(), 1);
    EXPECT_EQ(acc.GetBalance(), 100);
    EXPECT_THROW(acc.ChangeBalance(100), std::runtime_error);
    EXPECT_NO_THROW(acc.Lock());
    acc.ChangeBalance(100);
    EXPECT_EQ(acc.GetBalance(), 200);
    EXPECT_THROW(acc.Lock(), std::runtime_error);
}

TEST(Transaction, Mock) {
    TransactionMock tr;
    Account ac1(1, 50);
    Account ac2(2, 500);
    EXPECT_CALL(tr, Make(testing::_, testing::_, testing::_))
    .Times(6);
    tr.set_fee(100);
    tr.Make(ac1, ac2, 199);
    tr.Make(ac2, ac1, 500);
    tr.Make(ac2, ac1, 300);
    tr.Make(ac1, ac1, 0); // throw
    tr.Make(ac1, ac2, -1); // throw
    tr.Make(ac1, ac2, 99); // throw
}

TEST(Transaction, SimpleTest) {
    Transaction tr;
    Account ac1(1, 50);
    Account ac2(2, 500);
    tr.set_fee(100);
    EXPECT_EQ(tr.fee(), 100);
    EXPECT_THROW(tr.Make(ac1, ac1, 0), std::logic_error);
    EXPECT_THROW(tr.Make(ac1, ac2, -1), std::invalid_argument);
    EXPECT_THROW(tr.Make(ac1, ac2, 99), std::logic_error);
    EXPECT_FALSE(tr.Make(ac1, ac2, 199));
    EXPECT_FALSE(tr.Make(ac2, ac1, 500));
    EXPECT_FALSE(tr.Make(ac2, ac1, 300));
}
```
```
#include "Account.h"
#include "Transaction.h"

#include <gtest/gtest.h>
#include <gmock/gmock.h>
```

Импортируются необходимые заголовочные файлы, включая Google Test framework.

```
class AccountMock : public Account {
public:
    AccountMock(int id, int balance) : Account(id, balance) {}
    MOCK_CONST_METHOD0(GetBalance, int());
    MOCK_METHOD1(ChangeBalance, void(int diff));
    MOCK_METHOD0(Lock, void());
    MOCK_METHOD0(Unlock, void());
};

class TransactionMock : public Transaction {
public:
    MOCK_METHOD3(Make, bool(Account& from, Account& to, int sum));
};
```

Определяются два мок класса: `AccountMock` и `TransactionMock`. Они наследуются от `Account` и `Transaction` соответственно и имеют заменяемые методы. Используются макросы `MOCK_CONST_METHOD0`, `MOCK_METHOD1`, `MOCK_METHOD0` и `MOCK_METHOD3`, которые определяют мок-методы с соответствующим именем и числом аргументов.

```
TEST(Account, Mock) {
    AccountMock acc(1, 100);
    EXPECT_CALL(acc, GetBalance()).Times(1);
    EXPECT_CALL(acc, ChangeBalance(testing::_)).Times(2);
    EXPECT_CALL(acc, Lock()).Times(2);
    EXPECT_CALL(acc, Unlock()).Times(1);
    acc.GetBalance();
    acc.ChangeBalance(100); // throw
    acc.Lock();
    acc.ChangeBalance(100);
    acc.Lock(); // throw
    acc.Unlock();
}
```

Запускается тестирование методов класса `Account`. Создается объект класса `AccountMock` с помощью функции `EXPECT_CALL` и задаются ожидаемые вызовы мок-методов, которые будут проверены в тесте.

```
TEST(Account, SimpleTest) {
    Account acc(1, 100);
    EXPECT_EQ(acc.id(), 1);
    EXPECT_EQ(acc.GetBalance(), 100);
    EXPECT_THROW(acc.ChangeBalance(100), std::runtime_error);
    EXPECT_NO_THROW(acc.Lock());
    acc.ChangeBalance(100);
    EXPECT_EQ(acc.GetBalance(), 200);
    EXPECT_THROW(acc.Lock(), std::runtime_error);
}
```

Запускается простой тест методов класса `Account`. Создается объект класса `Account` и с помощью функций `EXPECT_EQ` и `EXPECT_THROW` проверяются результаты вызова методов, а также возникающие исключения.

```
TEST(Transaction, Mock) {
    TransactionMock tr;
    Account ac1(1, 50);
    Account ac2(2, 500);
    EXPECT_CALL(tr, Make(testing::_, testing::_, testing::_)).Times(6);
    tr.set_fee(100);
    tr.Make(ac1, ac2, 199);
    tr.Make(ac2, ac1, 500);
    tr.Make(ac2, ac1, 300);
    tr.Make(ac1, ac1, 0); // throw
    tr.Make(ac1, ac2, -1); // throw
    tr.Make(ac1, ac2, 99); // throw
}
```

Запускается тестирование методов класса `Transaction`. Создается объект класса `TransactionMock` с помощью функции `EXPECT_CALL` и задаются ожидаемые вызовы мок-методов, которые будут проверены в тесте.

```
TEST(Transaction, SimpleTest) {
    Transaction tr;
    Account ac1(1, 50);
    Account ac2(2, 500);
    tr.set_fee(100);
    EXPECT_EQ(tr.fee(), 100);
    EXPECT_THROW(tr.Make(ac1, ac1, 0), std::logic_error);
    EXPECT_THROW(tr.Make(ac1, ac2, -1), std::invalid_argument);
    EXPECT_THROW(tr.Make(ac1, ac2, 99), std::logic_error);
    EXPECT_FALSE(tr.Make(ac1, ac2, 199));
}
```

Запускается простой тест методов класса `Transaction`. Создается объект класса `Transaction` и с помощью функций `EXPECT_EQ` и `EXPECT_THROW` проверяются результаты вызова методов, а также возникающие исключения.
![image](https://github.com/lepeha81/lab05n/blob/main/8.PNG)

# Task 3 
Настройте сборочную процедуру на TravisCI

Сделаем`.yml` file:

![image](https://github.com/lepeha81/lab05n/blob/main/9.PNG)

Содержимое файла Action.yml:

![image](https://github.com/lepeha81/lab05n/blob/main/10.PNG)


# Task 4
Настройте Coveralls.io

Изменим CMakeLists.txt :

![image](https://github.com/lepeha81/lab05n/blob/main/11.PNG)

if (COVERAGE)
    target_compile_options(check PRIVATE --coverage)
    target_link_libraries(check --coverage)
Этот блок кода проверяет, была ли определена переменная COVERAGE, которая используется для измерения покрытия кода тестами. Если эта переменная существует, то опции компилятора и библиотеки выбираются так, чтобы генерировать отчет о покрытии кода.

![image](https://github.com/lepeha81/lab05n/blob/main/12.PNG)

Новое содержимое файла Action.ymk:

![image](https://github.com/lepeha81/lab05n/blob/main/13.PNG)

![image](https://github.com/lepeha81/lab05n/blob/main/14.PNG)

Первоначально мы имеем название проекта - `CMake`, а затем следует секция `on`, где описаны события, на которые реагирует данный workflow - `push` и `pull_request` ветки `master`.

Далее идет блок `jobs`, который содержит одну задачу - `build_Linux`. Названия задач могут быть выбраны произвольно.

```
 build_Linux:
  runs-on: ubuntu-latest
```

Задача будет запущена на последней версии Ubuntu.

```
  steps:
  - uses: actions/checkout@v3
```

Данный шаг использует действие `checkout`, чтобы склонировать репозиторий текущего проекта.

```
  - name: Adding gtest
    run: git clone https://github.com/google/googletest.git third-party/gtest -b release-1.11.0
```

Данный шаг клонирует репозиторий Google Test в каталог `third-party/gtest`.

```
  - name: Install lcov
    run: sudo apt-get install -y lcov
```

Данный шаг устанавливает lcov.

```
  - name: Config banking with tests
    run: cmake -H. -B ${{github.workspace}}/build -DBUILD_TESTS=ON
```

Данный шаг настраивает сборку проекта с тестами, используя CMake. Опция `-H.` указывает на расположение файла CMakeLists.txt в корне проекта, `-B ${{github.workspace}}/build` указывает на путь к каталогу сборки, а опция `-DBUILD_TESTS=ON` включает сборку тестов.

```
  - name: Build banking
    run: cmake --build ${{github.workspace}}/build
```

Этот шаг запускает команду `cmake --build` для сборки проекта.

```
  - name: Run tests
    run: |
      build/check
      cmake --build ${{github.workspace}}/build --target test -- ARGS=--verbose
```

Этот шаг позволяет запустить все тесты из предыдущего шага. Обратите внимание, что здесь используются две команды в блоке `run`.

```
  - name: Do lcov stuff
    run: lcov -c -d build/CMakeFiles/banking.dir/banking/ --include *.cpp --output-file ./coverage/lcov.info
```

Данный шаг использует инструмент lcov для генерации отчета о покрытии кода тестами.

```
  - name: Publish to coveralls.io
    uses: coverallsapp/github-action@v1.1.2
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
```

Этот шаг позволяет отправить данные о покрытии на сервис Coveralls, чтобы можно было отслеживать процент покрытия кода тестами. Здесь используется дополнительное действие с загрузкой из общедоступного репозитория - `coverallsapp/github-action@v1.1.2`.

