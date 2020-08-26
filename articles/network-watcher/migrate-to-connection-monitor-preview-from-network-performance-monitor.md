---
title: '从网络性能监视器迁移到连接监视器 (预览) '
titleSuffix: Azure Network Watcher
description: 了解如何从网络性能监视器迁移到连接监视器 (预览) 。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701468"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>从网络性能监视器迁移到连接监视器 (预览) 

你可以通过一次单击将测试网络性能监视器迁移到新的和改进的连接监视器 (预览) ，并且无需停机。 若要了解有关权益的详细信息，可以阅读 [连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> 只有服务连接监视器中的测试可以迁移到连接监视器 (预览) 。
>

## <a name="key-points-to-note"></a>需要注意的要点

* 本地代理和防火墙设置将按原样工作。 无需更改。 在 Azure 虚拟机上安装的 Log Analytics 代理需要替换为网络观察程序扩展
* 现有测试将映射到连接监视器 (预览) > 测试组 > 测试格式。 用户可以单击 " *编辑* " 来查看和修改新连接监视器的属性，并下载模板以更改连接监视器，并通过 Azure 资源管理器进行提交。
* 代理会将数据发送到 Log Analytics 工作区和指标。
* 监视数据
    * Log Analytics 中的数据–所有数据迁移将继续在 NetworkMonitoring 表中配置 NPM 的工作区中。 迁移后，数据将转到 NetworkMonitoring 表，并在同一工作区中 ConnectionMonitor_CL 表。 从 NPM 禁用测试后，数据将仅存储在 ConnectionMonitor_CL 表中
    * 基于日志的警报、仪表板和集成–您必须基于新的表 ConnectionMonitor_CL 手动编辑查询。 你还可以使用此链接在指标中重新创建警报。 即将推出在迁移过程中自动将基于 NetworkMonitoring 表的日志迁移到基于指标的警报的能力
    
## <a name="prerequisites"></a>先决条件

*   确保在 Log Analytics 工作区的订阅和区域中启用网络观察程序
*   已安装 Log analytics 代理的 Azure 虚拟机将需要通过网络观察程序扩展启用

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>将测试从网络性能监视器迁移到连接监视器 (预览版的步骤) 

1.  单击 "连接监视器"，导航到 "从 NPM 迁移测试"，将测试迁移到连接监视器 (预览) 

    ![显示将测试从 NPM 迁移到连接监视器预览的屏幕截图](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  选择要迁移的 "订阅"、"工作区" 和 "NPM" 功能。 目前只能从服务连接监视器迁移测试。  
1.  单击 "导入" 以迁移测试
1.  迁移开始后，将发生以下更改： 
    1. 已创建新的连接监视器资源
        1. 每个区域和订阅创建一个连接监视器。 对于包含本地代理的测试，新的连接监视器名称的格式为 <workspaceName> _"本地"。对于 Azure 代理测试，新的连接监视器名称格式 <workspaceName> _<Azure_region_name>
        1. 监视数据现在存储在启用了 NPM 的同一 Log Analytics 工作区中，在名为 Connectionmonitor_CL table 的新表中。 
        1. 测试名称将结转到测试组名称。 将不迁移测试说明。
        1. 源和目标终结点在创建的测试组中创建并使用。 对于本地代理，终结点的格式为 <workspaceName> _"终结点"_ <FQDN of on-premises machine> 。对于 Azure，如果迁移测试包含代理未在运行，你将需要启用代理并再次迁移。
        1. 目标端口和探测间隔将移到测试配置中，即 "TC"_ <testname> "和" tc "_ <testname> _" AppThresholds "。 根据端口值设置协议。 成功阈值和其他可选属性将留空。
    1. NPM 未禁用。 因此，迁移的测试会继续将数据发送到 NetworkMonitoring 表和 ConnectionMonitor_CL 表。 此步骤可确保现有的基于日志的警报和集成不受影响。 即将推出在迁移过程中自动将基于 NetworkMonitoring 表的基于日志的警报迁移到基于指标的警报。
    1. 新创建的连接监视器将在连接监视器 (预览中显示) 
1.  迁移后，需要手动禁用 NPM 中的测试。 在执行此操作之前，将继续为同一付费。 禁用 NPM 时，请确保在 ConnectionMonitor_CL 表或使用度量值时重新创建警报。 还要确保 Power BI、Grafana、与 SIEM 系统的集成中的仪表板等任何外部集成需要迁移到 ConnectionMonitor_CL 表


## <a name="next-steps"></a>后续步骤

* 了解 [如何从连接监视器迁移到连接监视器 (预览) ](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* 了解 [如何使用 Azure 门户创建连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
