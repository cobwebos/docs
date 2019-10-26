---
title: 使用搜寻书签在 Azure Sentinel 中进行搜寻时跟踪数据 |Microsoft Docs
description: 本文介绍如何使用 Azure Sentinel 搜寻书签跟踪数据。
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: cabailey
ms.openlocfilehash: f4714dd09ada01f1adaa9081819e836601599a53
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935267"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>在通过 Azure Sentinel 进行搜寻期间跟踪数据

威胁搜寻通常需要检查山上的日志数据，查找恶意行为的证据。 在此过程中，调查人员查找要记住、重新访问和分析的事件，并将其作为验证潜在假设的一部分，并了解整个安全漏洞。

Azure Sentinel 中的搜寻书签可帮助你执行此操作，方法是保留你在**Azure Sentinel 日志**中运行的查询，以及你认为相关的查询结果。 你还可以通过添加注释和标记来记录上下文观察并引用你的发现。 带有书签的数据对你和你的团队成员可以轻松协作。

你可以随时在 "**搜寻**" 窗格的 "**书签**" 选项卡上重新查看书签数据。 你可以使用筛选和搜索选项快速查找你的当前调查的特定数据。 或者，您可以直接在 Log Analytics 工作区中的**HuntingBookmark**表中查看书签数据。 例如：

> [!div class="mx-imgBorder"]
> ![查看 HuntingBookmark 表](./media/bookmarks/bookmark-table.png)

通过从表中查看书签，可以筛选、汇总和联接带有书签的数据和其他数据源，从而可以轻松查找 corroborating 的证据。

目前在预览版中，如果你发现在你的日志中搜索时需要解决的问题，只需单击几下鼠标，即可创建一个书签并将其提升为事件，或将书签添加到现有事件。 有关事件的详细信息，请参阅[教程：通过 Azure Sentinel 调查事件](tutorial-investigate-cases.md)。 

此外，还可以通过单击书签详细信息中的 "**调查**"，直观显示书签化的数据。 这会启动调查体验，你可以使用交互式实体-图形关系图和时间线来查看、调查和直观地传达你的发现。

## <a name="add-a-bookmark"></a>添加书签

1. 在 Azure 门户中，导航到 " **Sentinel** > **威胁管理**" > **搜寻**，以运行可疑和异常行为的查询。

2. 选择一个搜寻查询，然后在右侧的 "搜寻查询详细信息" 中，选择 "**运行查询**"。 

3. 选择 "**查看查询结果**"。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![查看 Azure Sentinel 搜寻](./media/bookmarks/new-processes-observed-example.png) 的查询结果
    
    此操作将打开 "**日志**" 窗格中的查询结果。

4. 从 "日志查询结果" 列表中，使用复选框来选择包含你感兴趣的信息的一个或多个行。

5. 选择 "**添加书签**"：
    
    > [!div class="mx-imgBorder"]
    > ![将搜寻书签添加到查询](./media/bookmarks/add-hunting-bookmark.png)

6. 在右侧的 "**添加书签**" 窗格中，可以选择更新书签名称、添加标记和注释，以帮助你确定对该项感兴趣的内容。

7. 在 "**查询信息**" 部分中，使用下拉框从 "**帐户**"、"**主机**" 和 " **IP 地址**" 实体类型的查询结果中提取信息。 此操作会将所选的实体类型映射到查询结果中的特定列。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![用于搜寻书签的实体类型](./media/bookmarks/map-entity-types-bookmark.png)
    
    若要在调查图中查看书签（当前为预览版），必须映射至少一种实体类型，即**帐户**、**主机**或**IP 地址**。 

5. 单击 "**保存**" 提交更改并添加书签。 所有带书签的数据都与其他调查人员共享，这是协作调查体验的第一步。

 
> [!NOTE]
> 每次从 Azure Sentinel 打开此窗格时，日志查询结果都支持书签。 例如，从导航栏中选择 "**常规** > **日志**"，在调查关系图中选择 "事件链接"，或从事件的完整详细信息（当前为预览版）中选择警报 ID。 从其他位置（如直接从 Azure Monitor）打开 "**日志**" 窗格时，不能创建书签。

## <a name="view-and-update-bookmarks"></a>查看和更新书签 

