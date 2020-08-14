---
title: 注册表性能疑难解答
description: 注册表性能常见问题的症状、原因和解决方法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 414e3a92b8ebd4ff58528fc5e9ec4794471bd775
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227109"
---
# <a name="troubleshoot-registry-performance"></a>注册表性能疑难解答

本文将帮助你解决 Azure 容器注册表性能可能遇到的问题。 

## <a name="symptoms"></a>症状

可能包括以下一项或多项：

* 使用 Docker CLI 请求或推送映像所需的时间比预期要长
* 将映像部署到服务（如 Azure Kubernetes 服务）所需的时间比预期要长
* 你无法在预期时间内完成大量并发请求或推送操作
* 异地复制的注册表中的请求或推送操作花费的时间比预期要长，或者推送失败并出现错误 `Error writing blob` 或 `Error writing manifest`

## <a name="causes"></a>原因

* 网络连接速度可能会导致注册表操作缓慢- [解决方案](#check-expected-network-speed)
* 客户端-[解决方案](#check-client-hardware)上的图像层压缩或提取可能比较慢  
* 在注册表服务层或环境中达到配置的限制- [解决方案](#review-configured-limits)
* 异地复制的注册表在附近的区域中有副本- [解决方案](#configure-geo-replicated-registry)
* 正在从地理位置更远的注册表副本-[解决方案](#configure-dns-for-geo-replicated-registry)中提取

如果未在此处解决问题，请参阅 [高级故障排除](#advanced-troubleshooting) 和其他选项的 [后续步骤](#next-steps) 。

## <a name="potential-solutions"></a>可能的解决方案

### <a name="check-expected-network-speed"></a>检查预期的网络速度

请检查 internet 上传和下载速度，或使用 AzureSpeed 等工具从 Azure blob 存储中测试 [上传](https://www.azurespeed.com/Azure/Uploadß) 和 [下载](https://www.azurespeed.com/Azure/Download) ，该存储用于承载注册表映像层。

根据支持的最大大小和注册表服务层支持的下载或上传带宽来检查映像大小。 如果注册表位于 "基本" 或 "标准" 级别，请考虑升级以提高性能。 

若要将映像部署到其他服务，请检查注册表和目标所在的区域。 请考虑在相同或网络关闭区域中查找注册表和部署目标，以提高性能。

相关链接：

* [Azure 容器注册表服务层级](container-registry-skus.md)    
* [容器注册表常见问题](container-registry-faq.md)
* [Azure Blob 存储的性能和可伸缩性目标](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>检查客户端硬件

Docker 客户端上的磁盘类型和 CPU 可能会影响在请求或推送操作过程中在客户端上提取或压缩映像层的速度。 例如，硬盘驱动器上的层提取需要的时间比固态磁盘上的要长。 比较 Azure 容器注册表和公共注册表（如 Docker 中心）的可比较映像的提取操作。

### <a name="review-configured-limits"></a>查看配置的限制

如果要同时将多个或多个多层映像推送或请求到注册表，请查看注册表服务层的支持的 ReadOps 和写入操作数限制。 如果注册表位于 "基本" 或 "标准" 级别，请考虑升级以增加限制。 另外，请与网络提供商联系，了解可能会发生许多并发操作的网络限制。 

查看 Docker 守护程序配置，以了解客户端上每个推送或请求操作的最大并发上载数或下载数。 如果需要，请配置更高的限制。

由于每个映像层都需要单独的注册表读取或写入操作，因此请检查映像中的层数。 考虑减少映像层数的策略。

相关链接：

* [Azure 容器注册表服务层级](container-registry-skus.md)
* [dockerd.exe](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>配置异地复制的注册表

将映像推送到异地复制的注册表的 Docker 客户端可能不会将所有映像层及其清单推送到一个复制的区域。 出现这种情况的原因可能是，Azure 流量管理器将注册表请求路由到网络最近复制的注册表。 如果注册表有两个附近的复制区域，则可以将映像层和清单分发到两个站点，并且在验证清单时推送操作将失败。

若要在推送映像时将 DNS 解析优化到最近的副本，请在推送操作源所在的 Azure 区域中配置异地复制注册表，或者在 Azure 外部工作时配置最近的区域。

若要使用异地复制的注册表对操作进行故障排除，还可以暂时禁用流量管理器到一个或多个复制的路由。

相关链接：

* [Azure 容器注册表中的异地复制](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>为异地复制的注册表配置 DNS

如果异地复制的注册表中的拉取操作出现速度较慢，则客户端上的 DNS 配置可能会解析为地理位置远的 DNS 服务器。 在这种情况下，流量管理器可能会将请求路由到网络接近于 DNS 服务器的副本，但远距离客户端。 `nslookup` `dig` 在 Linux) 上运行 (工具，以确定流量管理器将注册表请求路由到的副本。 例如：

```console
nslookup myregistry.azurecr.io
```

一种可能的解决方案是配置更接近的 DNS 服务器。

相关链接：

* [Azure 容器注册表中的异地复制](container-registry-geo-replication.md)
* [使用异地复制注册表对推送操作进行故障排除](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [暂时禁用到复制的路由](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [流量管理器常见问题解答](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>高级故障排除

如果对注册表资源的权限允许，请 [检查注册表环境的运行状况](container-registry-check-health.md)。 如果报告了错误，请查看 [错误引用](container-registry-health-error-reference.md) 以获取可能的解决方案。

如果在注册表中启用了 [资源日志收集](container-registry-diagnostics-audit-logs.md) ，请查看 ContainterRegistryRepositoryEvents 日志。 此日志存储有关推送或请求事件等操作的信息。 查询日志以查找 [存储库级操作失败的问题](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures)。 

相关链接：

* [诊断评估和审核日志](container-registry-diagnostics-audit-logs.md)
* [容器注册表常见问题](container-registry-faq.md)
* [Azure 容器注册表的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>后续步骤

如果未在此处解决问题，请参阅以下选项。

* 其他注册表疑难解答主题包括：
  * [注册表登录疑难解答](container-registry-troubleshoot-login.md)
  * [对注册表中的网络问题进行故障排除](container-registry-troubleshoot-access.md)
* [社区支持](https://azure.microsoft.com/support/community/) 选项
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [开具支持票证](https://azure.microsoft.com/support/create-ticket/)


