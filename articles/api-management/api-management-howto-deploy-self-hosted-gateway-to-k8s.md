---
title: 将自承载 Azure API 管理网关部署到 Kubernetes |Microsoft Docs
description: 了解如何将自承载 Azure API 管理网关部署到 Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513830"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>将自承载 Azure API 管理网关部署到 Kubernetes

本文提供了将自承载 Azure API 管理网关部署到 Kubernetes 群集的步骤。

> [!NOTE]
> 自承载网关功能处于预览阶段。 在预览期间，仅在开发人员和高级层提供自承载的网关，无额外费用。 开发人员层限制为单个自行托管的网关部署。


## <a name="prerequisites"></a>必备组件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
- 创建 Kubernetes 群集。 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)是一个很好的选择，用于开发和评估目的。 对于生产工作负荷，可以在外地云中使用[Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)或 Kubernetes 群集。
- [在 API 管理实例中预配网关资源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-gateway-to-kubernetes"></a>将网关部署到 Kubernetes

1. 从 "**设置**" 下选择 "**网关**"。
2. 选择要部署的网关资源。
3. 选择 "**部署**"。
4. 请注意，"**令牌**" 文本框中的新令牌是使用默认的 "**过期** **" 和 "密钥"** 值自动生成的。 如果需要，请调整其中一个或两个，然后选择 "**生成**" 以创建新令牌。
5. 请确保在 "**部署脚本**" 下选择 " **Kubernetes** "。
6. 选择 " **> <** "，然后选择 "**部署**" 旁边的 "docker-compose.override.yml 文件" 链接以下载文件。
7. 根据需要调整 docker-compose.override.yml 文件中的端口映射和容器名称。
8. 选择位于 "**部署**" 文本框右端的 "**复制**" 图标，将 `kubectl` 命令保存到剪贴板。 
9. 将命令粘贴到终端（或命令）窗口。 请注意，该命令要求当前目录中存在下载的环境文件。
```console
    kubectl apply -f <gateway-name>.yaml
```
10. 执行此命令。 命令指示 Kubernetes 群集运行容器，使用从 Microsoft 容器注册表下载的自承载网关的映像，并将该容器配置为公开 HTTP （8080）和 HTTPS （443）端口。
11. 运行以下命令，检查网关 pod 是否正在运行。 请注意，pod 名称将不同。 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. 运行以下命令，检查网关服务是否正在运行。 请注意，你的服务名称将不同。 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. 返回到 Azure 门户，确认刚部署的网关节点正在报告 "正常" 状态。

![网关状态](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> 使用 <code>kubectl logs <gateway-pod-name></code> 命令查看自承载网关日志的快照。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅[AZURE API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 了解有关[Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/intro-kubernetes)的详细信息


