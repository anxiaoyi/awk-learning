# awk-learning
The tutorial of awk  programming language

# AWK 编程语言

## 第一章 AWK教程

### 开始

#### awk程序结构

每一个awk程序都是一个或者多个的pattern-action语句：

pattern { action }
pattern { action }

pattern 和 action 都是可选的。

#### 运行awk程序
```
awk `program` input files

awk `program` file1 file2
```

awk一行接着一行扫描，然后尝试匹配pattern

注意程序是放在单引号``这个里面的，这是为了防止'$'被shell解释的。当你的程序很长的时候，可以单独写在一个文件里面：
```
awk -f progfile
```

Beth 4.00 0<br/>
Dan 3.75 0<br/>
Kathy 4.00 10<br/>
Mark 5.00 20<br/>
Mary 5.50 22<br/>
Susie 4.25 18<br/>

- 打印出工作时间大于0的每个人的姓名和总报酬

```
awk `$3 > 0 { print $1, $2 * $3 }` emp.data
```

- 打印出没有工作的员工
```
awk `$3 == 0 { print $1 } ` emp.data
```

### 简单输出

在awk中只有两种数据类型：数字和字符串。每一次 ，awk都会读取一行，然后将这一行拆分成多个字段，默认情况下，一个字段就是不包含任何空格或者tags键的字符序列。第一个字段是`$1`, 第二个字段是`$2`...整行称为`$0`，每一行和每一行的字段可能不相同。

#### 打印所有行
如果一个action没有pattern，那么将会针对的是所有行，下面这两段程序运行结果一样，都是讲整个文件的所有内容打印出来：

```
{ print }
{ print $0 }
```

#### 打印字段
在print语句中，用`,`来分割表达式，默认情况下，输出将会用空格间隔，并且每行末尾会加换行符。

```
{ print $1, $3 }
```

#### NF 字段的数量(The Number of Fields)
awk统计当前的行的字段数量，然后将它存储在内置变量`NF`中，所以`$NF`代表的是最后一个字段。

```
{ print NF, $1, $NF }
```

#### 计算然后打印
```
{ print $1, $2 * $3}
```

#### 打印行号
awk会将当前已经读取的行存放到一个内置变量`NR`中，所以我们可以很方便的显示行号

```
{ print NR, $0}
```

#### 打印额外的文字
```
{ print "total pay for", $1, "is", $2 * $3 }
```

### 花样输出
通过使用`printf(format, value1, value2, value3...)`可以格式化数据

#### Lining Up 字段
```
{ print("total pay for %s is %.2f\n", $1, $2 * $3)}
```

**注意：一旦使用`printf`，那么将不会自动生成空白符和换行符**

#### 对输出排序
可以借助`sort`来实现排序

```
awk `{ printf("%6.2f %s\n", $2 * $3, $0) }` emp.data | sort
```

### 选择

#### 比较选择
```
$2 >= 5
```

#### 计算选择
```
$2 * $3 > 50
```

#### 根据文字内容进行选择
```
$1 == "Susie"
```

当然你也可以根据正则表达式，下面这行打印出了所有包含'Susie'字符串的行：
```
/Susie/
```

#### 组合选择
使用`&&`表示AND，使用`||`表示OR，使用`!`表示NOT。

```
!($2 < 4 && $3 < 20)
```

#### 数据校验(Data Validation)
你可能会希望提前对数据的合法性进行校验：

```
NF != 3 { print $0, "number of fields is not equal to 3" }
$3 < 0 {print $0, "negative hours worked"}
```

#### 开始和结束(BEGIN and END)
可以使用`BEGIN`来打印头。`BEGIN`语句块在第一行被读取之前执行，而`END`在最后一行处理完毕后调用

```
BEGIN {print "NAME RATE HOURS"; print ""}
{ print }
```

### 使用awk进行计算
定义你自己的变量进行计算！不需要提前声明，不需要初始化数据(numbers默认初始值为0)

#### 计数
```
$3 > 15 { emp = emp + 1}
END { print emp, "employees worked more than 15 hours"}
```

#### 计算总和和平均值
总和：
```
END {print NR, "employees"}
```

平均工资：
```
{pay = pay + $2 * $3}
END {print NR, "employees"
	print "total pay is", pay
	print "average pay is", pay / NR
}
```

#### 处理文本
找到每小时工资最多的人
```
$2 > maxrate { maxrate = $2; maxemp = $1 }
END {print "highest hourly rate:", maxrate, "for", maxemp}
```

#### 字符串连接
使用空格连接姓名：

```
{names = names $1 " "}
END {print names}
```

#### 打印最后一行
```
{last = $0}
END {print last}
```

**内置函数**
`length`打印字符串有多少个字符：

```
{print $1, length($1)}
```

#### 计算行号，单词和字符数量
```
{nc = nc + length($0) + 1
nw = nw + NF}
END {print NR, "lines", nw, "words", nc, "characters"}
```

### 控制流

#### IF-ELSE语句
```
$2 > 6 {n = n + 1; pay = pay + $2 * $3}
END {if (n > 0) 
		print n, "employees, total pay is", pay, "average pay is", pay / n
	else
		print "no employees are paid more than $6/hour"
}
```

#### While语句
```
# meaning comment
# input:amout rate years
# output:compunded value at the end of each year
{
	i = 1
	while(i <= $3){ 
		print ("\t%.2f\n", $1 * (1 + $2) ^ i)
		i = i + 1
	} 
}
```

`^`表示指数.
`#`后面的表示注释

#### For语句
```
{
	for(i=1; i<= $3; i = i + 1){
		#your code
	}
}
```

### 数组
逆序输出：

```
{line[NR] = $0} # remember each input line
END{ 
	for (i = NR; i > 0; i = i - 1){ 
		print line[i]
	}
}
```

### 非常有用的"One-liners"

打印行数：
```
END {print NR}
```

打印每行最后一个字短：
```
{print $NF}
```

## AWK 语言

### 模式
所有模式总结：
```
BEGIN {statements}

END {statements}

expression {statements}

/regular expression {statements}

compound expression {statements}

pattern1, pattern2 {statements} # A range pattern matches each input line from a line matched by pattern1, to the next line matched by pattern2.
```

#### BEGIN and END
修改默认的字段分隔符
```
BEGIN {FS = "\t"}
```

#### 字符串匹配模式
```
/regexpr/

#组合
expression ~ /regexpr/

#排除
expression !~ /regexpr/
```

`/Asia`和`$0 ~ /Asia/`相同

#### 正则表达式
```
#元字符
\ ^ $ . [ ] | ( ) * + ?
```

#### 范围模式

### Actions

#### 表达式

内置变量：
![build-in variables](https://github.com/anxiaoyi/awk-learning/blob/master/images/built-in-variables.png)