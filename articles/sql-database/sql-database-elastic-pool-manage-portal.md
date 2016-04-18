<properties
	pageTitle="监视、管理弹性数据库池并调整其大小"
	description="了解如何使用 Azure 门户和 SQL 数据库的内置智能来管理、监视可缩放的弹性数据库池并正确调整其大小，以优化数据库性能和管理成本。"
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 Azure 门户监视、管理弹性数据库池并调整其大小

> [AZURE.SELECTOR]
- [Azure 门户](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)


本文介绍如何使用 Azure 门户来监视、管理弹性数据库池和池中的数据库，并正确调整其大小。SQL 数据库有内置的智能，它能分析历史使用量遥测数据并在更具成本效益的时候主动建议使用数据库池。还可以在提交更改之前添加和删除数据库并查看对池性能和存储形成的影响。

若要演练本文中的步骤，需要数据库和一个池。如果已拥有数据库，请参阅[创建池](sql-database-elastic-pool-create-portal.md)，如果没有，则参阅 [SQL 数据库教程](sql-database-get-started)。

**选择要使用的池：**

- 在 [Azure 门户](https://portal.azure.com)中，单击“浏览”，单击“SQL 弹性池”，然后从列表中单击要使用的池。

##监视池的资源利用率
选择要使用的池之后，在“弹性池监视”下，一个图表和动态磁贴会显示池的重要利用率信息。

![监视弹性池](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**更改显示的日期范围、图表类型（条形图或折线图）或资源：**

- 单击“编辑”，选取所需设置，然后单击“保存”以更新图表。

**更改动态磁贴：**

- 单击“添加磁贴”，然后从显示在左侧的磁贴库选择所需磁贴。

##向池资源添加警报
可以向资源添加规则，以便在资源达到设置的利用率阈值时，向人员发送电子邮件或是向 URL 终结点发送警报字符串。

**向任何资源添加警报：**

1. 单击“资源利用率”图表以打开“指标”边栏选项卡，单击“添加警告”，然后在“添加警报规则”边栏选项卡中填写信息（“资源”会自动设置为所使用的池）。
2. 输入可标识发送给你和收件人的警报的“名称”和“说明”。
3. 从列表中选择要发送相关警报的“指标”。

    图表会动态显示该指标的资源利用率，以帮助选择阈值。

4. 选择“条件”（大于、小于等）和“阈值”。
5. 单击**“确定”**。

##更改每个池的弹性 DTU 和数据库弹性 DTU
查看池的资源利用率时，你可能会发现池需要不同的弹性 DTU 设置，或池中的各个数据库需要不同的弹性 DTU 设置。可以随时更改池的设置以获得性能和成本的最佳平衡。有关详细信息，请参阅[价格和性能注意事项](sql-database-elastic-pool-guidance.md)。

**更改池弹性 DTU 和每个数据库的弹性 DTU：**

1. 单击“配置池”以打开“配置性能”边栏选项卡。

    在“弹性数据库池设置”下，一个图表会显示弹性 DTU 的最近趋势以及存储利用率（采用池容量的百分比）。

    ![弹性池资源利用率](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 单击不同的“池弹性 DTU”，你会看到要进行的更改的估计每月成本，图表会进行更新以显示具有所选的新弹性 DTU 最大值的预测利用率值。

    ![更新池和新的每月成本](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. 在“弹性数据库设置”下，一个条形图会显示池中每个数据库的弹性 DTU 利用率。

4. 单击“弹性 DTU 最大值”可为池中的数据库设置弹性 DTU 的最大数目，单击“弹性 DTU 最小值”可设置弹性 DTU 的最小数目。

    ![更新弹性数据库弹性 DTU 最小值和最大值](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##添加和删除数据库

创建池之后，你可以向池添加数据库或删除它们。只能在同一个 SQL Server 上添加数据库。

**添加数据库：**

1. 在池的边栏选项卡中的“弹性数据库”下，单击显示池中数据库数目的链接。

    ![数据库列出](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. 在“弹性数据库”边栏选项卡中，单击“添加数据库”，单击要添加的数据库，然后单击“选择”按钮。

    “弹性数据库”边栏选项卡现在会列出刚添加的数据库以及“平均 DTU”和“大小(GB)”形式的存储利用率，状态为“挂起”。池利用率值现在显示保存更改后的“新”值内容。

    ![建议的池](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. 单击“保存”，然后在门户显示请求已提交时单击“确定”。该操作完成后，池中的数据库数目会显示在池的边栏选项卡上。

**删除数据库：**

1. 在池的边栏选项卡中的“弹性数据库”下，单击显示池中数据库数目的链接。

    ![数据库列出](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. 在“弹性数据库”边栏选项卡中，从池中数据库的列表中单击要删除的数据库，然后单击“删除数据库”。

    池利用率值现在显示保存更改后的“新”值内容。

3. 单击“保存”，然后在门户显示请求已提交时单击“确定”。该操作完成后，池中的数据库数目会显示在池的边栏选项卡上。

## 在池中创建新数据库

在数据库的边栏选项卡中，只需单击“创建数据库”。系统已经为正确的服务器和池配置了 SQL 数据库，因此请单击选择其他设置，然后单击“确定”以在池中创建新数据库。

   ![创建弹性数据库](./media/sql-database-elastic-pool-portal/create-database.png)

##创建和管理弹性作业

弹性作业可以用来根据池中数据库的数目来运行 Transact-SQL 脚本。使用作业之前，请安装弹性作业组件并提供凭据。有关详细信息，请参阅[弹性数据库作业概述](sql-database-elastic-jobs-overview.md)。

## 其他资源

- [弹性数据库参考](sql-database-elastic-pool-reference.md)
- [SQL 数据库弹性池](sql-database-elastic-pool.md)
- [使用门户创建弹性数据库池](sql-database-elastic-pool-create-csharp.md)
- [使用 PowerShell 创建弹性数据库池](sql-database-elastic-pool-create-powershell.md)
- [使用 C# 创建弹性数据库池](sql-database-elastic-pool-create-csharp.md)
- [弹性数据库池的价格和性能注意事项](sql-database-elastic-pool-guidance.md)

<!---HONumber=Mooncake_0411_2016-->