# tar命令参数
### tar命令选项：
1. -z(gzip): 用gzip来压缩/解压缩文件
2. -j(bzip2): 用bzip2来压缩/解压缩文件
3. -v(verbose): 详细报告tar处理的文件信息
4. -c(create): 创建新的档案文件
5. -x(extract): 解压缩文件或目录
6. -f(file): 使用档案文件或设备，这个选项通常是必选的。

### 示例

~~~shell
[root@ubuntu]# tar -zvxf buodo.tar.gz 
[root@ubuntu]# tar -jvxf buodo.tar.bz2
~~~

