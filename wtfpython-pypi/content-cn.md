<p align="center"><img src="/images/logo.png" alt=""></p>
<h1 align="center"> python奇葩特性! 🐍 </h1>
<p align="center"> 谁看谁知道</p>



- [👀 Examples](#-example)
 - [第一部分:头脑风暴](#structure-of-the-example)
   - [▶ 有时候字符串很让人捉急  *](#-strings-can-be-tricky-sometimes-)


## 第一部分:头脑风暴
### ▶ 有时候字符串很让人捉急

1\.
```py
>>> a = "some_string"
>>> id(a)
140420665652016
>>> id("some" + "_" + "string") # Notice that both the ids are same.
140420665652016
```

2\.
```py
>>> a = "wtf"
>>> b = "wtf"
>>> a is b
True

>>> a = "wtf!"
>>> b = "wtf!"
>>> a is b
False

>>> a, b = "wtf!", "wtf!"
>>> a is b
True
```

3\.
```py
>>> 'a' * 20 is 'aaaaaaaaaaaaaaaaaaaa'
True
>>> 'a' * 21 is 'aaaaaaaaaaaaaaaaaaaaa'
False
```

### 💡 解释 
python中使用一种叫做intern的机制对字符串进行存储处理，所谓intern机制，是指会对字符串进行缓存，不仅python有，Java
中也有，如：
```java
String name1 = "name";
String name2 = "name";
System.out.println(name1 == name2); // true
```
以上创建的两个对象使用==时会返回true；但是如果使用new方式创建则不同:
```java
String name3 = new String("name");
String name4 = new String("name");
System.out.println(name3 == name4); // false
```
这是Java的，python页类似，不过python中没有new关键字，python的intern机制如下：
1) 编译时能确定的字符串对象会缓存，比如以下：
```py
a = "abc"
b = "a" + "bc"
```
以上中a的内容直接确定，而b的内容在编译时也可以直接确定下来；当创建b时，会去查看其内容是否已经缓存，如果缓存，则a与b会指向同一片内存区域，这样就提高了内存的利用率；

2) 编译时不能确定的内容，不会被缓存，如：
```py
a = "a"
b = "abc"
c = a + "bc"
b is c # false
```
因为编译时，并不能确定c的值，因此结果是false；

3) 太长的字符串是不会被缓存的，如上面的示例3，具体需要查看具体python版本中源码的实现机制；

4) 不同于java，intern并不支持所有字符，只支持数字母、下划线等少数几种构成的字符串；但这条不一定，如以下：
```py
a = "abcpython测试"
b = "abcpython测试"
a is b # 在python终端执行结果是false，但在pycharm中则是true；
```
总之，python比较奇怪，虽然有intern机制，但是又引入了一些坑，个人感受是对字符串进行相等判断时，尽量使用 == 而不是 is，这个与Java有点儿相反；
另外python默认创建了-5 - 256的int对象，如：
```python
a = 10
b = 10
a is b # true
a = 257
b = 257
a is b # false
```
因此对数字进行判断时，也同样应该使用== 而不是 is.