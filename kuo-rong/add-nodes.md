# 添加节点

## 常规操作

K8S环境中添加PX节点，只需要给新的节点打上**px/enabled=true**的标签即可。

```text
$ kubectl label node k8s-node-4 px/enabled=true
```

## 如何应对异构环境





