---
title: 从 Azure Kubernetes 服务 (AKS) 获取 kubelet 日志
description: 从 Azure Kubernetes 服务 (AKS) 群集节点获取 kubelet 日志
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0467be7e91fdbf4685fc41a375ea86a503e26009
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>从 Azure Kubernetes 服务 (AKS) 群集节点获取 kubelet 日志

有时，可能需要从 Azure Kubernetes 服务 (AKS) 节点获取 kubelet 日志以进行故障排除。 本文档详细介绍一个用于拉取这些日志的选项。

## <a name="create-an-ssh-connection"></a>创建 SSH 连接

首先，与需要在其上拉取 kubelet 日志的节点建立 SSH 连接。 在[通过 SSH 登录到 Azure Kubernetes 服务 (AKS) 群集节点][aks-ssh]文档中详细介绍了此操作。

## <a name="get-kubelet-logs"></a>获取 kubelet 日志

连接到节点后，运行以下命令以拉取 kubelet 日志。

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

示例输出：

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md