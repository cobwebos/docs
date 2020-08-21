---
title: '从连接监视器迁移到连接监视器 (预览) '
titleSuffix: Azure Network Watcher
description: 了解如何从连接监视器迁移到连接监视器 (预览) 。
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
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701471"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>从连接监视器迁移到连接监视器 (预览) 

你可以通过一次单击和零停机时间将现有连接监视器迁移到新的和改进的连接监视器 (预览) 。 若要了解有关权益的详细信息，可以阅读 [连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>需要注意的要点

* 代理和防火墙设置将按原样工作， (不需要任何触控)  
* 现有连接监视器将映射到连接监视器 (预览) > 测试组 > 测试格式。 用户可以单击 " *编辑* " 来查看和修改新连接监视器的属性，并下载模板以更改连接监视器，并通过 Azure 资源管理器进行提交。 
* 具有网络观察程序扩展的 Azure 虚拟机将数据发送到工作区和指标。 连接监视器将通过新度量值提供数据 (ChecksFailedPercent (Preview) 和 RoundTripTimeMs (预览) # A5，而不是停止旧度量值 (ProbesFailedPercent 和 AverageRoundtripMs)  
* 监视数据
    * 警报–将作为迁移的一部分迁移到新度量值
    * 仪表板和集成–必须手动编辑度量值集。 
    
## <a name="prerequisites"></a>先决条件

如果使用自定义工作区，请确保在 Log Analytics 工作区的订阅和区域中启用网络观察程序 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>从连接监视器迁移到连接监视器 (预览的步骤) 

1. 单击 "连接监视器"，导航到 "迁移连接监视器"，将连接监视器从较旧的解决方案迁移到较新的解决方案。

    ![显示将连接监视器迁移到连接监视器预览的屏幕截图](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 选择 "订阅和连接监视器"，然后单击 "迁移所选"。 单击 "将现有连接监视器迁移到连接监视器" (预览)  
1. 你可以自定义连接监视器属性、更改默认工作区、下载模板以及检查迁移状态。 
1. 迁移开始后，将发生以下更改： 
    1. Azure 资源管理器对较新连接监视器的资源更改
        1. 连接监视器的名称、区域和订阅保持不变。 因此，不会对资源 ID 产生任何影响。
        1. 除非自定义，否则将在连接监视器的区域和订阅中创建默认 Log Analytics 工作区。 此工作区是监视数据的存储位置。 测试结果数据也将存储在指标中。
        1. 每个测试都迁移到名为 * defaultTestGroup * 的测试组中
        1.  源和目标终结点在创建的测试组中创建并使用。 默认名称为 *defaultSourceEndpoint* 和 *defaultDestinationEndpoint*
        1. 目标端口和探测间隔将移动到名为 *defaultTestConfiguration*的测试配置。 根据端口值设置协议。 成功阈值和其他可选属性将留空。
    1. 指标警报会迁移到连接监视器 (预览) 指标警报。 度量值不同 <link to metric section in the doc> ，因此更改
    1. 已迁移的连接监视器不会显示在较旧的连接监视器解决方案中，它们现在仅可用于连接监视器 (预览) 
    1. 任何外部集成（如 Power BI 中的仪表板、Grafana、与 SIEM 系统的集成）都需要由用户直接迁移。 这是用户迁移其设置所需执行的唯一手动步骤。

## <a name="next-steps"></a>后续步骤

* 了解 [如何 (预览版从网络性能监视器迁移到连接监视器) ](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* 了解 [如何使用 Azure 门户创建连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
