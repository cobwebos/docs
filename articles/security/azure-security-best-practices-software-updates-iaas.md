---
title: "Azure IaaS 的软件更新最佳实践 | Microsoft 文档"
description: "本文提供有关 Microsoft Azure IaaS 环境中软件更新的最佳实践集合。  本文面向每天要进行变更控制、软件更新和资产管理的 IT 专业人员和安全分析人员（包括负责其组织安全和合规性工作的人员）。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: df6598ee-de5b-48cd-b321-0b2f19d7eeaa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 3cba38d95535ff5ed3cd62aac5c0aa04a310f48c
ms.openlocfilehash: 43d760ffcc108a70212e47ee8eab9ec2aca02a17


---
# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>有关 Microsoft Azure IaaS 上软件更新的最佳实践
在进行 Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) 环境的任何最佳实践深入探讨之前，请务必了解用于管理软件更新的方案和责任。 下图可帮助你了解这些边界：

![云模型和责任](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

最左侧列显示组织应考虑的七个责任（在后面的部分中定义），所有这些责任都与计算环境的安全和隐私相关。

数据分类和责任以及客户端和终结点保护是单独针对客户域的责任，而物理、主机和网络责任则针对 PaaS 和 SaaS 模型中云服务提供商的域。

其余的责任由客户与云服务提供商共同承担。 某些责任需要 CSP 和客户共同管理，包括对其域的审核。 例如，考虑使用 Azure Active Directory 服务时的标识和访问权管理；虽然服务（如多重身份验证）的配置取决于客户，但确保有效的功能是 Microsoft Azure 的责任。

> [!NOTE]
> 有关云中的共担责任的详细信息，请参阅[云计算的共担责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)
>
>

这些原则在混合方案中同样适用。在混合方案中，公司将使用 Azure IaaS VM 与本地资源通信，如下图所示。

![使用 Microsoft Azure 的典型混合方案](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>初始评估
即使公司已采用更新管理系统，并且已采用软件更新策略，也必须经常检查前面的策略评估并根据当前的要求进行更新。 这意味着你需要熟悉公司中资源的当前状态。 若要达到此状态，你需要知道：

* 企业中的物理和虚拟计算机。
* 这些物理和虚拟计算机所运行的操作系统和版本。
* 每台计算机当前安装的软件更新（Service Pack 版本、软件更新和其他修改）。
* 每台计算机在企业中执行的功能。
* 在每台计算机上运行的应用程序和程序。
* 每台计算机的所有权和联系人信息。
* 环境中存在的资产，以及用来判断哪些区域需要最大关注和保护的相对值。
* 已知的安全问题，以及企业在安全级别用于识别新安全问题或更改的过程。
* 为了保护环境而部署的应对措施。

你应该定期更新此信息，并且此信息应该可随时提供给那些参与软件更新管理过程的人员使用。

## <a name="establish-a-baseline"></a>建立基准
软件更新管理过程中的重要部分就是为企业中的计算机创建操作系统版本、应用程序和硬件的初始标准安装；这些措施称为基准。 基准是在特定时间点建立的产品或系统配置。 例如，应用程序或操作系统基准可让你将计算机或服务重建到特定状态。

基准可以减少必须在企业中部署的软件更新数目并提高监视合规性的能力，从而提供查找及修复潜在问题的基础并简化软件更新管理过程。

在执行企业的初始审核之后，你应该使用取自审核的信息来定义生产环境中 IT 组件的操作基准。 根据生产环境中部署的各种硬件和软件，可能需要许多的基准。

例如，某些服务器需要软件更新，以免在运行 Windows Server 2012 过程中进入关机过程时挂起。 这些服务器的基准应该包含此软件更新。

在大型组织中，这通常有助于将企业中的计算机划分为各种资产类别，并使用相同版本的软件和软件更新来使每个类别遵循标准基准。 然后，你可以在指定软件更新分发优先级时使用这些资产类别。

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>订阅适当的软件更新通知服务
在对企业中所用的软件执行初始审核之后，你应该针对每个软件产品和版本确定用于接收新软件更新通知的最佳方法。 根据具体的软件产品，最佳通知方法可能是电子邮件通知、网站或计算机发布内容。

例如，Microsoft 安全响应中心 (MSRC) 会响应 Microsoft 产品的所有安全相关疑虑，并提供 Microsoft 安全公告服务、针对新发现漏洞的免费电子邮件通知，以及为了解决这些漏洞所发布的软件更新。 可以在 http://www.microsoft.com/technet/security/bulletin/notify.mspx 订阅此服务。

## <a name="software-update-considerations"></a>软件更新注意事项
在对企业中所用的软件执行初始审核之后，应该根据使用的软件更新管理系统，确定设置软件更新管理系统的要求。 对于 WSUS，请参阅 [Windows Server Update Services 最佳实践](https://technet.microsoft.com/library/Cc708536)；对于 System Center，请参阅[在 Configuration Manager 中规划软件更新](https://technet.microsoft.com/library/gg712696)。

但是，无论使用的解决方案为何，都有一些适用的常规注意事项和最佳实践，后面的部分将会介绍。

### <a name="setting-up-the-environment"></a>设置环境
在计划设置软件更新管理环境时，请考虑以下实践：

* **基于稳定准则创建生产软件更新集合**：一般而言，使用稳定准则来创建可供盘点和分发软件更新的集合，有助于简化软件更新管理过程的各个阶段。 稳定准则可以包含已安装的客户端操作系统版本和 Service Pack 级别、系统角色或目标组织。
* **创建包含参考计算机的预生产集合**：预生产集合应该包含操作系统版本、业务线软件以及企业中运行的其他软件的代表性配置。

还应该考虑软件更新服务器的位置：位于云上的 Azure IaaS 基础结构中还是在本地。 这是一项重要决策，因为你需要评估本地资源与 Azure 基础结构之间的流量。 有关如何将本地基础结构连接到 Azure 的详细信息，请参阅[将本地网络连接到 Microsoft Azure 虚拟网络](https://technet.microsoft.com/library/Dn786406.aspx)。

根据当前的基础结构以及当前使用的软件更新系统，用于确定更新服务器位置的设计选项会有所不同。 对于 WSUS，请参阅[在组织中部署 Windows Server Update Services](https://technet.microsoft.com/library/hh852340.aspx)，对于 System Center Configuration Manager，请参阅[在 Configuration Manager 中规划站点和层次结构](https://technet.microsoft.com/library/Gg712681.aspx)。

### <a name="backup"></a>备份
定期备份很重要，不只是对软件更新管理平台本身而言，对即将更新的服务器而言也是如此。 采用[变更管理过程](https://technet.microsoft.com/library/cc543216.aspx)的组织要求 IT 部门合理解释需要更新服务器的原因、预估的停机时间以及可能的影响。 为了确保在更新失败时能采用回滚配置，请务必定期备份系统。

Azure IaaS 的某些备份选项包括：

* [使用 Data Protection Manager 保护 Azure IaaS 工作负荷](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)
* [备份 Azure 虚拟机](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>监视
应该定期运行报告，以针对每个已获授权的软件更新，监视遗漏或已安装的更新数目，或状态不完整的更新。 同样，报告未获授权的软件更新可让部署决策变得更容易。

你还应该考虑以下任务：

* 对公司内部所有计算机的适用和已安装安全更新执行审核。
* 授权更新并将其部署到适当的计算机。
* 跟踪公司内所有计算机的库存和更新安装状态与进度。

除了本文中所述的常规注意事项以外，你还应该考虑每个产品的最佳实践，例如：如果 Azure 中有一个装有 SQL Server 的 VM，请确保遵循该产品的软件更新建议。

## <a name="next-steps"></a>后续步骤
使用本文中所述的指导原则可以帮助判断适用于 Azure IaaS 中虚拟机软件更新的最佳选项。 传统数据中心与 Azure IaaS 之间的软件更新最佳实践有许多相似之处，因此建议评估当前的软件更新策略，以包含 Azure VM 并将本文中的相关最佳实践纳入整个软件更新过程。



<!--HONumber=Jan17_HO5-->


