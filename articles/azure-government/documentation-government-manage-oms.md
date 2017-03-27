---
title: "Azure 政府版 Operations Management Suite | Microsoft 文档"
description: "本文介绍美国政府机构和解决方案提供商如何运用 Operations Management Suite 中的 Log Analytics"
services: azure-government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
ms.assetid: 
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/05/2016
ms.author: saweld
translationtype: Human Translation
ms.sourcegitcommit: 35cefe7f394e0f332b7b6fd4ec1609ae04f5fc6f
ms.openlocfilehash: 84eabe4bb79262c47f530c59e36119bacc4115d7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite"></a>Azure 政府版网络安全：使用 Operations Management Suite 监视和保护资产 

## <a name="cybersecurity-in-the-cloud"></a>云中的网络安全
对于迁移到云的客户而言，保持已部署到云中的 Azure 政府版服务的资产管理和安全性是一个至关重要的考虑因素。 需正确配置虚拟机防火墙。 虚拟网络需应用正确的网络安全组。 需将资产访问权限锁定为在合适的时间方可访问。 这一切都是必需的工作流，需进行适当规划、设计和预配，使机构能够使用安全的基础结构。

但设置此类环境并非易事。 将服务器机群登记到任何监视服务都是一项难以调整的操作，同时，监视服务又很难以更新。 另一方面，在不同云提供程序上以及跨云和本地监视基础结构也比较困难。 最后，确保监视保持最新状态，让 Azure Application Insights 对网络安全威胁进行监控、检测、警告和采取应对措施，都需要一定的时间、资源和计算能力。

## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite
Azure 政府版中现已推出的 Microsoft Operations Management Suite 是可让用户快速轻松实现所有这些目的的 Azure 服务套件。 本文重点介绍如何在 Log Analytics 中使用超大规模日志搜索快速分析数据，暴露环境中的威胁。

Azure Log Analytics 可以：

* 在 Azure、其他云提供程序中和本地将代理部署到单个 VM（Linux 和 Windows）。
* 通过 Azure 政府版存储帐户或 System Center Operations Manager 终结点将现有日志连接到现有日志记录数据。

让我们探讨如何将 Log Analytics 集成到机群中，以及一些可解决上述忧虑的现成解决方案。

## <a name="onboarding-servers-to-log-analytics"></a>将服务器登记到 Log Analytics
若要将云资产与 Log Analytics 集成，第一个步骤就是在日志源中安装 Log Analytics 代理。 对于虚拟机，此过程很简单，因为可以 Log Analytics 门户手动下载代理。

![图 1：连接到 Operations Management Suite 的 Windows 服务器](./media/documentation-government-oms-figure1.png)
<p align="center">图 1：连接到 Log Analytics 的 Windows 服务器</p>

可通过 Azure 门户直接将 Azure VM 连接到 Log Analytics。 有关说明，请参阅 [New ways to enable Log Analytics on your Azure VMs](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/)（在 Azure VM 上启用 Log Analytics 的新方式）。

还可以使用编程方式连接这些服务器，或者将 Log Analytics 虚拟机扩展直接配置到 Azure Resource Manager 模板中。 有关基于 Windows 的计算机的连接说明，请参阅[将 Windows 计算机连接到 Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents)，有关基于 Linux 的计算机的连接说明，请参阅[将 Linux 计算机连接到 Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents)。

## <a name="onboarding-storage-accounts-and-operations-manager-to-log-analytics"></a>将存储帐户和 Operations Manager 登记到 Log Analytics
Log Analytics 还可以连接到存储帐户和/或现有 System Center Operations Manager 部署，在混合方案中（云提供程序之间或云/本地基础结构中）提供操作管理。

![图 2：将 Azure 存储和 Operations Manager 连接到 Log Analytics](./media/documentation-government-oms-figure2.png)
<p align="center">图 2：将 Azure 存储和 Operations Manager 连接到 Log Analytics</p>

Log Analytics 还支持从其他监视服务（如 Chef 和/或 Puppet）收集日志记录信息。 此外，我们针对 Azure 部署提供了 VM，VM 中具有启用了 Log Analytics 的 Azure Resource Manager 模板，因此可以同时部署计算并将其登记到 Log Analytics 工作区。

![图3：适用于 Azure VM 的具有 Log Analytics VM 扩展的 Azure Resource Manager 模板](./media/documentation-government-oms-figure3a.png)
![图3：适用于 Azure VM 的具有 Log Analytics VM 扩展的 Azure Resource Manager 模板](./media/documentation-government-oms-figure3b.png)
<p align="center">图3：适用于 Azure VM 的具有 Log Analytics VM 扩展的 Azure Resource Manager 模板</p>

