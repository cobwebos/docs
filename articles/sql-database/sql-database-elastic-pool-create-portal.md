<properties
	pageTitle="创建可缩放的弹性数据库池 | Microsoft Azure"
	description="如何将可缩放的弹性数据库池添加到 SQL 数据库配置，以简化多个数据库的管理和资源共享。"
	keywords="可缩放的数据库,数据库配置"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# 在 Azure 门户中为 SQL 数据库创建可缩放的弹性数据库池

> [AZURE.SELECTOR]
- [Azure 门户](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-elastic-pool-create-csharp.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)

本文介绍如何在 [Azure 门户](https://portal.azure.com/)中创建可缩放的[弹性数据库池](sql-database-elastic-pool.md)。可以通过两种方法创建池。知道所需的池设置时可以从头开始进行，或是从服务提供的建议开始，服务具有内置智能，可基于数据库过去的使用量遥测建议池设置（如果对于你更加经济高效）。

可以将多个池添加到服务器，但不能将数据库从不同的服务器添加到同一个池中。若要创建池，需要在 V12 服务器中至少有一个数据库。如果你没有这样的数据库，请参阅[创建首个 Azure SQL 数据库](sql-database-get-started.md)。可以创建只有一个数据库的池，但是池只有在具有多个数据库时才会经济高效。请参阅[弹性数据库池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)。

> [AZURE.NOTE] 池只能随 SQL 数据库 V12 服务器一起使用。如果你有更早版本的服务器，可以通过一个步骤[使用 PowerShell 升级到 V12 并创建池](sql-database-upgrade-server-powershell.md)。

##创建新池
1. 在 [Azure 门户](http://portal.azure.com/)中，单击“SQL 服务器”，然后单击包含要添加到池的数据库的服务器。
2. 单击“新建池”。

![将池添加到服务器](./media/sql-database-elastic-pool-create-portal/new-pool.png)

**- 或 -**

单击消息以查看基于历史数据库使用量遥测的建议池，然后单击层以查看更多详细信息并自定义池。请参阅本主题后面的[了解池建议](#understand-pool-recommendations)以了解如何进行建议。

![建议的池](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

“弹性数据库池”边栏选项卡随即出现，这是在其中设置池的位置。如果在上一步骤中单击了“新建池”，则门户会在“定价层”下选择“标准池”，为池选择唯一“名称”，并为池选择默认配置。如果选择了建议池，则池的建议层和配置已处于选择状态，不过你仍可以更改它们。

![配置弹性池](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. 若要更改池的定价层，请单击“定价层”，单击所需定价层，然后单击“选择”。

    > [AZURE.IMPORTANT] 选择定价层并通过在最后一个步骤中单击“确定”来提交更改之后，便无法更改池的定价层。

4. 单击“配置池”，在其中可以为池添加数据库并选择资源设置。
5. 若要添加数据库，请单击“添加数据库”，单击要添加的数据库，然后单击“选择”按钮。

    ![添加数据库](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    如果所使用的数据库具有足够的历史使用量遥测，“估计的弹性 DTU 和 GB 使用量”图表和“弹性 DTU 实际使用量”条形图会进行更新，以帮助进行配置决策。此外，服务可能会向你提供建议消息，以帮助正确调整池大小。请参阅[了解池建议](#understand-pool-recommendations)。

    ![动态建议](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. 使用“配置池”页面上的控件浏览设置并根据以下准则配置池：

    ![配置弹性池](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

    | 性能设置 | 说明 |
    | :--- | :--- |
    | “池弹性 DTU”和“池 GB”（每个池的设置）| 可供池中所有数据库使用和共享的最大弹性 DTU。池中可用的最大弹性 DTU 取决于定价层（服务层）。“池弹性 DTU”与可用于池的存储相关。对于分配给池的每个弹性 DTU，都会获取固定的数据库存储量，反之亦然。 |
    | “弹性 DTU 最小值”（每个数据库的设置）| 单一数据库在任何时间可以确保从池获得的弹性 DTU 最小数目。“弹性 DTU 最小值”通常设置为介于 0 和每个数据库的平均历史弹性 DTU 使用率之间的某个数字。这是应用于池中所有数据库的全局设置。 |
    | “弹性 DTU 最大值”（每个数据库的设置） | 池中单一数据库可以使用的弹性 DTU 最大数目。可以将此最大值设置为“池弹性 DTU”。将每个数据库的“弹性 DTU 最大值”设置得足够高，以处理数据库高峰使用率的最大突发或峰值。组可能会碰到某种程度的超量使用情况，因为池通常会假定数据库存在热使用模式和冷使用模式，即所有数据库不会同时达到峰值。**示例：**假设每个数据库的高峰使用率为 50 DTU，在该组的 100 个数据库中，仅有 20% 同时爆发到峰值。如果将每个数据库的弹性 DTU 最大值设置为 50 弹性 DTU，则可以认为超量 5 倍使用该池是合理的，因此将“池弹性 DTU”设置为 1,000。“弹性 DTU 最大值”不是数据库的资源保障，而是在可能情况下能够达到的弹性 DTU 上限。 |

    请参阅[弹性数据库池参考](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)以了解有关每个服务层的限制的详细信息，并参阅[弹性数据库池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)以了解有关正确调整池大小的详细指南。

7. 在完成时单击“选择”，然后单击“确定”以创建池。

##了解池建议
SQL 数据库服务将评估使用量历史记录，并在比使用单一数据库更符合成本效益时，建议使用一个或多个池。每项建议是使用最适合该池的服务器数据库的唯一子集配置的。池建议包括：

- 池的定价层（基本、标准或高级）
- 合适的“池弹性 DTU”（也称为每个池的“弹性 DTU 最大值”）
- 每个数据库的“弹性 DTU 最大值”和“弹性 DTU 最小值”
- 池的建议数据库列表

建议池时，服务将考虑过去 30 天的遥测数据。被视为弹性数据库池候选项的数据库必须至少存在 7 天。已在弹性数据库池中的数据库不被视为建议的弹性数据库池候选项。

服务会评估将每个服务层中的单一数据库移到同一层的池的资源需求和成本效益。例如，评估服务器上的所有标准数据库是否适合标准弹性池。这意味着，服务不进行跨层建议，例如将标准数据库移到高级池。

## 其他资源

- [使用门户管理 SQL 数据库弹性池](sql-database-elastic-pool-manage-portal.md)
- [使用 PowerShell 管理 SQL 数据库弹性池](sql-database-elastic-pool-manage-powershell.md)
- [使用 C# 管理 SQL 数据库弹性池](sql-database-client-library.md)
- [弹性数据库参考](sql-database-elastic-pool-reference.md)

<!---HONumber=Mooncake_0411_2016-->