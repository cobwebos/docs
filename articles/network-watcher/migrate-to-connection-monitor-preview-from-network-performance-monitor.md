---
title: '从网络性能监视器迁移到连接监视器 (预览) '
titleSuffix: Azure Network Watcher
description: 了解如何从网络性能监视器迁移到连接监视器 (预览) 。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: dcbb82c1315e6150ddcfadbb52b2976447329b87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441827"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>从网络性能监视器迁移到连接监视器 (预览) 

你可以将测试从网络性能监视器 (NPM) 迁移到新的、经过改进的连接监视器 (预览) ，只需单击一次，无需停机。 若要了解有关这些优势的详细信息，请参阅 [连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)。

>[!NOTE]
> 只有服务连接监视器中的测试可以迁移到连接监视器 (预览) 。
>

## <a name="key-points-to-note"></a>需要注意的要点

迁移有助于生成以下结果：

* 本地代理和防火墙设置按原样工作。 不需要进行任何更改。 需要将安装在 Azure 虚拟机上的 Log Analytics 代理替换为网络观察程序扩展。
* 现有测试映射到连接监视器 (预览) > 测试组 > 测试格式。 通过选择 " **编辑**"，可以查看和修改新连接监视器的属性 (预览 ") ，下载模板以对其进行更改，并通过 Azure 资源管理器提交模板。
* 代理会将数据发送到 Log Analytics 工作区和指标。
* 数据监视：
   * **Log Analytics 中的数据**：迁移之前，数据将保留在 NetworkMonitoring 表中配置 NPM 的工作区中。 迁移后，数据将转到 NetworkMonitoring 表，并在同一工作区中 ConnectionMonitor_CL 表。 在 NPM 中禁用测试后，数据仅存储在 ConnectionMonitor_CL 表中。
   * **基于日志的警报、仪表板和集成**：您必须基于新的 ConnectionMonitor_CL 表手动编辑查询。 若要在指标中重新创建警报，请参阅 [利用连接监视器进行网络连接监视 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor)。
    
## <a name="prerequisites"></a>必备条件

* 确保在你的订阅中启用了网络观察程序以及 Log Analytics 工作区的区域。
* 必须通过网络观察程序扩展启用装有 Log Analytics 代理的 Azure 虚拟机。

## <a name="migrate-the-tests"></a>迁移测试

若要将测试从网络性能监视器迁移到连接监视器 (预览版) ，请执行以下操作：

1. 在 "网络观察程序" 中，选择 " **连接监视器**"，然后选择 " **从 NPM 迁移测试** " 选项卡。 

    ![显示网络观察程序中的 "从 NPM 迁移测试" 窗格的屏幕截图 |连接监视器 (预览) 。](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1. 在下拉列表中，选择订阅和工作区，然后选择要迁移的 NPM 功能。 目前只能从服务连接监视器迁移测试。  
1. 选择 " **导入** " 以迁移测试。

迁移开始后，将进行以下更改： 
* 这将创建一个新的连接监视器资源。
   * 每个区域和订阅创建一个连接监视器。 对于包含本地代理的测试，新连接监视器名称的格式为 `<workspaceName>_"on-premises"` 。 对于 Azure 代理测试，新连接监视器名称的格式为 `<workspaceName>_<Azure_region_name>` 。
   * 监视数据现在存储在启用了 NPM 的同一 Log Analytics 工作区中，在名为 Connectionmonitor_CL 的新表中。 
   * 测试名称将作为测试组名称来执行。 不迁移测试说明。
   * 在新的测试组中创建并使用源和目标终结点。 对于本地代理，终结点的格式设置为 `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` 。 对于 Azure，如果迁移测试包含未运行的代理，则需要启用代理并再次迁移。
   * 目标端口和探测间隔将移到名为*TC_ \<testname> *的测试配置和*TC_ \<testname> _AppThresholds*。 协议是基于端口值设置的。 成功阈值和其他可选属性将留空。
* NPM 未被禁用，因此，已迁移的测试可以继续将数据发送到 NetworkMonitoring 并 ConnectionMonitor_CL 表。 此方法可确保现有的基于日志的警报和集成不受影响。
* 在连接监视器 (预览) 中显示新创建的连接监视器。

迁移之后，请务必：
* 手动禁用 NPM 中的测试。 在执行此操作之前，你将继续为它们付费。 
* 禁用 NPM 时，请在 ConnectionMonitor_CL 表中重新创建警报或使用度量值。 
* 将任何外部集成迁移到 ConnectionMonitor_CL 表。 外部集成的示例包括 Power BI 和 Grafana 中的仪表板，以及与安全信息和事件管理 (SIEM) 系统的集成。


## <a name="next-steps"></a>后续步骤

若要详细了解连接监视器 (预览) ，请参阅：
* [从连接监视器迁移到连接监视器 (预览) ](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* [使用 Azure 门户创建连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
