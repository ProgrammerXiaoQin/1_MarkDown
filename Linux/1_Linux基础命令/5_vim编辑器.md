##### 一. 常用操作
1. 视图模式下 , 按下# (shift+3), 会高亮显示光标后的单词(文件中所有该单词都会高亮显示) , 一般用于搜索关键词 , 在命令模式输入`:nohl`即可取消高亮
2. 命令模式下，`w filename` 保存`filename` 到当前文件夹（vim打开文件的位置）下 ， 可以用`:w %.bak`  备份当前文件到该文件夹下的`文件名.bak`

4. 快捷键
	1. 一般模式进入或退出编辑模式
		1. `a` : 当前光标后插入 ,  `A` : 行尾插入
		2. `i` : 当前光标前插入 ,  `I` : 行首插入
		3. `o` : 在当前光标下方插入空行 , `O` 上方插入空行
		4. `r` : 取代光标所在的那一个字符一次 ,  `R` : 一直取代光标所在的文字，直到按下 Esc 为止
		5. `Esc` : 回到一般模式
	2. 命令模式
		1. `[ctrl] + f` : 向下翻页
		2. `[ctrl] + b` : 向上翻页 
		3. `gg` : 光标跳转首行
		4. `G` : 光标跳转行尾
		5. `0` : 光标移动至行首 , `$` : 移动至行尾
		6. `p` : 将已复制的数据粘贴在光标下一行 , `P` : 粘贴至上一行
		7. `yy` : 复制当前光标所在行 , `nyy` : 复制从光标所在行开始的n行
		8. `dd` : 剪切当前光标所在行 , `ndd` : 剪切从光标所在行开始的n行
		9. `x` : 向后删除一个字符 , `X` 向前删除一个字符
		10. `u` : 撤销上一次修改，可以撤销多次
		11. `[Ctrl] + r` 重做上一个动作
	3. 末行模式
		1. `:q` : 不保存退出
		2. `:q!` : 强制退出
		3. `:wq` : 保存退出
		4. `/word` : 向光标之下寻找一个名称为 word 的字符串
		5. `?word` : 向光标之上寻找一个名称为 word 的字符串
		6. `:s/old/new/g` : 在当前行中查找到的所有字符串old替换为new
		7. `:%s/old/new/g` : 在整个文件范围内查找替换

##### 二. 文件目录
1. 当前文件名
	1. `%` 只读寄存器，保存着当前路径（Vim有48个寄存器）
	2. 在`/home/harttle/`下打开`src/main.cpp`，我们打印`%`的值
	3. `:echo @%                src/main.cpp` ，`src/main.cpp` 代表打开 

2. 利用%进行文件操作
	1. `:w %.bak`  备份当前文件`main.cpp`到`main.cpp.bak`
