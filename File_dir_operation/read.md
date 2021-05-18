#read

### 命令格式
- read [options] [name...]

### 注意事项

- The `read` command is used to split a line of input into words.
- 接收参数可以是用户终端输入、标准输入、管道
- 默认的定界符`空格``tab``newline(换行符)`，但注意：如果`read`用来接收参数的变量名比用户实际传入的参数少，那么最后所有的内容全部存到最后一个变量中，包括定界符。见示例
- 如果接收参数的变量比实际接收的参数多（和上面刚好相反），那么其余的变量值将是空
- 如果没有设置变量接收read输入，那么会将整行内容赋值给变量`REPLY`，比如`echo "Hello, world!" | (read; echo "$REPLY")`
- 修改定界符，默认是以`空格` `tab` `\n`作为定界符的，自定义定界符的方法：用`IFS=':'`指定`:`为定界符。见示例
- 还可以用`IFS`指定多个定界符，如`IFS=-_`, 见示例

### 选项

- `-r`        &emsp; #禁用`\`转义, 没加`-r`，`\t`就是`t`, 加了`-r`，`\t`就是`\t`
- `-p`        &emsp; #用户交互时，提示字符串给用户
- `-s`        &emsp; #不会显示用户输入内容，比如需要用户输入密码，就可以使用此选项
- `-a`        &emsp; #默认是把参数存储到多个变量中，此选项是把参数存储到一个**数组**中。
- `-t`        &emsp; #timeout, 在指定时间没有读取完，会返回失败。设置`0`表示立即返回，不会读取任何内容。

### 示例

- `echo "Hello, World!" | (read var1 var2; echo -e "$var1 \n$var2")`        &emsp; #从管道接收参数
- `echo "He ll o, World!" | (read var1 var2; echo -e "$var1 \n$var2")`        &emsp; #实际传入的参数是4个（以空格分隔）但是read后面只有两个变量接收，所以最终`var1=He``var2=ll o, World!`
- `echo 'Linux_is-awesome.' | (IFS="-_" read -r var1 var2 var3; echo -e "$var1 \n$var2 \n$var3")`       &emsp; #指定多个分隔符
- `echo "Linux:is:awesome." | (IFS=":" read -r var1 var2 var3; echo -e "$var1 \n$var2 \n$var3")`        &emsp; #指定一个分隔符
- `read -r -s -p "Enter your password: "`       &emsp; #`-s` 不显示用户输入内容
- `read -r -a MY_ARR <<< "Linux is awesome."`       &emsp; #把参数存到数组中

### 参考资料
- https://linuxize.com/post/bash-read/
