---
title: 快速入门 - 使用 Azure 门户创建 Apache Spark 池（预览版）
description: 遵循本指南中的步骤，使用 Azure 门户新建 Apache Spark 池。
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 04a413ab171ea65c72182928ad4897530cb00e15
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665403"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview-using-the-azure-portal"></a>快速入门：使用 Azure 门户新建 Apache Spark 池（预览版）

Azure Synapse Analytics 提供了各种分析引擎，可帮助你引入、转换、分析和分布数据，以及对数据建模。 Apache Spark 池提供开源大数据计算功能。 在 Synapse 工作区中创建 Apache Spark 池后，可以加载、处理和分布数据以及为数据建模，更快地获取分析见解。

本快速入门介绍如何使用 Azure 门户在 Synapse 工作区中创建 Apache Spark 池。

> [!IMPORTANT]
> 不管是否正在使用 Spark 实例，它们都会按分钟按比例计费。 请务必在用完 Spark 实例后将其关闭，或设置较短的超时。 有关详细信息，请参阅本文的**清理资源**部分。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [Synapse Analytics 工作区](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>导航到 Synapse 工作区 
1. 导航到要在其中创建 Apache Spark 池的 Synapse 工作区，方法是在搜索栏中键入服务名称（或直接键入资源名称）。
![Azure 门户中的搜索栏，其中键入了 Synapse 工作区。](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. 从工作区列表中，键入要打开的工作区的名称（或名称的一部分）。 在此示例中，我们将使用名为 contosoanalytics 的工作区。
![已筛选的 Synapse 工作区列表，显示包含名称 Contoso 的工作区。](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>创建新的 Apache Spark 池

1. 在要在其中创建 Apache Spark 池的 Synapse 工作区中，选择“新建 Apache Spark 池”。
    ![Synapse 工作区的概览，用于创建新 Apache Spark 池的命令四周有一个红色框](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. 在“基本信息”选项卡中输入以下详细信息：

    |设置 | 建议的值 | 说明 |
    | :------ | :-------------- | :---------- |
    | **Apache Spark 池名称** | 有效的池名称 | 这是 Apache Spark 池要使用的名称。 |
    | **节点大小** | 小 (4 vCPU / 32 GB) | 请将此项设置为最小大小，以降低本快速入门的成本 |
    | **自动缩放** | 已禁用 | 此快速入门不需要自动缩放 |
    | **节点数** | 5 | 在此快速入门中使用小尺寸来限制成本 |


    ![Apache Spark 池 - 创建流 -“基本信息”选项卡。](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > 请注意，Apache Spark 池可以使用的名称有特定的限制。 名称只能包含字母或数字，必须为 15 个或更少的字符，必须以字母开头，不能包含保留字，并且在工作区中必须是独一无二的。

3. 选择“下一步: 其他设置”并查看默认设置。 请不要修改任何默认设置。
    ![Apache Spark 池 - 创建流 -“其他设置”选项卡。](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. 选择“下一步: 标记”。 请不要添加任何标记。
    ![Apache Spark 池 - 创建流 -“其他设置”选项卡。](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. 选择“查看 + 创建”。

6. 根据前面输入的内容，确保详细信息正确，然后选择“创建”。
    ![Apache Spark 池 - 创建流 -“查看设置”选项卡。](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. 此时会启动资源预配流，指示其完成时间。
    ![Apache Spark 池 - 创建流 - 资源预配。](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. 预配完成后，导航回到工作区会显示新建的 Apache Spark 池对应的新条目。
    ![Apache Spark 池 - 创建流 - 资源预配。](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. 此时没有任何资源在运行，因此 Spark 不会产生费用，但你已创建有关所要创建的 Spark 实例的元数据。

## <a name="clean-up-resources"></a>清理资源

执行以下步骤，从工作区中删除 Apache Spark 池。
> [!WARNING]
> 删除 Apache Spark 池会从工作区中删除分析引擎。 将不再可以连接到该池，并且使用此 Apache Spark 池的所有查询、管道和笔记本都不再可以正常运行。

若要删除 Apache Spark 池，请执行以下操作：

1. 导航到工作区中的“Apache Spark 池”边栏选项卡。
2. 选择要删除的 Apache Spark 池（在本例中为 contosospark）。
3. 按“删除”。
 ![Apache Spark 池的列表，其中已选择最近创建的池。](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. 确认删除，然后按“删除”按钮。
 ![确认删除选定 Apache Spark 池的对话框。](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. 成功完成该过程后，工作区资源中将不再列出该 Apache Spark 池。

## <a name="next-steps"></a>后续步骤

- 请参阅[快速入门：创建 Apache Spark 笔记本](quickstart-apache-spark-notebook.md)。
- 请参阅[快速入门：使用 Azure 门户创建 Synapse SQL 池](quickstart-create-sql-pool-portal.md)。
