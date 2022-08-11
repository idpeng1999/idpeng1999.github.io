# Linux常用命令行

## ls命令
   list 的缩写,可以查看文件权限(包括目录、文件夹、文件权限),查看目录信息。

```shell
ls -a 列出目录所有文件，包含以.开始的隐藏文件
ls -A 列出除.及..的其它文件
ls -r 反序排列
ls -t 以文件修改时间排序
ls -S 以文件大小排序
ls -h 以易读大小显示
ls -l 除了文件名之外，还将文件的权限、所有者、文件大小等信息详细列出来
```

## cd命令

```shell
cd [目录名] 进入目录
```

## pwd命令

```shell
pwd 查看当前路径
```

## mkdik命令

```shell
mkdir t 在当前工作目录下创建名为 t的文件夹
mkdir -p /tmp/test/t1/t 在 tmp 目录下创建路径为 test/t1/t 的目录，若不存在，则创建
```

## rm命令

```shell
rm -i *.log 删除任何 .log 文件，删除前逐一询问确认
rm -rf test 删除 test 子目录及子目录中所有档案删除，并且不用一一确认
rm -- -f* 删除以 -f 开头的文件
```

## mv命令

 ```shell
mv test.log test1.txt 将文件 test.log 重命名为 test1.txt
mv llog1.txt log2.txt log3.txt /test3 将文件 log1.txt,log2.txt,log3.txt 移动到根的 test3 目录中
mv -i log1.txt log2.txt 将文件 file1 改名为 file2，如果 file2 已经存在，则询问是否覆盖
mv * ../ 移动当前文件夹下的所有文件到上一级目录
```

## cp命令
```shell
-i 提示
-r 复制目录及目录内所有项目
-a 复制的文件与原文件时间一样
```

## cat命令-查看文件内容
```shell
cat filename 一次显示整个文件
cat > filename 从键盘创建一个文件
cat file1 file2 > file 将几个文件合并为一个文件
```

```shell
sed -n "10p" move.sh   # 显示第10行
sed -n "7,10p" move.sh # 显示第7行到第10行
cat recorde | head -n 10|tail -n 1 # 显示第10行，先显示前10行，再显示这10行中的最后一行
cat recorde | tail -n +10 | head -n 1 # 显示第10行；先显示第10行及以后的内容，再显示这里的第一行
tail -n 100 #显示最后100行
tail -n +1000 # 从1000行开始显示，显示1000行以后所有行
head -n 1000 # 显示前面1000行
```

## chmod 赋予权限

```shell
chmod 644 mm.txt  即设定文件mm.txt的属性为：-rw-r--r--
```

* **r(Read，读取，权限值为4)**：对文件而言，具有读取文件内容的权限；对目录来说，具有浏览目 录的权限。 
* **w(Write,写入，权限值为2)**：对文件而言，具有新增、修改文件内容的权限；对目录来说，具有删除、移动目录内文件的权限。 
* **x(eXecute，执行，权限值为1)**：对文件而言，具有执行文件的权限；对目录了来说该用户具有进入目录的权限。

```shell
rwx rw- r– 764
rw- r– r– 644
rw- rw- r– 664
```


## more 命令
   功能类似于 cat, more 会以一页一页的显示方便使用者逐页阅读，而最基本的指令就是按空白键（space）就往下一页显示，按 b 键就会往回（back）一页显示。
### 命令参数：
```shell
+n      从笫 n 行开始显示
-n       定义屏幕大小为n行
+/pattern 在每个档案显示前搜寻该字串（pattern），然后从该字串前两行之后开始显示 
-c       从顶部清屏，然后显示
-d       提示“Press space to continue，’q’ to quit（按空格键继续，按q键退出）”，禁用响铃功能
-l        忽略Ctrl+l（换页）字符
-p       通过清除窗口而不是滚屏来对文件进行换页，与-c选项相似
-s       把连续的多个空行显示为一行
-u       把文件内容中的下画线去掉
```
### 常用操作命令
```shell
Enter    向下 n 行，需要定义。默认为 1 行
Ctrl+F   向下滚动一屏
空格键  向下滚动一屏
Ctrl+B  返回上一屏
=       输出当前行的行号
:f     输出文件名和当前行的行号
V      调用vi编辑器
!命令   调用Shell，并执行命令
q       退出more
```
## which 命令
```shell
which     查看可执行文件的位置。
whereis 查看文件的位置。
locate  配合数据库查看文件位置。
find        实际搜寻硬盘查询文件名称。
```



