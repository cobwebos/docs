---
title: "添加 Azure Log Analytics 管理解决方案 | Microsoft 文档"
description: "Operations Management Suite (OMS)/Log Analytics 管理解决方案是逻辑、可视化效果和数据采集规则的集合，用于提供围绕特定问题区域透视的指标。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ee0191c63f12ae32708095362b8028a61ca731cc
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>将 Azure Log Analytics 管理解决方案添加到工作区

Log Analytics 管理解决方案是**逻辑**、**可视化**和**数据采集规则**的集合，用于提供围绕特定问题区域透视的指标。 本文列出了 Log Analytics 支持的管理解决方案，并说明了如何使用 Azure 门户为工作区添加和删除管理解决方案。 还可以在 OMS 门户中使用解决方案库添加解决方案。

管理解决方案可提供深入探索，以便：

* 帮助更快地调查和解决操作问题
* 收集和关联各种类型的计算机数据
* 帮助你主动应对解决方案公开的活动。

> [!NOTE]
> Log Analytics 包括了日志搜索功能，因此无需安装管理解决方案便可启用该功能。 不过，可以通过向工作区添加管理解决方案来获取数据可视化、建议的搜索和洞察力。

根据本文，将使用 Azure 门户应用商店向工作区添加管理解决方案。 添加解决方案后，会从基础结构中的服务器收集数据并将其发送到 OMS 服务。 OMS 服务处理通常需要几分钟到一小时。 该服务处理数据后，可以在 OMS 中查看。

当不再需要某个管理解决方案时，可以轻松将其删除。 在删除管理解决方案时，其数据不会发送至 OMS。 如果在免费定价层，删除解决方案可减少数据使用量，帮助维持在数据的每日配额以下。

## <a name="view-available-management-solutions"></a>查看可用的管理解决方案

Azure 应用商店包含 [Log Analytics 的管理解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)列表。

可以通过单击每个解决方案底部的“立即获取”链接从 Azure 应用商店安装管理解决方案。

