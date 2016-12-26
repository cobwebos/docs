---
title: "Azure 政府版 OMS | Microsoft Docs"
description: "本文介绍适用于美国政府机构和解决方案提供商的 OMS 方案"
services: Azure-Government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
editor: 
translationtype: Human Translation
ms.sourcegitcommit: ccbcf15eaf9b13625338ea1d66554eae7dc7d2eb
ms.openlocfilehash: 0d010bb21d807239da37172bc2885ddadc5c8028

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite-oms"></a>Azure 政府版网络安全：使用 Operations Management Suite (OMS) 监视和保护资产

## <a name="cybersecurity-in-the-cloud"></a>云中的网络安全
对于迁移到云的客户而言，保持已部署到云中的 Azure 政府版服务的资产管理和安全性至关重要。 需正确配置虚拟机防火墙。 虚拟网络需应用正确的网络安全组。 需将资产访问权限锁定为合适的人员在合适的时间方可访问。 这一切都是必需的工作流，需进行适当规划、设计和预配，使机构能够使用安全的基础结构。

但设置此类环境并非易事。 一方面，将服务器机群载入任何监视服务都很难扩展和更新。 另一方面，在不同云提供程序上以及跨云和本地监视基础结构也比较困难。 最后，确保监视保持最新状态，提供见解以对网络安全威胁进行监控、检测、警告并采取相应操作，这需要一定的时间、资源和计算能力。

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)
Azure 政府版现已提供 Microsoft Operations Management Suite，该服务能够利用映射化简和机器学习服务的强大功能来完成所有这些操作。 OMS 可以：
* 在 Azure、其他云提供程序和/或本地上将代理部署到单独 VM（Linux 和 Windows）。
* 通过 Azure 政府版存储帐户或 SCOM 端点将现有日志连接到现有日志记录数据。
* 运行由超大规模日志搜索支持的长期有效的机器学习和映射化简服务，暴露环境中存在的威胁。

我们来看看如何将 OMS 集成到机群中，以及一些可解决上述要点的现成可用的解决方案。

## <a name="onboarding-servers-to-oms"></a>将服务器载入 OMS
将云资产与 Operations Management Suite 集成，首要事情就是在日志源中安装 OMS 代理。
对于虚拟机，这很简单 - 可从 OMS 工作区手动下载代理。

![图 1：连接到 OMS 的 Windows 服务器](./media/documentation-government-oms-figure1.png)
<p align="center">图 1：连接到 OMS 的 Windows 服务器</p>

可通过门户直接将 Azure VM 连接到 OMS。 请参阅[此处](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/)的说明。

还可以以编程方式连接它们和/或将 OMS 扩展直接配置到 Azure Resource Manager 模板中。 [此处](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents)为基于 Windows 的计算机说明，[此处](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents)为基于 Linux 的计算机说明。

## <a name="onboarding-storage-accounts-and-scom-to-oms"></a>将存储帐户和 SCOM 载入 OMS
OMS 还可以连接到存储帐户和/或现有 SCOM 2013 部署，在混合方案中（云提供程序之间或云/本地基础结构中）提供操作管理。

![图 2：将 Azure 存储和 SCOM 连接到 OMS](./media/documentation-government-oms-figure2.png)
<p align="center">图 2：将 Azure 存储和 SCOM 连接到 OMS</p>

OMS 还支持从其他监视服务（如 Chef 和/或 Puppet）记录信息。 此外，我们还针对 Azure 部署提供了 VM，VM 中具有启用了 OMS 的 Azure Resource Manager 模板，因此可以同时部署计算并将其载入 OMS 工作区。 

![图3：适用于 Azure VM 的具有 OMS 扩展的 Azure Resource Manager 模板](./media/documentation-government-oms-figure3a.png)
![图3：适用于 Azure VM 的具有 OMS 扩展的 Azure Resource Manager 模板](./media/documentation-government-oms-figure3b.png)
<p align="center">图 3：适用于 Azure VM 的具有 OMS 扩展的 Azure Resource Manager 模板</p>

