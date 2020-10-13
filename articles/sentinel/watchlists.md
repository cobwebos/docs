---
title: 使用 Azure Sentinel watchlists
description: 本文介绍如何使用 Azure Sentinel watchlists 调查威胁、导入业务数据、创建允许列表以及丰富事件数据。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 25252b73f25a96f85d5e2cf1d68b76f9eaa3ca75
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979468"
---
# <a name="use-azure-sentinel-watchlists"></a>使用 Azure Sentinel watchlists

Azure Sentinel watchlists 允许从外部数据源收集数据，以便与 Azure Sentinel 环境中的事件相关。 创建后，可以在搜索、检测规则、威胁搜寻和响应行动手册中使用 watchlists。 Watchlists 以名称-值对的形式存储在 Azure Sentinel 工作区中，并缓存以获得最佳查询性能和低延迟。

使用 watchlists 的常见方案包括：

- 通过快速导入 IP 地址、文件哈希和 CSV 文件中的其他数据，**调查威胁**并快速响应事件。 导入后，可以在警报规则、威胁搜寻、工作簿、笔记本和常规查询中使用联接和筛选器的播放列表名称/值对。

- 以 watchlists 的形式**导入业务数据**。 例如，导入具有特权系统访问权限或终止员工的用户列表，然后使用播放列表创建用于检测或阻止这些用户登录到网络的允许和拒绝列表。

- **降低警报疲劳**。 "创建允许列表" 可禁止来自一组用户的警报，如已授权 IP 地址的用户，这些用户执行通常会触发警报的任务，并防止良性事件成为警报。

- **浓缩事件数据**。 使用 watchlists 可以通过派生自外部数据源的名称-值组合来丰富事件数据。

## <a name="create-a-new-watchlist"></a>创建新的播放列表

1. 在 Azure 门户中，导航到 " **Azure Sentinel**  >  **配置**  >  **播放列表**"，然后选择 "**添加**" "新建"。

    > [!div class="mx-imgBorder"]
    > ![新播放列表](./media/watchlists/sentinel-watchlist-new.png)

1. 在 " **常规** " 页上，提供播放列表的 "名称"、"说明" 和 "别名"，然后选择 " **下一步**"。

    > [!div class="mx-imgBorder"]
    > ![播放列表常规页](./media/watchlists/sentinel-watchlist-general.png)

1. 在 " **源** " 页上，选择数据集类型，上传文件，然后选择 " **下一步**"。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="播放列表源页面" lightbox="./media/watchlists/sentinel-watchlist-source.png":::


1. 查看信息，验证它是否正确，然后选择 " **创建**"。

    > [!div class="mx-imgBorder"]
    > ![播放列表检查页](./media/watchlists/sentinel-watchlist-review.png)

    创建播放列表后，将显示一条通知。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="播放列表源页面" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="use-watchlists-in-queries"></a>在查询中使用 watchlists

1. 在 Azure 门户中，导航到 " **Azure Sentinel**  >  **配置**  >  **播放列表**"，选择要使用的播放列表，然后选择 "**在 Log Analytics 中查看**"。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="播放列表源页面" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::


1. 播放列表中的项目会自动提取，并显示在 " **结果** " 选项卡上。以下示例显示了 " **服务器名称** " 和 " **ip 地址** " 字段的提取结果。

    > [!NOTE]
    > 查询的时间戳将在查询 UI 和计划的警报中被忽略。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="播放列表源页面" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
## <a name="use-watchlists-in-analytics-rules"></a>在分析规则中使用 watchlists

若要在分析规则中使用 watchlists，请在 Azure 门户中导航到**Azure Sentinel**  >  **Configuration**  >  **analytics**，并使用 `_GetWatchlist('<watchlist>')` 查询中的函数创建规则。

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="播放列表源页面" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::


## <a name="view-list-of-watchlists-aliases"></a>查看 watchlists 别名列表

若要获取播放列表别名列表，请在 Azure 门户中导航到 " **Azure Sentinel**  >  **一般**  >  **日志**"，并运行以下查询： `_GetWatchlistAlias` 。 可以在 " **结果** " 选项卡中查看别名列表。

> [!div class="mx-imgBorder"]
> ![列出 watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>后续步骤
本文档介绍了如何使用 Azure Sentinel 中的 watchlists 来丰富数据并改进调查。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