## <a name="add-a-management-solution"></a>添加管理解决方案
1. 如果尚未登录 [Azure 门户](https://portal.azure.com)，请使用 Azure 订阅登录。
2. 在“新建”边栏选项卡中，在“应用商店”下，选择“监视 + 管理”。
3. 在“监视 + 管理”边栏选项卡中，单击“查看所有”。  
    ![“监视 + 管理”边栏选项卡](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. 在“管理解决方案”的右侧，单击“更多”。
5. 在“管理解决方案”边栏选项卡中，选择要添加到工作区的管理解决方案。  
    ![“监视 + 管理”边栏选项卡](./media/log-analytics-add-solutions/management-solutions.png)  
6. 在管理解决方案边栏选项卡中，复查有关管理解决方案的信息，并单击“创建”。
7. 在*管理解决方案名称*边栏选项卡中，选择要与该管理解决方案相关联的工作区。
8. （可选）针对 Azure 订阅、资源组和位置更改工作区设置。 还可以选择“自动化选项”。 单击“创建”。  
    ![解决方案工作区](./media/log-analytics-add-solutions/solution-workspace.png)  
9. 若要开始使用已添加到工作区的管理解决方案，请导航到“Log Analytics” > “订阅” > ***工作区名称*** > “概述”。 管理解决方案的新磁贴随即出现。 单击该磁贴以将其打开并在收集解决方案的数据后开始使用该解决方案。

## <a name="remove-a-management-solution"></a>删除管理解决方案

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“Log Analytics” > “订阅” > ***工作区名称***，然后在***工作区名称***边栏选项卡中单击“解决方案”。
2. 在管理解决方案列表中，选择要删除的解决方案。
3. 在工作区的解决方案边栏选项卡中，单击“删除”。  
    ![删除解决方案](./media/log-analytics-add-solutions/solution-delete.png)  
4. 在确认对话框中，单击“是”。

## <a name="offers-and-pricing-tiers"></a>产品和定价层

下表列出了属于每个 Operations Management Suite 产品的管理解决方案。
该表还标识了适合每个管理解决方案的定价层。
下表中的所有解决方案都可从 Azure 门户和 Log Analytics 门户的解决方案库中获取。

| 管理解决方案                                                                       | 产品                                                                     | 定价层<sup>1</sup>                                                 | 说明 |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [活动 Log Analytics](log-analytics-activity.md)                                                                   | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 90 天内数据免费使用<br>数据不受免费层资源占用限额的约束 |
| [AD 评估](log-analytics-ad-assessment.md)                                           | <ul><li>见解与分析&nbsp;&nbsp;</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [AD 复制状态](log-analytics-ad-replication-status.md)                           | <ul><li>见解与分析&nbsp;&nbsp;</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 无法从 Azure 门户/应用商店添加。 |
| 代理运行状况                                                                              | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 数据不受免费层资源占用限额的约束<br> 无法从 Azure 门户/应用商店添加。 |
| [警报管理](log-analytics-solution-alert-management.md)                            | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 无法从 Azure 门户/应用商店添加。 |
| Application Insights 连接器 <br>（预览版）                                              | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| 自动化混合辅助角色                                                                  | <ul><li>自动化与控制</li></ul>                                  | 免费<br> 按&nbsp;节点&nbsp;(OMS)                                                                         | 需要将 Log Analytics 工作区链接到自动化帐户 |
| [Azure 应用程序网关分析](log-analytics-azure-networking-analytics.md)    | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [Azure 网络安全组分析](log-analytics-azure-networking-analytics.md)     | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [Azure SQL Analytics（预览版）](log-analytics-azure-sql.md)                                                       | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br>按&nbsp;节点&nbsp;(OMS)                                                                          | 需要将 Log Analytics 工作区链接到自动化帐户|
| 备份                                                                                    | <ul><li>见解与分析</li></ul>                                   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)                                                                       | 需要经典备份保管库。<br> 无法从 Azure 门户/应用商店添加。 |
| 容量和性能 <br>（预览版）                                                    | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [更改跟踪](log-analytics-change-tracking.md)                                       | <ul><li>自动化与控制</li></ul>                                  | 免费<br> 按&nbsp;节点&nbsp;(OMS)                                                                         | 需要将 Log Analytics 工作区链接到自动化帐户 |
| [容器](log-analytics-containers.md)                                                 | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [IT Service Management Connector](log-analytics-itsmc-overview.md)<br>（预览版）                                               | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 按&nbsp;节点&nbsp;(OMS)     | |
| HDInsight HBase 监视 <br>（预览版）                                                  | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [密钥保管库分析](log-analytics-azure-key-vault.md)                   | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| 逻辑应用 B2B                    | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 无法从 Azure 门户/应用商店添加。 |
| [恶意软件评估](log-analytics-malware.md)                                            | <ul><li>安全与合规性</li></ul>                                 | 免费<br> 独立<br>按&nbsp;节点&nbsp;(OMS)                                                                           | 如果在 2017 年 6 月 19 日后添加安全性和符合性解决方案，则将按[节点计费](https://azure.microsoft.com/pricing/details/security-compliance/)，无论工作区定价层如何，都是如此。 前 60 天免费。  |
| [网络性能监视器](log-analytics-network-performance-monitor.md) <br>  | <ul><li>见解与分析</li></ul>                                   | 免费<br> 按&nbsp;节点&nbsp;(OMS)                                                                         | |
| Office 365 分析 <br>（预览版）                                                        | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [安全和审核](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>安全&nbsp;与&nbsp;合规性</li></ul>                       | 免费<br> 独立<br>按&nbsp;节点&nbsp;(OMS)                                                                           | 需要通过此解决方案来收集安全事件日志<br>如果在 2017 年 6 月 19 日后添加安全性和符合性解决方案，则将按[节点计费](https://azure.microsoft.com/pricing/details/security-compliance/)，无论工作区定价层如何，都是如此。 前 60 天免费。 |
| [Service Fabric 分析](log-analytics-service-fabric.md) <br>（预览版）                 | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| [服务地图](../operations-management-suite/operations-management-suite-service-map.md) <br>（预览版）| <ul><li>见解与分析</li></ul>                       | 免费<br> 按&nbsp;节点&nbsp;(OMS)                                                                         | 在美国东部、西欧和美国中西部可用    |
| Site Recovery                                                                             | <ul><li>见解与分析</li></ul>                                   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)                                                                       | 需要经典 Site Recovery 保管库。<br> 无法从 Azure 门户/应用商店添加。 |
| [SQL 评估](log-analytics-sql-assessment.md)                                         | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| 在非工作时间启动/停止 VM<br>（预览版）                                              | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 按&nbsp;节点&nbsp;(OMS)                                                                         | 需要将 Log Analytics 工作区链接到自动化帐户 |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 无法从 Azure 门户/应用商店添加。 |
| [System Center Operations Manager 评估](log-analytics-scom-assessment.md) <br>（预览版） | <ul><li>见解与分析</li><li>Log Analytics</li></ul>         | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| 更新管理                                                                         | <ul><li>自动化与控制</li></ul>                                  | 免费<br> 按&nbsp;节点&nbsp;(OMS)                                                                         | 需要将 Log Analytics 工作区链接到自动化帐户 |
| [更新符合性](https://technet.microsoft.com/en-us/itpro/windows/manage/update-compliance-get-started) <br>（预览版）                                                             | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 数据或节点免费<br>数据不受免费层资源占用限额的约束。<br> 无法从 Azure 门户/应用商店添加。 |
| [升级准备情况](https://technet.microsoft.com/itpro/windows/deploy/manage-windows-upgrades-with-upgrade-readiness)                                                            | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | 数据或节点免费<br>数据不受免费层资源占用限额的约束。<br> 无法从 Azure 门户/应用商店添加。 |
| [VMware 监视](log-analytics-vmware.md) <br>（预览版）                                | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 标准<br> 高级&nbsp;(OMS)<br> 按&nbsp;GB&nbsp;（独立）<br> 按&nbsp;节点&nbsp;(OMS)   | |
| 传输数据 2.0<br>（预览版）                                                                | <ul><li>见解与分析</li></ul>                                   | 免费<br> 按&nbsp;节点&nbsp;(OMS)                                                                         | 在美国东部、西欧和美国中西部可用 |

<sup>1</sup> 标准和高级 (OMS) 定价层仅适用于在 2016 年 9 月 21 日前创建 Log Analytics 工作区的客户。

### <a name="community-provided-management-solutions"></a>社区提供的管理解决方案

社区提供的管理解决方案可从 [Azure 模板库](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS))或直接从作者处获取。

| 管理解决方案               | 产品                                                                     | 定价层                         | 说明 |
| ---                               | ---                                                                       | ---                                   | ---   |
| 所有社区提供的解决方案  | <ul><li>见解&nbsp;与&nbsp;分析</li><li>Log Analytics</li></ul>   | 免费<br> 按&nbsp;节点&nbsp;(OMS)     |   需要将 Log Analytics 工作区链接到自动化帐户 |




## <a name="data-collection-details"></a>数据收集详细信息
以下各表显示了数据收集方法以及有关如何为 Log Analytics 管理解决方案和数据源收集数据的其他详细信息。 这些表按等同于[订阅定价层](https://go.microsoft.com/fwlink/?linkid=827926)的解决方案产品进行分类。 活动 Log Analytics 解决方案可供所有定价层免费使用。

Log Analytics Windows 代理和 System Center Operations Manager 代理实质上相同。 Windows 代理包括附加功能，可允许它连接到 OMS 工作区并通过代理服务器进行路由。 如果使用 Operations Manager 代理，则必须将其定为 OMS 代理才能与 OMS 进行通信。 此表中的 Operations Manager 代理是连接到 Operations Manager 的 OMS 代理。 有关将现有 Operations Manager 环境连接到 OMS 的信息，请参阅[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md)。

> [!NOTE]
> 使用的代理类型确定在以下条件下，数据如何发送到 OMS：
> - 你使用 Windows 代理或连接了 Operations Manager 的 OMS 代理。
> - 需要 Operations Manager 时，请始终使用 Operations Manager 管理组，将解决方案的 Operations Manager 代理数据发送至 OMS。 此外，需要 Operations Manager 时，解决方案仅使用 Operations Manager 代理。
> - 不需要 Operations Manager 且表显示使用管理组将 Operations Manager 代理数据发送到 OMS 时，始终使用管理组将 Operations Manager 代理数据发送到 OMS。 Windows 代理绕过管理组，直接将其数据发送到 OMS。
> - 不使用管理组发送 Operations Manager 代理数据时，这些数据绕过管理组直接发送到 OMS。

### <a name="insight--analytics--log-analytics"></a>见解与分析 / Log Analytics

| 管理解决方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 活动 Log Analytics | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 通知时 |
| AD 评估 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 天 |
| AD 复制状态 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |5 天 |
| 代理运行状况 | Windows 和 Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | 1 分钟 |
| 警报管理 (Nagios) |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |到达时 |
| 警报管理 (Zabbix) |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |1 分钟 |
| 警报管理 (Operations Manager) |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |3 分钟 |
| Application Insights Connector（预览版） | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 通知时 |
| Azure 应用程序网关分析 | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 通知时 |
| Azure 网络安全组分析 | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 通知时 |
| Azure SQL Analytics（预览版） |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 分钟 |
| 容量管理 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |到达时 |
| 容器 | Windows 和 Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 分钟 |
| 密钥保管库分析 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |通知时 |
| 网络性能监视器 | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 每隔 5 秒钟进行 TCP 握手，每隔 3 分钟发送数据 |
| Office 365 分析（预览版） |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |通知时 |
| Service Fabric 分析 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分钟 |
| 服务映射 | Windows 和 Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 15 秒 |
| SQL 评估 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 天 |
| SurfaceHub |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |到达时 |
| System Center Operations Manager 评估（预览版） | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | 七天 |
| Upgrade Analytics（预览版） | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 2 天 |
| VMware 监视（预览版） | Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 分钟 |
| 传输数据 |Windows（2012 R2 / 8.1 或更高版本） |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 1 分钟 |


### <a name="automation--control"></a>自动化与控制

| 管理解决方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 自动化混合辅助角色 | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 不适用 |
| 更改跟踪 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |每小时 |
| 更改跟踪 |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |每小时 |
| 更新管理 | Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |每天和安装更新后的 15 分钟内至少 2 次 |

### <a name="security--compliance"></a>安全与合规性

| 管理解决方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 反恶意软件评估 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |每小时 |
| 安全与审核<sup>1</sup> | Windows 和 Linux | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | 各种 |

<sup>1</sup>“安全与审核”解决方案可以通过 Windows、Operations Manager 和 Linux 代理收集日志。 请参阅[数据源](#data-sources)，了解有关以下各项的数据收集信息：

- Syslog
- Windows 安全事件日志
- Windows 防火墙日志
- Windows 事件日志



### <a name="protection--recovery"></a>保护与恢复

| 管理解决方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 备份 | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 不适用 |
| Azure Site Recovery | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 不适用 |


### <a name="data-sources"></a>数据源


| 数据源 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Azure 活动日志 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |通知时 |
| Azure 诊断日志 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |通知时 |
| Azure 诊断指标 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |通知时 |
| ETW |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分钟 |
| IIS Logs |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分钟 |
| 性能计数器 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |根据计划，最小值为 10 秒 |
| 性能计数器 |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |根据计划，最小值为 10 秒 |
| Syslog |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |来自 Azure 存储：10 分钟；来自代理：到达时 |
| Windows 安全事件日志 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |对于 Azure 存储：10 分钟；对于代理：到达时 |
| Windows 防火墙日志 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |到达时 |
| Windows 事件日志 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |对于 Azure 存储：10 分钟；对于代理：到达时 |



## <a name="preview-management-solutions-and-features"></a>预览版管理解决方案与功能
通过运行服务并遵循开发/运营实践，我们能够与客户合作开发功能和解决方案。

在个人预览版期间，我们为一组少量客户提供先期实现的功能或解决方案的访问权，以获得反馈和进行改进。 这种先期实现具有最少的特性和操作功能。

我们的目标是快速尝试操作，以便可以找到哪些适用，哪些不适用。 我们将遍历此过程，直到个人预览版客户的反馈通知我们可以准备公开预览版为止。

在公开预览版期间，我们提供的功能或解决方案可供所有用户使用，以获得更多反馈并验证我们的拓展和效率。 在此阶段中：

* 预览版功能显示在“设置”选项卡中，可以由任何用户启用。
* 通过库或使用脚本来添加预览版解决方案。

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>我应了解预览版功能和解决方案的哪些信息？
新功能和管理解决方案令人欢欣鼓舞，我们非常愿意与你一起进行开发。

预览功能和解决方案并不适合每个人。 在要求加入个人预览版或启用公开预览版之前，请确保你有能力处理仍处于开发阶段的内容。

通过门户启用预览功能时，会看到一条警告，提醒你该功能处于预览中。

#### <a name="for-both-private-and-public-preview"></a>对于*个人*和*公共*预览
以下信息适用于公开预览版和个人预览版：

* 事情并不始终如愿。
  * 问题范围可能从一点小麻烦发展到根本不工作。
* 预览版可能会对系统/环境产生负面影响。
  * 我们尽量避免了对与 OMS 一起使用的系统产生负面影响，但有时会发生意想不到的问题。
* 可能发生数据丢失/损坏。
* 我们可能要求收集诊断日志或其他数据来帮助解决问题。
* 可能会（临时或永久）删除功能或解决方案。
  * 根据在预览版期间了解的情况，我们可能决定不发布功能或解决方案。
* 预览版可能无法工作，或未用所有配置进行测试，我们可能会限制：
  * 可使用的操作系统（例如，在预览版中某项功能可能仅适用于 Linux）。
  * 无法使用的代理类型（MMA、Operations Manager）（例如，某些功能在预览时可能无法用于 Operations Manager）。  
* 预览版解决方案和功能不包括在服务级别协议中。
* 使用预览版功能将产生使用费用。
* 为使功能/解决方案有用所需的功能可能丢失或不完整。
* 功能/解决方案可能并非在所有地区都可用。
* 功能/解决方案可能未本地化。
* 功能/解决方案可能对可使用它的客户或设备的数量有限制。
* 可能需要使用脚本来执行配置和启用解决方案/功能。
* 用户界面 (UI) 会不完整，并且可能会逐渐更改。
* 公开预览版可能不适合生产/关键系统。

#### <a name="for-private-preview"></a>对于*个人*预览版
除了以上各项，还有以下特定于个人预览版的信息：

* 我们期望你向我们提供关于体验的反馈，以便我们可以更好地提供功能/解决方案。
* 我们可能会通过调查、打电话或电子邮件的方式与你联系反馈事宜。
* 功能不会始终正常工作。
* 需要签订保密协议 (NDA) 才能参与，其中可能包含机密内容。
  * 在发布博客、推文或与第三方进行通信之前，请咨询负责预览版的项目经理，了解关于公开的任何限制。
* 不要在生产/关键系统上运行。

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>如何获取个人预览版功能和解决方案的访问权？
根据预览内容，我们会通过多种不同的方式邀请客户体验个人预览版。

* 如果回答每月的客户调查并授予我们跟进的权限，则可以增加你受邀体验个人预览版的机会。
* Microsoft 帐户团队会你将提名。
* 可以根据 twitter [msopsmgmt](https://twitter.com/msopsmgmt) 上发布的详细信息进行注册。
* 可以根据共享社区活动的详细信息进行注册 – 在见面会、会议和在线社区中查找我们。

## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看管理解决方案收集的详细信息。

