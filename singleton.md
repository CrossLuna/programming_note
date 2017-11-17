# Singleton Pattern 單例模式

## Single Thread 單線程
```C++
template<typename T>
class Singleton {
public:
    static T& getInstance() {
        if(!value) {
            value_ = new T();
        }
        return *value_;
    }
private:
    Singleton();
    ~Singleton();
    static T* value_;
};

template<typename T>
T* Singleton<T>::value_ = nullptr;
```

## Multithread 多線程

### Lock 加鎖
Pseudo code
``` C++
template<typename T>
class Singleton {
public:
    static T& getInstance() {
        {
            MutexGuard guard(mutex_) //RAII
            if(!value_) {
                value_ = new T();
            }
        }
        return *value_;
    }

private:
    Singleton();
    ~Singleton();
    static T*    value_;
    static Mutex mutex_;
};

template<typename T>
T* Singleton<T>::value_ = nullptr;

template<typename T>
Mutex Singleton<T>::mutex_;
```
優點：簡單，加一個mutex就解決
缺點：如果頻繁調用getInstance，都要進入臨界區，影響性能

### Double-Checked Locking (DCL)
進行兩次check，第一次check為假時，才加鎖進行第二次check
``` C++
template<typename T>
class Singleton {
public:
    static T& getInstance() {
        if(!value_) {
            MutexGuard guard(mutex_);
            if(!value_) {
                value_ = new T();
            }
        }
        return *value_;
    }
private:
    Singleton();
    ~Singleton();
    static T*    value_;
    static Mutex mutex_;
};

template <typename T>
T* Singleton<T>::value_ = nullptr;

template <typename T>
Mutex Singleton<T>::mutex_;

```
有問題！！！

## Reference
originlee.com/2015/01/31/thread-safe-singleton-in-cxx/