---
title: 以 SSH 方式登录到 Azure 容器服务 (AKS) 群集节点
description: 创建与 Azure 容器服务 (AKS) 群集节点的 SSH 连接
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 085a2976443db8ece7a36dbfc133b173432ce4c8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>以 SSH 方式登录到 Azure 容器服务 (AKS) 群集节点

有时，你可能需要访问 Azure 容器服务 (AKS) 节点来进行维护、收集日志以及执行其他故障排除操作。 Azure 容器服务 (AKS) 节点不会公开给 Internet。 请使用本文档中详细介绍的步骤来创建与 AKS 节点的 SSH 连接。

## <a name="get-aks-node-address"></a>获取 AKS 节点地址

使用 `az vm list-ip-addresses` 命令获取 AKS 群集节点的 IP 地址。 将资源组名称替换为你的 AKS 资源组的名称。

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>创建 SSH 连接

运行 `debian` 容器映像并将一个终端会话附加到它。 然后，可以使用该容器来建立与 AKS 群集中的任意节点的 SSH 会话。

```console
kubectl run -it --rm aks-ssh --image=debian
```

在容器中安装 SSH 客户端。

```console
apt-get update && apt-get install openssh-client -y
```

打开另一个终端并列出所有 pod 以获取新创建的 pod 名称。

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

将你的 SSH 密钥复制到该 pod，将 pod 名称替换为正确的值。

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

更新 `id_rsa` 文件以使其仅可供用户读取。

```console
chmod 0600 id_rsa
```

现在，创建到 AKS 节点的 SSH 连接。 AKS 群集的默认用户名是 `azureuser`。 如果此帐户在创建群集时已更改，请将其替换为正确的管理员用户名。

```console
$ ssh -i id_rsa azureuser@10.240.0.6

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

完成后，退出 SSH 会话，然后退出交互式容器会话。 此操作将从 AKS 群集中删除用于 SSH 访问的 pod。