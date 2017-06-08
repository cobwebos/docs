---
title: "Azure 门户：创建和管理 SQL 数据库弹性池 | Microsoft Docs"
description: "了解如何使用 Azure 门户和 SQL 数据库的内置智能来管理、监视可缩放的弹性池并正确调整其大小，以优化数据库性能和管理成本。"
keywords: 
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: cgronlun
ms.assetid: 3dc9b7a3-4b10-423a-8e44-9174aca5cf3d
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 05/08/2017
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 66f7cc63d311b6b5cd223fe0d8a9bf9f636abef1
ms.contentlocale: zh-cn
ms.lasthandoff: 04/20/2017


---
# <a name="create-and-manage-an-elastic-pool-with-the-azure-portal"></a>使用 Azure 门户创建和管理弹性池
本主题介绍如何使用 Azure 门户创建和管理可缩放的[弹性池](sql-database-elastic-pool.md)。 也可以使用 [PowerShell](sql-database-elastic-pool-manage-powershell.md)、REST API 或 [C#](sql-database-elastic-pool-manage-csharp.md) 创建和管理 Azure 弹性池。 此外还可以使用 [Transact-SQL](sql-database-elastic-pool-manage-tsql.md) 创建弹性池和将数据库移入和移出弹性池。

## <a name="create-an-elastic-pool"></a>创建弹性池 

可以通过两种方法创建弹性池。 如果你知道想要的池设置，可以从头开始创建，或者根据服务的建议进行创建。 SQL 数据库具有内置智能，可以根据数据库过去的使用情况遥测数据来推荐更加经济高效的弹性池设置。

可以在服务器上创建多个池，但不能将数据库从不同的服务器添加到同一个池中。

> [!NOTE]
> 弹性池在所有 Azure 区域中均已正式发布 (GA)，但印度西部除外，这些区域当前仅发布了预览版。  将尽快在此区域中正式发布弹性池。
>
>

### <a name="step-1-create-an-elastic-pool"></a>步骤 1：创建弹性池

在门户的现有“服务器”边栏选项卡中创建弹性池，这是将现有数据库移到弹性池中的最简单方法。

> [!NOTE]
> 还可以通过在“应用商店”中搜索“SQL 弹性池”或者通过在“SQL 弹性池”浏览边栏选项卡中单击“+添加”来创建弹性池。 可以通过此池预配工作流指定新的或现有的服务器。
>
>

1. 在 [Azure 门户](http://portal.azure.com/)中，单击“更多服务”**>**“SQL 服务器”，然后单击包含要添加到弹性池的数据库的服务器。
2. 单击“新建池”。

    ![将池添加到服务器](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **- 或 -**

    你可能会看到一条消息，指出该服务器已有建议的弹性池。 单击消息以查看基于历史数据库使用量遥测的建议池，然后单击层以查看更多详细信息并自定义池。 有关如何进行建议的信息，请参阅本主题后面的 [了解池建议](#understand-pool-recommendations) 。

    ![建议的池](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. 显示“弹性池”边栏选项卡，可在其中指定池的设置。 如果在上一步骤中单击了“新建池”，则定价层将默认为“标准”，并且未选择任何数据库。 可以创建一个空池，或者指定该服务器中要移动到池中的一组现有数据库。 如果要创建建议的池，则会预填充建议的定价层、性能设置和数据库列表，但仍然可以更改它们。

    ![配置弹性池](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. 指定弹性池的名称，或保留默认名称。

### <a name="step-2-choose-a-pricing-tier"></a>步骤 2：选择定价层

该池的定价层决定了池中弹性数据库的可用功能、eDTU 数目上限 (eDTU MAX)，以及每个数据库的可用存储 (GB)。 有关详细信息，请参阅“服务层”。

若要更改池的定价层，请单击“定价层”，单击所需的定价层，然后单击“选择”。

> [!IMPORTANT]
> 选择定价层并在最后一个步骤中单击“确定”来提交更改之后，便无法更改池的定价层。 若要更改现有弹性池的定价层，请在所需的定价层中创建弹性池，然后将数据库移转到这个新池。
>
>

![选择定价层](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

### <a name="step-3-configure-the-pool"></a>步骤 3：配置池

设置定价层后，请单击“配置池”，在其中添加数据库、设置池 eDTU 和存储（池 GB），并且在其中为池中的弹性数据库设置最小值和最大值 eDTU。

1. 单击“配置池” 
2. 选择要添加到池的数据库。 创建池时该步骤是可选的。 创建池后可以添加数据库。
    若要添加数据库，请单击“添加数据库”，单击要添加的数据库，然后单击“选择”按钮。

    ![添加数据库](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    如果所使用的数据库具有足够的历史使用情况遥测数据，则将更新“估计的弹性 DTU 和 GB 使用量”图表和“弹性 DTU 实际使用量”条形图，以帮助进行配置决策。 此外，服务可能会向你提供建议消息，以帮助正确调整池大小。 请参阅 [动态建议](#dynamic-recommendations)。

3. 使用“配置池”  页面上的控件浏览设置并配置池。 请参阅[弹性池限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)，了解有关每个服务层限制的详细信息；参阅[弹性池价格和性能注意事项](sql-database-elastic-pool.md)，了解有关正确调整弹性池大小的详细指南。 有关池设置的详细信息，请参阅[弹性池属性](sql-database-elastic-pool.md#database-properties-for-pooled-databases)。

    ![配置弹性池](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. 更改设置后，单击“配置池”边栏选项卡中的“选择”。
5. 单击“确定”创建池。

## <a name="understand-elastic-pool-recommendations"></a>了解弹性池建议

SQL 数据库服务将评估使用量历史记录，并在比使用单一数据库更符合成本效益时，建议使用一个或多个池。 每项建议是使用最适合该池的服务器数据库的唯一子集配置的。

![建议的池](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

池建议包括：

- 池的定价层（基本、标准、高级或高级 RS）
- 合适的 **池弹性 DTU** （也称为每个池的“弹性 DTU 最大值”）
- 每个数据库的“弹性 DTU 最大值”和“弹性 DTU 最小值”
- 池的建议数据库列表

> [!IMPORTANT]
> 建议池时，服务将考虑过去 30 天的遥测数据。 数据库若要被视为弹性池的候选项，则必须至少存在 7 天。 已在弹性池中的数据库不会被视为推荐的弹性池候选项。
>

服务会评估将每个服务层中的单一数据库移到同一层的池的资源需求和成本效益。 例如，评估服务器上的所有标准数据库是否适合标准弹性池。 这意味着，服务不进行跨层建议，例如将标准数据库移到高级池。

将数据库添加到池中后，会基于所选数据库的历史使用情况动态地生成建议。 这些建议显示在 eDTU 和 GB 使用情况图表中以及“配置池”边栏选项卡顶部的建议横幅中。 这些建议旨在帮助创建针对特定数据库进行了优化的弹性池。

![动态建议](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="manage-and-monitor-an-elastic-pool"></a>管理和监视弹性池

可以使用 Azure 门户监视和管理弹性池和池中的数据库。 在该门户中，可以监视弹性池和该池中的数据库的利用率。 还可以对弹性池进行一组更改，然后同时提交所有更改。 这些更改包括添加或删除数据库、更改弹性池设置或更改数据库设置。

下图显示一个示例弹性池。 视图包括：

*  监视弹性池以及池中包含的数据库的资源使用率的图表。
*  用于对弹性池进行更改的“配置”池按钮。
*  用于创建数据库并将其添加到当前弹性池的“创建数据库”按钮。
*  弹性作业，通过对列表中的所有数据库运行 Transact SQL 脚本来帮助管理大量数据库。

![池视图][2]

可以转到特定池以查看其资源利用率。 默认情况下，池配置为显示最近一小时的存储和 eDTU 使用率。 图表可以配置为按各种时间范围显示不同指标。

1. 选择要使用的弹性池。
2. “弹性池监视”下是标记为“资源利用率”的图表。 单击该图表。

    ![弹性池监视][3]

    “指标”边栏选项卡会打开，其中显示指定指标在指定时间范围内的详细视图。   

    ![“度量值”边栏选项卡][9]

### <a name="to-customize-the-chart-display"></a>自定义图表显示

可以编辑图表和指标边栏选项卡以显示其他指标，如 CPU 百分比、数据 IO 百分比和已用日志 IO 百分比。

1. 在指标边栏选项卡上，单击“编辑”。

    ![单击“编辑”][6]

2. 在“编辑图表”边栏选项卡中，选择时间范围（前一个小时、今天或过去一周），或单击“自定义”选择过去两周中的任何日期范围。 选择图表类型（条形图或折线图），然后选择要监视的资源。

   > [!Note]
   > 仅可同时在图表中显示具有相同度量单位的指标。 例如，如果选择“eDTU 百分比”，则只能选择以百分比作为度量单位的其他指标。
   >

    ![单击“编辑”](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)



3. 。

## <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>管理和监视弹性池中的数据库

还可以监视单个数据库以发现潜在问题。

1. “弹性数据库监视”下有一个图表，其中显示五个数据库的指标。 默认情况下，该图表显示池中按前一个小时平均 eDTU 使用率排名前五的数据库。 单击该图表。

    ![弹性池监视][4]

2. “数据库资源利用率”边栏选项卡随即出现。 这可提供池中数据库使用率的详细视图。 使用该边栏选项卡下半部分中的网格，可以选择池中的任何数据库以在图表中显示其使用率（最多 5 个数据库）。 还可以通过单击“编辑图表”来自定义在图表中显示的指标和时间范围。

    ![“数据库资源利用率”边栏选项卡][8]

### <a name="to-customize-the-view"></a>自定义视图

1. 在“数据库资源利用率”边栏选项卡中，单击“编辑图表”。

    ![单击“编辑图表”](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. 在“编辑图表”边栏选项卡中，选择时间范围（前一个小时或过去 24 小时），或单击“自定义”选择过去 2 周内的另一天进行显示。

    ![单击“自定义”](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. 单击“数据库比较依据”下拉列表以选择要在比较数据库时使用的其他指标。

    ![编辑图表](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>选择要监视的数据库

在“数据库资源利用率”边栏选项卡上的数据库列表中，可以通过浏览列表中的各页或通过输入数据库名称来查找特定数据库。 使用复选框可选择数据库。

![搜索要监视的数据库][7]


## <a name="add-an-alert-to-an-elastic-pool-resource"></a>向弹性池资源添加警报

可以向弹性池添加规则，以便在弹性池达到设置的利用率阈值时，向人员发送电子邮件或是向 URL 终结点发送警报字符串。

> [!IMPORTANT]
> 对弹性池资源利用率的监视存在至少 20 分钟的延迟。 当前不支持为弹性池设置短于 30 分钟的警报。 为弹性池设置的任何时长（PowerShell API 中名为“-WindowSize”的参数）短于 30 分钟的警报可能无法被触发。 请确保为弹性池定义的任何警报的时长不短于 30 分钟 (WindowSize)。
>
>

**向任何资源添加警报：**

1. 单击“资源利用率”图表以打开“指标”边栏选项卡，单击“添加警报”，然后在“添加警报规则”边栏选项卡中填写信息（“资源”会自动设置为所使用的池）。
2. 键入可标识发送给你和收件人的警报的“名称”和“说明”。
3. 从列表中选择要发送相关警报的“指标”。

    图表会动态显示该指标的资源利用率，以帮助选择阈值。

4. 选择“条件”（大于、小于等）和“阈值”。
5. 单击 **“确定”**。

## <a name="move-a-database-into-an-elastic-pool"></a>将数据库移入弹性池

可以对现有池添加或删除数据库。 数据库可以位于其他池中。 但是，只能添加位于相同逻辑服务器上的数据库。

1. 在池的边栏选项卡中的“弹性数据库”下，单击“配置池”。

    ![单击“配置池”][1]

2. 在“配置池”边栏选项卡中，单击“添加到池”。

    ![单击“添加到池”](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. 在“添加数据库”边栏选项卡中，选择要添加到池的一个或多个数据库。 然后单击“选择”。

    ![选择要添加的数据库](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    “配置池”边栏选项卡现在会列出选择要添加的数据库，其状态设置为“挂起”。

    ![挂起池添加](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. 在“配置池”边栏选项卡中，单击“保存”。

    ![点击“保存”(Save)](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>将数据库移出弹性池

1. 在“配置池”边栏选项卡中，选择要删除的一个或多个数据库。

    ![数据库列出](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. 单击“从池中删除”。

    ![数据库列出](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    “配置池”边栏选项卡现在会列出选择要删除的数据库，其状态设置为“挂起”。

    ![预览数据库添加和删除](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. 在“配置池”边栏选项卡中，单击“保存”。

    ![点击“保存”(Save)](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-an-elastic-pool"></a>更改弹性池的性能设置

监视弹性池的资源利用率时，可能会发现需要进行一些调整。 池可能需要在性能或存储限制方面进行更改。 你可能要更改池中的数据库设置。 可以随时更改池的设置以获得性能和成本的最佳平衡。 请参阅[何时应使用弹性池？](sql-database-elastic-pool.md)，了解详细信息。

更改每个池的 eDTU 或存储限制以及每个数据库的 eDTU：

1. 打开“配置池”边栏选项卡。

    在“弹性池设置”下，使用任一滑块更改池设置。

    ![弹性池资源利用率](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 该设置更改时，屏幕会显示更改的估计每月成本。

    ![更新弹性池和新的每月成本](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## <a name="latency-of-elastic-pool-operations"></a>弹性池操作延迟
* 更改每个数据库的最小 eDTU 数或每个数据库的最大 eDTU 数通常可在 5 分钟或更少的时间内完成。
* 更改每个池的 eDTU 数取决于池中所有数据库使用的空间总量。 更改平均起来每 100 GB 需要 90 分钟或更短的时间。 例如，如果池中所有数据库使用的总空间为 200 GB，则更改每个池的池 eDTU 时，预计延迟为 3 小时或更短的时间。

## <a name="next-steps"></a>后续步骤

- 若要了解什么是弹性池，请参阅 [SQL 数据库弹性池](sql-database-elastic-pool.md)。
- 有关使用弹性池的指南，请参阅[弹性池的价格和性能注意事项](sql-database-elastic-pool.md)。
- 若要使用弹性作业根据池中数据库的数目来运行 Transact-SQL 脚本，请参阅[弹性作业概述](sql-database-elastic-jobs-overview.md)。
- 有关跨池中任意数量数据库的查询，请参阅[弹性查询概述](sql-database-elastic-query-overview.md)。
- 有关池中任意数量数据库的事务，请参阅[弹性事务](sql-database-elastic-transactions-overview.md)。


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png

