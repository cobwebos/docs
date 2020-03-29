---
title: 将 Azure 活动数据连接到 Azure 哨兵 |微软文档
description: 了解如何将 Azure 活动数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124989"
---
# <a name="connect-data-from-azure-activity-log"></a>从 Azure 活动日志连接数据

只需单击一下，即可将[Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)流式传输到 Azure Sentinel 中。 活动日志是一个订阅日志，用于记录和显示 Azure 中的订阅级事件，从 Azure 资源管理器操作数据到服务运行状况事件更新。 使用"活动"日志，您可以确定对订阅中的资源执行的任何写入操作（PUT、POST、DELETE）的"什么、谁和时间"。 您还可以了解操作和其他相关属性的状态。 活动日志不包括使用经典/"RDFE"模型的资源的读取 （GET） 操作或操作。 

## <a name="prerequisites"></a>先决条件

- 您的用户必须具有日志分析工作区的"参与者"权限。
- 用户必须具有要流式传输到 Azure Sentinel 的任何订阅的读取器权限。

## <a name="set-up-the-azure-activity-connector"></a>设置 Azure 活动连接器

1. 在 Azure Sentinel 导航菜单中，选择 **"数据连接器**"。 从连接器列表中，单击 Azure**活动**，然后在右下角的 **"打开连接器"页面**按钮上单击。

2. 在 **"说明"** 选项卡下，单击"**配置 Azure 活动日志>** 链接。

3. 在**Azure 活动日志**窗格中，选择要流式传输到 Azure Sentinel 的日志的订阅。 

4. 在向右打开的订阅窗格中，单击"**连接**"。

5. 要在日志分析中使用 Azure 活动警报的相关架构，`AzureActivity`请键入查询窗口。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Azure 活动日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用[内置](tutorial-detect-threats-built-in.md)或[自定义](tutorial-detect-threats-custom.md)规则开始使用 Azure Sentinel 检测威胁。
