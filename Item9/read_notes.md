## ***Item9: Never call virtual functions during construction or destruction.***
<font size="3">

析构和构造函数中不要调用virtual函数，因为这类调用从不下降至derived class。 derived class的函数几乎必然取用 local成员变量，而那些成员变量还未被初始化。

```cpp
class Transaction {
public:
    Transaction() { 
        logTransaction(); //基类的构造函数中调用虚函数
    }
    //virtual void logTransaction() const = 0; //无法调用纯虚函数，连接器找不到必要的 Transaction::logTransaction 实现代码
    virtual void logTransaction() const {
        cout <<"logTransaction log"<<endl;
    }
};

class BuyTransaction : public Transaction {
public:
    BuyTransaction(){}
    virtual void logTransaction() const {
        cout <<"BuyTransaction log"<<endl;
    }
};

class SellTransaction : public Transaction {
public:
    SellTransaction(){}
    virtual void logTransaction() const {
        cout <<"SellTransaction log"<<endl;
    }
};
int main(){
    BuyTransaction a; // "logTransaction log" -> base class构造期间virtual函数绝不会下降到derived classes
    SellTransaction b; //"logTransaction log"
    return 0;
}
```
替代的做法就是在base class中将log函数改为non-virtual函数，然后derived class的构造函数将必要的信息传递给base class的构造函数.
```cpp
class Transaction {
public:
    Transaction(const string& logInfo) {
        logTransaction(logInfo);
    }
    void logTransaction(const string& logInfo) const {
        cout << logInfo << endl;
    }
};

class BuyTransaction : public Transaction {
public:
    BuyTransaction(parameters)
        :Transaction(createLogString(parameters))
    {}
private:
    static string createLogString(parameters);
};
```