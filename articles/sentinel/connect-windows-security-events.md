---
title: 将 Windows 安全事件数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Windows 安全事件数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 70190eeb3adec239d1e52f51afcd173497d08e6a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588002"
---
# <a name="connect-windows-security-events"></a>连接 Windows 安全事件 



可以从连接到 Azure Sentinel 工作区的 Windows 服务器流式传输所有安全事件。 此连接使你可以查看仪表板、创建自定义警报和改进调查。 这样，你就可以更深入地了解组织的网络并改善安全操作功能。  你可以选择要流式传输的事件：

- **所有事件**-所有 Windows 安全性和 AppLocker 事件。
- 用于**审核的标准**事件集。 此集中包含完整的用户审核记录。 例如，此集包含用户登录和用户注销事件（事件 ID 4634）。 我们加入审核操作，如安全组更改、关键域控制器 Kerberos 操作以及行业组织建议的其他事件。

数量非常少的事件包含在通用集中，因为在所有事件中选择该集的主要动机是为了减少数量，而不是筛选出特定事件。
- **最小**-一小部分事件，可能表明存在潜在威胁。 启用此选项后，您将无法拥有完整的审核跟踪。  此集仅包含可能指示成功入侵的事件和包含非常少的重要事件。 例如，此集包含用户成功和失败的登录（事件 Id 4624、4625），但它不包含注销信息，这对于审核非常重要，但对于检测并不有意义，它的数量相对较高。 此集的大部分数据量是 "登录事件" 和 "进程创建" 事件（事件 ID 4688）。
- **无**-无安全性或 AppLocker 事件。

> [!NOTE]
> 
> - 数据将存储在运行 Azure Sentinel 的工作区的地理位置。
> - 如果 Azure 安全中心和 Azure Sentinel 在同一工作区上运行，则只能从 Azure 安全中心或 Azure Sentinel 连接安全事件连接器。 若要从 Azure Sentinel 管理这些事件，建议你将其从 Azure 安全中心断开连接并仅连接到 Azure Sentinel。


以下列表提供了每个组的安全和应用锁定程序事件 Id 的完整细目：

| 数据层 | 收集的事件指示器 |
| --- | --- |
| 轻微 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 通用 | 1、299、300、324、340、403、404、410、411、412、413、431、500、501、1100、1102、1107、1108、4608、4610、4611、4614、4622、 |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>设置 Windows 安全事件连接器

若要将 Windows 安全事件与 Azure Sentinel 完全集成：

1. 在 Azure Sentinel 门户中，选择 "**数据连接器**"，然后单击 "**安全事件**" 磁贴。 
1. 选择要流式传输的数据类型。
1. 单击“更新”。
6. 若要在 Windows 安全事件 Log Analytics 中使用相关架构，请搜索**SecurityEvent**。

## <a name="validate-connectivity"></a>验证连接

在 Log Analytics 中开始显示日志之前，可能需要大约20分钟的时间。 



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Windows 安全事件连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

