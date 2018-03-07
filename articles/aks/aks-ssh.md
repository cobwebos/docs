---
title: "以 SSH 方式登录到 Azure 容器服务 (AKS) 群集节点"
description: "创建与 Azure 容器服务 (AKS) 群集节点的 SSH 连接"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>以 SSH 方式登录到 Azure 容器服务 (AKS) 群集节点

有时，你可能需要访问 Azure 容器服务 (AKS) 节点来进行维护、收集日志以及执行其他故障排除操作。 Azure 容器服务 (AKS) 节点不会公开给 Internet。 请使用本文档中详细介绍的步骤来创建与 AKS 节点的 SSH 连接。

## <a name="configure-ssh-access"></a>配置 SSH 访问

 若要以 SSH 方式登录到特定节点，请创建一个具有 `hostNetwork` 访问权限的 pod。 还会创建用于 pod 访问的一个服务。 此配置是一项特权配置，在使用后应当删除。

创建一个名为 `aks-ssh.yaml` 的文件并将其复制到此清单中。 将节点名称更新为目标 AKS 节点的名称。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

运行清单来创建 pod 和服务。

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

获取已公开的服务的外部 IP 地址。 可能需要花费一分钟时间才能完成 IP 地址配置。 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

创建 ssh 连接。 

AKS 群集的默认用户名是 `azureuser`。 如果此帐户在创建群集时已更改，请将其替换为正确的管理员用户名。 

如果密钥不在 `~/ssh/id_rsa`，请使用 `ssh -i` 参数提供正确的位置。

```azurecli-interactive
$ ssh azureuser@13.92.154.191

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>删除 SSH 访问

完成后，删除 SSH 访问 pod 和服务。

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```