---
title: 将自承载 Azure API 管理网关部署到 Kubernetes | Microsoft Docs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513830"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>将自承载 Azure API 管理网关部署到 Kubernetes

本文提供将自承载 Azure API 管理网关部署到 Kubernetes 群集的步骤。

> [!NOTE]
> 自承载网关功能目前以预览版提供。 在预览期间，自承载网关仅在“开发人员”层和“高级”层提供，不额外收费。 “开发人员”层仅限单个自承载网关部署。


## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
- 创建 Kubernetes 群集。 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) 是用于开发和评估的极佳选项。 对于生产工作负荷，可以在外部云中使用 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)或 Kubernetes 群集。
- [在 API 管理实例中预配网关资源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-gateway-to-kubernetes"></a>将网关部署到 Kubernetes

1. 在“设置”下选择“网关”。********
2. 选择要部署的网关资源。
3. 选择**部署**。
4. 请注意，“令牌”文本框中已自动生成使用默认“过期时间”和“机密密钥”值的新令牌。************ 根据需要调整其中的一个或两个值，然后选择“生成”以创建新令牌。****
5. 确保在“部署脚本”下选择“Kubernetes”。********
6. 选择“部署”旁边的“<网关名称>.yml”文件链接以下载该文件。********
7. 在该 yml 文件中根据需要调整端口映射和容器名称。
8. 选择“部署”文本框右侧的“复制”图标，将 `kubectl` 命令保存到剪贴板。******** 
9. 将该命令粘贴到终端（或命令）窗口。 请注意，该命令要求下载的环境文件位于当前目录中。
```console
    kubectl apply -f <gateway-name>.yaml
```
10. 执行命令。 该命令指示 Kubernetes 群集使用从 Microsoft 容器注册表下载的自承载网关映像运行容器，并将该容器配置为公开 HTTP (8080) 和 HTTPS (443) 端口。
11. 运行以下命令来检查网关 pod 是否正在运行。 请注意，你的 pod 名称与此不同。 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. 运行以下命令来检查网关服务是否正在运行。 请注意，你的服务名称与此不同。 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. 返回 Azure 门户，确认刚刚部署的网关节点是否报告正常状态。

![网关状态](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> 使用 <code>kubectl logs <gateway-pod-name></code> 命令查看自承载网关日志的快照。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 详细了解 [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/intro-kubernetes)


