# Kubectl 常用命令

## 资源查看

```bash
# 查看资源列表
kubectl get nodes                      # 节点
kubectl get pods                       # Pod
kubectl get pods -A                    # 所有命名空间
kubectl get pods -o wide               # 显示更多信息（IP、节点）
kubectl get svc                        # Service
kubectl get deploy                     # Deployment
kubectl get ingress                    # Ingress
kubectl get cm                         # ConfigMap
kubectl get secret                     # Secret
kubectl get ns                         # 命名空间

# 查看所有资源
kubectl get all

# 查看资源详细信息
kubectl describe pod <pod>
kubectl describe svc <svc>
```

## 命名空间

```bash
# 指定命名空间
kubectl get pods -n <namespace>

# 设置默认命名空间
kubectl config set-context --current --namespace=<namespace>

# 创建命名空间
kubectl create ns <name>
```

## Pod 操作

```bash
# 查看 Pod 日志
kubectl logs <pod>
kubectl logs -f <pod>                          # 实时跟踪
kubectl logs --tail=100 <pod>                  # 最后 100 行
kubectl logs <pod> -c <container>              # 指定容器
kubectl logs <deployment> --all-containers     # 所有容器

# 进入 Pod
kubectl exec -it <pod> -- /bin/bash
kubectl exec -it <pod> -- /bin/sh              # Alpine 用 sh

# 端口转发（本地调试）
kubectl port-forward <pod> 8080:80
kubectl port-forward svc/<svc> 8080:80

# 拷贝文件
kubectl cp <pod>:/path/to/file ./local/file
kubectl cp ./local/file <pod>:/path/to/file
```

## 部署与扩缩

```bash
# 应用配置
kubectl apply -f manifest.yaml
kubectl apply -f dir/                       # 目录下所有 yaml
kubectl apply -f https://url/to/manifest    # 远程 yaml

# 删除资源
kubectl delete -f manifest.yaml
kubectl delete pod <pod>
kubectl delete deploy <deploy>

# 扩缩容
kubectl scale deploy <deploy> --replicas=3

# 重启 Deployment（滚动重启）
kubectl rollout restart deploy <deploy>

# 查看滚动更新状态
kubectl rollout status deploy <deploy>

# 回滚
kubectl rollout undo deploy <deploy>
kubectl rollout undo deploy <deploy> --to-revision=2

# 查看历史版本
kubectl rollout history deploy <deploy>
```

## 编辑资源

```bash
# 编辑资源（打开编辑器）
kubectl edit deploy <deploy>
kubectl edit svc <svc>
kubectl edit cm <configmap>

# 设置镜像
kubectl set image deploy/<deploy> <container>=<image>:<tag>

# 打标签
kubectl label pod <pod> key=value
kubectl label node <node> key=value
```

## 排查调试

```bash
# 查看事件
kubectl get events --sort-by='.lastTimestamp'
kubectl get events -n <namespace>

# 查看资源占用
kubectl top nodes
kubectl top pods
kubectl top pods -A

# 查看资源定义（导出 yaml）
kubectl get pod <pod> -o yaml
kubectl get deploy <deploy> -o yaml

# 运行临时 Pod（调试用）
kubectl run tmp --rm -it --image=busybox -- sh
kubectl run tmp --rm -it --image=curlimages/curl -- sh
```

## Context 与集群

```bash
# 查看上下文
kubectl config get-contexts

# 切换上下文
kubectl config use-context <context>

# 查看当前上下文
kubectl config current-context

# 查看集群信息
kubectl cluster-info
```
