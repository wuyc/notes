## 安装 libfastcommon

```shell
cd /usr/local/
git clone https://github.com/happyfish100/libfastcommon.git
cd libfastcommon/
./make.sh
./make.sh install
```

## 安装 fastdfs

```shell
cd /usr/local/
git clone https://github.com/happyfish100/fastdfs.git
cd fastdfs/
./make.sh
./make.sh install
```

## fastdfs 服务脚本

```
/etc/init.d/fdfs_storaged
/etc/init.d/fdfs_trackerd
```

## fastdfs 配置文件

```
/etc/fdfs/client.conf.sample
/etc/fdfs/storage.conf.sample
/etc/fdfs/tracker.conf.sample
```

## fastdfs 命令行工具

```shell
ls /usr/bin/ | grep fdfs

fdfs_appender_test
fdfs_appender_test1
fdfs_append_file
fdfs_crc32
fdfs_delete_file
fdfs_download_file
fdfs_file_info
fdfs_monitor
fdfs_regenerate_filename
fdfs_storaged
fdfs_test
fdfs_test1
fdfs_trackerd
fdfs_upload_appender
fdfs_upload_file
```

## 配置 tracker.conf

```shell
cd /etc/fdfs
cp tracker.conf.sample tracker.conf
vim /etc/fdfs/tracker.conf

# 默认为false，表示是否无效
disabled=false
# 默认为22122
port=22122
# Tracker 数据和日志目录地址(根目录必须存在,子目录会自动创建)
base_path=/root/fastdfs/tracker
```

## 配置 storage.conf

```shell
cd /etc/fdfs
cp storage.conf.sample storage.conf
vim /etc/fdfs/storage.conf

# 默认为false，表示是否无效
disabled=false
# 默认为23000
port=23000
# 指定组名
group_name=group1
# 用于存储数据
base_path=/root/fastdfs/storage
# 设置设备数量
store_path_count=2
# 指定存储路径0
store_path0=/root/fastdfs/storage/file
# 指定存储路径1
store_path1=/root/fastdfs/storage/file2
# 注意：同一组内存储路径不能冲突，例如：下一个节点的存储路径就是file2,file3…
# 指定 tracker
tracker_server=192.168.0.109:22122
# 默认为 8888，nginx 中配置的监听端口那之一致
http.server_port=80
```

## 配置 client.conf

```shell
cd /etc/fdfs
cp client.conf.sample client.conf
vim /etc/fdfs/client.conf

# client 的数据和日志目录
base_path=/root/fastdfs/client
tracker_server=192.168.0.109:22122
```

## 创建 base_path 目录

```shell
mkdir /root/fastdfs
mkdir /root/fastdfs/tracker
mkdir /root/fastdfs/storage
mkdir /root/fastdfs/client
```

## 防火墙开放服务端口

```shell
systemctl status firewalld                     # 查看防火墙状态
systemctl start firewalld                      # 开启防火墙
systemctl stop firewalld                       # 关闭防火墙

firewall-cmd 常用命令介绍
firewall-cmd --state                           ##查看防火墙状态，是否是running
firewall-cmd --reload                          ##重新载入配置，比如添加规则之后，需要执行此命令
firewall-cmd --get-zones                       ##列出支持的zone
firewall-cmd --get-services                    ##列出支持的服务，在列表中的服务是放行的
firewall-cmd --query-service ftp               ##查看ftp服务是否支持，返回yes或者no
firewall-cmd --add-service=ftp                 ##临时开放ftp服务
firewall-cmd --add-service=ftp --permanent     ##永久开放ftp服务
firewall-cmd --remove-service=ftp --permanent  ##永久移除ftp服务
firewall-cmd --add-port=80/tcp --permanent     ##永久添加80端口
iptables -L -n                                 ##查看规则，这个命令是和iptables的相同的
man firewall-cmd                               ##查看帮助
```

## 启动 tracker

```shell
service fdfs_trackerd start      # CentOS 6
/etc/init.d/fdfs_trackerd start  # CentOS 7
```

## 启动 storage

```shell
service fdfs_storage start      # CentOS 6
/etc/init.d/fdfs_storage start  # CentOS 7
```

## 测试通讯

```shell
fdfs_monitor /etc/fdfs/storage.conf # 查看Storage和Tracker是否在通信

fdfs_upload_file /etc/fdfs/client.conf /root/test.png # 文件上传测试
group1/M00/00/00/wKjbFVoXuUqALADZAAdIM2UokFM563.png
返回文件ID即说明文件已经上传成功
```



## 参考资料

- https://www.cnblogs.com/chiangchou/p/fastdfs.html
- https://blog.csdn.net/alex_bean/article/details/78625131
- http://www.ityouknow.com/fastdfs/2018/01/06/distributed-file-system-fastdfs.html

