---
title: 将自承载网关部署到 Kubernetes | Microsoft Docs
description: 了解如何将 Azure API 管理的自承载网关组件部署到 Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500124"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>将自托管网关部署到 Kubernetes

本文描述了将 Azure API 管理的自承载网关组件部署到 Kubernetes 群集的步骤。

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
- 创建 Kubernetes 群集。
   > [!TIP]
   > [单节点群集](https://kubernetes.io/docs/setup/#learning-environment)非常适用于开发和评估目的。 将本地或云中的 [Kubernetes 认证](https://kubernetes.io/partners/#conformance)多节点群集用于生产工作负载。
- [在 API 管理实例中预配自承载网关资源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-to-kubernetes"></a>部署到 Kubernetes

1. 选择“部署和基础结构”下的“网关” 。
2. 选择要部署的自承载网关资源。
3. 选择“部署”。
4. " **令牌** " 文本框中的访问令牌是根据默认的 " **过期** **" 和 "密钥"** 值自动生成的。 如果需要，请在其中一个或两个控件中选择值以生成新令牌。
5. 选择“部署脚本”下的“Kubernetes”选项卡 。
6. 选择** \<gateway-name\> docker-compose.override.yml**文件链接并下载 YAML 文件。
7. 选择“部署”文本框右下角的“复制”图标，将 `kubectl` 命令保存到剪贴板 。
8. 将命令粘贴到终端（或命令）窗口。 第一个命令创建 Kubernetes 机密，其中包含在步骤 4 中生成的访问令牌。 第二个命令将在步骤 6 中下载的配置文件应用于 Kubernetes 群集，并假定该文件位于当前目录中。
9. 运行命令以在[默认命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)中创建必要的 Kubernetes 对象，并从 Microsoft 容器注册表下载的[容器映像](https://aka.ms/apim/sputnik/dhub)中启动自承载网关 Pod。
10. 运行以下命令，检查部署是否成功。 请注意，创建所有对象和初始化 Pod 可能需要一些时间。
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. 运行以下命令，检查是否已成功创建服务。 请注意，你的服务 IP 和端口与此不同。
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. 返回到 Azure 门户，选择“概述”。
13. 确认“状态”显示绿色复选标记，后跟与 YAML 文件中指定的副本数匹配的节点计数。 此状态表示部署的自承载网关 Pod 正在成功地与 API 管理服务通信，并且具有常规的“检测信号”。

    ![网关状态](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> 运行 <code>kubectl logs deployment/<gateway-name></code> 命令以查看随机选择的 Pod 的日志（如果有多个 Pod）。
> 运行 <code>kubectl logs -h</code> 以获得一组完整的命令选项，例如如何查看特定 Pod 或容器的日志。

## <a name="production-deployment-considerations"></a>生产部署注意事项

### <a name="access-token"></a>访问令牌
如果没有有效的访问令牌，自承载网关将无法从关联的 API 管理服务的终结点访问和下载配置数据。 访问令牌的有效期最长为 30 天。 必须在到期前重新生成令牌，并手动或通过自动化方式用新令牌配置群集。

自动刷新令牌时，请使用[此管理 API 操作](/rest/api/apimanagement/2019-12-01/gateway/generatetoken)生成新令牌。 有关管理 Kubernetes 机密的信息，请参阅 [Kubernetes 网站](https://kubernetes.io/docs/concepts/configuration/secret)。

### <a name="namespace"></a>命名空间
Kubernetes [命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)有助于在多个团队、项目或应用程序之间划分单个群集。 命名空间提供了资源和名称的范围。 它们可以与资源配额和访问控制策略相关联。

Azure 门户提供了在“默认”命名空间中创建自承载网关资源的命令。 此命名空间为自动创建，存在于每个群集中且无法删除。
请考虑在生产环境中将自承载网关[创建和部署](https://kubernetesbyexample.com/ns/)到单独的命名空间中。

### <a name="number-of-replicas"></a>副本数
适合生产环境的最小副本数量是两个。

默认情况下，使用“RollingUpdate”部署[策略](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)部署自承载网关。 检查默认值并考虑显式设置 [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) 和 [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) 字段，尤其是在使用大量副本时。

### <a name="container-resources"></a>容器资源
默认情况下，Azure 门户中提供的 YAML 文件不指定容器资源请求。

无法可靠地预测和建议每个容器 CPU 和内存资源的容量以及支持特定工作负载所需的副本数量。 有许多影响因素，例如：

- 运行群集的特定硬件。
- 虚拟化的存在情况和类型。
- 并发客户端连接的数量和速率。
- 请求速率。
- 已配置策略的种类和数量。
- 有效负载大小以及有效负载是进行缓冲还是流式传输。
- 后端服务延迟。

建议最初将资源请求设置为两个内核和 2 个 GiB。 执行负载测试并根据结果纵向/横向扩展或缩减。

### <a name="container-image-tag"></a>容器映像标记
Azure 门户中提供的 YAML 文件使用“最新”标记。 此标记始终引用自承载网关容器映像的最新版本。

请考虑在生产环境中使用特定版本的标记，以避免无意中升级到较新版本。

可以[下载可用标记的完整列表](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list)。

### <a name="dns-policy"></a>DNS 策略
DNS 名称解析对于自承载网关是否能连接到 Azure 中的依赖项并将 API 调用分派到后端服务起着关键作用。

Azure 门户中提供的 YAML 文件将应用默认的 [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) 策略。 此策略可导致群集 DNS 未解析的名称解析请求转发到从节点继承的上游 DNS 服务器。

若要了解 Kubernetes 中的名称解析，请参阅 [Kubernetes 网站](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service)。 请考虑根据你的设置自定义 [DNS 策略](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)或 [DNS 配置](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config)。

### <a name="external-traffic-policy"></a>外部流量策略
Azure 门户将 `externalTrafficPolicy` [服务](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) 对象上的字段设置为所提供的 YAML 文件 `Local` 。 这会保留调用方 IP 地址 (在 [请求上下文](api-management-policy-expressions.md#ContextVariables) 中可访问) 并禁用跨节点负载平衡，从而消除由其导致的网络跃点。 请注意，此设置可能会导致部署中的流量不对称分布于每个节点的网关箱数不相等的部署中。

### <a name="custom-domain-names-and-ssl-certificates"></a>自定义域名和 SSL 证书

如果为 API 管理终结点使用自定义域名，尤其是对管理终结点使用自定义域名时，你可能需要更新 `config.service.endpoint` ** \<gateway-name\> yaml**文件中的值，以将默认域名替换为自定义域名。 请确保可以从 Kubernetes 群集中自承载网关的 pod 访问管理终结点。

在这种情况下，如果管理终结点使用的 SSL 证书不是由已知 CA 证书签名的，则必须确保自托管网关的 pod 信任 CA 证书。

### <a name="configuration-backup"></a>配置备份
若要了解出现临时 Azure 连接中断时的自承载网关行为，请参阅[自承载网关概述](self-hosted-gateway-overview.md#connectivity-to-azure)。

为自承载网关容器配置本地存储卷，使其能够保留最新下载配置的备份副本。 如果连接断开，则存储卷可以在重启时使用备份副本。 卷安装路径必须是 <code>/apim/config</code>。 请参阅 [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml) 上的示例。
若要了解 Kubernetes 中的存储，请参阅 [Kubernetes 网站](https://kubernetes.io/docs/concepts/storage/volumes/)。

### <a name="local-logs-and-metrics"></a>本地日志和指标
自承载网关根据关联的 API 管理服务中的配置设置，向 [Azure Monitor](api-management-howto-use-azure-monitor.md) 和 [Azure Application Insights](api-management-howto-app-insights.md) 发送遥测。
当[与 Azure 的连接](self-hosted-gateway-overview.md#connectivity-to-azure)暂时丢失时，到 Azure 的遥测流将中断，并且在中断期间数据将丢失。
请考虑[设置本地监视](how-to-configure-local-metrics-logs.md)，以确保能够在 Azure 连接中断期间观察 API 流量并防止遥测丢失。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅[自承载网关概述](self-hosted-gateway-overview.md)。
