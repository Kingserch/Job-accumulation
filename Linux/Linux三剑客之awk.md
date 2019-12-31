awk是Linux系统下一个处理文本的编程语言工具，能用简短的程序处理标准输入或文件、数据排序、计算以及生成报表等等，应用非常广泛.  
基本的命令语法：awk option 'pattern {action}' file  
其中pattern表示awk在数据中查找的内容，而action是在找到匹配内容时所执行的一系列命令。花括号用于根据特定的模式对一系列指令进行分组。
awk处理的工作方式与数据库类似，支持对记录和字段处理，这也是grep和sed不能实现的。
在awk中，缺省的情况下将文本文件中的一行视为一个记录，逐行放到内存中处理，而将一行中的某一部分作为记录中的一个字段。用1,2,3...数字的方式顺序的表示行（记录）中的不同字段。用$后跟数字，引用对应的字段，以逗号分隔，0表示整个行。  
下面根据工作经验总结了10个实用的awk案例.  
### 1、分析访问日志（Nginx为例）  
`日志格式：'$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" "$http_x_forwarded_for"'`  
#### 统计访问IP次数：  
`awk '{a[$1]++}END{for(v in a)print v,a[v]}' access.log`

#### 统计访问访问大于100次的IP：  
`awk '{a[$1]++}END{for(v ina){if(a[v]>100)print v,a[v]}}' access.log`

#### 统计访问IP次数并排序取前10：  
` awk '{a[$1]++}END{for(v in a)print v,a[v]|"sort -k2 -nr |head -10"}' access.log`

#### 统计时间段访问最多的IP：  
` awk'$4>="[02/Jan/2017:00:02:00" &&$4<="[02/Jan/2017:00:03:00"{a[$1]++}END{for(v in a)print v,a[v]}'access.log`

#### 统计上一分钟访问量：  
` date=$(date -d '-1 minute'+%d/%d/%Y:%H:%M)`
` awk -vdate=$date '$4~date{c++}END{printc}' access.log`

#### 统计访问最多的10个页面：  
` awk '{a[$7]++}END{for(vin a)print v,a[v]|"sort -k1 -nr|head -n10"}' access.log`

#### 统计每个URL数量和返回内容总大小：  
` awk '{a[$7]++;size[$7]+=$10}END{for(v ina)print a[v],v,size[v]}' access.log`

#### 统计每个IP访问状态码数量：  
` awk '{a[$1" "$9]++}END{for(v ina)print v,a[v]}' access.log`

#### 统计访问IP是404状态次数：  
` awk '{if($9~/404/)a[$1" "$9]++}END{for(i in a)print v,a[v]}' access.log`

### 2、两个文件差异对比

#### 文件内容：
seq 1 5 > a
seq 3 7 > b
#### 找出b文件在a文件相同记录：
```
awk 'FNR==NR{a[$0];next}{if($0 in a)print $0}' a b
3
4
5
```
#### 找出b文件在a文件不同记录：
```
awk 'FNR==NR{a[$0];next}!($0 in a)' a b
6
7
```
### 3、合并两个文件
```
# a文件内容：
cat << EOF > a
zhangsan 20
lisi 23
wangwu 29
EOF
# b文件内容：
cat << EOF > b
zhangsan man
lisi woman
wangwu man
EOF
```
#### 将a文件合并到b文件：
` awk 'FNR==NR{a[$1]=$0;next}{print a[$1],$2}' a b
zhangsan 20 man
lisi 23 woman
wangwu 29 man
方法2：
` awk 'FNR==NR{a[$1]=$0}NR>FNR{print a[$1],$2}' a b
zhangsan 20 man
lisi 23 woman
wangwu 29 man
将a文件相同IP的服务名合并：
` cat a
192.168.1.1: httpd
192.168.1.1: tomcat
192.168.1.2: httpd
192.168.1.2: postfix
192.168.1.3: mysqld
192.168.1.4: httpd
` awk 'BEGIN{FS=":";OFS=":"}{a[$1]=a[$1] $2}END{for(v in a)print v,a[v]}' a
192.168.1.4: httpd
192.168.1.1: httpd tomcat
192.168.1.2: httpd postfix
192.168.1.3: mysqld

解读：
数组a存储是$1=a[$1] $2，第一个a[$1]是以第一个字段为下标，值是a[$1] $2，也就是$1=a[$1] $2，值的a[$1]是用第一个字段为下标获取对应的值，但第一次数组a还没有元素，那么a[$1]是空值，此时数组存储是192.168.1.1=httpd，再遇到192.168.1.1时，a[$1]通过第一字段下标获得上次数组的httpd，把当前处理的行第二个字段放到上一次同下标的值后面，作为下标192.168.1.1的新值。此时数组存储是192.168.1.1=httpd tomcat。每次遇到相同的下标（第一个字段）就会获取上次这个下标对应的值与当前字段并作为此下标的新值。