若要深入了解如何在本地使用现有 SCOM 实现设置 OMS，请访问[此处](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents)。

## <a name="leveraging-intelligence-through-oms-solution-packs"></a>利用 OMS 解决方案包中的智能。
现在已具有各种日志记录数据源，接下来有一个需要单独考虑的问题，即如何理解所有这些数据。
OMS 的核心是日志搜索服务，可用于编写强大的查询，在数千甚至数百万条日志中进行快速搜索。 但是，发现需要为此编写查询的这些问题却非常困难。

使用 OMS 解决方案（与 OMS 映射化简和机器学习技术在本地集成的查询包），可主动探索 OMS 管理的机群。

在网络安全主题中，我将简要介绍 OMS 可立即解决的三种网络安全情况。

###<a name="antimalware-assessment"></a>反恶意软件评估
反恶意软件评估提供一组固有的查询和通知，通过监视仪表板可一览机群对恶意软件的防护程度。

此仪表板提供以下 4 项内容：
* 具有活动和/或修复威胁的任何服务器。
* 当前检测到的威胁
* 未受到充分保护的计算机。 OMS 通过抓取计算机日志执行此操作，并在常用 Web 浏览器中查找任何 FW 处于打开状态和/或未正确配置其规则的站点。
* 分析如何保护受保护的服务器。 例如，通过本机 Windows OS 病毒防护和/或类似 System Center Endpoint Protection 等方式。

例如，下图中，可看到 System Center 捕获威胁并自动对其进行鉴别分类：

![图 4：OMS 反恶意软件评估解决方案](./media/documentation-government-oms-figure4.png)
<p align="center">图 4：OMS 反恶意软件评估解决方案</p>

有关反恶意软件评估的详细信息，可访问 [https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/)

### <a name="identity-and-access"></a>标识和访问
云中另一个常见的网络安全情况是凭据泄露。 不仅云订阅具有凭据，而且每个单独的 VM 都具有与其相关联的用户和/或机密（通常是证书和/或密码）。

OMS 将合计和组织机群中的所有登录尝试，并根据类型（远程、本地、所用用户名等）将其放入存储桶。例如，下图中，可以看到大量将随机字符串用作用户名导致的不成功登录尝试。 这很可能是因为计算机遭到暴露，没有受到防火墙和访问控制列表的适当保护。

![图 5：过去 24 小时内 97.3% 登录失败](./media/documentation-government-oms-figure5.png)
<p align="center">图 5：过去 24 小时内 97.3% 登录失败</p>

### <a name="threat-intelligence"></a>威胁智能
OMS 还提供针对恶意内部情况的防护，此恶意内部情况指组织内部存在安全危害，恶意用户试图泄露数据。

OMS 威胁智能会查看计算机上的所有网络日志，自动搜索并通知有关与已知恶意 IP（例如，未编入索引的暗网上的 IP 地址）连接的入站/出站网络。
例如，下图中，可以看到存在与中华人民共和国的入站和出站网络连接。 

双击入站标记可以发现，由 OMS 管理的 Linux VM 正在与中国的已知暗网 IP 进行出站连接。

还可以对 OMS 解决方案（如威胁智能）设置“警报”。 如下图所示，我已设置警报，因此如果 OMS 检测到 10 个以上与已知恶意 IP 连接的出站，则将通过电子邮件向我发送警报。 然后，将该警报配置为触发 Azure 自动化作业，此作业设置为自动关闭该 VM。

![图 6：OMS 警报和自动化](./media/documentation-government-oms-figure6.png)
<p align="center">图 6：OMS 警报和自动化</p>

这只是现成的 OMS 解决方案的其中一个示例，无论服务器机群在何处运行（Azure、另一个云服务提供程序或本地），该解决方案都可应用于机群。
OMS 将继续自动更新其机器学习以应对最新的威胁，且我们将持续向 OMS 解决方案库推出新的解决方案。

有关 OMS 的详细信息，请参阅文档页：[https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/) 



<!--HONumber=Nov16_HO4-->


