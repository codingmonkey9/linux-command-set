# sed

> SED accepts inputs from files as well as pipes. Additionally, it can also accept inputs from standard input streams.  
> [sed workflow](https://www.edrawsoft.cn/viewer/public/s/max/ed868134912334)
<details>
  <summary>Points to Note</summary>
1. Pattern buffer is a private, in-memory, volatile storage area used by the SED.  
  
2. There is another memory area called hold buffer which is also private, in- memory, volatile storage area. 
Data can be stored in a hold buffer for later retrieval. At the end of each cycle, SED removes the contents of the pattern buffer but the contents of the hold buffer remains persistent between SED cycles. However SED commands cannot be directly executed on hold buffer, hence SED allows data movement between the hold buffer and the pattern buffer.

3. If address range is not provided by default, then SED operates on each line.
</details>
> **地址范围、模式匹配、选项、命令都可以组合在一起使用**

### 选项
- `sed  ''`       &emsp;&emsp; #相当于`cat` 命令，显示文本内容。可接*pipe，stdin，file*
- `sed  -i[suffix]`       &emsp;&emsp; #更新源文件，不加`-i`默认不修改源文件.如果加了后缀，会先把源文件备份，再修改源文件。见示例
- `sed  -n`       &emsp;&emsp; #屏幕上不会**自动**打印输出，如果指定了`p`还是会打印输出的（因为默认是自动打印输出）
- `sed  -e`       &emsp;$emsp; #有脚本命令的时候使用此选项，指定多个命令的时候也使用此选项。见示例
- `sed  -f`       &emsp;&emsp; #脚本命令在文件中，指定文件的时候使用此选项
- `sed  --follow-symlinks`

### 示例

- `sed  -e '1d' test.txt`       &emsp; #*1d* 表示删除第一行
- `sed  -e '' -e '1d' test.txt`       &emsp; #删除第一行并将其余行打印出来，如果没有`-e` 选项，指定多个命令会报错的。
- `sed  -i.bak '2d' test.txt`       &emsp; #首先会将test.txt文件备份，备份的文件名就是源文件名加上后缀名=>*test.txt.bak*，然后对源文件删除第二行。

### 命令

<details>
  <summary>命令大小写区别</summary>
  默认情况下，SED在单行上运行，但是也可以在多行上运行。多行命令由大写字母表示.
</details>

- `b`       &emsp; #可以指定要跳转的标签（用于循环）
- `t`       &emsp; #指定跳转的标签（用于分支）
- `p`       &emsp; #打印输出
- `d`       &emsp; #删除，仅删除pattern buffer中的数据，源文件不受影响。删除源文件内容加上`-i`
- `w`       &emsp; #备份，和选项`-i[suffix]` 不同，`-i[suffix]`是在操作前把源文件备份，而`w`是把操作的结果保存到指定的文件中。注：如果`w`指定的文件不存在则创建；存在则覆盖。
- `a`       &emsp; #追加，不是在行的末尾添加内容，而是另起一行。如果是地址范围或模式匹配，那么成功匹配的每一行后面都会追加一行。
- `c`       &emsp; #修改，把指定的行的内容用新文本替换，**注：**如果指定**连续的多行**，sed会把这些行当作一个组（group），用新文本替换这个组。而不是每一行都替换。见示例
- `i`       &emsp; #插入，和`a`基本相同，唯一不同的是`a`是将内容加到紧挨着指定行的下一行，而`i`则是加到指定行的上一行
- `l`       &emsp; #打印当前*pattern  buffer*中的内容，如果后面接数字，则表示多少个字符以后换行（为了方便阅读），如果是0，则不会换行（除非有换行符）（`l`打印出的内容结尾都有一个`$`）
- `q`       &emsp; #退出，遇到`q`退出当前sed执行流
- `r`       &emsp; #读取，读取一个文件内容，可以读取一个文件内容插入另一个文件中，见示例
- `e`       &emsp; #执行`e`后面指令，如果`e`后面没有给出指令，则会把pattern buffer中的内容当作指令来执行
- `N`       &emsp; #在*pattern buffer*中的行末尾添加`\n`然后把下一行（next line）追加到`\n`后面。此时在*pattern  buffer*中内容就是`cur line`+`\n`+`next line`
- `=`       &emsp; #把行号一起打印出来，行号和行内容不在一行上
- `&`       &emsp; #把模式匹配成功的内容存储到`&`中，可以把`&`理解为一个变量，变量值就是成功匹配到的内容。`&`通常与替换命令一同使用
- `s`       &emsp; #替换，`sed 's/pattern/replacement/[flag]'` 用replacement替换搜索到的pattern。下面列出常用flag
    -   `g`       &emsp; #`sed` 默认会匹配所有行，但是同一行有多个要替换的值时，默认只会替换第一个匹配到的值。如果想要将匹配的同一行的所有值都替换，则需要加`g`
    -   `i`       &emsp; #忽略大小写
- `x`       &emsp; #交换pattern buffer的内容和hold buffer内容（pattern buffer 和 hold buffer初始都为空行）hold buffer中未交换出去的内容会被一直保留，直到sed整个周期结束
- `h`       &emsp; #Copy pattern space to hold space.（读取`n`，复制`h g` 都会覆盖原来的内容）
- `H`       &emsp; #append pattern space to hold space. (所有的追加`a H G N`，都会在原来的内容结尾先添加一个`\n`，然后再追加内容)
- `g`       &emsp; #Copy hold space to pattern space.（和`s`搭配的`g`是flags，不是命令。注意区分。命令之间用`;`分隔）
- `G`       &emsp; #append hold space to pattern space.
- `n`       &emsp; #Read the next line of input into the pattern space.
- `N`       &emsp; #append the next line of input into the pattern space.
- `!`       &emsp; #逻辑非，和编程语言的!一个意思
- `{}`        &emsp; #把多个命令作为一组，比如：`sed -n '/pau/x;p' testfile` 和 `sed -n '/pau/{x;p}' testfile` 不同，`{x;p}`是满足条件才执行，而第一个`p`没有条件判断，只`x`判断

<details>
  <summary>pattern buffer and hold buffer</summary>
  pattern  buffer中的内容在执行完sed命令后会被清空，就好比局部变量。而hold buffer中的内容在sed整个周期都存在，就好比全局变量。
  pattern buffer：可以存储一行，也可以存储多行。因为有些命令是向pattern  buffer中添加内容的，比如`N`，关键是无论一行还是多行，执行完指令后pattern  buffer会被清空。
  hold buffer：同样可以存储一行或多行。
  什么时候清空pattern buffer？
  举个例子：一个文件中有10行，其中5行含有*hold*，执行命令`sed -n '/hold/ x;p;x;p' testfile`，并不是说执行一个`x`后就清空pattern buffer，而是将含有*hold*的一行读取到pattern buffer中，执行完`x;p;x;p`这4条指令后（自然是执行完全部指令，只不过这里只有四条指令），清空pattern buffer，然后读取下一行到pattern  buffer中，如果符合条件就继续执行这几条指令。如此重复，直到该文件中所有行全部读取过（读取到pattern buffer后再进行条件比较？） 
  什么时候清空hold buffer？
  上面的清楚了，自然就知道当文件中所有行都读取完毕并且指令执行完毕，sed的生命周期结束了，hold buffer中的内容就被清空了。
</details>

### 地址范围

> **地址范围既可以是行号，也可以是模式字符串**，它就是一个判断条件，符合的才会执行命令.例如`3d`，只有是第3行的才会删除

- `sed '3p' file`       &emsp; #打印第3行，如果不加数字只有命令p，则打印所有行
- `sed '$p' file`       &emsp; #打印最后一行
- `sed 'm,n p' file`        &emsp; #打印第m行到第n行的内容
- `sed 'm,+n p' file`       &emsp; #打印从第m行到m+n行的内容或者说从第m行开始接下来的n行
- `sed 'm~n p' file`        &emsp; #从第m行开始，每隔n行打印一次（包括m行）。见示例

### 模式匹配

> A pattern range can be a simple text or a complex regular expression.
> 模式匹配的定界符可以是`/` or `@` or `^` or `!` or `+` 等等。在某些情况下，比如匹配目录时，虽然可以将`/`写成`\/`转义，但是太乱可读性不是太好。所以可以更换定界符。
> 模式匹配中获取匹配到的子串的方式： 被`\(` 和 `\)`包裹的pattern 按顺序存储在`\number`中 （number为1，2，3……），见示例

- `sed -n '/Paulo/ p' test.txt`        &emsp; #打印包含*Paulo*的行
- `sed -n '/Alchemist/, 5 p' test.txt`       &emsp; #从含有*Alchemist*的行到第5行
- `sed -n '/Two/, /Pilgrimage/ p' books.txt`        &emsp; #从含有*Two*的行到含有*Pilgrimage*的行
- `sed -n '/Two/, +4 p' books.txt`          &emsp; #从含有*Two*的行开始，打印共4行

### 示例

- `sed 'p' test.txt`        &emsp; #会将每一行打印两遍，这不是错误。而是因为`sed` 默认会打印一次输出，加上`p`后又打印了一次输出。所以`-n` 选项这时候就起作用了
- `sed -n 'p' test.txt`       &emsp; #这时候`-n` 就会把默认打印的忽略掉，只打印一次。
- `sed -n '3p' test.txt`        &emsp; #只打印第三行
- `sed -n '2,5 p' test.txt `        &emsp; #打印2到5行，即2，3，4，5行
- `sed -n '$ p' test.txt`       &emsp; #打印最后一行
- `sed -n '3,$ p' test.txt`       &emsp; #打印3到最后一行
- `sed -n '2,+4 p' test.txt`        &emsp; #打印从第二行开始的四行，即 2，3，4，5行。**使用+**
- `sed -n '10~5' p test.txt`       &emsp; #从第10行开始，每隔5行打印一次，即输出 10，15，20，25……
- `sed -n '/Paulo/ p' test.txt`        &emsp; #打印包含*Paulo*的行
- `sed -n '/Alchemist/, 5 p' test.txt`       &emsp; #从含有*Alchemist*的行到第5行
- `sed -n '/Two/, /Pilgrimage/ p' books.txt`        &emsp; #从含有*Two*的行到含有*Pilgrimage*的行
- `sed -n '/Two/, +4 p' books.txt`          &emsp; #从含有*Two*的行开始，打印共4行
- `sed '2d;w testing.bak1' testing`       &emsp; #在buffer中把testing第二行删除，再把删除后的结果保存到testing.bak1文件（**不加`-i`都是修改buffer，下面不再声明**）
- `sed -n -e '/Martin/ w Martin.txt' -e '/Paulo/ w Paulo.txt' -e '/Tolkien/ w Tolkien.txt' books.txt`       &emsp; #假设books.txt的每一行内容是书名和作者，那么现在要统计一个作者的所有书名，就可以组合使用选项、命令、模式匹配来达到目的。（ps：多个脚本命令可以使用`;`来分隔，这样就不需要写多个`-e`了）
- `sed -e '2 a text contents' test.txt`       &emsp; #会在第二行后面插入一行内容*text contents*
- `sed -e '2,4 a text contents' testing`        &emsp; #第二行到第四行，每一行下面都会追加一行*text contents*
- `sed '4, 6 c Adultry' books.txt`        &emsp; #用一行*Adultry*替换掉4到6行，最终是只有一行*Adultry*，而不是三行。注意和`a`的区别
- `sed '1~2 c 4) Adultry, Paulo Coelho, 324' testing`       &emsp; #如果是**不连续的多行**，那就是分别替换。 （只把连续的行当作一组）
- `sed '3 r junk.txt' books.txt`        &emsp; #把*junk.txt*内容插入到*books.txt*的第3行的下面，即第3行和第4行之间插入新行（相当于`a`的追加）
- `sed '3,5 r junk.txt' books.txt`        &emsp; #3，4，5每行下面都插入
- `sed '3 e date' books.txt`        &emsp; #执行`date`得到日期，再把日期作为新行插入到第3行之前
- `sed 'N; s/\n/==/' testing`       &emsp; #用`==`连接两行
- `sed '3N; s/\n/==/' testing`        &emsp; #只把第3行和第4行用`==`连接起来
- `sed '/Paulo/ =' books.txt`       &emsp; #打印所有行内容，但只把*Paulo*所在行行号打印出来
- `sed -n '$ =' books.txt`        &emsp; #把总行数打印出来（`-n`把最后一行内容忽略掉了）
- `sed 's/[[:digit:]]/Book number &/' books.txt`        &emsp; #用`Book number [[:digit:]]` 替换`[[:digit:]]` （ps:[[:digit:]] 表示数字）
- `echo "Three,One,Two" | sed 's|\(\w\+\),\(\w\+\),\(\w\+\)|\2,\3,\1|'`       &emsp; #分别获取三个单词并调整顺序
- `echo $'1\n2\n3\n4' | sed -n '1~2h;2~2{p;x;p}'`       &emsp; #通过hold buffer调整顺序
- （1）`sed -n 'x;n;p' books.txt ` 和 （2）`sed -n 'x;p' books.txt ` 的区别：（1）打印的是从books.txt读取的内容，（2）打印的是从hold buffer中交换的内容

### 循环和分支

- sed 可以实现类似`goto` 的功能，sed可以跳转到**标签**标记的行，然后继续执行后续的命令。标签的定义格式如下(冒号后面就是标签名)：
```
:label 
:start 
:end 
:up
```

### 参考资料
- https://www.tutorialspoint.com/sed/index.htm
- https://stackoverflow.com/questions/12833714/the-concept-of-hold-space-and-pattern-space-in-sed/12834372#12834372

### 手册（应该不是官方手册）
- https://www.grymoire.com/Unix/Sed.html#uh-52
