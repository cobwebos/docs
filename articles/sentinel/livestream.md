---
title: 在 Azure 哨兵中使用搜索实时流来检测威胁*微软文档
description: 本文介绍如何在 Azure Sentinel 中使用寻线实时流来跟踪数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582120"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>在 Azure 哨兵中使用搜索实时流来检测威胁

> [!IMPORTANT]
> Azure Sentinel 中的寻线直播当前处于公共预览阶段，并逐步向租户推出。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


使用搜索直播创建交互式会话，这些会话允许您在事件发生时测试新创建的查询，在找到匹配时从会话中获取通知，并在必要时启动调查。 您可以使用任何日志分析查询快速创建实时流会话。

- **在事件发生时测试新创建的查询**
    
    您可以测试和调整查询，而不会与正在主动应用于事件的当前规则发生冲突。 确认这些新查询按预期工作后，通过选择将会话提升为警报的选项，可以轻松地将它们提升为自定义警报规则。

- **在威胁发生时收到通知**
    
    您可以将威胁数据馈送与聚合日志数据进行比较，并在发生匹配时收到通知。 威胁数据馈送是持续的数据流，与潜在或当前威胁相关，因此通知可能表示对您的组织的潜在威胁。 当您希望收到潜在问题通知而不保留自定义警报规则的开销时，请创建实时流会话，而不是自定义警报规则。

- **启动调查**
    
    如果存在涉及资产（如主机或用户）的活动调查，则可以在日志数据中查看该资产上的特定（或任何）活动。 活动发生时，可以通知您。


## <a name="create-a-livestream-session"></a>创建实时流会话

可以从现有寻线查询创建实时流会话，也可以从头开始创建会话。

1. 在 Azure 门户中，导航到**哨兵** > **威胁管理** > **搜索**。

2. 要从寻线查询创建实时流会话，请：
    
    1. 从 **"查询"** 选项卡中，找到要使用的狩猎查询。
    2. 右键单击查询并选择"**添加到实时流**"。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure 哨兵搜索查询创建实时流会话](./media/livestream/livestream-from-query.png)

3. 要从头开始创建实时流会话，请： 
    
    1. 选择 **"实时流**"选项卡
    2. 选择**转到直播**。
    
4. 在 **"实时流"** 窗格中：
    
    - 如果从查询开始实时流，请查看查询并进行任何更改。
    - 如果从头开始实时流，请创建查询。 

5. 从命令栏**中选择"播放**"。
    
    命令栏下的状态栏指示直播会话是正在运行的还是暂停的。 在下面的示例中，会话正在运行：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure 哨兵搜索创建实时流会话](./media/livestream/livestream-session.png)

6. 从命令栏中选择 **"保存**"。
    
    除非选择 **"暂停**"，否则会话将继续运行，直到从 Azure 门户注销。

## <a name="view-your-livestream-sessions"></a>查看直播会话

1. 在 Azure 门户中，导航到**哨兵** > **威胁管理** > **搜索** > **实时流**选项卡。

2. 选择要查看或编辑的实时流会话。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure 哨兵搜索查询创建实时流会话](./media/livestream/livestream-tab.png)
    
    您选定的直播会话将打开，以便您播放、暂停、编辑等。

## <a name="receive-notifications-when-new-events-occur"></a>发生新事件时接收通知

由于新事件的实时流通知使用 Azure 门户通知，因此每当使用 Azure 门户时，都会看到这些通知。 例如：

![实时流的 Azure 门户通知](./media/livestream/notification.png)

选择通知以打开 **"实时流"** 窗格。
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>将实时流会话提升为警报

您可以通过选择 **"提升"以**从相关直播会话上的命令栏发出警报来将直播会话提升到新警报：

> [!div class="mx-imgBorder"]
> ![将实时流会话提升为警报](./media/livestream/elevate-to-alert.png)

此操作将打开规则创建向导，该向导预填充了与实时流会话关联的查询。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何在 Azure Sentinel 中使用狩猎直播。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动寻找威胁](hunting.md)
- [使用笔记本运行自动狩猎活动](notebooks.md)
