---
title: 将自承载网关部署到 Kubernetes |Microsoft Docs
description: 了解如何将 Azure API 管理的自承载网关组件部署到 Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 38cfab8a3b73eeef28249f53bd2f5c56e26b21a1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854110"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>将自托管网关部署到 Kubernetes

本文介绍将 Azure API 管理的自承载网关组件部署到 Kubernetes 群集的步骤。

## <a name="prerequisites"></a>必备条件

- 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
- 创建 Kubernetes 群集。
   > [!TIP]
   > [单节点群集](https://kubernetes.io/docs/setup/#learning-environment)非常适合用于开发和评估目的。 在本地或在云中为生产工作负荷使用[Kubernetes 认证](https://kubernetes.io/partners/#conformance)的多节点群集。
- [预配 API 管理实例中的自承载网关资源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-to-kubernetes"></a>部署到 Kubernetes

1. 选择 "**部署和基础结构**" 下的**网关**。
2. 选择想要部署的自承载的网关资源。
3. 选择 "**部署**"。
4. "**令牌**" 文本框中的访问令牌根据默认的 "**过期** **" 和 "密钥"** 值自动生成。 如果需要，请选择其中一个或两个控件中的值，以生成新令牌。
5. 在 "**部署脚本**" 下选择 " **Kubernetes** " 选项卡。
6. 选择 **<"网关名称"> "docker-compose.override.yml**文件" 链接并下载 "YAML" 文件。
7. 选择 "**部署**" 文本框右下角的 " `kubectl` **复制**" 图标，将命令保存到剪贴板。
8. 将命令粘贴到终端（或命令）窗口。 第一个命令创建 Kubernetes 机密，其中包含在步骤4中生成的访问令牌。 第二个命令将在步骤6中下载的配置文件应用于 Kubernetes 群集，并要求文件位于当前目录中。
9. 运行命令，在[默认命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)中创建必要的 Kubernetes 对象，并从 Microsoft 容器注册表下载的[容器映像](https://aka.ms/apim/sputnik/dhub)中启动自承载的网关。
10. 运行以下命令以检查部署是否成功。 请注意，创建所有对象并将其初始化可能需要一些时间。
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. 运行以下命令以检查是否已成功创建服务。 请注意，你的服务 Ip 和端口将不同。
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. 返回 Azure 门户，然后选择 "**概述**"。
13. 确认 "**状态**" 显示一个绿色的复选标记，后跟一个与 YAML 文件中指定的副本数相匹配的节点计数。 此状态表示已部署的自承载网关盒已成功与 API 管理服务通信，并且具有常规的 "检测信号"。

    ![网关状态](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> 如果有<code>kubectl logs deployment/<gateway-name></code>多个，请运行命令以查看随机选定的 pod 中的日志。
> 运行<code>kubectl logs -h</code>以获得一组完整的命令选项，如如何查看特定 pod 或容器的日志。

## <a name="production-deployment-considerations"></a>生产部署注意事项

### <a name="access-token"></a>访问令牌
如果没有有效的访问令牌，则自承载的网关将无法从关联的 API 管理服务的终结点访问和下载配置数据。 访问令牌的有效期最长为30天。 必须重新生成它，并且在该群集过期之前手动或通过自动化配置了新令牌。 

自动刷新令牌时，使用[此管理 API 操作](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken)生成新令牌。 有关管理 Kubernetes 机密的信息，请参阅[Kubernetes 网站](https://kubernetes.io/docs/concepts/configuration/secret)。

### <a name="namespace"></a>命名空间
Kubernetes[命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)有助于在多个团队、项目或应用程序之间划分单个群集。 命名空间提供资源和名称的作用域。 它们可以与资源配额和访问控制策略相关联。

Azure 门户提供了用于在**默认**命名空间中创建自承载的网关资源的命令。 此命名空间是自动创建的，存在于每个群集中，无法删除。
请考虑在生产环境中创建自承载网关并将其[部署](https://kubernetesbyexample.com/ns/)到单独的命名空间中。

### <a name="number-of-replicas"></a>副本数
适用于生产的最小副本数为2个。

默认情况下，使用**RollingUpdate**部署[策略](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)部署自承载网关。 查看默认值，并考虑显式设置[maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable)和[maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge)字段，尤其是在使用高副本计数时。

### <a name="container-resources"></a>容器资源
默认情况下，Azure 门户中提供的 YAML 文件不指定容器资源请求。

无法可靠地预测和建议每个容器的 CPU 和内存资源量，以及支持特定工作负载所需的副本数。 许多因素在播放中，例如：

- 运行群集的特定硬件。
- 虚拟化的状态和类型。
- 并发客户端连接的数量和速率。
- 请求速率。
- 配置策略的种类和数量。
- 负载大小以及是否对负载进行缓冲或流式处理。
- 后端服务延迟。

建议将资源请求设置为两个核心，将2个 GiB 设置为一个起点。 执行负载测试并根据结果向上/向下或向下或向下缩放。

### <a name="container-image-tag"></a>容器映像标记
Azure 门户中提供的 YAML 文件使用**最新**的标记。 此标记始终引用自承载网关容器映像的最新版本。

请考虑在生产环境中使用特定版本标记，以避免无意中升级到较新的版本。

您可以[下载可用标记的完整列表](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list)。

### <a name="dns-policy"></a>DNS 策略
DNS 名称解析在自承载的网关连接到 Azure 中的依赖项并将 API 调用调度到后端服务的能力中扮演着关键角色。

Azure 门户中提供的 YAML 文件应用默认的[ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)策略。 此策略会导致群集 DNS 未解析的名称解析请求转发到从节点继承的上游 DNS 服务器。

若要了解 Kubernetes 中的名称解析，请参阅[Kubernetes 网站](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service)。 请考虑根据你的设置自定义[dns 策略](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)或[dns 配置](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config)。

### <a name="configuration-backup"></a>配置备份
若要了解在出现临时 Azure 连接中断时的自承载网关行为，请参阅[自承载网关概述](self-hosted-gateway-overview.md#connectivity-to-azure)。

为自承载网关容器配置本地存储卷，使其能够保留最新下载配置的备份副本。 如果连接已关闭，则存储卷可以在重新启动时使用备份副本。 卷装入路径必须是<code>/apim/config</code>。 请参阅[GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)上的示例。
若要了解 Kubernetes 中的存储，请参阅[Kubernetes 网站](https://kubernetes.io/docs/concepts/storage/volumes/)。

### <a name="local-logs-and-metrics"></a>本地日志和指标
自承载网关根据关联 API 管理服务中的配置设置将遥测发送到[Azure Monitor](api-management-howto-use-azure-monitor.md)和[Azure 应用程序 Insights](api-management-howto-app-insights.md) 。
暂时失去[与 azure 的连接](self-hosted-gateway-overview.md#connectivity-to-azure)时，将中断到 azure 的流量，并在中断期间丢失数据。
请考虑[设置本地监视](how-to-configure-local-metrics-logs.md)，以确保能够在 Azure 连接中断期间观察到 API 流量并阻止遥测损失。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅[自承载网关概述](self-hosted-gateway-overview.md)。
