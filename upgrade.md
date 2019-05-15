---
description: Upgrade
---

# 升级

## 准备

## K8S环境下升级

```text
kubectl label node --all px/enabled-
```

```text
kubectl apply -f px-yamls/oci/oci-monitor.yaml
```

```text
kubectl label node <NODE_NAME> px/enabled=true
```

## 非K8S环境下升级

```text
px-tools/cmd/px-node.sh upgrade --tag <NEW_PX_VERSION>
```

## 常见问题