4、将第一列合并到一行


` cat file
1 2 3
4 5 6
7 8 9
` awk '{for(i=1;i<=NF;i++)a[i]=a[i]$i" "}END{for(vin a)print a[v]}' file
1 4 7
2 5 8
3 6 9
解读：
for循环是遍历每行的字段，NF等于3，循环3次。
读取第一行时：
第一个字段：a[1]=a[1]1" "  值a[1]还未定义数组，下标也获取不到对应的值，所以为空，因此a[1]=1 。
第二个字段：a[2]=a[2]2" "  值a[2]数组a已经定义，但没有2这个下标，也获取不到对应的值，为空，因此a[2]=2 。
第三个字段：a[3]=a[3]3" "  值a[2]与上面一样，为空,a[3]=3 。
读取第二行时：
第一个字段：a[1]=a[1]4" "  值a[2]获取数组a的2为下标对应的值，上面已经有这个下标了，对应的值是1，因此a[1]=1 4
第二个字段：a[2]=a[2]5" "  同上，a[2]=2 5
第三个字段：a[3]=a[3]6" "  同上，a[2]=3 6
读取第三行时处理方式同上，数组最后还是三个下标，分别是1=1 4 7，2=2 5 8，3=36 9。最后for循环输出所有下标值。

5、字符串拆分

字符串拆分：
方法1：
` echo "hello" |awk -F '''{for(i=1;i<=NF;i++)print $i}'
h
e
l
l
o
方法2：
` echo "hello" |awk '{split($0,a,"''");for(v in a)print a[v]}'
l
o
h
e
l

6、统计出现的次数

统计字符串中每个字母出现的次数：
` echo "a.b.c,c.d.e" |awk -F'[.,]' '{for(i=1;i<=NF;i++)a[$i]++}END{for(v in a)print v,a[v]}'
a 1
b 1
c 2
d 1
e 1

7、费用统计

得出每个员工出差总费用及次数：
` cat a
zhangsan 8000 1
zhangsan 5000 1
lisi 1000 1
lisi 2000 1
wangwu 1500 1
zhaoliu 6000 1
zhaoliu 2000 1
zhaoliu 3000 1
` awk '{name[$1]++;cost[$1]+=$2;number[$1]+=$3}END{for(v in name)print v,cost[v],number[v]}' a
zhangsan 5000 1
lisi 3000 2
wangwu 1500 1
zhaoliu 11000 3

8、获取某列数字最大数


` cat a
a b 1
c d 2
e f 3
g h 3
i j 2
获取第三字段最大值：
` awk 'BEGIN{max=0}{if($3>max)max=$3}END{print max}' a
3
打印第三字段最大行：
` awk 'BEGIN{max=0}{a[$0]=$3;if($3>max)max=$3}END{for(v in a)if(a[v]==max)print v}'a
g h 3
e f 3

9、去除文本第一行和最后一行

` seq 5 |awk'NR>2{print s}{s=$0}'
2
3
4

解读：
读取第一行，NR=1，不执行print s，s=1
读取第二行，NR=2，不执行print s，s=2 （大于为真）
读取第三行，NR=3，执行print s，此时s是上一次p赋值内容2，s=3
最后一行，执行print s，打印倒数第二行，s=最后一行

10、获取Nginx upstream块内后端IP和端口


` cat a
upstream example-servers1 {
   server 127.0.0.1:80 weight=1 max_fails=2fail_timeout=30s;
}
upstream example-servers2 {
   server 127.0.0.1:80 weight=1 max_fails=2fail_timeout=30s;
   server 127.0.0.1:82 backup;
}
` awk '/example-servers1/,/}/{if(NR>2){print s}{s=$2}}' a
127.0.0.1:80
` awk '/example-servers1/,/}/{if(i>1)print s;s=$2;i++}' a
` awk '/example-servers1/,/}/{if(i>1){print s}{s=$2;i++}}' a
127.0.0.1:80

解读：
读取第一行，i初始值为0，0>1为假，不执行print s，x=example-servers1，i=1
读取第二行，i=1，1>1为假，不执行prints，s=127.0.0.1:80,i=2
读取第三行，i=2，2>1为真，执行prints，此时s是上一次s赋值内容127.0.0.1:80，i=3
最后一行，执行print s，打印倒数第二行，s=最后一行。
这种方式与上面一样，只是用i++作为计数器。