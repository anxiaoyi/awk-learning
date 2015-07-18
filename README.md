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