---
title: 在 Azure 安全中心进行安全监视 | Microsoft 文档
description: 本文介绍如何开始使用 Azure 安全中心的监视功能。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: dc362306e20b4d39aa73a552e47cbcbd3037edbd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063385"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>在 Azure 安全中心进行安全运行状况监视
本文介绍如何通过 Azure 安全中心的监视功能监视合规性。

## <a name="what-is-security-health-monitoring"></a>什么是安全运行状况监视？
通常可以认为，监视就是指观察并等待某个事件的发生，以便采取应对措施。 安全监视是指制定前瞻性策略对资源进行审核，确定不符合组织标准或最佳规范的系统。

## <a name="monitoring-security-health"></a>监视安全运行状况
用户为订阅的资源启用[安全策略](security-center-policies.md)以后，安全中心将分析相关资源的安全性，确定可能的漏洞。 可立即提供有关网络配置的信息。 根据安装了代理的 VM 和计算机的数目，在可以使用前可能需要一小时或更长的时间来收集 VM 和计算机配置信息，例如安全更新状态和操作系统配置。 在“预防”部分，可以查看资源的安全状态以及任何问题。 也可在“建议”磁贴中查看此类问题的列表。

如需详细了解如何应用建议，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

在“资源运行状况监视”下，可以监视资源的安全状态。 在以下示例中，可以看到每个资源的磁贴（计算和应用、网络、数据安全性、标识和访问）中都有已发现问题的总数。

![资源安全运行状况磁贴](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>监视计算和应用
有关详细信息，请参阅[在 Azure 安全中心内保护计算机和应用程序](security-center-virtual-machine-recommendations.md)。

### <a name="monitor-virtual-networks"></a>监视虚拟网络
单击“网络”磁贴即可打开“网络”边栏选项卡，其中包含更多详细信息，如以下屏幕截图所示：

![“网络”边栏选项卡](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>网络建议
在这里会看到顶部有汇总问题列表，在底部有所监视网络的列表，与虚拟机资源运行状况信息类似。

网络状态细分部分列出了可能的安全问题，并提供了[建议](security-center-network-recommendations.md)。 可能的问题包括：

* 未安装下一代防火墙 (NGFW)
* 子网上未启用网络安全组
* 虚拟机上未启用网络安全组
* 禁止通过公共的外部终结点进行外部访问
* 面向 Internet 的终结点运行正常

单击建议时，可以看到有关建议的更多详细信息，如以下示例所示：

![“网络”中建议的详细信息](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

在此示例中，“为子网配置缺少的网络安全组”提供了一个列表，其中的子网和虚拟机缺少网络安全组保护。 如果单击要向其应用网络安全组的子网，则会显示“选择网络安全组”。 此处既可以选择最适合子网的网络安全组，也可以创建新的网络安全组。

#### <a name="internet-facing-endpoints-section"></a>面向 Internet 的终结点部分
在“面向 Internet 的终结点”部分，可以看到当前配置了面向 Internet 的终结点的虚拟机及其当前状态。

![配置了面向 Internet 的终结点的虚拟机及状态](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

此表提供代表虚拟机的终结点名称、面向 Internet 的 IP 地址，以及网络安全组和 NGFW 当前的严重性状态。 此表按严重性排序：

* 红色（顶部）：优先级高，应立即处理
* 橙色：优先级中，应尽快处理
* 绿色（最后一条）：运行状况正常

#### <a name="networking-topology-section"></a>网络拓扑部分
“网络拓扑”部分提供了资源的分层视图，如以下屏幕截图所示：

![“网络拓扑”部分中资源的分层视图](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

此表按严重性排序（虚拟机和子网）：

* 红色（顶部）：优先级高，应立即处理
* 橙色：优先级中，应尽快处理
* 绿色（最后一条）：运行状况正常

在此拓扑视图中，第一层级包括[虚拟网络](../virtual-network/virtual-networks-overview.md)、[虚拟网络网关](/vpn-gateway/vpn-gateway-site-to-site-create.md)和[虚拟网络（经典）](/virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 第二层级包括子网，第三层级包括属于这些子网的虚拟机。 右侧一栏为这些资源的网络安全组的当前状态，如以下示例所示：

![“网络拓扑”部分中网络安全组的状态](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

此边栏选项卡底部为对此虚拟机的建议，与前述内容类似。 单击某个建议即可了解其详细信息或应用所需的安全控制或配置。

### <a name="monitor-data-security"></a>监视数据安全性

单击“预防”部分中的“数据安全性”时，“数据资源”会打开，其中显示了有关 SQL 和存储的建议。 此外还有针对数据库常规运行状况的[建议](security-center-sql-service-recommendations.md)。 有关存储加密的详细信息，请阅读 [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md)（在 Azure 安全中心为 Azure 存储帐户启用加密）。

![数据资源](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

在“SQL 建议”下面，可以单击任一建议，获取有关进一步操作的更多详细信息来解决问题。 以下示例显示了“SQL 数据库的数据库审核与威胁检测”建议的补充说明。

![有关 SQL 建议的详细信息](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

“对 SQL 数据库启用审核与威胁检测”包含以下信息：

* SQL 数据库列表
* 所在的服务器
* 此设置是从服务器继承的，还是此数据库独有的
* 当前状态
* 问题的严重性

单击数据库处理建议时，会打开“审核和威胁检测”，如以下屏幕截图所示。

![审核和威胁检测](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

若要启用审核，请在“审核”选项下选择“启用”。

### <a name="monitor-identity--access"></a>监视标识和访问

有关详细信息，请参阅[在 Azure 安全中心内监视标识和访问](security-center-identity-access.md)。

## <a name="see-also"></a>另请参阅
本文介绍了如何使用 Azure 安全中心的监视功能。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [在 Azure 安全中心设置安全策略](security-center-policies.md)：了解如何在 Azure 安全中心配置安全设置。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题](security-center-faq.md)：查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/):.查找关于 Azure 安全性及合规性的博客文章
