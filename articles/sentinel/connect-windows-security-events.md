---
title: 将 Windows 安全事件数据连接到 Azure Sentinel 预览版 |Microsoft Docs
description: 了解如何连接到 Azure Sentinel Windows 安全事件数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 188febf090ddb3f685f9d3c3b94d822f15bbcfcb
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673768"
---
# <a name="connect-windows-security-events"></a>连接 Windows 安全事件 

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以对来自连接到 Azure Sentinel 工作区的 Windows 服务器的所有安全事件进行流式都传输。 此连接可以查看仪表板、 创建自定义警报和改进的调查。 这为您提供了更详细地了解您组织的网络，并提高你的安全操作功能。  您可以选择哪些事件进行流式处理：

- **所有事件**-所有 Windows 安全性和 AppLocker 事件。
- **常见**-一组标准的审核的事件。 完整用户审核记录包含在此集中。 例如，此集包含用户的登录和用户注销事件 （事件 ID 4634）。 我们加入审核操作，如安全组更改、关键域控制器 Kerberos 操作以及行业组织建议的其他事件。

数量非常少的事件包含在通用集中，因为在所有事件中选择该集的主要动机是为了减少数量，而不是筛选出特定事件。
- **最小**-少量的事件可能表示潜在的威胁。 通过启用此选项，你将无法具有完整的审核跟踪。  此集涵盖可能指示成功违反的事件以及数量很少的重要事件。 例如，此集包含用户成功和失败的登录 （事件 Id 4624 和 4625），但它不包含注销这是重要的审核但未对检测毫无意义，相对较多的信息。 大部分此集的数据量是登录事件和进程创建事件 （事件 ID 4688）。
- **无**-没有安全性或 AppLocker 事件。

> [!NOTE]
> 
> - 数据将存储在其运行 Azure Sentinel 的工作区的地理位置。

以下列表提供的完整分类的安全和 App Locker 事件 Id 为每个集：

| 数据层 | 收集的事件指示器 |
| --- | --- |
| 轻微 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 常见 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>设置 Windows 安全事件连接器

若要完全将与 Azure Sentinel 集成在 Windows 安全事件：

1. 在 Azure Sentinel 门户中，选择**数据连接器**，然后单击**Windows 安全事件**磁贴。 
1. 选择你想要流式传输的数据类型。
1. 单击“更新”  。
6. 若要使用 Log Analytics 中的 Windows 安全事件相关的架构，搜索**SecurityEvent**。

## <a name="validate-connectivity"></a>验证连接

可能需要约 20 分钟，直到你的日志开始在 Log Analytics 中显示。 



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Windows 安全事件连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。

