## awk

学习书籍：The AWK Programming Language
### awk tutorial
#### pattern action
```
emp.data
Beth 21 0
Dan 19 0
Kathy 15.50 10
Mark 25 20
Mary 22.50 22
Susie 17 18


awk '$3 > 0 {print $1,$2*$3}' emp.data

$3 > 0是pattern
{}包含的是action
```
awk的结构，匹配pattern执行action,pattern和action都是可以省略的，省略pattern则所有都匹配；省略action则输出匹配的所有。
注意：完全的空行会被忽略

```
pattern1 {action1}{action2}
pattern2 {action3}

#直接的命令，不指定文件，会进入交互模式；依据输入实时进行判断处理
awk '$3 > 0 {print $1,$2*$3}'
#单引号用于区分awk程序和shell，所以单引号框起来的是可以换行的
```

> *-f* 参数 指定执行的程序文件

awk处理的对象文件是空格和tab间隔的文字和数字；读入时一次一行，分割field，以1为起始。0表示整行

> 内建变量

NF输出field总数

```
{print NF,$1,$NF}
输出字段数、以及最后一列
```
NR输出行号

length()输出字符串长度

```
awk '{print $1 " " length($1)}' emp.data
```

ARGV 内建列表

程序调用的参数存储在列表ARGV中，ARGV[1]是第一个参数，ARGV[ARGC-1]表示最后一个参数，ARGV[0]表示程序名




> 改变输出内容

```
{print "total pay for",$1}
```
支持占位符输出

> BEGIN and END

BEGIN用来输出heading首行文本，END用来输出结束文本
```
awk '$3 > 0 {a=a+1} END {print a,"persons"}' emp.data
awk '$3 > 0 {a=a+1} {print $0} END {print a,"persons"}' emp.data
```

> 查找最大

```
awk '$3 > maxa {maxa=$3;maxv=$1} END {print "maxa:",maxa,"maxv",maxv}' emp.data

第三列的值大于变量，将最大值赋给变量；最终变量的值就是最大值
```
> 字符串拼接

```
awk '{a = a $1 " "} END {print a}' emp.data 
```

> if条件

```
awk ' $3>40 {num +=1;sum += $2*$3} END {if (num>0) print num,sum/num; else print "null";}' emp.data

#可以用大括号
awk ' $3>10 {num +=1;sum += $2*$3} END {if (num>0) {print num,sum/num} else {print "null"}}' emp.data
```
> while循环

```
awk '{i=0;while(i<$3){print $1,$3;i++}}' emp.data
```

> for循环

```
awk 'BEGIN {for(i=0;i<100;i++){if(i%3==0){print i,"bizz"}}}'
```

> Arrays

```
#倒序输出
awk '{nums[NR] = $0}END{for(i=NR;i>0;i--){print nums[i]}}' emp.data
```

#### 常用的one-liners

```
#打印文件总行数
awk 'END {print NR}' emp.data
#打印包含文本的行数
awk '/Ma/ {line++} END {print line}' emp.data

#替换文件结尾的\r
{sub(/\r$/,"");print}
```
#### 注意事项
awk使用加法时，若是数字和字符串相加，数字会与字符串中的前缀数字相加
```
root@crane-pc:/home/codes/awklearning# awk 'BEGIN {print 1+"2_text"}'
3
```

awk使用正则匹配后获得的文本还可以继续使用sub将不需要的文本替换成空
```
awk '/locationForName/{sub(/result/,"aaaaa");print}' info
```

awk中的pattern可以同时存在多个，表示对一行的多次匹配，分别处理

next用于跳过后面的表达式，直接进行下一行处理；提高脚本速度。

split(s,arr,sep) 使用sep分割s，形成arr；如果sep为空，会被分割成单个字符。

awk的array也是map
```
awk 'BEGIN {m["a"]=1;m["b"]=2;print m["a"]}'
1
```

awk的array/map遍历
```
awk 'BEGIN {m["a"]=1;m["b"]=2;for(i in m){print i,m[i]}}'
```

可以利用awk的map去重
```
a 1
b 2
b 2
c 3
a 1
a 1
awk '{a[$1]++}END{for(i in a){print i,a[i]}}' cul.txt
c 1
b 2
a 3

```
