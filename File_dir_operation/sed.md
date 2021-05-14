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

- `b`       &emsp; #可以指定要跳转的标签（用于循环）
- `t`       &emsp; #指定跳转的标签（用于分支）
- `p`       &emsp; #打印输出
- `d`       &emsp; #删除，仅删除pattern buffer中的数据，源文件不受影响。删除源文件内容加上`-i`
- https://www.tutorialspoint.com/sed/sed_basic_commands.htm

### 地址范围

- `sed '3p' file`       &emsp; #打印第3行，如果不加数字只有命令p，则打印所有行
- `sed '$p' file`       &emsp; #打印最后一行
- `sed 'm,n p' file`        &emsp; #打印第m行到第n行的内容
- `sed 'm,+n p' file`       &emsp; #打印从第m行到m+n行的内容或者说从第m行开始接下来的n行
- `sed 'm~n p' file`        &emsp; #从第m行开始，每隔n行打印一次（包括m行）。见示例

### 模式匹配

> A pattern range can be a simple text or a complex regular expression.

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

### 循环和分支

- sed 可以实现类似`goto` 的功能，sed可以跳转到**标签**标记的行，然后继续执行后续的命令。标签的定义格式如下(冒号后面就是标签名)：
```
:label 
:start 
:end 
:up
```
