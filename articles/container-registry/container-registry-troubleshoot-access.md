---
title: 对注册表中的网络问题进行故障排除
description: 在虚拟网络或防火墙后面访问 Azure 容器注册表时的常见问题、原因和解决方法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 227eeeadb2aef4b4d3feb7923a198b129a6267d3
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227105"
---
# <a name="troubleshoot-network-issues-with-registry"></a>对注册表中的网络问题进行故障排除

本文将帮助你解决在访问虚拟网络或防火墙后面的 Azure 容器注册表时可能遇到的问题。 

## <a name="symptoms"></a>症状

可能包括以下一项或多项：

* 无法推送或请求映像，出现错误 `dial tcp: lookup myregistry.azurecr.io`
* 无法推送或请求映像，出现 Azure CLI 错误 `Could not connect to the registry login server`
* 无法将映像从注册表提取到 Azure Kubernetes 服务或其他 Azure 服务
* 无法访问 HTTPS 代理后面的注册表，出现错误 `Error response from daemon: login attempt failed with status: 403 Forbidden`
* 无法使用 Azure CLI 访问或查看 Azure 门户或管理注册表中的注册表设置
* 无法添加或修改虚拟网络设置或公共访问规则
* ACR 任务无法推送或请求映像
* Azure 安全中心无法在注册表中扫描映像，或者在 Azure 安全中心未显示扫描结果

## <a name="causes"></a>原因

* 客户端防火墙或代理阻止访问- [解决方案](#configure-client-firewall-access)
* 注册表上的公共网络访问规则阻止访问- [解决方案](#configure-public-access-to-registry)
* 虚拟网络配置阻止访问- [解决方案](#configure-vnet-access)
* 尝试将 Azure 安全中心与具有专用终结点或服务终结点[解决方案](#configure-image-scanning-solution)的注册表集成

## <a name="further-diagnosis"></a>进一步诊断 

运行 [az acr 检查运行状况](/cli/azure/acr#az-acr-check-health) 命令，以获取有关注册表环境运行状况的详细信息，并可以选择访问目标注册表。 例如，诊断某些网络连接或配置问题。 

有关命令示例，请参阅 [查看 Azure 容器注册表的运行状况](container-registry-check-health.md) 。 如果报告了错误，请查看 [错误引用](container-registry-health-error-reference.md) 和以下部分，了解建议的解决方法。

> [!NOTE]
> 当注册表身份验证或授权出现问题时，也可能出现一些网络连接症状。 请参阅 [排查注册表登录问题](container-registry-troubleshoot-login.md)。

## <a name="potential-solutions"></a>可能的解决方案

### <a name="configure-client-firewall-access"></a>配置客户端防火墙访问权限

若要从客户端防火墙或代理服务器后面访问注册表，请配置防火墙规则以访问注册表的 REST 和数据终结点。 如果启用 [专用数据终结点](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) ，则需要用于访问的规则：

* REST 终结点： `<registryname>.azurecr.io`
* 数据终结点)  (： `<registry-name>.<region>.data.azurecr.io`

对于异地复制的注册表，请为每个区域副本配置对数据终结点的访问权限。

在 HTTPS 代理后面，确保你的 Docker 客户端和 Docker 守护程序均已配置为使用代理行为。

ContainerRegistryLoginEvents 表中的注册表资源日志可能有助于诊断被阻止的连接。

相关链接：

* [配置规则以访问防火墙后的 Azure 容器注册表](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS 代理配置](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [异地 replicationin Azure 容器注册表](container-registry-geo-replication.md)
* [用于诊断评估和审核的 Azure 容器注册表日志](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>配置对注册表的公共访问权限

如果通过 internet 访问注册表，请确认注册表允许来自客户端的公共网络访问。 默认情况下，Azure 容器注册表允许从所有网络访问公共注册表终结点。 注册表可以限制对所选网络或所选 IP 地址的访问。 

如果为具有服务终结点的虚拟网络配置了注册表，禁用公共网络访问还会禁用对服务终结点的访问。 如果已为具有专用链接的虚拟网络配置了注册表，则 IP 网络规则不适用于注册表的专用终结点。 

相关链接：

* [配置公共 IP 网络规则](container-registry-access-selected-networks.md)
* [使用 Azure Private Link 将专用连接到 Azure 容器注册表](container-registry-private-link.md)
* [使用 Azure 虚拟网络中的服务终结点限制对容器注册表的访问](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>配置 VNet 访问

确认虚拟网络配置了专用终结点作为专用链接或服务终结点 (预览) 。 当前不支持 Azure 堡垒终结点。

查看用于限制从网络中的其他资源到注册表的流量的 NSG 规则和服务标记。 

如果配置了注册表的服务终结点，请确认已将网络规则添加到允许从该网络子网进行访问的注册表。 服务终结点仅支持从网络中的虚拟机和 AKS 群集进行访问。

如果在网络中配置了 Azure 防火墙或类似的解决方案，请检查是否已启用来自其他资源（如 AKS 群集）的传出流量，以访问注册表终结点。

相关链接：

* [使用 Azure Private Link 将专用连接到 Azure 容器注册表](container-registry-private-link.md)
* [使用 Azure 虚拟网络中的服务终结点限制对容器注册表的访问](container-registry-vnet.md)
* [AKS 群集所需的出站网络规则和 FQDN](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes：调试 DNS 解析](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

### <a name="configure-image-scanning-solution"></a>配置映像扫描解决方案

如果你的注册表配置了专用终结点或服务终结点，则当前无法与 Azure 安全中心集成以进行图像扫描。 还可以配置 Azure Marketplace 中提供的其他映像扫描解决方案，其中包括：

* [浅绿色云本机安全平台](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security)
* [Twistlock Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock)

相关链接：

* [安全中心扫描 Azure 容器注册表映像](../security-center/azure-container-registry-integration.md)
* 提供 [反馈](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)


## <a name="advanced-troubleshooting"></a>高级故障排除

如果在注册表中启用了 [资源日志收集](container-registry-diagnostics-audit-logs.md) ，请查看 ContainterRegistryLoginEvents 日志。 此日志存储身份验证事件和状态，包括传入的标识和 IP 地址。 查询日志中的 [注册表身份验证失败](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)。 

相关链接：

* [诊断评估和审核日志](container-registry-diagnostics-audit-logs.md)
* [容器注册表常见问题](container-registry-faq.md)
* [Azure 容器注册表的 Azure 安全基线](security-baseline.md)
* [Azure 容器注册表的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>后续步骤

如果未在此处解决问题，请参阅以下选项。

* 其他注册表疑难解答主题包括：
  * [注册表登录疑难解答](container-registry-troubleshoot-login.md) 
  * [注册表性能疑难解答](container-registry-troubleshoot-performance.md)
* [社区支持](https://azure.microsoft.com/support/community/) 选项
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [开具支持票证](https://azure.microsoft.com/support/create-ticket/)


