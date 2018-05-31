---
title: Azure 虚拟机安全最佳做法
description: 本文提供了可用于 Azure 中虚拟机的各项最佳安全做法。
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: ''
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: e0c823982bb799e324dc6fb0fb811fd9ace37878
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364397"
---
# <a name="best-practices-for-azure-vm-security"></a>Azure VM 安全最佳做法

在大多数基础结构即服务 (IaaS) 方案中，[Azure 虚拟机 (VM)](https://docs.microsoft.com/azure/virtual-machines/) 是使用云计算的组织的主要工作负荷。 这种事实在[混合方案](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)中尤为明显，组织希望在混合方案中慢慢将工作负荷迁移到云。 在这种方案中，应遵循 [IaaS 常规安全注意事项](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)，并向所有 VM 应用安全最佳做法。

本文介绍各项 VM 安全最佳做法，每项做法源自我们的客户以及我们自己在 VM 方面的经验。

最佳做法以观点的共识以及 Azure 平台功能和特性集为基础。 由于观点和技术可随时改变，我们已计划定期更新本文以反映这些变化。

对于每项最佳做法，本文将说明：

* 最佳做法是什么。
* 为何要启用该最佳做法。
* 如何启用最佳做法。
* 如果无法启用最佳做法，可能的结果是什么。
* 最佳做法的可能替代方案。

本文介绍以下 VM 安全最佳做法：

* VM 身份验证和访问控制
* VM 可用性和网络访问
* 通过强制加密保护 VM 中的静态数据
* 管理 VM 更新
* 管理 VM 安全状况
* 监视 VM 性能

## <a name="vm-authentication-and-access-control"></a>VM 身份验证和访问控制

保护 VM 安全的第一步是确保只有授权用户才能设置新的 VM。 可使用 [Azure 策略](../azure-policy/azure-policy-introduction.md)建立组织中的资源约定、创建自定义策略和将策略应用到资源，例如[资源组](../azure-resource-manager/resource-group-overview.md)。

属于该资源组的 VM 自然而然将继承该组的策略。 尽管我们建议使用此方法来管理 VM，但也可使用[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 来控制对单个 VM 策略的访问。

启用 Resource Manager 策略和 RBAC 来控制 VM 访问有助于提高 VM 的整体安全性。 建议将具有相同生命周期的 VM 合并到同一个资源组中。 使用资源组可以部署和监视资源，并统计资源的计费成本。 若要让用户访问和设置 VM，可使用[最低特权方法](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)。 向用户分配特权时，请规划使用以下内置 Azure 角色：

- [虚拟机参与者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)：可管理 VM，但无法管理虚拟机连接的虚拟网络或存储帐户。
- [经典虚拟机参与者](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor)：可管理使用经典部署模型创建的 VM，但无法管理这些 VM 连接到的虚拟网络或存储帐户。
- [安全管理员](../role-based-access-control/built-in-roles.md#security-admin)：可管理安全组件和安全策略。
- [开发测试实验室用户](../role-based-access-control/built-in-roles.md#devtest-labs-user)：可查看所有内容，以及连接、启动、重新启动和关闭 VM。

不要在管理员之间共享帐户和密码，或在多个用户帐户或服务之间重复使用密码，特别是用于社交媒体或其他非管理活动的密码。 最好是使用 [Azure 资源管理器](../azure-resource-manager/resource-group-authoring-templates.md)模板来安全设置 VM。 通过此方法可以增强部署选项并在整个部署中实施安全设置。

未利用 RBAC 等功能实施数据访问控制的组织可能会向其用户分配超过需要的特权。 针对某些数据分配不适当的用户访问权限可能会直接导致数据泄露。

## <a name="vm-availability-and-network-access"></a>VM 可用性和网络访问

如果 VM 运行需要具有高可用性的关键应用程序，我们强烈建议使用多个 VM。 为提高可用性，请在[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)中至少创建两个 VM。

[Azure 负载均衡器](../load-balancer/load-balancer-overview.md)还要求负载均衡 VM 属于同一可用性集。 如果必须通过 Internet 访问 VM，则必须配置[面向 Internet 的负载均衡器](../load-balancer/load-balancer-internet-overview.md)。

对 Internet 公开 VM 后，请务必[使用网络安全组 (NSG) 控制网络流量](../virtual-network/security-overview.md)。 由于 NSG 可以应用于子网，因此可以通过按子网来组合资源以及将 NSG 应用到子网来尽量减少 NSG 的数量。 其目的是创建一个网络隔离层（可通过在 Azure 中正确配置[网络安全](../best-practices-network-security.md)功能来适当实现）。

还可在 Azure 安全中心使用适时 (JIT) VM 访问功能来控制谁可远程访问特定 VM 及其访问持续时间。

如果组织未强制执行对面向 Internet 的 VM 的网络访问限制，会面临远程桌面协议 (RDP) 暴力攻击等安全风险。

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>通过强制加密保护 VM 中的静态数据

[静态数据加密](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)是实现数据隐私性、符合性和数据主权的必要措施。 [Azure 磁盘加密](../security/azure-security-disk-encryption.md)可让 IT 管理员加密 Windows 和 Linux IaaS VM 磁盘。 磁盘加密利用符合行业标准的 Windows BitLocker 功能和 Linux dm-crypt 功能为 OS 和数据磁盘提供卷加密。

可以根据组织的安全与符合性要求，运用磁盘加密来帮助保护数据。 组织应考虑使用加密来帮助降低与未经授权访问数据相关的风险。 此外，建议在将敏感数据写入驱动器之前先将驱动器加密。

确保将 VM 数据卷加密，以保护 Azure 存储帐户中的静态数据。 使用 [Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/) 来保护加密密钥和机密。

未实施数据加密的组织面临的数据完整性问题风险更大。 例如，未经授权的用户或恶意用户可能会窃取已入侵帐户中的数据，或者未经授权访问以明文格式编码的数据。 除了应对这些风险以外，为了遵守行业法规，公司还必须证明他们在不断作出相应努力并使用正确的安全控制机制来增强数据的安全性。

有关磁盘加密的详细信息，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](azure-security-disk-encryption.md)。


## <a name="manage-your-vm-updates"></a>管理 VM 更新

与所有本地 VM 一样，Azure VM 应该由用户自行管理，因此，Azure 不会向这些 VM 推送 Windows 更新。 但是，我们依然建议启用 Windows 自动更新设置。 另一种方法是在另一个 VM 上或本地部署 [Windows Server 更新服务 (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) 或其他适合的更新管理产品。 WSUS 和 Windows 更新都可使 VM 保持最新状态。 此外，我们建议使用扫描产品来验证是否所有 IaaS VM 都处于最新状态。

Azure 提供的库存映像会定期更新，以包含最新的 Windows 更新。 但是，无法保证映像在部署时为最新状态， 其版本可能比公共版略有滞后（最多几周）。 每个部署的第一步应是检查和安装所有 Windows 更新。 在部署自己或库中提供的映像时，采用此措施就特别重要。 默认情况下，作为 Azure Marketplace 一部分提供的映像会自动更新。

未实施软件更新策略的组织面临更多利用已修复的已知漏洞的威胁。 除了应对此类威胁以外，为了遵守行业法规，公司还必须证明他们在不断作出相应努力并使用正确的安全控制机制来帮助确保云中工作负荷的安全性。

必须强调的是，传统数据中心与 Azure IaaS 之间的软件更新最佳做法存在许多相似之处。 因此，我们建议评估当前的软件更新策略，将 VM 包含在内。

## <a name="manage-your-vm-security-posture"></a>管理 VM 安全状况

网络威胁不断加剧，因此保护 VM 需要更丰富的监视功能，以便快速检测威胁、防止有人未经授权访问资源、触发警报并减少误报。 此类工作负荷的安全状况包括 VM 的所有安全因素，例如更新管理和网络访问安全等。

若要监视 [Windows](../security-center/security-center-virtual-machine.md) 和 [Linux VM](../security-center/security-center-linux-virtual-machine.md) 的安全状况，可以使用 [Azure 安全中心](../security-center/security-center-intro.md)。 可以利用 Azure 安全中心的以下功能来保护 VM：

* 应用包含建议的配置规则的 OS 安全设置
* 识别并下载可能缺少的系统安全更新和关键更新
* 部署终结点反恶意软件防护建议措施
* 验证磁盘加密
* 评估并修正漏洞
* 检测威胁

安全中心可主动监视威胁，并通过“安全警报”公开潜在的威胁。 关联的威胁将合并到名为“安全事件”的单个视图中。

若要了解安全中心如何帮助识别云中 VM 的潜在威胁，请观看以下视频：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player]

没有为 VM 实施强大安全保护措施的组织将意识不到未经授权的用户试图绕过组织建立的安全控制机制。

## <a name="monitor-vm-performance"></a>监视 VM 性能

如果 VM 进程消耗的资源多过实际所需的量，可能会造成资源滥用的问题。 VM 性能问题可能会导致服务中断，从而违反可用性安全原则。 因此，不仅要在出现问题时被动监视 VM 的访问，而且还要在正常运行期间针对基准性能进行主动监视。

通过分析 [Azure 诊断日志文件](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)，可以监视 VM 资源并识别可能会损害性能与可用性的潜在问题。 Azure 诊断扩展在基于 Windows 的 VM 上提供监视和诊断功能。 在 [Azure 资源管理器模板](../virtual-machines/windows/extensions-diagnostics-template.md)中包含该扩展即可启用这些功能。

还可使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) 来洞察资源的运行状况。

不监视 VM 性能的组织无法确定性能模式的某些变化是正常还是异常。 如果 VM 消耗的资源超过平常，这种异常可能意味着存在来自外部资源的潜在攻击，或者此 VM 中有不安全的进程正在运行。