有关在本地使用现有 Operations Manager 实现设置 Log Analytics 的信息，请参阅[将 Operations Manager 连接到 Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents)。

## <a name="applying-intelligence-through-operations-management-suite-solution-packs"></a>通过 Operations Management Suite 解决方案包应用智能
获取各种日志记录数据源后，必须理解所有这些数据。

Log Analytics 的核心是日志搜索服务，可用于编写强大的查询，在数千甚至数百万条日志中进行快速搜索。 但是，发现需要为此编写查询的这些问题可能十分困难。

进入 Operations Management Suite 解决方案。 这些查询包原生与 Log Analytics 集成，可主动探索 Log Analytics 管理的机群。

在网络安全主题方面，本文将简要介绍 Log Analytics 可立即解决的三种网络安全情况。

### <a name="antimalware-assessment"></a>反恶意软件评估
反恶意软件评估提供一组固有的查询和通知，通过监视仪表板可一览机群对恶意软件的防护程度。

此仪表板提供以下四项内容：
* 具有活动和/或修复威胁的任何服务器。
* 当前检测到的威胁。
* 未受到充分保护的计算机。 Log Analytics 通过捕获计算机日志查找此信息，在常用 Web 浏览器中查找任何 FW 处于打开状态或未正确配置其规则的站点。
* 分析如何保护受保护的服务器，例如，通过本机 Windows OS 病毒防护或类似 System Center Endpoint Protection 的解决方案。

例如，在下图中，可以看到 System Center 捕获威胁并自动对其进行鉴别分类：

![图 4：Operations Management Suite 反恶意软件评估解决方案](./media/documentation-government-oms-figure4.png)
<p align="center">图 4：Operations Management Suite 反恶意软件评估解决方案</p>

有关反恶意软件评估的详细信息，请参阅 [Log Analytics 中的恶意软件评估解决方案](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/)一文。

### <a name="identity-and-access"></a>标识和访问
云中另一个常见的网络安全问题与凭据泄露有关。 不仅云订阅具有凭据，而且每个单独的 VM 都具有与其相关联的用户和/或机密（通常是证书或密码）。

Log Analytics 将组织机群中的所有登录尝试，并根据类型（远程、本地、用户名等）将其放入存储桶。 例如，在以下示例中，可以看到大量将随机字符串用作用户名导致的不成功登录尝试。 这表明计算机很可能遭到暴露，没有受到防火墙和访问控制列表的适当保护。

![图 5：过去 24 小时内 97.3% 登录失败](./media/documentation-government-oms-figure5.png)
<p align="center">图 5：过去 24 小时内 97.3% 登录失败</p>

### <a name="threat-intelligence"></a>威胁情报
Log Analytics 还提供针对恶意内部情况的防护，此恶意内部情况指组织内部存在安全危害，恶意用户试图泄露数据。

Log Analytics 威胁智能会查看计算机上的所有网络日志，自动搜索并通知有关与已知恶意 IP（例如，未编入索引的暗网上的 IP 地址）连接的入站/出站网络。

例如，在以下屏幕截图中，可以看到存在与中华人民共和国的入站和出站网络连接。

双击入站标记可以发现，由 Log Analytics 管理的 Linux VM 正在与中国的已知暗网 IP 地址进行出站连接。

还可以对 Operations Management Suite 解决方案（如威胁智能）设置“警报”。 如以下屏幕截图中所示，我已设置警报，因此如果 Log Analytics 检测到与已知恶意 IP 地址连接建立了 10 个以上的出站连接，则会通过电子邮件向我发送警报。 然后，将该警报配置为触发 Azure 自动化作业，此作业设置为自动关闭该 VM。

![图 6：Operations Management Suite 警报和自动化](./media/documentation-government-oms-figure6.png)
<p align="center">图 6：Operations Management Suite 警报和自动化</p>

这只是现成的 Operations Management Suite 解决方案的其中一个示例，无论服务器机群在何处运行（Azure、另一个云服务提供程序或本地），该解决方案都可应用于机群。

Operations Management Suite 会持续自动更新其机器学习以应对最新的威胁，我们也将不断地在 Azure 应用商店中推出新的解决方案。

有关 Operations Management Suite 的详细信息，请参阅[我们的文档页](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/)。



<!--HONumber=Jan17_HO1-->


