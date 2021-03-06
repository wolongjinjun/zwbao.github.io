# grep及正则表达式
## grep, egrep, fgrep	
### grep
grep（global search regular expression(RE) and print out the line，全面搜索正则表达式并把行打印出来）是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。
- Pattern：由文本字符和正则表达式的元字符组合而成匹配条件
- grep [options] PATTERN [FILE...]
```shell
-i 忽略字符大小写的差别。
--color 用颜色突出显示匹配的字符。
-v 反转查找，显示没有被模式匹配到的行。
-o 只输出文件中匹配到的部分。
```
glob：

```shell
*：任意长度的任意字符
?：任意单个字符
[ ]:
[^]:	
```
#### 元字符：注意与路径匹配（glob）中的区别

```
.：匹配任意单个字符
[]：匹配指定范围内的任意单个字符
[^]：匹配指定范围外的任意单个字符
```

#### 字符集合：外面还要加 [ ]

```
[:digit:], [:lower:], [:upper:], [:punct:], [:space:], [:alpha:], [:alnum:]
```

#### 字符匹配次数（贪婪模式，即尽可能长得匹配）：

	*：匹配其前面的字符任意次	
	.*：任意长度的任意字符
	\?：匹配其前面的字符1次或0次
	\{m,n\}：匹配其前面的字符至少m次，至多n次
	\{1,\}：至少1次
	\：用于转义

#### 位置锚定：

```
^：锚定行首，此字符后面的任意内容必须出现在行首
$：锚定行尾，此字符前面的任意内容必须出现在行尾
^$：空白行
\<或\b：锚定词首，其后面的任意字符必须作为单词首部出现
\>或\b：锚定词尾，其前面的任意字符必须作为单词的尾部出现
```

#### 分组：

	\(\)
	\(ab\)* ：把 ab 当做一个整体

分组的目的是为了后向引用

```
\1：引用第一个左括号以及与之对应的右括号所包括的所有内容
\2：引用第二个左括号以及与之对应的右括号所包括的所有内容
\3：引用第三个左括号以及与之对应的右括号所包括的所有内容
```
###  egrep：grep -E 

### fgrep：(fast) 不支持正则表达式
练习：

1. 显示/proc/meminfo文件中以不区分大小的s开头的行；

```shell
grep -i '^s' /proc/meminfo
grep '^[sS]' /proc/meminfo
```

2. 显示/etc/passwd中以nologin结尾的行; 

```shell
grep 'nologin$' /etc/passwd
#取出默认shell为/sbin/nologin的用户列表
grep "nologin$' /etc/passwd | cut -d：-f1
#取出默认shell为bash，且其用户ID号最小的用户的用户名
grep 'bash$' /etc/passwd | sort -n -t：-k3 | head -1 | cut -d：-f1
```

3. 显示/etc/inittab中以#开头，且后面跟一个或多个空白字符，而后又跟了任意非空白字符的行；

```shell
grep "^#[[:space:]]\{1,\}[^[:space:]]" /etc/inittab
```

4. 显示/etc/inittab中包含了:一个数字:(即两个冒号中间一个数字)的行；

```shell
grep ':[0-9]:' /etc/inittab
```

5. 显示/boot/grub/grub.conf文件中以一个或多个空白字符开头的行；

```shell
grep '^[[:space:]]\{1,\}' /boot/grub/grub.conf
```

6. 显示/etc/inittab文件中以一个数字开头并以一个与开头数字相同的数字结尾的行；

```shell
grep '^\([0-9]\).*\1$' /etc/inittab
```

7. 找出某文件中的，1位数，或2位数；

```shell
grep '[0-9]\{1,2\}' /proc/cpuinfo
grep --color '\<[0-9]\{1,2\}\>' /proc/cpuinfo
```

8. 查找当前系统上名字为student(必须出现在行首)的用户的帐号的相关信息, 文件为/etc/passwd

```shell
grep '^student\>' /etc/passwd | cut -d：-f3
```

练习：分析/etc/inittab文件中如下文本中前两行的特征(每一行中出现在数字必须相同)，请写出可以精确找到类似两行的模式：
l1:1:wait:/etc/rc.d/rc 1
l3:3:wait:/etc/rc.d/rc 3

```shell
grep '^l\([0-9]\):\1.*\1$' /etc/inittab
```

## 正则表达式：REGular EXPression, REGEXP

- Basic REGEXP：基本
- Extended REGEXP：扩展

### 基本正则表达式：

```shell
.：匹配任意单个字符
[]：匹配指定范围内的任意单个字符
[^]：匹配指定范围外的任意单个字符
```

### 次数匹配：

```shell
*：匹配其前面的字符任意次
\?：0或1次
\{m,n\}：匹配其前面的字符至少m次，至多n次
.*：任意长度的任意字符
```

### 锚定：

```shell
^：锚定行首，此字符后面的任意内容必须出现在行首
$：锚定行尾，此字符前面的任意内容必须出现在行尾
^$：空白行
\<或\b：锚定词首，其后面的任意字符必须作为单词首部出现
\>或\b：锚定词尾，其前面的任意字符必须作为单词的尾部出现
```
### 分组：

	\(\)
	\(ab\)* ：把 ab 当做一个整体

分组的目的是为了后向引用

```
\1：引用第一个左括号以及与之对应的右括号所包括的所有内容
\2：引用第二个左括号以及与之对应的右括号所包括的所有内容
\3：引用第三个左括号以及与之对应的右括号所包括的所有内容
```

### grep：使用基本正则表达式定义的模式来过滤文本的命令；

```shell
-E 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
-A<显示列数> 除了显示符合范本样式的那一行之外，并显示该行之后的内容。
-B<显示列数> 除了显示符合范本样式的那一行之外，并显示该行之前的内容。
-C<显示列数> 除了显示符合范本样式的那一行之外，并显示该行前后行的内容。
```

### 扩展正则表达式：

字符匹配：与之前一样

次数匹配：加上以下内容

```shell
+：匹配其前面的字符至少1次
{m,n}：不用转义
```

位置锚定：与之前一样

分组：与之前一样（不用转义）

#### 或者：

```shell
|：or
C|cat：C或cat
(C|c)at：Cat或cat
```

