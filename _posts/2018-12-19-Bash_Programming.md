---
layout: post
title: Bash编程
data: 2018-12-19
tags: Linux
---


### 1. 变量

**变量定义**

```
$ myvar="Hello World!"
```

*注意：*

1.此处等号两边不能有空格
2.若变量内容为一个单词，可以省略引号

*申明环境变量(即退出Shell后仍能使用的变量)*

```
export JAVA_HOME="/usr/local/java"

或者

JAVA_HOME="/usr/local/java"
export JAVA_HOME
```

**变量使用**

```
$ echo $myvar
Hello World!
```

*注意：*

1.尽量使用双引号，单引号在变量替换时不会替换
2.可以在变量两端增加大括号来限定所要替换的变量名称，如下

```
$ echo foo${myvar}bar
foohello world!bar
```

### 2. 命令替换

就是在**SHELL**内嵌多条命令，一次性执行得到结果

有` `` `和 ` $() `两种方式

```
$ echo whoami
whoami
$ echo `whoami`
tom
$ echo $(whoami)
tom

$ echo "hello, `whoami`"
hello, tom
$ echo "hello, $(whoami)"
hello, tom
```

### 3. 变量替换

即使用`${}`

```
$ A=B
$ echo ${A}B
BB
```

变量替换还可以有一些特殊的功能，比如定义一个变量file如下

```
file=/dir1/dir2/dir3/my.file.txt
```

#### 1) 变量内容截取

