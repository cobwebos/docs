---
title: "Azure 虚拟机安全最佳做法 | Microsoft Docs"
description: "本文提供了一系列可用于 Azure 中虚拟机的最佳安全做法。"
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Azure 虚拟机安全最佳做法

大多数 IaaS（基础结构即服务）方案中，[虚拟机](https://docs.microsoft.com/en-us/azure/virtual-machines/)是使用云计算的组织的主要工作负荷。 这在[混合方案](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)中占主导地位，组织希望在混合方案中慢慢将工作负荷迁移到云。 应遵循 [IaaS 常规安全注意事项](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)方案，并确保将安全最佳做法应用到 Azure 中的所有 VM。

本文将介绍一系列 Azure VM 安全最佳做法。 这些最佳做法源自我们的 Azure VM 经验和客户经验。 

对于每项最佳实践，我们将说明：

- 最佳实践是什么
- 为何要启用该最佳实践
- 如果你无法启用该最佳实践，可能的结果是什么
- 最佳实践的可能替代方案
- 如何学习启用最佳实践

这篇 Azure 虚拟机安全最佳做法以共识以及 Azure 平台功能和特性集（因为在编写本文时已存在）为基础。 看法和技术将随着时间改变，本文将会定期更新以反映这些更改。

本文介绍的 Azure VM安全最佳实践包括：

- 虚拟机身份验证和访问控制
- 虚拟机可用性和网络访问
- 通过强制加密保护 Azure VM 中的静态数据
- 管理虚拟机更新
- 管理虚拟机安全状况
- 监视虚拟机性能

## <a name="virtual-machine-authentication-and-access-control"></a>虚拟机身份验证和访问控制

保护虚拟机安全的第一步是确保只有授权用户才能设置新的 VM。 可使用 [Resource Manager 策略](../azure-resource-manager/resource-manager-policy.md)建立组织中的资源约定、创建自定义策略和将策略应用到资源，例如[资源组](../azure-resource-manager/resource-group-overview.md)。 属于该资源组的 VM 将继承这些策略。 尽管推荐使用此方式来管理具有不同需求且位于不同资源组中的资源（包括 VM），但也可通过使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 来控制对 VM 的单独访问权限。

通过启用 Azure Resource Manager 策略和 RBAC 来控制 VM 访问，可提高 VM 的整体安全性。 对共享同一资源组相同生命周期的紧密耦合 VM，建议使用此种方式。 资源组可让你以组的形式部署和监视资源，并按资源组汇总计费成本。 向用户分配特权时，使用[最小特权](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)方式对用户开启访问权限来设置 VM，并计划使用 Azure 中以下内置角色：

- [虚拟机参与者](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)：可管理虚拟机，但无法管理虚拟机连接的虚拟网络或存储帐户。
- [经典虚拟机参与者](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor)：可管理经典虚拟机，但无法管理其连接的虚拟网络或存储帐户。
- [安全管理人员](../active-directory/role-based-access-built-in-roles.md#security-manager)：可管理安全组件、安全策略和虚拟机。
- [DevTest 实验室用户](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user)：可查看一切内容，并可连接、启动、重启和关闭虚拟机

不要在管理员之间共享帐户和密码，或在多个用户帐户或服务之间重复使用密码，特别是用于社交媒体或其他非管理活动的帐户或服务。 最好应使用 [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 模板来确保 VM 设置安全。 通过此方式可在整个部署强制执行部署选项和安全设置。

未使用 RBAC 等功能实施数据访问控制的组织可能会给其用户分配超过需要的权限。 这会使某些用户在最初拥有其不应拥有的特定级别数据的访问权限，从而直接导致数据泄露。
 

## <a name="virtual-machine-availability-and-network-access"></a>虚拟机可用性和网络访问

如果 VM 运行需要具有高可用性的关键应用程序，强烈建议使用多个 VM。  为提高可用性，请在[可用性集](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md)中至少创建两个 VM。 Azure [负载均衡器](../load-balancer/load-balancer-overview.md)还要求负载均衡 VM 属于同一可用性集。 如果需要通过 Internet 访问 VM，则需配置[面向 Internet 的负载均衡器](../load-balancer/load-balancer-internet-overview.md)。

对 Internet 公开 VM 后，请务必确保[使用网络安全组控制网络流量](../virtual-network/virtual-networks-nsg.md)。  由于 NSG 可以应用于子网，因此你可以通过按子网来组合资源以及将 NSG 应用到子网来尽量减少 NSG 的数量。 其目的是创建一个网络隔离层（可通过在 Azure 中正确配置[网络安全](../best-practices-network-security.md)功能实现）。  

还可在 Azure 安全中心使用实时 VM 访问功能来控制谁可远程访问特定 VM 及其访问持续时间。 有关如何使用该功能的详细信息，请观看下方视频：


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

如果组织未强制执行对面向 Internet 的 VM 的网络访问限制，将会面临 RDP 暴力攻击等安全风险。 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>通过强制加密保护 Azure VM 中的静态数据

如今，[静态数据加密](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)是实现数据隐私性、符合性和数据所有权的必要措施。 [Azure 磁盘加密](../security/azure-security-disk-encryption.md)可让 IT 管理员加密 Windows 和 Linux IaaS 虚拟机 (VM) 磁盘。 Azure 磁盘加密利用 Windows 的行业标准 BitLocker 功能和 Linux 的 DM-Crypt 功能，为 OS 和数据磁盘提供卷加密。

你可以利用 Azure 磁盘加密来帮助保护数据的安全，以满足组织安全与合规性要求。 组织还应考虑使用加密来帮助降低与未经授权访问数据相关的风险。 此外，建议在将敏感数据写入驱动器之前先将驱动器加密。 

确保将 VM 的数据卷加密，以保护 Azure 存储帐户中的静态数据卷。 使用 [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/) 来保护加密密钥和机密。 

未实施数据加密的组织面临的数据完整性问题风险更大，例如恶意或流氓用户可能窃取数据与入侵帐户，以及未经授权访问明文格式的数据。 除这些风险外，遵守行业法规的公司还必须证明他们在不断作出相应努力并使用正确的安全控件来增强数据安全性。

有关 Azure 磁盘加密的详细信息，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](azure-security-disk-encryption.md)一文。


## <a name="manage-virtual-machine-updates"></a>管理虚拟机更新

Azure 不会向 Microsoft Azure 虚拟机推送 Windows 更新，因为这些虚拟机应由用户进行管理。 正如任何本地计算机一样。 但是，依然建议用户启用 Windows 自动更新设置。 另一方法是在另一个 Azure 虚拟机或本地部署 [Windows Server 更新服务 (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) 服务器或其他适合的更新管理产品。 WSUS 和 Windows 更新都会使 VM 保持最新状态。 此外，建议使用扫描产品来验证是否所有 IaaS 虚拟机都处于最新状态。

Azure 提供的储备图像会定期更新，以将最新的 Windows 更新包含在内。 但是，无法保证图像在部署时为最新状态。 与公共版本相比可能略有滞后（最多几周）。 每个部署的第一步应是检查和安装所有 Windows 更新。 在部署你提供的图像或你自己的库中的图像时，这点尤为重要。 默认情况下，作为 Microsoft Azure 库中的一部分提供的图像始终启用 Windows 自动更新。

未强制实施软件更新策略的组织面临更多利用已修复的已知漏洞的威胁。 除这些风险外，遵守行业法规的公司还必须证明他们在不断作出相应努力并使用正确的安全控件来增强云中工作负荷的安全性。
值得强调的是，传统数据中心与 Azure IaaS 之间的软件更新最佳做法存在许多相似之处，因此建议评估当前的软件更新策略以将 Azure VM 包含在内。

## <a name="manage-virtual-machine-secure-posture"></a>管理虚拟机安全状况

网络威胁不断加剧，因此保护 VM 安全需要更强大的监视能力，通过这种监视能力来快速检测威胁、触发警报并减少误报。 对资源的未授权访问尝试导致的威胁进行活动监视时，此类型工作负荷的安全状况包括 VM 的所有安全因素，例如更新管理和网络访问安全等因素。

可使用 [Azure 安全中心](../security-center/security-center-intro.md)来监视 [Windows](../security-center/security-center-virtual-machine.md) 和 [Linux VM](../security-center/security-center-linux-virtual-machine.md) 的安全状况。 可利用 Azure 安全中心中的以下功能监视 VM：

- 包含建议配置规则的操作系统 (OS) 安全设置
- 缺少的系统安全更新和关键更新
- 终结点保护建议（反恶意软件）
- 磁盘加密验证
- 漏洞评估和补救
- 威胁检测

安全中心会主动监视威胁，“安全警报”中会公开这些威胁。 关联威胁会合并在名为“安全事件”的单一视图中。 若要了解安全中心如何有助于确定云中 VM 的潜在威胁，请观看下方视频。

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

未强制实施 VM 安全状况监视的组织并没有意识到攻击者可能会试图绕过组织所用的安全控件。

## <a name="monitoring-virtual-machine-performance"></a>监视虚拟机性能

如果 VM 中存在消耗多于实际所需资源的进程，资源滥用也就成为了一个问题。 存在性能问题的虚拟机可能导致服务中断，这就与可用性安全主体相悖。 因此，不应仅被动地（出现问题时）监视 VM 访问，还应在正常运行时段进行基线监视。

[Azure 诊断日志](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)有助于监视虚拟机资源和确定影响性能和可用性的潜在问题。 Azure 诊断扩展可在基于 Windows 的 Azure 虚拟机上提供监视和诊断功能。 通过将该扩展纳入为 Azure Resource Manager [模板](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)的一部分，可以在虚拟机上启用这些功能。 还可使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) 监视资源运行状况。

未进行虚拟机性能监视的组织无法确定某些性能模式改变是否属于正常使用范围，也无法确定是否存在消耗多于正常所需资源的异常状况。 这种异常可能意味着存在来自外部资源的潜在攻击或者此虚拟机中有不安全的进程正在运行。 
