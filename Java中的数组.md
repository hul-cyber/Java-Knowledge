# Java中的数组
## Java数组的声明
声明Java数组,使用下面的语法:
```java
int[] a;
```
或者
```java
int a[];
```
创建Java数组,使用下面的语法:
```java
int[] a = new int[100];
```
一旦创建了数组,就不能够改变它的长度了。
另一种方式:
```java
int[] smallPrimes = { 2, 3, 5, 7, 11, 13 };
```
并且最后一个值后面可以有逗号:
```java
int[] smallPrimes = { 2, 3, 5, 7, 11, 13, };
```
以方便接下来添加新的值。还可以声明一个匿名数组:
```java
new int[] { 2, 3, 5, 7, 11, 13 };
```
可以直接复制给一个新的数组变量
```java
smallPrimes = new int[] { 2, 3, 5, 7, 11, 13 };
```
是下面语句的简写:
```java
int[] anonymous = { 2, 3, 5, 7, 11, 13 };
smallPrimes = anonymous;
```
声明长度为0的数组:
```java
new elementType[0]
```
或者
```java
new elementType[] {}
```
长度为0的数组与`null`并不相同。

## 访问数组元素
如果访问的元素超出了数组的索引,那么就会产生`array index out of bounds`异常。
如果想要获得数组中的元素个数,可以使用`array.length`。

## 数组拷贝
一个数组变量拷贝另一个数组变量,这两个变量将引用同一个数组:
```java
int[] luckyNumbers = smallPrimes;
luckyNumbers[5] = 12; // now smallPrimes[5] is also 12
```
使用`copyOf`方法可以将数组中所有值拷贝到一个新的数组中去。
```java
int[] copiedLuckyNumbers = Arrays.copyOf(luckyNumbers, luckyNumbers.length);
```
第二个参数代表新数组的长度,如果比原来的数组长,数组型的额外的元素被赋值为0,布尔型的被赋值为false,如果小于原来数组的长度,那么只拷贝前面的值。

## 命令行参数
`main`方法会接受一个字符串数组,当我们使用下面的方式来调用程序的时候,就可以指定相应的参数:
> java Message -g cruel world

`args`数组中第一个元素是"-g"。

## 数组排序
使用`Arrays`类中的`sort`方法,可以对数值型数组进行排序。
```java
int[] a = new int[10000];
...
Arrays.sort(a);
```
这个方法使用了优化的快速排序算法。

## 数组的默认值
1. 如果是一个引用类型,那么默认值为空
```java
    Student[] students = new Student[2];
    System.out.println(students);
    for (Student s1 : students) {
        System.out.println(s1);
    }
```
2. 如果是数值类型,那么默认值为`null`。
3. 如果是boolean类型,那么默认值为`false`。

## 多维数组
多维数组的声明:
```java
double[][] balances;
```
进行初始化:
```java
balances = new double[N][N];
```
也可以采用简洁的方式进行初始化:
```java
int[][] magicSquare = 
    {
        { 16, 2, 3, 13 },
        { 17, 18, 19, 4 },
        { 8, 6, 5, 4}
    }
```
要想知道二维数组有多少行,使用`magic.length`,要想知道每行有多少个,使用`magic[i].length`。
使用增强`for`来获取二维数组中的每个数据。
```java
//double[][] balances
for(double[] row : balances) {
    for(double b : row) {
        ...
    }
}
```
## 不规则数组
可以将Java的数组当作“数组的数组”,比如`balances[10][6]`,相当于包含10个元素的数组,每个元素是由6个浮点数组成的数组。
创建一个不规则数组:
```java
int[][] odds = new int[MAX + 1][];
for(int n = 0; n <= MAX; n++) {
    odds[n] = new int[n + 1];
}
```

## 关于`Arrays`的一些函数
1. `static xxx[] copyOfRange(xxx[] a, int start, int end)`
    返回与`a`类型相同的一个数组,其长度为`length`或者`end - starr`，数组元素为`a`的值。如果`end`大于a.length,结果会填充`0`或`false`值。
2. Java给数组赋初值 
   > public static void fill​(int[] a, int val)
   > 用值val填充数组a