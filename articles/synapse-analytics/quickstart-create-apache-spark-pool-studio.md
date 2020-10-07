---
title: 快速入门：使用 Synapse Studio 创建 Apache Spark 池（预览版）
description: 遵循本指南中的步骤，使用 Synapse Studio 新建 Apache Spark 池。
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 3/19/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: cdf9a1f211e889146c9a6986eeea7502d3177057
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260829"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-using-synapse-studio"></a>快速入门：使用 Synapse Studio 创建 Apache Spark 池（预览版）

Azure Synapse Analytics 提供了各种分析引擎，可帮助你引入、转换、分析和提供数据，以及对数据建模。 Apache Spark 池提供开源大数据计算功能。 在 Synapse 工作区中创建 Apache Spark 池后，可以加载、处理和提供数据以及为数据建模，以获取见解。  

本快速入门介绍了使用 Synapse Studio 在 Synapse 工作区中创建 Apache Spark 池的步骤。

> [!IMPORTANT]
> 不管是否正在使用 Spark 实例，它们都会按分钟按比例计费。 请务必在用完 Spark 实例后将其关闭，或设置较短的超时。 有关详细信息，请参阅本文的**清理资源**部分。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [Synapse 工作区](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>导航到 Synapse 工作区

1. 导航到要在其中创建 Apache Spark 池的 Synapse 工作区，方法是在搜索栏中键入服务名称（或直接键入资源名称）。
![Azure 门户中的搜索栏，其中键入了 Synapse 工作区。](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. 从工作区列表中，键入要打开的工作区的名称（或名称的一部分）。 在此示例中，我们将使用名为 contosoanalytics 的工作区。
![已筛选的 Synapse 工作区列表，显示包含名称 Contoso 的工作区。](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>启动 Synapse Studio 

1. 从工作区概述中，选择“启动 Synapse Studio”以打开将在其中创建 Apache Spark 池的位置。 直接在搜索栏中键入服务名称或资源名称。
![Azure 门户 Synapse 工作区概述，突出显示了“启动 Synapse Studio”。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>在 Synapse Studio 中创建 Apache Spark 池

1. 在 Synapse Studio 主页上，选择“管理”图标以导航到左侧导航栏中的“管理中心” 。
![Synapse Studio 主页，突出显示了“管理中心”部分。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. 进入管理中心后，导航到“Apache Spark 池”部分，查看工作区中可用的 Apache Spark 池的当前列表。
![Synapse Studio 管理中心，其中 Apache Spark 池导航处于选中状态](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. 选择“+ 新建”，随即会显示新 Apache Spark 池创建向导。 
![Synapse Studio 管理中心 Spark 池列表。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-23.png)

1. 在“基本信息”选项卡中输入以下详细信息：

    | 设置 | 建议的值 | 说明 |
    | :------ | :-------------- | :---------- |
    | **Apache Spark 池名称** | contosospark | 这是 Apache Spark 池要使用的名称。 |
    | **节点大小** | 小 (4 vCPU / 32 GB) | 请将此项设置为最小大小，以降低本快速入门的成本 |
    | **自动缩放** | 已禁用 | 使用此快速入门时无需进行自动缩放 |
    | **节点数** | 8 | 在此快速入门中使用小尺寸来限制成本|
    
    ![Synapse Studio 新的 Apache Spark 池外形。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    > [!IMPORTANT]
    > 请注意，Apache Spark 池可以使用的名称有特定的限制。 名称只能包含字母或数字，必须为 15 个或更少的字符，必须以字母开头，不能包含保留字，并且在工作区中必须是独一无二的。

1. 在下一个选项卡（其他设置）中，保留所有默认值，并按“查看 + 创建”（我们不会添加任何标记）。
 ![Synapse Studio 新的 Apache Spark 池外形。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-25.png)

1. 目前不会添加任何标记，因此请选择“查看 + 创建”。

1. 在“查看 + 创建”选项卡中，根据前面输入的内容，确保详细信息正确，然后按“创建” 。 
 ![Synapse Studio 新的 Apache Spark 池外形。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. Apache Spark 池将开始预配过程。
![Synapse Studio 新的 Apache Spark 池外形。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-27.png)

1. 预配完成后，新的 Apache Spark 池将显示在列表中。
![Synapse Studio 新的 Apache Spark 池外形。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>使用 Synapse Studio 清理 Apache Spark 池资源

执行以下步骤，使用 Synapse Studio 从工作区中删除 Apache Spark 池。
> [!WARNING]
> 删除 Spark 池会从工作区中删除分析引擎。 将不再可以连接到该池，并且使用此 Spark 池的所有查询、管道和笔记本都不再可以正常运行。

若要删除 Apache Spark 池，请执行以下操作：

1. 在 Synapse Studio 的管理中心导航到 Apache Spark 池。
1. 选择要删除的 Apache 池旁边的省略号（在本例中为 contosospark）以显示 Apache Spark 池的命令。
![Apache Spark 池的列表，其中已选择最近创建的池。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)
1. 按“删除”。
1. 确认删除，然后按“删除”按钮。
 ![确认删除选定 Apache Spark 池的对话框。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-30.png)
1. 成功完成该过程后，工作区资源中将不再列出该 Apache Spark 池。 

## <a name="next-steps"></a>后续步骤

- 请参阅[快速入门：使用 Web 工具在 Synapse Studio 中创建 Apache Spark 池](quickstart-apache-spark-notebook.md)。
- 请参阅[快速入门：使用 Azure 门户创建 Apache Spark 池](quickstart-create-apache-spark-pool-portal.md)。
