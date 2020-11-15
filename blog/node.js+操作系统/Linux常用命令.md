<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-09-28 15:05:38
 * @FilePath: \opencvc:\Users\23163\Desktop\web\Blog\blog\Linux常用命令.md
 * @Description: add some description
-->
## cp

> -a 将文件的特性一起复制

> -p 连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份

> -i 若目标文件已经存在时，在覆盖时会先询问操作的进行

> -r 递归持续复制，用于目录的复制行为

> -u 目标文件与源文件有差异时才会复制

```
cp [options] source dest
```

## vim

**命令模式**：

用户刚刚启动vim，便进入了命令模式。

此状态下敲击键盘动作会被vim识别为命令，而非输入字符

常用的命令：

- i 切换到输入模式，以输入字符

- x 删除当前光标所在处的字符

- : 切换到底线命令模式，以在最低一行输入命令

**输入模式**：

- 字符按键以及Shift组合，输入字符

- ENTER，回车换行

- DEL，删除光标后的一个字符

- 方向键，在文本中移动光标

- Page Up/Down，上/下翻页

- Insert，切换光标为输入/替换模式

- ESC，退出输入模式，切换到命令模式

**底线命令模式**：

在命令模式下按:就进入了底线命令模式

基本的命令有：

- q 退出程序

- w 保存文件

- x 保存退出

按ESC键可随时退出底线命令模式


## rm

> -f 就是force的意思，忽略不存在的文件，不会出现警告信息

> -i 互动模式，在删除前会询问用户是否操作

> -r 递归删除，最常用于目录删除，它是一个非常危险的参数

``` 
rm -r -f dist
```



## scp

1. 从服务器下载文件

```  
scp panda@182.92.201.214:/tmp/dist /tmp/local
```

2. 上传本地文件到服务器

```
scp /tmp/local/dist panda@182.92.201.214:/tmp/dist
```

> -r 上传整个目录

## ssh

用于远程登录上Linux主机

``` 
ssh [-l login_name] [-p port] [user@]hostname
```