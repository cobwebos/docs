---
title: 使用 Azure Sentinel 中的搜寻 Livestream 检测威胁 |Microsoft Docs
description: 本文介绍如何使用 Azure Sentinel 中的搜寻 Livestream 跟踪数据。
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582120"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>使用 Azure Sentinel 中的搜寻 livestream 检测威胁

> [!IMPORTANT]
> Azure Sentinel 中的搜寻 livestream 目前为公共预览版，并逐渐推出到租户。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


使用搜索 livestream 创建交互式会话，使您可以在事件发生时测试新创建的查询，在找到匹配项时从会话中获取通知，并根据需要启动调查。 可以使用任何 Log Analytics 查询快速创建 livestream 会话。

- **在事件发生时测试新创建的查询**
    
    您可以测试和调整查询，而不会对当前应用于事件的当前规则进行任何冲突。 确认这些新查询按预期工作后，可以通过选择一个将会话提升到警报的选项来轻松地将其升级到自定义警报规则。

- **出现威胁时接收通知**
    
    您可以将威胁数据馈送与聚合日志数据进行比较，并在发生匹配时收到通知。 威胁数据馈送是正在进行的数据流，这些数据与潜在或当前的威胁相关，因此通知可能表明组织存在潜在威胁。 如果希望在没有维护自定义警报规则的开销的情况下收到潜在问题的通知，请创建 livestream 会话而不是自定义警报规则。

- **启动调查**
    
    如果有活动的调查涉及到某个资产（如主机或用户），则可以在日志数据中查看它在该资产上发生的特定（或任何）活动。 当活动发生时，可以通知你。


## <a name="create-a-livestream-session"></a>创建 livestream 会话

你可以从现有的搜寻查询创建 livestream 会话，或者从头开始创建你的会话。

1. 在 Azure 门户中，导航到 " **Sentinel** > **威胁管理**" > **搜寻**。

2. 通过搜寻查询创建 livestream 会话：
    
    1. 从 "**查询**" 选项卡中，找到要使用的搜寻查询。
    2. 右键单击该查询，然后选择 "**添加到 livestream**"。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure Sentinel 搜寻查询创建 Livestream 会话](./media/livestream/livestream-from-query.png)

3. 若要从头开始创建 livestream 会话： 
    
    1. 选择**Livestream**选项卡
    2. 选择 "**中转到 livestream**"。
    
4. 在 " **Livestream** " 窗格中：
    
    - 如果从查询启动了 livestream，请查看查询，并进行所需的任何更改。
    - 如果从头开始 livestream，请创建查询。 

5. 从命令栏中选择 "**播放**"。
    
    命令栏下的状态栏指示 livestream 会话是正在运行还是已暂停。 在下面的示例中，该会话正在运行：
    
    > [!div class="mx-imgBorder"]
    > 从 Azure Sentinel 搜寻 ![创建 livestream 会话](./media/livestream/livestream-session.png)

6. 从命令栏中选择 "**保存**"。
    
    除非你选择 "**暂停**"，否则该会话将继续运行，直到你从 Azure 门户中注销为止。

## <a name="view-your-livestream-sessions"></a>查看你的 livestream 会话

1. 在 Azure 门户中，导航到 " **Sentinel** > **威胁管理**" > **搜寻** > **Livestream** "选项卡。

2. 选择要查看或编辑的 livestream 会话。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure Sentinel 搜寻查询创建 livestream 会话](./media/livestream/livestream-tab.png)
    
    此时会打开所选的 livestream 会话，以便您播放、暂停、编辑等。

## <a name="receive-notifications-when-new-events-occur"></a>出现新事件时接收通知

由于新事件的 livestream 通知使用 Azure 门户通知，因此当你使用 Azure 门户时，你会看到这些通知。 例如：

![Livestream 的 Azure 门户通知](./media/livestream/notification.png)

选择通知以打开 " **Livestream** " 窗格。
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>将 livestream 会话提升为警报

可以通过从相关 livestream 会话上的命令栏中选择 "**提升到警报**"，将 livestream 会话升级为新警报：

> [!div class="mx-imgBorder"]
> ![将 livestream 会话提升为警报](./media/livestream/elevate-to-alert.png)

此操作将打开规则创建向导，其中预填充了与 livestream 会话关联的查询。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure livestream 中使用搜寻。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动寻找威胁](hunting.md)
- [使用笔记本运行自动搜寻活动](notebooks.md)
