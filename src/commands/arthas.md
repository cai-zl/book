# Arthas 常用命令

Arthas 是阿里巴巴开源的 Java 诊断工具，可以在不重启应用的情况下排查线上问题。

## 安装与启动

```bash
# 下载
curl -O https://arthas.aliyun.com/arthas-boot.jar

# 启动（列出当前 Java 进程，选择要诊断的）
java -jar arthas-boot.jar

# 直接附加到指定 PID
java -jar arthas-boot.jar <pid>

# 退出
quit
stop            # 退出并卸载增强代理
```

## 查看类与方法信息

```bash
# 搜索已加载的类
sc -d com.example.MyClass

# 搜索方法
sm com.example.MyClass *

# 查看方法源码（反编译）
jad com.example.MyClass methodName

# 查看类路径下的 classloader
sc -d -c <hash> com.example.MyClass
```

## 方法执行监控

```bash
# 监控方法调用（每 5 秒统计一次）
monitor com.example.MyService query -c 5

# 观察方法参数和返回值
watch com.example.MyService query "{params, returnObj}" -x 2

# 观察方法抛出的异常
watch com.example.MyService query "{params, throwExp}" -e -x 2

# 追踪方法调用链路及耗时
trace com.example.MyService query

# 追踪多次调用（过滤耗时 > 100ms 的）
trace com.example.MyService query '#cost > 100'

# 记录方法调用数据，事后查看
tt -t com.example.MyService query

# 查看 tt 记录列表
tt -l

# 查看 tt 某条记录详情
tt -i 1000
```

## 热更新

```bash
# 反编译类并保存到文件
jad --source-only com.example.MyClass > /tmp/MyClass.java

# 编译修改后的类（指定 classloader）
mc /tmp/MyClass.java -c <classloaderHash>

# 重新加载类
retransform /tmp/com/example/MyClass.class

# 查看已热更新的记录
retransform -l

# 撤销热更新
retransform -d 1
```

## 线程与性能

```bash
# 查看线程概览
thread

# 查看 CPU 占用最高的 3 个线程
thread -n 3

# 查看死锁
thread -b

# 查看指定线程的堆栈
thread <threadId>

# 生成火焰图
profiler start
profiler stop               # 默认生成 CPU 火焰图到 ./arthas-output/

# 生成内存分配火焰图
profiler start --event alloc
profiler stop
```

## 排查内存与 GC

```bash
# 查看 JVM 信息
vmtool --action getInstances --className java.lang.String --limit 10

# 查看 Heap 信息
heapdump /tmp/dump.hprof

# 查看对象在内存中的大小（需要 class 统计）
vmtool --action getInstances --className com.example.MyClass --limit 5

# 查看 MBean 信息（含 GC 统计）
mbean | grep -i gc

# 查看垃圾收集器信息
vmoption | grep -i gc
```

## 其他常用

```bash
# 查看 Spring 上下文中的 Bean
vmtool --action getInstances --className org.springframework.context.ApplicationContext

# 查看 Logger 配置
logger --name com.example

# 动态修改日志级别
logger --name com.example --level debug

# 查看 System Properties
sysprop

# 查看 Environment Variables
sysenv

# 查看 OGNL 表达式结果
ognl '@com.example.MyClass@staticField'

# Dashboard（实时总览）
dashboard

# 导出 arthas 会话记录
session
```
