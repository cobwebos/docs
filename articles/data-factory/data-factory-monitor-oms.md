---
title: 使用 OMS 监视 Azure 数据工厂 | Microsoft Docs
description: 了解如何通过将数据路由到 Operations Management Suite (OMS) 进行分析来监视 Azure 数据工厂。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303978"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>使用 Operations Management Suite (OMS) 监视 Azure 数据工厂

可以使用 Azure 数据工厂与 Azure Monitor 的集成将数据路由到 Operations Management Suite (OMS)。 此集成在以下情况下非常有用：

1.  你希望针对由数据工厂发布到 OMS 的丰富指标集编写复杂查询。 你还可以通过 OMS 创建针对这些查询的自定义警报。

2.  你希望跨数据工厂进行监视。 你可以将来自多个数据工厂的数据路由到单个 OMS 工作区。

## <a name="get-started"></a>入门

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>配置诊断设置和 OMS 工作区

为数据工厂启用诊断设置。

1.  选择“Azure Monitor” -> “诊断设置”-> 选择数据工厂 -> 启用诊断。

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  提供包括 OMS 工作区配置在内的诊断设置。

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>从 Azure 市场安装 Azure 数据工厂分析 OMS 包

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

单击“创建”并选择 OMS 工作区和 OMS 工作区设置。

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>使用 OMS 监视 Azure 数据工厂指标

安装 **Azure 数据工厂分析** OMS 包将创建一组默认视图，这些视图将启用以下指标：

- ADF 运行- 1) 数据工厂执行的管道运行

- ADF 运行- 2) 数据工厂执行的活动运行

- ADF 运行- 3) 数据工厂执行的触发器运行

- ADF 错误-1) 数据工厂引发的排名前 10 的管道错误

- ADF 错误- 2) 数据工厂执行的排名前 10 的活动运行

- ADF 错误- 3) 数据工厂引发的排名前 10 的触发器错误

- ADF 统计信息- 1) 按类型显示的活动运行

- ADF 统计信息- 2) 按类型显示的触发器运行

- ADF 统计信息- 3) 最大管道运行持续时间

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

你可以将上述指标可视化，查看这些指标背后的查询，编辑查询，创建警报，等等。

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>后续步骤

参阅[以编程方式监视和管理管道](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically)一文，了解如何通过运行脚本来监视和管理管道。
