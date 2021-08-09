---
layout: post
---

## operator+

```c++
#include <iostream>

using namespace std;

class A {
public:
  A operator+(A);
  A(int a): a(a) {};
  int get() { return a; };
private:
  int a;
};

A A::operator+(A aa) {
  return A(a + aa.a);
}

int main()
{
  A a1(5), a2(6);

  std::cout << (a1 + a2).get() << std::endl;
}
```

注意：
1. 函数里，用this->a，当没有别的同名local variable时，也可以直接使用a。
2. 在同一个类中，第二个operand的私有成员是可以访问的。(这和operator overloading没有关系)。access control限制的只是不同类的对象之间的私有成员的访问规则。
3. constructor initializer的用法。
4. (a1 + a2)得到了一个匿名对象，可以直接调用它的函数，无需额外创建一个对象赋值再调用。
5. 它不是static function。必须创建了对象之后才可以调用。

operator function也可以用nonmember function的方式定义：

```c++
#include <iostream>

using namespace std;

class A {
public:
  A(int a): a(a) {};
  int get() { return a; };
  int get(A aa) { return aa.a; };
private:
  int a;
};

A operator+(A a1, A a2) {
  return A(5);
}

int main()
{
  A a1(5), a2(6);

  std::cout << (a1 + a2).get() << std::endl;
}
```

nonmember function的局限是，不能方便地使用对象的私有成员。

operator+是函数名，a1 + a2只是一种shorthand写法。完整的写法是a1.operator+(a2)

## operator new

c++允许重定义new。new默认的行为是从free store开辟空间，或者称作heap memory/dynamic memory。

以下的new的用法，称作placement syntax：

```c++
class A {
};

void *buf = malloc(10);
A *a = new (buf) A;
```

面试经常会问一个对象的size是多少。实际上对象就是struct，struct是多少，对象就是多少。要考虑到内存alignment问题，不要被class干扰了思路。所以这里placement syntax提供的buf要有足够的空间存放新的A对象。
