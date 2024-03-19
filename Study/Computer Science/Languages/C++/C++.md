

### this pointer

클래스의 멤버 함수를 호출할 떄 C++는 어떻게 객체를 찾는가?

```c++
#include <iostream>
using namespace std;

class Obj {
  private:
    int a;

  public:
    void setA(int a) { this->a = a; }
    int getA() { return a; }
};


int main() {
  Obj obj;
  obj.setA(10);
  cout << obj.getA() << endl;
  return 0;
}
```

참조자의 도입
```c++

#include <iostream>

int change_val(int *p) 
{
  *p = 3;
  return 0;
}

int main() 
{
    int number = 5;
    std::cout << number << std::endl;
    change_val(&number);
    std::cout << number << std::endl;
}

```

```c++
#include <iostream>
int main() {
  int a = 3;
  int& another_a = a;

  another_a = 5;
  std::cout << "a : " << a << std::endl;
  std::cout << "another_a : " << another_a << std::endl;

  return 0;
}
```