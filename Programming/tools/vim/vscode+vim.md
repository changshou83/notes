## vim高效的原因

### 模式(mode)

普通模式(normal)

插入模式(insert)

命令行模式(command line)

可视模式(visual)

#### 普通模式切换到其他模式

**普通模式进入插入模式：**

行内：

	光标前：`i(nsert)`

	行首：`I`

	光标后：`a(ppend)`

	行尾：`A`

行间：

	上一行：`o(pen)`

	下一行：`O`

**普通模式进入到可视模式**：`v`

**普通模式进入到命令模式**：`:`

### 光标移动

以字符为单位：`h j k l`

以单词为单位：

	跳到单词开头：`w(ord) b(ack)`

	跳到单词结尾：`e(nd) ge`

以行为单位：

	跳到行首：`0`

	跳到第一个非空字符：`^`

	跳到行尾：`$`

	跳到第一行：`gg`

	跳到最后一行：`G`

搜索：

	行间搜索：`f{char} F{char}`

	重复操作：`; ,`

### 动作(motion)

`i(nner){char}`和`a(round){char}`的区别：inner不包含字符，around包含字符

`b`在与`i`和`a`的配合中中表示`b(racket)`，而不是`b(ack)`，即小括号

`e`也是特殊的，表示`e(ntitle)`，而不是`e(nd)`

`t`也是特殊的，表示`t(ag)`，而不是`t(ill)`

再加上光标移动里的那些。

操作符和动作间还可以加上数字。

### 操作符(operator)

删除：`d(elete)`

修改：`c(hange)`

复制：`y(ank)`

粘贴：`p(aste)`

撤销：`u(ndo)`

撤销撤销：`U(ndo)`

选中并进入可视模式：`v(isual)`

## VSCode常用操作对应替代

查看函数定义：`g(oto)d(efination)`

查看函数签名：`g(oto)h(over)`

切换标签页：`gt gT <number>gt alt<number>`

跳到侧边栏：`<C-0>`

从侧边栏切换回来：`l`

打开文件：`Enter`

分屏：`<C-\>`

分屏切换：`<C-number>`

## EasyMotion

作用：快速跳转

用法：`<leader> <leader> <motion>`

## Surround

作用：快速修改包裹的元素

用法：

`y s <motion> <desired>`

`d s <existing>`

`c s <existing> <desired>`

例如：

快速添加大括号：`ysw{`

修改数组为对象：`cs[{`

修改双引号为单引号：`cs"'`

删除双引号：`ds"`

在两边添加标签：`yswtdiv`

## vim 替代常用操作

向下复制：`yyp`

上移：`alt + ↑`

缩进：`shift + >>`

快速添加括号：`ysw(`

搜索：`/{chars}`

替换：`:%s/text/replace/g`，`:start,ends/text/replace/g`

切换文件：`gt`，`alt + <number>`

开一个新Tab：`ctrl + \ `

切换Tab：`ctrl + <number>`

切换到侧栏：`ctrl + 0`

再切回来：`l`

开关终端：`ctrl + j`

开关侧栏：`ctrl + shift + b`(改键了)

切换侧栏：`ctrl + shift + j/k`(改键了)

开关代码块：`shift + {`

查看函数签名：`gh`

查看函数定义：`gd`，`ctrl + shift + l`(改键了)

从函数定义退回来：`alt + ⬅`，`ctrl + shift + h`(改键了)

快速退出插入模式：`jj`

快速跳转：`<leader> <leader> w/b/j/k/s/f`

快速打开文件：`ctrl + p`

快速删除小括号内的字符：`dib`
