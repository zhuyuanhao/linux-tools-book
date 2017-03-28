## 查看内存
```bash
$ free -mh
             total       used       free     shared    buffers     cached
Mem:          7.7G       4.3G       3.4G       118M       299M       852M
-/+ buffers/cache:       3.2G       4.5G
Swap:         7.9G         0B       7.9G
```
* total——总物理内存
* used——已使用内存，一般情况这个值会比较大，因为这个值包括了cache+应用程序使用的内存
* free——完全未被使用的内存
* shared——应用程序共享内存
* buffers——Buffer Cache缓存，主要用于目录方面,inode值等（ls大目录可看到这个值增加）
* cached——Page Cache缓存，用于已打开的文件
* -buffers/cache——应用程序使用的内存大小，used减去缓存值
* +buffers/cache——所有可供应用程序使用的内存大小，free加上缓存值

## 清理内存
```bash
$ sudo su

# 将所有未写的系统缓冲区写到磁盘中，包含已修改的 i-node、映射文件
$ sync

# free pagecache
$ echo 1 > /proc/sys/vm/drop_caches

# free dentries and inodes
$ echo 2 > /proc/sys/vm/drop_caches

# free pagecache, dentries and inodes
$ echo 3 > /proc/sys/vm/drop_caches
```