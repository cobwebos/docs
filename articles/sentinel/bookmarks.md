---
title: 在 Azure 哨兵中使用狩猎书签进行数据调查
description: 本文介绍如何使用 Azure Sentinel 搜索书签来跟踪数据。
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588682"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>使用 Azure 哨兵在搜索过程中跟踪数据

威胁寻猎通常需要查看大量日志数据，以查找恶意行为的证据。 在此过程中，调查人员会发现他们想要记住、重新审视和分析的事件，以验证潜在的假设并了解妥协的全部故事。

Azure Sentinel 中的搜索书签通过保留在**Azure Sentinel - Logs**中运行的查询以及您认为相关的查询结果来帮助您做到这一点。 您还可以通过添加注释和标记来记录上下文观测并引用您的发现。 您和您的团队成员可以看到书签数据，便于协作。

您可以随时在 **"搜索**"窗格的 **"书签**"选项卡上重新访问书签数据。 您可以使用筛选和搜索选项快速查找当前调查的特定数据。 或者，您也可以直接在日志分析工作区中的**HuntingBook 标记**表中查看书签数据。 例如：

> [!div class="mx-imgBorder"]
> ![查看亨廷顿Book标志表](./media/bookmarks/bookmark-table.png)

通过查看表中的书签，您可以筛选、汇总书签数据并将其与其他数据源联接，从而轻松查找确凿的证据。

当前在预览版中，如果您在日志中搜索时发现急需解决的问题，只需单击几下，即可创建书签并将其升级为事件，或将书签添加到现有事件。 有关事件的详细信息，请参阅[教程：使用 Azure Sentinel 调查事件](tutorial-investigate-cases.md)。 

此外，在预览中，您可以通过单击书签详细信息中的 **"调查"** 来可视化书签数据。 这将启动调查体验，您可以在其中使用交互式实体图和时间线查看、调查和直观地传达您的发现。

## <a name="add-a-bookmark"></a>添加书签

1. 在 Azure 门户中，导航到**哨兵** > **威胁管理** > **搜索**以运行可疑和异常行为的查询。

2. 选择其中一个狩猎查询，在右侧，在狩猎查询详细信息中，选择 **"运行查询**"。 

3. 选择 **"查看查询结果**"。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![查看 Azure 哨兵搜索的查询结果](./media/bookmarks/new-processes-observed-example.png)
    
    此操作将在 **"日志"** 窗格中打开查询结果。

4. 在日志查询结果列表中，使用复选框选择一个或多个包含您感兴趣的信息的行。

5. 选择 **"添加书签**"
    
    > [!div class="mx-imgBorder"]
    > ![添加狩猎书签进行查询](./media/bookmarks/add-hunting-bookmark.png)

6. 在右侧，在 **"添加书签"** 窗格中，可以选择更新书签名称、添加标记和注释，以帮助您识别有关该项目的有趣内容。

7. 在 **"查询信息**"部分中，使用下拉框从**帐户**、**主机**和**IP 地址**实体类型的查询结果中提取信息。 此操作将所选实体类型映射到查询结果中的特定列。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![用于狩猎书签的映射实体类型](./media/bookmarks/map-entity-types-bookmark.png)
    
    要查看调查图中的书签（当前处于预览中），必须映射至少一个实体类型，即**帐户**、**主机**或**IP 地址**。 

5. 单击 **"保存**"以提交更改并添加书签。 所有书签数据都与其他调查人员共享，是迈向协作调查体验的第一步。

 
> [!NOTE]
> 每当从 Azure Sentinel 打开此窗格时，日志查询结果都支持书签。 例如，从导航栏中选择 **"常规** > **日志**"，在调查图中选择事件链接，或从事件的完整详细信息中选择警报 ID（当前处于预览状态）。 当从其他位置（如直接从 Azure 监视器）打开 **"日志"** 窗格时，无法创建书签。

## <a name="view-and-update-bookmarks"></a>查看和更新书签 

1. 在 Azure 门户中，导航到**哨兵** > **威胁管理** > **搜索**。 

