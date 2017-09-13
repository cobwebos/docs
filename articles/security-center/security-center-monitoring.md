---
title: "在 Azure 安全中心进行安全监视 | Microsoft 文档"
description: "本文介绍如何开始使用 Azure 安全中心的监视功能。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 4752199803f1fab04608e6a8618e3294e85fb38a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="security-health-monitoring-in-azure-security-center"></a>在 Azure 安全中心进行安全运行状况监视
本文介绍如何通过 Azure 安全中心的监视功能监视合规性。

## <a name="what-is-security-health-monitoring"></a>什么是安全运行状况监视？
通常可以认为，监视就是指观察并等待某个事件的发生，以便采取应对措施。 安全监视是指制定前瞻性策略对资源进行审核，确定不符合组织标准或最佳规范的系统。

## <a name="monitoring-security-health"></a>监视安全运行状况
用户为订阅的资源启用[安全策略](security-center-policies.md)以后，安全中心将分析相关资源的安全性，确定可能的漏洞。 可立即提供有关网络配置的信息。 根据安装了代理的 VM 和计算机的数目，可能需要一小时或更长的时间来收集可用的 VM 和计算机配置信息，例如安全更新状态和操作系统配置。 在“预防”部分，可以查看资源的安全状态以及任何问题。 也可在“建议”磁贴中查看此类问题的列表。

如需详细了解如何应用建议，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

在“预防”部分，可以监视资源的安全状态。 在以下示例中，可以看到每个资源的磁贴（“计算”、“网络”、“存储与数据”、“应用程序”）中都有已发现问题的总数。

