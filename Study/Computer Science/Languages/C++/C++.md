

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