2. 选择 **"书签**"选项卡以查看书签列表。

3. 要帮助您查找特定书签，请使用搜索框或筛选器选项。

4. 选择单个书签，并在右侧详细信息窗格中查看书签详细信息。

5. 根据需要进行更改，这些更改将自动保存。

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>探索调查图中的书签

> [!IMPORTANT]
> 探索调查图和调查图本身中的书签，目前正在公开预览。
> 这些功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 门户中，导航到**哨兵** > **威胁管理** > **狩猎** > **书签**选项卡，然后选择要调查的书签或书签。

2. 在书签详细信息中，确保至少映射一个实体。 例如，对于**实体**，您将看到**IP、****计算机**或**帐户**的条目。

3. 单击 **"调查**"以查看调查图中的书签。

有关使用调查图的说明，请参阅[使用调查图进行深度潜水](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)。

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>将书签添加到新事件或现有事件

> [!IMPORTANT]
> 向新或现有事件添加书签当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 门户中，导航到**哨兵** > **威胁管理** > **狩猎** > **书签**选项卡，然后选择要添加到事件的书签或书签。

2. 从命令栏中选择**事件操作（预览）：**
    
    > [!div class="mx-imgBorder"]
    > ![向事件添加书签](./media/bookmarks/incident-actions.png)

3. 根据需要选择 **"创建新事件**"或 **"添加到现有事件**"。 然后：
    
    - 对于新事件：可选更新事件的详细信息，然后选择 **"创建**"。
    - 要向现有事件添加书签：选择一个事件，然后选择"**添加**"。 

要查看事件中的书签：导航到**哨兵** > **威胁管理** > **事件**，然后使用书签选择事件。 选择 **"查看完整详细信息**"，然后选择 **"书签**"选项卡。

> [!TIP]
> 作为命令栏上 **"事件操作（预览）"** 选项的替代方法，您可以使用上下文菜单 **（...）** 对一个或多个书签选择选项以**创建新事件**、**添加到现有事件**以及**从事件中删除**。 

## <a name="view-bookmarked-data-in-logs"></a>在日志中查看书签数据

要查看书签查询、结果或其历史记录，请从 **"狩猎** > **书签"** 选项卡中选择书签，并使用详细信息窗格中提供的链接： 

- **查看源查询**以查看 **"日志"** 窗格中的源查询。

- **查看书签日志**以查看所有书签元数据，其中包括谁进行了更新、更新的值以及更新发生的时间。

您还可以通过从 **"狩猎** > **书签**"选项卡上的命令栏中选择**书签日志**来查看所有书签的原始书签数据：

> [!div class="mx-imgBorder"]
> ![书签日志](./media/bookmarks/bookmark-logs.png)

此视图显示具有关联元数据的所有书签。 您可以使用[关键字查询语言](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference)（KQL） 查询筛选到要查找的特定书签的最新版本。

> [!NOTE]
> 在创建书签和在 **"书签"** 选项卡中显示书签之间，可能会有显著的延迟（以分钟为单位）。

## <a name="delete-a-bookmark"></a>删除书签
 
1.  在 Azure 门户中，导航到**哨兵** > **威胁管理** > **狩猎** > **书签**选项卡，然后选择要删除的书签或书签。 

2. 右键单击您的选择，然后选择删除书签或书签的选项。 例如，如果只选择了一个书签，**则删除书签**;如果选择了两个书签，则**删除 2 个书签**。
    
删除书签会从 **"书签"** 选项卡中列表中删除书签。日志分析工作区的**HuntingBook 标记**表将继续包含以前的书签条目，但最新的条目将**SoftDelete**值更改为 true，从而轻松筛选出旧书签。 删除书签不会从与其他书签或警报关联的调查体验中删除任何实体。 


## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何使用 Azure Sentinel 中的书签运行狩猎调查。 要详细了解 Azure Sentinel，请参阅以下文章：


- [主动寻找威胁](hunting.md)
- [使用笔记本运行自动狩猎活动](notebooks.md)