1. 在 Azure 门户中，导航到 " **Sentinel** > **威胁管理**" > **搜寻**。 

2. 选择 "**书签**" 选项卡以查看书签列表。

3. 若要帮助你查找特定书签，请使用搜索框或筛选选项。

4. 选择各个书签，并在右侧的详细信息窗格中查看书签详细信息。

5. 根据需要进行更改，这些更改会自动保存。

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>在调查图中浏览书签

> [!IMPORTANT]
> 在调查图中浏览书签和调查图本身目前为公共预览版。
> 提供这些功能没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 门户中，导航到 " **Sentinel** > **威胁管理**" > **搜寻** > **书签**"选项卡，然后选择要调查的书签。

2. 在 "书签详细信息" 中，确保至少映射一个实体。 例如，对于**实体**，你将看到**IP**、**计算机**或**帐户**的条目。

3. 单击 "**调查**" 可在调查图中查看书签。

有关使用调查关系图的说明，请参阅[使用调查图深入探讨](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)。

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>将书签添加到新的或现有的事件

> [!IMPORTANT]
> 将书签添加到新的或现有的事件当前在公共预览版中。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 门户中，导航到 " **Sentinel** > **威胁管理**" > **搜寻** > **书签**"选项卡，然后选择要添加到事件中的一个或哪些书签。

2. 从命令栏中选择 "**事件操作（预览版）** "：
    
    > [!div class="mx-imgBorder"]
    > ![将书签添加到事件](./media/bookmarks/incident-actions.png)

3. 根据需要，选择 "**创建新事件**" 或 "**添加到现有事件**"。 然后：
    
    - 对于新事件：可以选择更新事件的详细信息，然后选择 "**创建**"。
    - 若要将书签添加到现有事件：选择一个事件，然后选择 "**添加**"。 

查看事件中的书签：导航到**Sentinel** > **威胁管理** > **事件**，并选择带有书签的事件。 选择 "**查看完整详细信息**"，然后选择 "**书签**" 选项卡。

> [!TIP]
> 作为命令栏上 "**事件操作（预览）** " 选项的替代方法，你可以使用一个或多个书签的上下文菜单（ **...** ）来选择**创建新事件**、**添加到现有事件**以及**从中删除事件**。 

## <a name="view-bookmarked-data-in-logs"></a>查看日志中的带书签的数据

若要查看带有书签的查询、结果或其历史记录，请从 "**搜寻** > **书签**" 选项卡中选择书签，并使用细节窗格中提供的链接： 

- **查看源查询**，以在 "**日志**" 窗格中查看源查询。

- **查看书签日志**以查看所有书签元数据，其中包括执行更新的人员、更新的值以及更新发生的时间。

还可以**通过在 "查找 > ** **书签**" 选项卡上的命令栏中选择 "**书签日志**"，查看所有书签的原始书签数据：

> [!div class="mx-imgBorder"]
> ![书签日志](./media/bookmarks/bookmark-logs.png)

此视图显示具有关联元数据的所有书签。 你可以使用[关键字查询语言](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference)（KQL）查询来筛选到你要查找的特定书签的最新版本。

> [!NOTE]
> 创建书签的时间与在 "**书签**" 选项卡中显示的时间之间可能存在很长的延迟（以分钟为单位）。

## <a name="delete-a-bookmark"></a>删除书签
 
1.  在 Azure 门户中，导航到 " **Sentinel** > **威胁管理**" > **搜寻** > **书签**"选项卡，然后选择要删除的一个或哪些书签。 

2. 右键单击您的选择，然后选择删除书签或书签的选项。 例如，如果选择了 "仅一个书签"，则**删除书签**; 如果选择了两个书签，则**删除2个书签**。
    
删除书签会将书签从 "**书签**" 选项卡的列表中删除。Log Analytics 工作区的 " **HuntingBookmark** " 表将继续包含以前的书签条目，但最新条目会将 " **SoftDelete** " 值更改为 "true"，从而轻松筛选出旧书签。 删除书签不会删除与其他书签或警报关联的调查体验中的任何实体。 


## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Sentinel 中使用书签运行搜寻调查。 要详细了解 Azure Sentinel，请参阅以下文章：


- [主动寻找威胁](hunting.md)
- [使用笔记本运行自动搜寻活动](notebooks.md)
