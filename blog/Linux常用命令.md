<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-09-27 19:33:52
 * @FilePath: \myblogc:\Users\23163\Desktop\web\Blog\blog\Linux常用命令.md
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