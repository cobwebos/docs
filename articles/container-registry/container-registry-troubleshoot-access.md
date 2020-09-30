---
title: 排查与注册表相关的网络问题
description: 访问位于虚拟网络中或防火墙后面的 Azure 容器注册表时的常见问题的症状、原因和解决方法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 06c5b65537fd7d256010260bb3a93888721f643b
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532442"
---
# <a name="troubleshoot-network-issues-with-registry"></a>排查与注册表相关的网络问题

本文将帮助你排查在访问位于虚拟网络中或防火墙后面的 Azure 容器注册表时可能遇到的问题。 

## <a name="symptoms"></a>症状

可能包括以下一项或多项：

* 无法推送或拉取映像，出现错误 `dial tcp: lookup myregistry.azurecr.io`
* 无法推送或拉取映像，出现 Azure CLI 错误 `Could not connect to the registry login server`
* 无法将映像从注册表拉取到 Azure Kubernetes 服务或其他 Azure 服务
* 无法访问 HTTPS 代理后面的注册表，出现错误 `Error response from daemon: login attempt failed with status: 403 Forbidden`
* 无法在 Azure 门户中访问或查看注册表设置，或者无法使用 Azure CLI 管理注册表
* 无法添加或修改虚拟网络设置或公共访问规则
* Azure 容器注册表任务无法推送或拉取映像
* Azure 安全中心无法扫描注册表中的映像，或者扫描结果未显示在 Azure 安全中心内

## <a name="causes"></a>原因

* 客户端防火墙或代理阻止访问 - [解决方案](#configure-client-firewall-access)
* 注册表上的公用网络访问规则阻止访问 - [解决方案](#configure-public-access-to-registry)
* 虚拟网络配置阻止访问 - [解决方案](#configure-vnet-access)
* 尝试将 Azure 安全中心或其他某些 Azure 服务与具有专用终结点、服务终结点或公共 IP 访问规则的注册表集成- [解决方案](#configure-service-access)

## <a name="further-diagnosis"></a>进一步诊断 

运行 [az acr check-health](/cli/azure/acr#az-acr-check-health) 命令可详细了解注册表环境的运行状况，以及对目标注册表的访问（可选）。 例如，诊断某些网络连接或配置问题。 

参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)以查看命令示例。 如果报告了错误，请查看[错误参考](container-registry-health-error-reference.md)和以下部分，以了解建议的解决方案。

> [!NOTE]
> 当注册表身份验证或授权存在问题时，也可能出现一些网络连接症状。 请参阅[注册表登录故障排除](container-registry-troubleshoot-login.md)。

## <a name="potential-solutions"></a>可能的解决方案

### <a name="configure-client-firewall-access"></a>配置客户端防火墙访问权限

若要从客户端防火墙或代理服务器后面访问注册表，请将防火墙规则配置为可访问注册表的 REST 和数据终结点。 如果启用了[专用数据终结点](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)，则需要用于访问以下终结点的规则：

* REST 终结点：`<registryname>.azurecr.io`
* 数据终结点：`<registry-name>.<region>.data.azurecr.io`

对于异地复制的注册表，请为每个区域副本配置对数据终结点的访问权限。

在 HTTPS 代理后面，确保 Docker 客户端和 Docker 守护程序均已针对代理行为进行了配置。

ContainerRegistryLoginEvents 表中的注册表资源日志可能有助于诊断尝试的连接被阻止的问题。

相关链接：

* [配置规则以访问防火墙后的 Azure 容器注册表](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS 代理配置](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Azure 容器注册表中的异地复制](container-registry-geo-replication.md)
* [用于诊断评估和审核的 Azure 容器注册表日志](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>配置对注册表的公共访问权限

如果通过 Internet 访问注册表，请确认注册表允许从客户端通过公用网络进行的访问。 默认情况下，Azure 容器注册表允许从所有网络访问公共注册表终结点。 注册表可以将访问限制为来自所选网络或所选 IP 地址。 

如果为具有服务终结点的虚拟网络配置了注册表，禁用公用网络访问还会禁止通过服务终结点进行访问。 如果为具有专用链接的虚拟网络配置了注册表，则 IP 网络规则不适用于注册表的专用终结点。 

相关链接：

* [配置公共 IP 网络规则](container-registry-access-selected-networks.md)
* [使用 Azure 专用链接以私密方式连接到 Azure 容器注册表](container-registry-private-link.md)
* [使用 Azure 虚拟网络中的服务终结点限制对容器注册表的访问](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>配置 VNet 访问权限

确认虚拟网络为专用链接配置了专用终结点，或者配置了服务终结点（预览版）。 当前不支持 Azure Bastion 终结点。

查看用于限制从网络中的其他资源发往注册表的流量的 NSG 规则和服务标记。 

如果配置了注册表的服务终结点，请确认用于允许从该网络子网进行访问的网络规则已添加到注册表。 服务终结点仅支持从网络中的虚拟机和 AKS 群集进行访问。

如果在网络中配置了 Azure 防火墙或类似的解决方案，请检查是否已允许来自其他资源（如 AKS 群集）的出口流量到达注册表终结点。

相关链接：

* [使用 Azure 专用链接以私密方式连接到 Azure 容器注册表](container-registry-private-link.md)
* [使用 Azure 虚拟网络中的服务终结点限制对容器注册表的访问](container-registry-vnet.md)
* [AKS 群集所需的出站网络规则和 FQDN](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes：调试 DNS 解析](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>配置服务访问

目前，Azure 安全中心无法在注册表中执行 [映像漏洞扫描](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) ，从而限制对专用终结点、选定子网或 IP 地址的访问。 此外，以下服务的资源无法访问具有网络限制的容器注册表：

* Azure DevOps Services 
* Azure 容器实例
* Azure 容器注册表任务

如果需要使用容器注册表访问或集成这些 Azure 服务，请删除网络限制。 例如，删除注册表的专用终结点，或删除或修改注册表的公共访问规则。

相关链接：

* [通过安全中心扫描 Azure 容器注册表映像](../security-center/azure-container-registry-integration.md)
* 提供[反馈](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [配置公共 IP 网络规则](container-registry-access-selected-networks.md)
* [使用 Azure 专用链接以私密方式连接到 Azure 容器注册表](container-registry-private-link.md)


## <a name="advanced-troubleshooting"></a>高级故障排除

如果在注册表中启用了[收集资源日志](container-registry-diagnostics-audit-logs.md)，请查看 ContainterRegistryLoginEvents 日志。 此日志存储身份验证事件和状态，包括传入标识和 IP 地址。 查询此日志可获得有关[注册表身份验证失败](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)的信息。 

相关链接：

* [用于诊断评估和审核的日志](container-registry-diagnostics-audit-logs.md)
* [容器注册表常见问题解答](container-registry-faq.md)
* [Azure 容器注册表的 Azure 安全基线](security-baseline.md)
* [Azure 容器注册表的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>后续步骤

如果此处无法解决你的问题，请参阅以下选项。

* 其他注册表故障排除主题包括：
  * [注册表登录故障排除](container-registry-troubleshoot-login.md) 
  * [注册表性能故障排除](container-registry-troubleshoot-performance.md)
* [社区支持](https://azure.microsoft.com/support/community/)选项
* [Microsoft 问答](https://docs.microsoft.com/answers/products/)
* [开具支持票证](https://azure.microsoft.com/support/create-ticket/)


