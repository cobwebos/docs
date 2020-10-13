---
title: 注册表性能故障排除
description: 注册表性能常见问题的症状、原因和解决方法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 414e3a92b8ebd4ff58528fc5e9ec4794471bd775
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227109"
---
# <a name="troubleshoot-registry-performance"></a>注册表性能故障排除

本文将帮助你排查可能遇到的 Azure 容器注册表性能问题。 

## <a name="symptoms"></a>症状

可能包括以下一项或多项：

* 使用 Docker CLI 拉取或推送映像所需的时间长于预期时间
* 将映像部署到服务（如 Azure Kubernetes 服务）所需的时间长于预期时间
* 你无法在预期时间内完成大量并发拉取或推送操作
* 异地复制的注册表中的拉取或推送操作花费的时间长于预期时间，或者推送失败，出现错误 `Error writing blob` 或 `Error writing manifest`

## <a name="causes"></a>原因

* 网络连接速度可能会降低注册表操作速度 - [解决方案](#check-expected-network-speed)
* 在客户端中，映像层压缩或提取速度可能会很慢 - [解决方案](#check-client-hardware)  
* 在注册表服务层或环境中即将达到配置的限制 - [解决方案](#review-configured-limits)
* 异地复制的注册表在附近的区域中有副本 - [解决方案](#configure-geo-replicated-registry)
* 你要从地理位置较远的注册表副本拉取 - [解决方案](#configure-dns-for-geo-replicated-registry)

如果此处无法解决你的问题，请参阅[高级故障排除](#advanced-troubleshooting)和[后续步骤](#next-steps)了解其他选项。

## <a name="potential-solutions"></a>可能的解决方案

### <a name="check-expected-network-speed"></a>检查预期的网络速度

请检查 Internet 上传和下载速度，或使用 AzureSpeed 等工具来测试从 Azure Blob 存储（用于承载注册表映像层）[上传](https://www.azurespeed.com/Azure/Uploadß)和[下载](https://www.azurespeed.com/Azure/Download)。

根据注册表服务层级支持的最大大小和支持的下载或上传带宽来检查映像大小。 如果注册表位于“基本”或“标准”层级，请考虑升级以提高性能。 

若要将映像部署到其他服务，请检查注册表和目标所在的区域。 请考虑在相同区域或网络上相近的区域中查找注册表和部署目标，以提高性能。

相关链接：

* [Azure 容器注册表服务层级](container-registry-skus.md)    
* [容器注册表常见问题解答](container-registry-faq.md)
* [Azure Blob 存储的性能和可伸缩性目标](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>检查客户端硬件

Docker 客户端上的磁盘类型和 CPU 可能会影响在拉取或推送操作过程中客户端上提取或压缩映像层的速度。 例如，硬盘驱动器上的层提取所需时间比固态磁盘上的要长。 请针对 Azure 容器注册表和公共注册表（如 Docker Hub）中的可比较映像比较提取操作。

### <a name="review-configured-limits"></a>查看已配置的限制

如果要同时将多个或许多多层映像推送或拉取到注册表，请查看注册表服务层级支持的 ReadOps 和WriteOps 限制。 如果注册表位于“基本”或“标准”层级，请考虑升级以提高限制。 另外，请与网络提供商联系，了解在执行许多并发操作时可能会发生的网络限制。 

查看 Docker 守护程序配置，以了解客户端上每个推送或拉取操作的最大并发上载数或下载数。 如果需要，请配置更高的限制。

由于每个映像层都需要单独的注册表读取或写入操作，因此请检查映像中的层数。 考虑用于减少映像层数的策略。

相关链接：

* [Azure 容器注册表服务层级](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>配置异地复制的注册表

如果 Docker 客户端将映像推送到异地复制的注册表，那么该客户端可能不会将所有映像层及其清单推送到单个已复制的区域。 出现这种情况可能是因为 Azure 流量管理器将注册表请求路由到在网络上最近的已复制注册表。 如果注册表有两个附近的复制区域，则可以将映像层和清单分发到两个站点，并且在验证清单时推送操作将失败。

若要在推送映像时将 DNS 解析优化到最近的副本，请在推送操作源所在的 Azure 区域中配置异地复制注册表，或者在 Azure 外部工作时配置最近的区域。

若要排查异地复制的注册表的操作问题，还可以暂时禁止流量管理器路由到一个或多个副本。

相关链接：

* [Azure 容器注册表中的异地复制](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>为异地复制的注册表配置 DNS

如果针对异地复制的注册表执行的拉取操作速度较慢，则客户端上的 DNS 配置可能会解析为地理位置较远的 DNS 服务器。 在这种情况下，流量管理器可能会将请求路由到网络上离 DNS 服务器较近但离客户端较远的副本。 运行 `nslookup` 或 `dig`（在 Linux 上）等工具来确定流量管理器将注册表请求路由到的副本。 例如：

```console
nslookup myregistry.azurecr.io
```

一种可能的解决方案是配置更近的 DNS 服务器。

相关链接：

* [Azure 容器注册表中的异地复制](container-registry-geo-replication.md)
* [使用异地复制注册表对推送操作进行故障排除](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [暂时禁止路由到副本](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [流量管理器常见问题解答](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>高级故障排除

如果对注册表资源的权限允许，请[检查注册表环境的运行状况](container-registry-check-health.md)。 如果报告了错误，请查看[错误参考](container-registry-health-error-reference.md)来了解可能的解决方案。

如果在注册表中启用了[收集资源日志](container-registry-diagnostics-audit-logs.md)，请查看 ContainterRegistryRepositoryEvents 日志。 此日志存储有关推送或拉取事件等操作的信息。 查询此日志可获得有关[存储库级操作失败](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures)的信息。 

相关链接：

* [用于诊断评估和审核的日志](container-registry-diagnostics-audit-logs.md)
* [容器注册表常见问题解答](container-registry-faq.md)
* [Azure 容器注册表的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>后续步骤

如果此处无法解决你的问题，请参阅以下选项。

* 其他注册表故障排除主题包括：
  * [注册表登录故障排除](container-registry-troubleshoot-login.md)
  * [排查与注册表相关的网络问题](container-registry-troubleshoot-access.md)
* [社区支持](https://azure.microsoft.com/support/community/)选项
* [Microsoft 问答](https://docs.microsoft.com/answers/products/)
* [开具支持票证](https://azure.microsoft.com/support/create-ticket/)


