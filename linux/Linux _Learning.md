## Linux

是一个基于 POSIX 和 UNIX 的多用户、多任务、支持多线程和多 CPU 的操作系统。Linux 能运行主要的 UNIX 工具软件、应用程序和网络协议。它支持 32 位和 64 位硬件。Linux 继承了 Unix 以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。

### Linux常用命令学习

1.ls命令：查看Linux文件夹包含的文件，且可以查看文件权限(包括目录、文件夹、文件权限)

```go
ls -a //列出目录所有文件，包含以.开始的隐藏文件
ls -A //列出除.及..的其他文件
ls -r //反序排列
ls -t //以文件修改时间排序
ls -s //以文件大小排序
ls -h //以易读大小显示
ls -l //除了文件名之外，还将文件的权限、所有者、文件大小等信息详细列出来
```

例子：

```go
ls -lhrt //按易读方式按时间反序排列，并显示文件的详细信息
ls -lrs //按文件大小反序排列并显示详细信息
ls -l t* //列出当前目录中所有以“t”开头的目录的详细内容
ls | sed "s:^:`pwd`/:"   //列出文件绝对路径（不包含隐藏文件）
find $pwd -maxdepth 1 | xargs ls -ld  //列出文件绝对路径（包含隐藏文件）
```

2.cd命令：更改目录，切换目录

```go
cd [目录名] //切换当前目录到dirName
```

例子:

```go
cd /     //进入要目录
cd ~     //进入"home"目录
cd -     //进入上一次工作路径
cd !$    //把上个命令参数作为cd参数使用
```

3.pwd命令：查看当前工作目录路径

```go
pwd //查看当前路径
pwd -p //查看软链接的实际路径
```

4.mkdir命令：用于创建文件夹

"-m"：对新建目录设置存取权限，也可以用chmod命令

"-p":可为一个路径名称。此时若路径中的某些目录不存在，加上此选项后，系统将自动建立好那些尚不存在的目录，即一次可以建立多个目录

```go
mkdir y //当前工作目录下创建名为y的文件夹
mkdir -p /tmp/test/y1/y     //在tmp目录下创建路径为test/y1/y的目录
```

5.rm命令：删除一个目录中
