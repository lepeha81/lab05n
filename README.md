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

Создаём CMakeLists.txt для:tests

![image](https://github.com/lepeha81/lab05n/blob/main/5.PNG)

![image](https://github.com/lepeha81/lab05n/blob/main/6.PNG)

Содержимое файла CMakeLists.txt:

![image](https://github.com/lepeha81/lab05n/blob/main/7.PNG)

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

![image](https://github.com/lepeha81/lab05n/blob/main/12.PNG)

Новое содержимое файла Action.ymk:

![image](https://github.com/lepeha81/lab05n/blob/main/13.PNG)

![image](https://github.com/lepeha81/lab05n/blob/main/14.PNG)
