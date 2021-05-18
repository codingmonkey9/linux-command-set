#declare

> 功能：声明变量属性：整型，可读，环境变量等

> 变量默认类型：字符串 `sum=1+2+3` 那么`echo $sum` 输出的是`1+2+3`，而不是6.  `declare -i sum=1+2+3; echo $sum` 声明变量类型为整型，输出的就是6 了。
> bash 中只有整型，没有浮点型，即1/3的结果是0

### 格式

- `declare [选项] 变量`

### 选项

- `-a`        &emsp; #将变量定义为数组类型
- `-i`        &emsp; #将变量定义为整型
- `-x`        &emsp; #将变量定义为环境变量
- `-r`        &emsp; #将变量定义为readonly（只读属性不允许修改变量，也不能unset）