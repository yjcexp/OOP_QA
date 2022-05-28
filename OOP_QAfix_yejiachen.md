### Lecture-3

2.仅仅作为补充：

+ `g`中`O`作为参数时，先进行一次复制构造，产生一个临时变量，这个临时变量将在`g`返回后被析构，但是由于`move`函数，被转为右值引用，可以继续使用其值。所以`O`作为参数时其$scope$应该是在`g`内。
+ `h`的返回值不是常引用类型，因此在函数返回时会创建一个临时变量作为右值引用。此时`O`作为临时变量，仍可以作为右值引用，其值存在寄存器内，但被析构，对应的内存可以被分配到别处。所以`O`作为返回值时其$scope$应该是在`h`内。

### Lecture-4

4.函数的每个参数都能用默认值默认，只要保证所有未分配默认值的参数**都在**有默认值的参数之前即可。

9.补充：

针对`f2`：`f2 (static_cast<char>(altitude::high));`即需要进行显示的类型转换。

### Lecture-7

8.

```cpp
class Derived extends Base {
	int i ;
public:
	Derived(int ii) : i(ii), Base(ii) {}
	~Derived() { cout << “destructor Derived”; }
};
```



貌似C++没有extends Base这种继承语法？复制后会报错。

### Lecture-9

4.

```cpp
class Genius:public Man {
public:
  void speak() override { cout << "I am a genius." << endl; }
}
```

代码部分，需要补上`public Man`。

