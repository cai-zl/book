# Linux / Shell 常用命令

## 文件与目录

```bash
# 目录操作
pwd                           # 当前目录
ls -la                        # 详细列表（含隐藏文件）
tree -L 2                     # 树形展示（2 层）

# 文件操作
cp -r src/ dest/              # 递归复制
mv old new                    # 移动 / 重命名
rm -rf dir/                   # 递归强制删除
mkdir -p a/b/c                # 递归创建目录

# 查找
find . -name "*.log"          # 按名称查找
find . -mtime -7              # 7 天内修改的文件
find . -type d -name "node_modules" -exec rm -rf {} +   # 批量删除

# 磁盘
du -sh *                      # 各目录大小
du -sh * | sort -rh | head    # 排序取前 N
df -h                         # 磁盘使用情况
```

## 文本处理

```bash
# 查看文件
cat file
head -n 20 file               # 前 20 行
tail -f file                  # 实时跟踪（看日志常用）
tail -n 100 file              # 最后 100 行

# 搜索
grep "pattern" file
grep -r "pattern" dir/        # 递归搜索
grep -i "pattern" file        # 忽略大小写
grep -n "pattern" file        # 显示行号

# 文本处理
sort file                     # 排序
sort file | uniq              # 排序去重
sort file | uniq -c | sort -rn    # 计数排序
wc -l file                    # 行数
awk '{print $1}' file         # 提取第一列
sed -i 's/old/new/g' file     # 替换文本
```

## 进程管理

```bash
# 查看进程
ps aux                        # 所有进程
ps aux | grep <name>          # 搜索进程
pgrep -f <name>               # 获取 PID

# 终止进程
kill <pid>
kill -9 <pid>                 # 强制终止
pkill -f <name>               # 按名称终止
killall <name>                # 终止同名进程

# 系统监控
top                           # 实时进程
htop                          # 增强版（需安装）
```

## 网络

```bash
# 端口与连接
netstat -tlnp                 # 监听端口
ss -tlnp                      # 更快的替代
lsof -i :8080                 # 查看端口占用

# 网络请求
curl <url>                    # GET 请求
curl -X POST -d 'data' <url>  # POST 请求
curl -o file.zip <url>        # 下载文件
wget <url>                    # 下载文件

# DNS
nslookup <domain>
dig <domain>
```

## 权限与用户

```bash
# 权限
chmod 755 file                # rwxr-xr-x
chmod +x script.sh            # 添加执行权限
chown user:group file         # 修改所有者

# 用户
whoami                        # 当前用户
sudo -u <user> <command>      # 以其他用户执行
```

## 压缩与解压

```bash
# tar
tar -czf archive.tar.gz dir/          # 压缩
tar -xzf archive.tar.gz               # 解压
tar -xzf archive.tar.gz -C /target    # 解压到指定目录

# zip
zip -r archive.zip dir/
unzip archive.zip
```

## 压缩文件查看（不解压）

```bash
# 查看压缩文件内容
zcat file.gz                     # 查看 gzip 文件（等价于 gunzip -c）
zless file.gz                    # 分页查看 gzip 文件
zgrep "pattern" file.gz          # 在 gzip 文件中搜索
zdiff file1.gz file2.gz          # 比较两个 gzip 文件

# bz2 格式
bzcat file.bz2
bzgrep "pattern" file.bz2

# xz 格式
xzcat file.xz

# zstd 格式
zstdcat file.zst

# 查看 tar.gz / tar.bz2 内容（不解压）
tar -tzf archive.tar.gz          # 列出 gzip 包内文件
tar -tjf archive.tar.bz2         # 列出 bz2 包内文件

# 结合管道使用
zcat access.log.gz | grep "ERROR" | wc -l          # 统计日志中错误数
zcat access.log.1.gz access.log.2.gz | grep "404"   # 同时查多个压缩日志
```

## 其他实用命令

```bash
# 环境变量
echo $PATH
export VAR=value              # 设置环境变量
env                           # 查看所有环境变量

# 定时任务
crontab -l                    # 查看定时任务
crontab -e                    # 编辑定时任务

# 系统信息
uname -a                      # 系统信息
uptime                        # 运行时间
free -h                       # 内存使用
```
