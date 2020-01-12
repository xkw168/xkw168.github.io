layout: post
title:  "variadic template with lambda"
date:   2019-09-29
categories: C++
tag: 专业内容

C++11引入了variadic template（可变参数模板）的概念，这是一个可以接受任意参数个数的模板（为了实现任意参数个数，必须使用到递归）.

维基百科上面有着比较详细的讲解：[link]([https://zh.wikipedia.org/wiki/%E5%8F%AF%E5%8F%98%E5%8F%82%E6%95%B0%E6%A8%A1%E6%9D%BF](https://zh.wikipedia.org/wiki/可变参数模板))

有几个重要的地方：

* 想要接受0个或更多个的参数`template<typename First, typename... Rest>`
* 想要接受1个或以上的参数`template<typename... values>`
* 省略号(...) 在参数左边，声明了一个参数包（parameter pack）
* 省略号(...) 在参数右边，代表参数包展开（parameter pack expansion），将参数宝解开为一组实参

来看一段代码:

```c++
#include <cstring>
#include <iostream>
#include <sstream>

// We define a namespace so printf does not clash
namespace practice {
  template<typename T>
  size_t replace(const char * format, T value) {
    for (size_t i = 0; i < strlen(format); i++) {
      if (format[i] == '$') {
        std::cout << value;
        return i + 1;
      }
      else {
        std::cout << format[i];
      }
    }
    return strlen(format);
  }

  // base function
  void printf(const char * format) { std::cout << format; }

  // 'recursive' function
  template<typename First, typename... Rest args>
  void printf(const char * format, First value, Rest args... args) {
    // each time we skip one $ and repeat the same operation at the next position
    printf(&format[replace(format, value)], args...);
  }

}  // namespace practice

int main(void) {
  // Should print: Hello World foobar 4
  practice::printf("$ $ foo$ $\n", "Hello", "World", "bar", 4);
  return EXIT_SUCCESS;
}

```

这里我们利用了variadic template实现了一个printf，与内置的printf不同的是，我们使用\$作为格式控制符。

利用variadic template的整体思路如下：

* 定义一个“基础函数”（base function），定义了只有一个参数的时候应该进行的操作
* 定义一个“递归函数”，逐个处理变量
* 在这里，递归函数中，我们一次替换一个控制符\$，最后在基础函数里面，已经替换了所有的控制符，直接输出就好

再来看看在C++14引入了*type deduction*之后，如何将variadic template和lambda搭配使用

用一段代码来分析：

```C++
#include <iostream>

// TODO: Base case
template<typename Ftype>
Ftype compose(const Ftype & fn) {
  Ftype f = fn;
  return f;
}

// TODO: Recursive case
template<typename Ftype, typename... Other>
auto compose(Ftype fn, Other... other) {
  return [fn, other...](const int x) { return compose(other...)(fn(x)); };
}

int main(void) {
  auto add_func = [](const int x) { return x + 5; };
  auto sub_func = [](const int x) { return x - 3; };
  auto mul_func = [](const int x) { return x * 1; };
  int res = compose(add_func, sub_func, mul_func)(0);
  std::cout << "Result: " << res << "\n";
}
```

这里主函数中我们声明了三个lambda函数，分别完成加减乘的运算，然后利用variadic template将其合并成一个lambda函数。

* 仔细观察主函数我们可以知道，compose函数的返回值应该是一个lambda函数（因为直接使用了调用函数的方式传递了参数0）故compose的返回值应为`[](const int x){ return ...}`的形式
* 再分析可知，compose函数传入的全是lambda函数，基本逻辑应该是，先利用第一个lambda函数和参数x计算出新的x值，再将这个值当做参数传递给下一层的lambda函数
* 当只剩下一个参数时（基础函数），由于剩下的本身就是一个lambda函数，所以直接返回即可
* 最终目标应该是生成如下的嵌套lambda函数

```c++
auto func = [add_func, sub_func, mul_func](const int x) {
        return [sub_func, mul_func](const int x) {
            return [mul_func](const int x){
                return mul_func(x);
            }(sub_func(x));
        }(add_func(x));
    };
```

