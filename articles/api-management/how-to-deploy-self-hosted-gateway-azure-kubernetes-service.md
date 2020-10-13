---
title: 将自承载网关部署到 Azure Kubernetes 服务 | Microsoft Docs
description: 了解如何将 Azure API 管理的自承载网关组件部署到 Azure Kubernetes 服务
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015215"
---
# <a name="deploy-to-azure-kubernetes-service"></a>部署到 Azure Kubernetes 服务

本文提供将 Azure API 管理的自承载网关组件部署到 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)的步骤。 若要了解如何将自承载网关部署到 Kubernetes 群集，请参阅此[文档](how-to-deploy-self-hosted-gateway-kubernetes.md)。

## <a name="prerequisites"></a>先决条件

- [创建一个 Azure API 管理实例](get-started-create-service-instance.md)
- [创建 Azure Kubernetes 群集](../aks/kubernetes-walkthrough-portal.md)
- [在 API 管理实例中预配网关资源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>将自承载网关部署到 AKS

1. 选择“部署和基础结构”下的“网关” 。
2. 选择要部署的自承载网关资源。
3. 选择“部署”。
4. 请注意，“令牌”文本框中已自动生成使用默认“过期时间”和“机密密钥”值的新令牌。   根据需要调整其中的一个或两个值，然后选择“生成”以创建新令牌。
5. 确保在“部署脚本”下选择“Kubernetes”。 
6. 选择“部署”旁边的“<网关名称>.yml”文件链接以下载该文件。 
7. 在该 yml 文件中根据需要调整端口映射和容器名称。
8. 根据你的方案，你可能需要更改[服务类型](../aks/concepts-network.md#services)。 默认值为 `NodePort`。
9. 选择“部署”文本框右侧的“复制”图标，将 `kubectl` 命令保存到剪贴板。 
10. 将该命令粘贴到终端（或命令）窗口。 请注意，该命令要求下载的环境文件位于当前目录中。
```console
    kubectl apply -f <gateway-name>.yaml
```
11. 执行命令。 该命令指示 AKS 群集使用从 Microsoft 容器注册表下载的自承载网关映像运行容器，并将该容器配置为公开 HTTP (8080) 和 HTTPS (443) 端口。
12. 运行以下命令来检查网关 pod 是否正在运行。 请注意，你的 pod 名称与此不同。
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. 运行以下命令来检查网关服务是否正在运行。 请注意，你的服务名称和 IP 地址与此不同。
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. 返回 Azure 门户，确认刚刚部署的网关节点是否报告正常状态。

> [!TIP]
> 使用 <code>kubectl logs <gateway-pod-name></code> 命令查看自承载网关日志的快照。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 详细了解 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)
* 了解[如何在云中配置和保留日志](how-to-configure-cloud-metrics-logs.md)
* * 了解[如何在本地配置和保留日志](how-to-configure-local-metrics-logs.md)
