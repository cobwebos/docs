---
title: "使用 Azure Log Analytics 监视 Active Directory 复制状态 | Microsoft 文档"
description: "Active Directory 复制状态解决方案包定期监视 Active Directory 环境中是否有任何复制失败，并在 OMS 仪表板上报告结果。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: bfe52ef5d9d09ffe179faaf6ffbd90ef964fbda9
ms.contentlocale: zh-cn
ms.lasthandoff: 08/14/2017

---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>使用 Log Analytics 监视 Active Directory 复制状态

![AD 复制状态符号](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Active Directory 是企业 IT 环境的关键组件。 若要确保高可用性和高性能，每个域控制器都有其自己的 Active Directory 数据库副本。 域控制器会彼此相互复制，以便在整个企业内传播更改。 这一复制过程中的失败可能导致整个企业内出现各种问题。

AD 复制状态解决方案包定期监视 Active Directory 环境中是否有任何复制失败，并在 OMS 仪表板上报告结果。

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案
使用以下信息安装和配置解决方案。

* 你必须将代理安装在属于要评估的域成员的域控制器上。 或者，你必须在成员服务器上安装代理，并配置代理以将 AD 复制数据发送到 OMS。 要了解如何将 Windows 计算机连接到 OMS，请参阅[将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md)。 如果域控制器已经是你要连接到 OMS 的现有 System Center Operations Manager 环境的一部分，请参阅[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md)。
* 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将 Active Directory 复制状况解决方案添加到 OMS 工作区。  无需进一步的配置。

## <a name="ad-replication-status-data-collection-details"></a>AD 复制状态数据收集详细信息
下表显示了 AD 复制状态的数据收集方法和其他数据收集方式的详细信息。

| 平台 | 直接代理 | SCOM 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |每隔五天 |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>（可选）启用非域控制器以将 AD 数据发送到 OMS
如果不需要将任何域控制器直接连接到 OMS，则可以使用域中任何其他连接 OMS 的计算机来收集 AD 复制状态解决方案包的数据，并让它发送数据。

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>启用非域控制器以将 AD 数据发送到 OMS
1. 确认计算机是你要使用 AD 复制状态解决方案监视的域成员。
2. 如果该计算机尚未连接，请[将 Windows 计算机连接到 OMS](log-analytics-windows-agents.md) 或[使用现有 Operations Manager 环境将它连接到 OMS](log-analytics-om-agents.md)。
3. 在该计算机上，设置以下注册表项：

   * 关键值：**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**
   * 值：**IsTarget**
   * 值数据：**true**

   > [!NOTE]
   > 在重新启动 Microsoft Monitoring Agent 服务 (HealthService.exe) 之后，这些更改才会生效。
   >
   >

## <a name="understanding-replication-errors"></a>了解复制错误
将 AD 复制状态数据发送到 OMS 后，会在 OMS 仪表板上看到类似于下图的磁贴，指示当前的复制错误数。  
![AD 复制状态磁贴](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

严重复制错误是指那些处于或高于 Active Directory 林 75% [逻辑删除生存期](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx)的错误。

当单击该磁贴时，会看到这些错误的详细信息。
![AD 复制状态仪表板](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>目标服务器状态和源服务器状态
这些列显示遇到复制错误的目标服务器和源服务器的状态。 每个域控制器名称后面的数字表示该域控制器上的复制错误数。

目标服务器和源服务器的错误都会显示出来，因为某些问题从源服务器的角度更容易解决，而其他问题从目标服务器的角度更容易解决。

在此示例中，可以看到许多目标服务器的错误数都大致相同，但有一个源服务器 (ADDC35) 具有比其他所有服务器多很多的错误。 ADDC35 上很可能出现一些问题，导致它无法将数据发送到其复制伙伴。 解决 ADDC35 上的问题可能会解决很多在目标服务器区域中显示的错误。

### <a name="replication-error-types"></a>复制错误类型
此区域可提供有关在整个企业中检测到的错误类型的信息。 每个错误都有唯一的数值代码和消息，可帮助你确定错误的根本原因。

顶部的圆环图可让你了解在环境中哪些错误频繁或较少出现。

并且，还可以通过此圆环图查看多个域控制器遇到相同复制错误的情况。 在这种情况下，可在一个域控制器上发现或识别解决方案，并在受相同错误影响的其他域控制器上重复该解决方案。

### <a name="tombstone-lifetime"></a>逻辑删除生存期
逻辑删除生存期确定已删除的对象（称为逻辑删除）保留在 Active Directory 数据库中的时间长度。 当已删除的对象超过逻辑删除生存期时，垃圾回收进程自动将其从 Active Directory 数据库中删除。

在最新版本的 Windows 中，默认逻辑删除生存期为 180 天；但在较早的版本中为 60 天，Active Directory 管理员可以显式更改它。

了解你是否有接近或超过逻辑删除生存期的复制错误是非常重要的。 如果有两个域控制器遇到的复制错误的保留时间超过逻辑删除生存期，则即使已解决基础复制错误，也会禁用这两个域控制器之间的复制。

逻辑删除生存期区域可帮助识别禁用复制危险可能发生的位置。 **超过 100 % TSL** 类别中的每个错误表示至少在林的逻辑删除生存期内，尚未在其源和目标服务器之间复制的分区。

在此情况下，仅修复复制错误是不够的。 至少需要手动调查，识别和清理延迟对象，才可以重新启动复制。 甚至可能需要取消配置域控制器。

除了识别保留时间超过逻辑删除生存期的任何复制错误，还需要留意任何落入 50-75% TSL 或 75-100% TSL 类别的错误。

这些是明显延迟（不是暂时的）的错误，因此，它们可能需要干预才能解决。 值得庆幸的是，它们尚未到达逻辑删除生存期。 如果立即和在达到逻辑删除生存期*之前*解决这些问题，则可以最少的手动干预来重新启动复制。

如上所述，AD 复制状态解决方案的仪表板磁贴显示你环境中的严重复制错误数，其定义为超过 75% 的逻辑删除生存期的错误（包括超过 100% TSL 的错误）。 请尽可能将此数字保持为 0。

> [!NOTE]
> 所有逻辑删除生存期百分比计算都基于 Active Directory 林的实际逻辑删除生存期，因此，可以确信这些百分比是准确的（即使你已设置自定义的逻辑删除生存期值）。
>
>

### <a name="ad-replication-status-details"></a>AD 复制状态详细信息
当单击其中一个列表的任何一项时，可以查看有关使用日志搜索的其他详细信息。 这些结果已经过筛选，只显示与该项相关的错误。 例如，如果单击“目标服务器状态 (ADDC02)”下列出的第一个域控制器，会看到搜索结果经过筛选，显示将该域控制器列为目标服务器的错误：

![搜索结果中的 AD 复制状态错误](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

从这里，可以进一步筛选，修改搜索查询，等等。 有关使用日志搜索的详细信息，请参阅[日志搜索](log-analytics-log-searches.md)。

“**HelpLink**”字段显示了包含该特定错误的其他详细信息的 TechNet 页的 URL。 可以将此链接复制并粘贴到浏览器窗口，以查看有关故障排除和修复该错误的信息。

还可以单击“**导出**”将结果导出到 Excel。 导出数据可让你以任何喜欢的方式来可视化复制错误数据。

![Excel 中导出的 AD 复制状态错误](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD 复制状态常见问题
**问：AD 复制状态数据多长时间更新一次？**
答：信息每 5 天更新一次。

**问：是否有方法来配置此数据的更新频率？**
答：目前没有。

**问：是否需要将所有域控制器添加到我的 OMS 工作区中以查看复制状态？**
答：不需要，只需要添加一个域控制器。 如果 OMS 工作区中有多个域控制器，则所有域控制器的数据发送到 OMS。

**问：我不想将任何域控制器添加到我的 OMS 工作区。是否仍可以使用 AD 复制状态解决方案？**
答：是的。 可以设置注册表项的值来实现此目的。 请参阅[允许非域控制器将 AD 数据发送到 OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms)。

**问：执行数据收集的进程名称是什么？**
答：AdvisorAssessment.exe

**问：收集数据需要花费多长时间？**
答：数据收集时间取决于 Active Directory 环境的大小，但通常不会超过 15 分钟。

**问：收集什么类型的数据？**
答：通过 LDAP 收集复制信息。

**问：是否有方法来配置何时收集数据？**
答：目前没有。

**问：需要哪些权限才能收集数据？**
答：针对 Active Directory 的普通用户权限就足够了。

## <a name="troubleshoot-data-collection-problems"></a>数据收集问题疑难解答
为了收集数据，AD 复制状态解决方案包需要至少一个域控制器以连接到 OMS 工作区。 直到你连接域控制器，出现一条消息，指示“仍在收集数据”。

如果在连接某个域控制器时需要帮助，可以查看[将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md) 文档。 或者，如果域控制器已连接到现有 System Center Operations Manager 环境，则可以查看[将 System Center Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md) 文档。

如果不希望将任何域控制器直接连接到 OMS 或 SCOM，请参阅[允许非域控制器将 AD 数据发送到 OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms)。

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)查看详细的 Active Directory 复制状态数据。

