<p align="center"><img src="/images/logo.png" alt=""></p>
<h1 align="center"> python奇葩特性! 🐍 </h1>
<p align="center"> 谁看谁知道</p>



- [👀 Examples](#-example)
 - [第一部分:头脑风暴](#structure-of-the-example)
   - [▶ 有时候字符串很让人捉急  *](#-strings-can-be-tricky-sometimes-)
   - [▶ 哈希警告来了!](#-time-for-some-hash-brownies)
   - [▶ 到处皆可return!](#-return-return-everywhere)


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

### 💡 Explanation
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

### ▶ 哈希警告来了!
1\.
```py
some_dict = {}
some_dict[5.5] = "Ruby"
some_dict[5.0] = "JavaScript"
some_dict[5] = "Python"
```

**Output:**
```py
>>> some_dict[5.5]
"Ruby"
>>> some_dict[5.0]
"Python"
>>> some_dict[5]
"Python"
```

”Python“干掉了"JavaScript"的存在?

#### 💡 Explanation

* python的字典在检查key的相等性时，会先比较两个值是否相等，在python中值相等的两个可哈希对象他们的哈希值一定相等，如1.0==1，则他们被哈希时的结果一定相同：
  ```py
   >>> print(hash(1),hash(1.0),hash(1.00000),hash(1.0000000000000000000000000000000001),hash(1.0001))
   >>> # 输出：1 1 1 1 230584300921345
  ```
  虽然可以使得hash(1 != hash(1.0),但这违背了上面值相等哈希相等的要求，而python认为值相等更重要，有pyer甚至认为值相等但hash不同会带来混乱，还有人认为用浮点数
  做key很少也容易产生问题（如上面的1.0与5.00000000000000000000000000000000000000000001以及1.0001），因此就出现了上面的情况；在java中，假如用上面的
  情况做key时，会直接编译不通过：
  ```java
    HashMap<Float,String> hashMap = new HashMap<>();
    hashMap.put(1,"abc");
    System.out.println(hashMap.get(1.0));
  java: 对于put(int,java.lang.String), 找不到合适的方法
    方法 java.util.Map.put(java.lang.Float,java.lang.String)不适用
      (参数不匹配; int无法转换为java.lang.Float)
    方法 java.util.AbstractMap.put(java.lang.Float,java.lang.String)不适用
      (参数不匹配; int无法转换为java.lang.Float)
    方法 java.util.HashMap.put(java.lang.Float,java.lang.String)不适用
      (参数不匹配; int无法转换为java.lang.Float)
  ```
  ```java
    System.out.println(1==1.0); // true
    System.out.println(new Integer(1).hashCode()); // 1
    System.out.println(new Float(1.0).hashCode()); // 1065353216
  ```
  上面还能看到java与python是不同的，java中1==1.0，但两个的hash并不相同。
  上面说的是值相等一定hash同，但值不等不一定hash不同，还是有可能冲突的。
  更多细节可以参考 [answer](https://stackoverflow.com/a/32211042/4354153).

---

### ▶ 到处皆可return!

```py
def some_func():
    try:
        return 'from_try'
    finally:
        return 'from_finally'
```

**Output:**
```py
>>> some_func()
'from_finally'
```

#### 💡 Explanation:

- 这主要是因为finally是一定会执行的，只要程序不是直接崩溃，那么不管try中是否发生异常，finally都会执行，哪怕在try中直接调用了exit，如：
```python
try:
    print("exit测试")
    exit(1)
finally:
    print("finally")
# exit测试
# finally
```
上面可以看到，虽然try中直接exit，但是finally还是被执行了，而所有的return都已最终的return为准，即后来的return会覆盖之前的return值，这个可能跟
os堆栈机制有关。
需要注意，调用exit或者sys.exit时其实抛出了一个SysExit异常，这个异常基于BaseException而不是Exception，而Exception也是基于BaseException，即exit其实是可以
被捕获的，但不能被Exception捕获，详见下面：
```python
try:
    print("exit测试")
    exit(1)
except Exception as e: 
    print("Exception") # 不会打印
except SystemExit as e:
    print("SysExit") # 会捕获异常，导致程序并没有停止
except BaseException as e:
    print("BaseException") # 会捕获异常，导致程序并不能停止
finally:
    print("finally")
print("只有BaseException或SysExit异常被捕获时才会打印这里")
```
上面的几行注释掉相关内容，即可以看到退出与不退出的混乱情况。

---