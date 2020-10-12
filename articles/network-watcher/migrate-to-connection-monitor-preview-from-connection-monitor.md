---
title: '从连接监视器迁移到连接监视器 (预览) '
titleSuffix: Azure Network Watcher
description: 了解如何从连接监视器迁移到连接监视器 (预览) 。
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
ms.openlocfilehash: 05b42024529b8d9de3590488ecafbdf83283e007
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441810"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>从连接监视器迁移到连接监视器 (预览) 

你可以将现有连接监视器迁移到新的、经过改进的连接监视器 (预览) 只需单击几下鼠标，无需停机。 若要了解有关这些优势的详细信息，请参阅 [连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)。

## <a name="key-points-to-note"></a>需要注意的要点

迁移有助于生成以下结果：

* 代理和防火墙设置按原样工作。 不需要进行任何更改。 
* 现有连接监视器映射到连接监视器 (预览) > 测试组 > 测试格式。 通过选择 " **编辑**"，可以查看和修改新连接监视器的属性，下载模板以更改连接监视器，并通过 Azure 资源管理器进行提交。 
* 具有网络观察程序扩展的 Azure 虚拟机将数据发送到工作区和指标。 连接监视器使数据可通过新指标 (ChecksFailedPercent [预览版] 和 RoundTripTimeMs [Preview] ) ，而不是旧指标 (ProbesFailedPercent 和 AverageRoundtripMs) 。 
* 数据监视：
   * **警报**：自动迁移到新的度量值。
   * **仪表板和集成**：需要手动编辑度量值集。 
    
## <a name="prerequisites"></a>必备条件

如果使用的是自定义工作区，请确保在订阅和 Log Analytics 工作区的区域中启用了网络观察程序。 

## <a name="migrate-the-connection-monitors"></a>迁移连接监视器

1. 若要将较旧的连接监视器迁移到较新的监视器，请选择 " **连接监视器**"，然后选择 " **迁移连接监视器**"。

    ![显示将连接监视器迁移到连接监视器 (预览) 的屏幕截图。](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 选择要迁移的订阅和连接监视器，然后选择 " **迁移所选**"。 

只需单击几下，即可将现有连接监视器迁移到连接监视器 (预览) 。 

你现在可以自定义连接监视器 (预览) 属性、更改默认工作区、下载模板以及检查迁移状态。 

迁移开始后，将进行以下更改： 
* Azure 资源管理器资源会更改为较新的连接监视器。
    * 连接监视器的名称、区域和订阅保持不变。 资源 ID 不受影响。
    * 除非自定义连接监视器，否则将在订阅和连接监视器的区域中创建默认 Log Analytics 工作区。 此工作区是监视数据的存储位置。 测试结果数据也存储在指标中。
    * 每个测试都迁移到名为 *defaultTestGroup*的测试组。
    * 在新的测试组中创建并使用源和目标终结点。 默认名称为 " *defaultSourceEndpoint* " 和 " *defaultDestinationEndpoint*"。
    * 目标端口和探测间隔将移动到名为 *defaultTestConfiguration*的测试配置。 协议是基于端口值设置的。 成功阈值和其他可选属性将留空。
* 指标警报会迁移到连接监视器 (预览) 指标警报。 度量值不同，因此进行了更改。 有关详细信息，请参阅 [具有连接监视器的网络连接监视 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor)。
* 已迁移的连接监视器不再作为较旧的连接监视器解决方案显示。 它们现在仅可用于连接监视器 (预览) 。
* 任何外部集成（如 Power BI 和 Grafana 中的仪表板，以及与安全信息和事件管理 (SIEM) 系统的集成）都必须手动迁移。 这是迁移安装程序所需执行的唯一手动步骤。

## <a name="next-steps"></a>后续步骤

若要详细了解连接监视器 (预览) ，请参阅：
* [从网络性能监视器迁移到连接监视器 (预览版) ](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* [使用 Azure 门户创建连接监视器 (预览) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