![资源安全运行状况磁贴](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>监视计算节点
单击“计算”磁贴时，会显示三个选项卡：

- 概览：监视和建议。
- VM 和计算机：列出所有虚拟机、计算机及其当前安全状态。
- **云服务**：列出安全中心监视的所有 Web 角色和辅助角色。

![各虚拟机缺少的系统更新](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

每个选项卡可能包含多个部分，在每个部分，可以选择单个选项详细查看可解决该特定问题的建议步骤。 

#### <a name="monitoring-recommendations"></a>监视建议
此部分显示已进行自动预配初始化的虚拟机和计算机的总数及其当前状态。 单击此项时，会打开“VM 代理缺失或无响应”。 

![各虚拟机缺少的系统更新](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)


#### <a name="recommendations"></a>建议
此部分提供了一组受 Azure 安全中心监视的[针对每个虚拟机和计算机的建议](security-center-virtual-machine-recommendations.md)。 第一列列出了建议。 第二列显示受该建议影响的虚拟机和计算机的总数。 第三列显示问题的严重性，如以下屏幕截图所示：

![虚拟机建议](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> “网络拓扑”列表的“网络运行状况”中仅显示至少具有一个公共终结点的虚拟机。
>

每个建议都有一组操作，单击即可执行。 例如，如果单击“缺失的系统更新”，则会显示一个列表，其中包含缺少修补程序的虚拟机和计算机，以及所缺更新的严重性，如以下屏幕截图所示：

![各虚拟机缺少的系统更新](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

“缺失的系统更新”以图形格式汇总了关键更新，分别适用于 Windows 和 Linux。 第二部分有一个表，其中包含以下信息：

* 名称：所缺更新的名称。
* VM 和计算机数：缺少该更新的 VM 和计算机的总数。
* **状态**：该建议的当前状态：
  * **未解决**：建议尚未得到处理。
  * **正在进行**：目前已将建议应用到相关资源，不需用户采取行动。
  * **已解决**：已完成建议。 （解决问题后，此条目将变暗）。
* **严重性**：描述该特定建议的严重性：
  * **高**：重要资源（应用程序、虚拟机或网络安全组）存在漏洞，需要引起注意。
  * **中**：需要采取非关键步骤或额外步骤来完成某个过程或消除某个漏洞。
  * **低**：漏洞需要解决，但不需立即处理。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。）

若要查看建议详细信息，请单击列表中缺失更新的名称。 

![特定虚拟机缺少的系统更新](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> 此处提供的安全建议与“建议”选项中的相同。 请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)，详细了解如何应用建议。 这不仅适用于虚拟机和计算机，还适用于“资源运行状况”磁贴中提供的所有资源。
>

#### <a name="vms--computers-section"></a>VM 和计算机部分
可以通过虚拟机和计算机部分概要了解所有虚拟机和计算机的建议。 每一列代表一组建议，如以下屏幕截图所示：

![所有虚拟机和建议的概览](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

此列表（见上）显示了四类图标：

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) 非 Azure 计算机。

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Azure 资源管理器 VM。

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Azure 经典 VM。

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) 只能通过工作区（属于所查看的订阅）标识的 VM。 其中包括其他订阅中向此订阅中的工作区报告的 VM，以及使用 SCOM 直接代理安装的 VM（没有资源 ID）。

每个建议下都会显示一个图标，用于快速确定需要关注的虚拟机和计算机以及建议的类型。 也可使用“筛选器”选项，选择需要在此屏幕上显示的具体选项。

![筛选器](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

在以上示例中，一个虚拟机有一条关于终结点保护的关键建议。 若要获取有关虚拟机的详细信息，请单击它：

![虚拟机安全详细信息](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

此处显示虚拟机或计算机的安全详细信息。 在底部可以看到建议的操作以及每个问题的严重性。

#### <a name="cloud-services-section"></a>云服务部分
对于云服务，会在操作系统版本过期时创建建议，如以下屏幕截图所示：

![云服务的运行状态](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

如果确实提供了建议（在上一示例中不是这样），则需要遵循建议中的步骤更新操作系统版本。 如果有可用的更新，会出现警报（红色或橙色 - 取决于问题的严重性）。 如果单击 WebRole1（运行 Windows Server，将 Web 应用自动部署到 IIS）或 WorkerRole1（运行 Windows Server，将 Web 应用自动部署到 IIS）行中的此警报，则会显示有关此建议的更多详细信息，如以下屏幕截图所示：

![云服务详细信息](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

若要查看有关此建议的更全面的说明，请在“说明”栏下单击“更新 OS 版本”。 

![云服务建议](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

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

单击建议时，会显示有关建议的更多详细信息，如以下示例所示：

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

### <a name="monitor-storage--data"></a>监视存储与数据

在“预防”部分单击“存储与数据”时，“数据资源”会打开，其中显示了有关 SQL 和存储的建议。 此外还有针对数据库常规运行状况的[建议](security-center-sql-service-recommendations.md)。 有关存储加密的详细信息，请阅读 [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md)（在 Azure 安全中心为 Azure 存储帐户启用加密）。

![数据资源](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

在“SQL 建议”下面，可以单击任一建议，获取有关进一步操作的更多详细信息来解决问题。 以下示例显示了“SQL 数据库的数据库审核与威胁检测”建议的补充说明。

![有关 SQL 建议的详细信息](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

“对 SQL 数据库启用审核与威胁检测”包含以下信息：

* SQL 数据库列表
* 所在的服务器
* 此设置是从服务器继承的，还是此数据库独有的
* 当前状态
* 问题的严重性

单击数据库解决建议的问题时，会打开“审核和威胁检测”，如以下屏幕截图所示。

![审核和威胁检测](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

若要启用审核，请在“审核”选项下选择“启用”。

### <a name="monitor-applications"></a>监视应用程序

如果 Azure 工作负荷中的应用程序位于[（通过 Azure Resource Manager 创建的）虚拟机](../azure-resource-manager/resource-manager-deployment-model.md)中，且后者使用公开的 Web 端口（TCP 端口 80 和 443），安全中心可以监视这些端口，确定潜在的安全问题并提供补救步骤方面的建议。 单击“应用程序”磁贴即可打开“应用程序”，在“应用程序建议”部分包含一系列建议。 此外还会显示每个主机的应用程序明细、IP/域，以及是否已安装 WAF 解决方案：

![应用程序安全运行状况](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

与其他建议一样，单击建议即可查看有关问题的更多详细信息以及补救方法。 下图所示示例为已被确定为不安全 Web 应用程序的应用程序。 选择被视为不安全的应用程序时，以下选项可用：

![详细信息](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

此处会提供一个列表，其中包含对此应用程序的所有建议。 单击“添加 Web 应用程序防火墙”建议即可打开“添加 Web 应用程序防火墙”，其中包含安装合作伙伴所提供 Web 应用程序防火墙 (WAF) D 的选项，如以下屏幕截图所示。

![“添加 Web 应用程序防火墙”对话框](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>另请参阅
本文介绍了如何使用 Azure 安全中心的监视功能。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [在 Azure 安全中心设置安全策略](security-center-policies.md)：了解如何在 Azure 安全中心配置安全设置。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题](security-center-faq.md)：查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/):.查找关于 Azure 安全性及合规性的博客文章

