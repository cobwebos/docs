---
title: 为 Azure 流分析作业实现异地冗余
description: 本文介绍了如何实现 Azure 流分析作业的异地冗余，而不是进行异地故障转移。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 44b0394773485f8054eddc94c7d7e9b91baebc6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88951088"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>为 Azure 流分析作业实现异地冗余

Azure 流分析不提供自动异地故障转移，但你可以通过在多个 Azure 区域中部署相同的流分析作业来实现异地冗余。 每个作业都连接到本地输入和本地输出源。 您的应用程序负责将输入数据发送到两个区域输入并在两个区域输出之间进行协调。 流分析作业是两个不同的实体。

下图描绘了使用事件中心输入和 Azure 数据库输出进行异地冗余流分析作业部署的示例。

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="异地冗余流分析作业示意图":::

## <a name="primarysecondary-strategy"></a>主要/辅助策略

您的应用程序需要管理将哪个区域的输出数据库视为主要数据库，并将该数据库视为辅助数据库。 在主要区域发生故障时，应用程序将切换到辅助数据库，并开始从该数据库读取更新。 允许最小化重复读取的实际机制取决于应用程序。可以通过将其他信息写入输出来简化此过程。 例如，可以将时间戳或序列 ID 添加到每个输出，以使重复行跳到一个简单的操作。 在主要区域恢复后，它将使用类似的机制与辅助数据库捕获。

尽管不同的输入和输出类型允许不同的异地复制选项，我们仍建议使用本文中介绍的模式来实现异地冗余，因为它为事件创建者和事件使用者提供灵活性和控制。

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 监视和管理 Azure 流分析作业](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Azure 流分析中的数据驱动调试](stream-analytics-job-diagram-with-metrics.md)