---
title: SAP 解决方案概述和体系结构的 Azure Monitor |Microsoft Docs
description: 本文提供有关 Azure monitor for SAP 解决方案的常见问题的解答
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: d9730324b2557c8f0bb203f7badbd00e0e7e704e
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994260"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>适用于 SAP 解决方案的 Azure monitor (预览版) 

## <a name="overview"></a>概述

SAP 解决方案的 Azure Monitor 是一种 Azure 本机监视产品，适用于在 Azure 上运行 SAP 环境的客户。 该产品适用于 [Azure 虚拟机上的 sap](./hana-get-started.md) 和 [azure 大型实例上的 sap](./hana-overview-architecture.md)。
利用针对 SAP 解决方案的 Azure Monitor，客户可以在一个中心位置收集来自 Azure 基础结构和数据库的遥测数据，并直观关联遥测数据，以便更快地进行故障排除。

SAP 解决方案的 Azure Monitor 通过 Azure Marketplace 提供。 它提供简单、直观的设置体验，只需单击几下鼠标，就能为 SAP 解决方案的 Azure Monitor 部署资源 (称为 **sap Monitor 资源**) 。

客户可以通过添加相应的 **供应商** 来监视 SAP 布局（如 Azure 虚拟机、高可用性群集、SAP HANA 数据库等）的不同组件。

支持的基础结构：

- Azure 虚拟机
- Azure 大型实例

支持的数据库：
- SAP HANA 数据库
- Microsoft SQL server