|变量|取值|说明|
|:-|:-|:-|
|${file#*/}|dir1/dir2/dir3/my.file.txt|拿掉第一个“/”及其左边的字符串|
|${file##*/}|my.file.txt|拿掉最后一个“/”及其左边的字符串|
|${file#*.}|file.txt|拿掉第一个“.”及其左边的字符串|
|${file##*.}|txt|拿掉最后一个“.”及其左边的字符串|
|${file%/*}|/dir1/dir2/dir3|拿掉最后一个“/”及其右边的字符串|
|${file%%/*}|(空值)|拿掉第一个“/”及其右边的字符串|
|${file%.*}|/dir1/dir2/dir3/my.file|拿掉最后一个“.”及其右边的字符串|
|${file%%.*}|/dir1/dir2/dir3/my|拿掉第一个“.”及其右边的字符串|

**记忆方法**

  - *#*是去掉左边(键盘上*#*在*$*的左边)
  - *%*是去掉右边(键盘上*#*在*$*的右边)
  - 单一符号是最小匹配
  - 两个符号是最大匹配
  
#### 2) 变量长度截取

字符从左到右进行编号，编号从0开始

|变量|值|说明|
|:-|:-|:-|
|${file:0:5}|/dir1|提取最左边的5个字节|
|${file:5:5}|/dir2|提取第5个字节右边的连续5个字节|

#### 3) 变量值的字符串替换

|变量|值|说明|
|:-|:-|:-|
|${file/dir/path}|/path1/dir2/dir3/my.file.txt|将第一个dir替换为path|
|${file//dir/path}|/path1/path2/path3/my.file.txt|将全部dir替换为path|

#### 4) 计算变量值的长度

```
$ echo ${#file}
27
```

### 4. 数组array

**定义**

```
A=(a b c def)
```

**操作**

|操作|说明|
|:-|:-|
|${A[@]或A[*]}|可得到a b c def(全部数组元素)|
|${A[0]}|可得到a(第一个元素)|
|${#A[@]或#A[*]}|可得到4(数组元素数量)|

### 5. if语句

bash对齐非常讲究，*if*、*then*、*else*、*elif*、*fi*必须首字母对齐

```
if [[ condition ]]
then
  action1
else
  action2
fi
```

或者

```
if [[ condition1 ]]; then
  action1
elif [[ condition2 ]]; then
  action2
else
  action3
fi
```

**注意：**

*if*、*[[*、*condition*、*]]*之间必须插入空白符

### 6. 位置参数

在脚本编程时，可能需要在运行脚本的时候给脚本传递参数，这种通过命令行传递给脚本的参数称为**位置参数**。分别通过$1、$2、$3···来引用命令行传递的第一个、第二个、第三个···参数。

```
#!/usr/bin/env bash

echo all arguments are $@
echo name of srcipt is $0
echo first argument is $1
echo second argument is $2
echo fifth argument is $5
echo number of arguments is $#
```

调用

```
之前先给予权限
chmod a+x test.sh

$ ./test.sh 1 2 3 4 5 6 7 8 9
all arguments are 1 2 3 4 5 6 7 8 9
name of srcipt is ./test.sh
first argument is 1
second argument is 2
fifth argument is 5
number of arguments is 9
```
**注意：**

如果引用下标大于等于10，则需要用`${17}`之类的形式，不然会解析为${1}和7

### 7. 条件表达式

|运算符|描述|示例|
|:-|:-|:-|
|文件属性检测运算符|
|-e filename|如果 filename 存在，则为真|[ -e /var ]|
|-d filename|如果 filename 为目录，则为真|[ -d /tmp/mydir ]|
|-f filename|如果 filename 为常规文件，则为真|[ -f /usr/bin/grep ]|
|-r filename|如果 filename 可读，则为真|[ -r /var/log/syslog ]|
|-w filename|如果 filename 可写，则为真|[ -w /var/mytmp.txt ]|
|-x filename|如果 filename 可执行，则为真|[ -x /var/test.sh ]|
|filename1 -nt filename2|如果 filename1 比 filename2 新，则为真|[ /test1.txt -nt test2.txt ]|
|filename1 -ot filename2|如果 filename1 比 filename2 旧，则为真|[ /test1.txt -ot test2.txt ]|
|字符串比较运算符|
|-z string|如果 string 长度为0，则为真|[ -z "$myvar" ]|
|-n string|如果 string 长度非0，则为真|[ -n "$myvar" ]|
|string1 = string2|如果 string1 与 string2 相同，则为真|[ "$myvar" = "one two three" ]|
|string1 != string2|如果 string1 与 string2 不同，则为真|[ "$myvar" != "one two three" ]|
|数值比较运算符|
|num1 - eq num2|等于|[ 3 -eq $mynum ]|
|num1 - ne num2|不等于|[ 3 -ne $mynum ]|
|num1 - lt num2|小于|[ 3 -lt $mynum ]|
|num1 - le num2|小于或等于|[ 3 -le $mynum ]|
|num1 - gt num2|大于|[ 3 -gt $mynum ]|
|num1 - ge num2|大于或等于|[ 3 -ge $mynum ]|

### 8. 循环结构

**注意：**

以下所有的 ';' 都可以用一个或多个换行符代替

#### 1) until

```
until 测试命令; do 命令块; done
```

#### 2) while

```
while 测试命令; do 命令块; done
```

#### 3) for

```
for 变量 [[ in 单词列表: ]]; do 命令块; done

for (( 表达式1; 表达式2; 表达式3 )); do 命令块; done
```

### 9. 算术运算

只能进行简单的整数运算，用 `$((` 和 `))`

```
$ echo $((100/3))
33
```

### 10. case语句

```
case "${x##*.}" in
gz)
  gunzip ${x}
  ;;
gz2)
  gunzip2 ${x}
  ;;
*)
  echo "Archive format not recognized."
  exit
  ;;
esac
```

上例中，bash首先扩展 "${x##*.}" ，其中 "$x" 时文件的名称， "${x##*.}" 是去掉文件名左侧开始最大匹配的句点字符串。然后与 *gz*、*bz2*比较，执行接下来的语句。其他不匹配的字符串都将与 *\** 模式匹配

### 11. 函数与名称空间

函数的使用与脚本接受命令行变量类似的方式(位置参数)来接受函数参数

值得注意的是，函数内部的局部变量与函数外部的全局变量共用，即函数内部变量值发生变化，外部同名全局变量值也将发生改变

如果要限定函数内部使用，可以调用 *local* 修饰词修饰变量