SAP 解决方案的 Azure Monitor 利用现有 [Azure Monitor](../../../azure-monitor/overview.md) 功能（如 Log Analytics 和 [工作簿](../../../azure-monitor/platform/workbooks-overview.md) ）的强大功能来提供其他监视功能。 客户可以通过编辑 SAP 解决方案 Azure Monitor 提供的默认工作簿来创建 [自定义可视化效果](../../../azure-monitor/platform/workbooks-overview.md#getting-started) ，使用 Azure Log Analytics 工作区编写 [自定义查询](../../../azure-monitor/log-query/get-started-portal.md) 并创建 [自定义警报](../../../azure-monitor/learn/tutorial-response.md) ，利用 [灵活的保留期](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 并将监视数据与票证系统连接。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>SAP 解决方案 Azure Monitor 收集哪些数据？

SAP 解决方案 Azure Monitor 中的数据收集取决于客户配置的访问接口。 公共预览期间，将收集以下数据。

高可用性 Pacemaker 群集遥测：
- 节点、资源和 SBD 设备状态
- Pacemaker 位置约束
- 仲裁投票和振铃状态
- [其他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 遥测：
- CPU、内存、磁盘和网络利用率
- HANA 系统复制 (HSR) 
- HANA 备份
- HANA 主机状态
- 索引服务器和名称服务器角色

Microsoft SQL server 遥测：
- CPU、内存、磁盘利用率
- 主机名、SQL 实例名称、SAP 系统 ID
- 随时间推移的批处理请求、编译和页生存期
- 一段时间内排名前10的最昂贵 SQL 语句
- SAP 系统中的最大12个表
- SQL Server 错误日志中记录的问题
- 阻塞进程以及一段时间内的 SQL 等待统计信息

## <a name="data-sharing-with-microsoft"></a>与 Microsoft 共享数据

针对 SAP 解决方案的 Azure Monitor 收集系统元数据，以为 Azure 客户的 SAP 提供改进的支持。 未收集 PII/EUII。
在为 SAP 解决方案资源创建 Azure Monitor 时，客户可以通过从下拉菜单中选择 " *共享* " 来启用与 Microsoft 的数据共享。
强烈建议客户启用数据共享，因为它向 Microsoft 支持部门和工程团队提供有关客户环境的详细信息，并为 Azure 客户的关键任务 SAP 提供改进的支持。

## <a name="architecture-overview"></a>体系结构概述

从较高层次来看，下图说明了 SAP 解决方案 Azure Monitor 如何从 SAP HANA 数据库中收集遥测数据。 无论 SAP HANA 部署在 Azure 虚拟机上还是 Azure 大型实例上，该体系结构都不可知。

![SAP 解决方案体系结构的 Azure Monitor](./media/azure-monitor-sap/azure-monitor-architecture.png)

该体系结构的关键组件是：
- Azure 门户–客户的起点。 客户可以在 Azure 门户中导航到 marketplace，并发现 SAP 解决方案的 Azure Monitor
- SAP 解决方案资源的 Azure Monitor –客户用于查看监视遥测的登陆位置
- 托管资源组–作为 SAP 解决方案资源部署的 Azure Monitor 的一部分自动部署。 托管资源组中部署的资源可帮助收集遥测数据。 部署的关键资源及其用途如下：
   - Azure 虚拟机：也称为 *收集器 VM*。 这是一个 Standard_B2ms VM。 此 VM 的主要目的是托管 *监视负载*。 监视负载是指从源系统收集遥测数据并将收集的数据传输到监视框架的逻辑。 在上面的关系图中，监视负载包含通过 SQL 端口连接到 SAP HANA 数据库的逻辑。
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md)：部署此资源以安全地保存 SAP HANA 数据库凭据并存储有关 [提供程序](./azure-monitor-providers.md)的信息。
   - Log Analytics 工作区：遥测数据所在的目标。
      - 可视化基于使用 [Azure 工作簿](../../../azure-monitor/platform/workbooks-overview.md)Log Analytics 中的遥测构建。 客户可以自定义可视化效果。 客户还可以将工作簿或工作簿中的特定视觉对象固定到 Azure 仪表板，以实现 autorefresh 功能，最小粒度为30分钟。
      - 在部署时，客户可以通过选择此选项，将其现有的工作区与 SAP 监视器资源在同一订阅中。
      - 客户可以使用 Kusto 查询语言 (KQL) 对 Log Analytics 工作区内的原始表运行 [查询](../../../azure-monitor/log-query/log-query-overview.md) 。 查看 *自定义日志*。

> [!Note]
> 客户负责修补和维护部署在托管资源组中的 VM。

> [!Tip]
> 如果将现有 Log Analytics 工作区部署到与 SAP 解决方案的 Azure Monitor 资源相同的 Azure 订阅中，则客户可以选择使用该工作区进行遥测收集。

### <a name="architecture-highlights"></a>体系结构亮点

下面是体系结构的主要重点：
 - **多实例** -客户可以为给定组件类型的多个实例创建监视器 (例如，HANA DB、HA 群集、Microsoft SQL server) 跨 VNET 内多个 sap sid，其中包含 SAP 解决方案的单个 Azure Monitor 资源。
 - **多提供程序** -上述体系结构关系图以示例形式显示 SAP HANA 提供程序。 同样，客户也可以为相应的组件配置其他提供程序 (例如，HANA DB、HA 群集、Microsoft SQL server) 从这些组件收集数据。
 - **开放源代码** - [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions)中提供了适用于 SAP 解决方案的 Azure Monitor 的源代码。 客户可以参考提供商代码，并了解有关该产品、参与或共享反馈的详细信息。
 - **可扩展查询框架** -以 [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)格式编写用于收集遥测数据的 SQL 查询。 可以轻松添加其他用于收集更多遥测数据的 SQL 查询。 客户可以通过本文档结尾处的链接来请求将特定遥测数据添加到 Azure Monitor 用于 SAP 解决方案，或联系其帐户团队。

## <a name="pricing"></a>定价
SAP 解决方案的 Azure Monitor 是免费的产品 (无许可费) 。 客户负责为托管资源组中的基础组件付费。

## <a name="next-steps"></a>后续步骤

了解提供程序并为 SAP 解决方案资源创建第一个 Azure Monitor。
 - 了解有关[提供程序](./azure-monitor-providers.md)的详细信息
 - [使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions](azure-monitor-sap-quickstart-powershell.md)
 - 对于 SAP 解决方案 Azure Monitor 是否有疑问？ 检查 [FAQ](./azure-monitor-faq.md) 部分
