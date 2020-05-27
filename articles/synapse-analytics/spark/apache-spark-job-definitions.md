---
title: 教程 - Apache Spark for Azure Synapse Analytics：用于 Synapse 的 Apache Spark 作业定义
description: 教程 - 使用 Azure Synapse Analytics 创建 Spark 作业定义，并将定义提交到 Apache Spark for Azure Synapse Analytics 池。
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587929"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>教程：使用 Azure Synapse Analytics 创建 Synapse Spark 池的 Apache Spark 作业定义

本教程演示如何使用 Azure Synapse Analytics 创建 Spark 作业定义，然后将其提交到 Synapse Spark 池。 可按多种方式使用该插件：

* 开发 Spark 作业定义，并将其提交到 Synapse Spark 池中。
* 提交后查看作业详细信息。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 开发 Spark 作业定义，并将其提交到 Synapse Spark 池中。
> * 提交后查看作业详细信息。

## <a name="prerequisites"></a>先决条件

* 一个 Azure Synapse Analytics 工作区。 有关说明，请参阅[创建 Azure Synapse Analytics 工作区](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)。

## <a name="get-started"></a>入门

在提交 Spark 作业定义之前，你需要成为所要使用的 ADLS Gen2 文件系统的存储 Blob 数据所有者。 如果还不是该所有者，则需要手动添加权限。

### <a name="scenario-1-add-permission"></a>应用场景 1：添加权限

1. 打开 [Microsoft Azure](https://ms.portal.azure.com)，然后打开“存储帐户”。

2. 单击“容器”，然后创建一个文件系统。  本教程使用 `sparkjob`。

    ![单击“提交”按钮提交 Spark 作业定义](./media/apache-spark-job-definitions/open-azure-container.png)

    ![“Spark 提交”对话框](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. 打开 `sparkjob`，依次单击“访问控制(IAM)”、“添加”，然后选择“添加角色分配”。  

    ![单击“提交”按钮提交 Spark 作业定义](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![单击“提交”按钮提交 Spark 作业定义](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. 单击“角色分配”，输入用户名，然后验证用户角色。

    ![单击“提交”按钮提交 Spark 作业定义](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>应用场景 2：准备文件夹结构

在提交 Spark 作业定义之前，需要完成的一个工作是将文件上传到 ADLS Gen2，并在 ADLS Gen2 中准备文件夹结构。 我们将使用 Synapse Studio 中的“存储”节点来存储文件。

1. 打开 [Azure Synapse Analytics](https://web.azuresynapse.net/)。

2. 单击“数据”，选择“存储帐户”，并将相关文件上传到 ADLS Gen2 文件系统。  我们支持 Scala、Java、.NET 和 Python。 本教程使用图中的示例进行演示，你可以根据需要更改项目结构。

    ![设置 Spark 作业定义的值](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>创建 Spark 作业定义

1. 打开 [Azure Synapse Analytics](https://web.azuresynapse.net/)，选择“开发”。

2. 在左侧窗格中选择“Spark 作业定义”。

3. 单击“Spark 作业定义”右侧的“操作”节点。

     ![创建新的 Spark 作业定义](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. 在“操作”下拉列表中，选择“新建 Spark 作业定义” 

     ![创建新的 Spark 作业定义](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. 在“新建 Spark 作业定义”窗口中选择语言，然后提供以下信息：  

   * 选择“Spark(Scala)”作为“语言”。 

    |  properties   | 说明   |  
    | ----- | ----- |  
    |作业定义名称| 输入 Spark 作业定义的名称。  本教程使用 `job definition sample`。 在发布作业定义之前，随时可以更新此名称。|  
    |主定义文件| 用于作业的主文件。 从存储中选择一个 JAR 文件。 可以选择“上传文件”以将文件上传到存储帐户。 |
    |主类名| 主定义文件中的完全限定标识符或主类。|
    |命令行参数| 作业的可选参数。|
    |引用文件| 用于主定义文件中的引用的其他文件。 可以选择“上传文件”以将文件上传到存储帐户。|
    |Spark 池| 作业将提交到选定的 Spark 池。|
    |Spark 版本| 正在运行 Spark 池的 Spark 版本。|
    |执行程序| 要在作业的指定 Spark 池中提供的执行程序数目。|
    |执行程序大小| 作业的指定 Spark 池中提供的执行程序要使用的核心数和内存量。|  
    |驱动程序大小| 作业的指定 Spark 池中提供的驱动程序要使用的核心数和内存量。|

    ![设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-scala-definition.png)

   * 选择“PySpark(Python)”作为“语言”。 

    |  properties   | 说明   |  
    | ----- | ----- |  
    |作业定义名称| 输入 Spark 作业定义的名称。  本教程使用 `job definition sample`。 在发布作业定义之前，随时可以更新此名称。|  
    |主定义文件| 用于作业的主文件。 从存储中选择一个 PY 文件。 可以选择“上传文件”以将文件上传到存储帐户。|
    |命令行参数| 作业的可选参数。|
    |引用文件| 用于主定义文件中的引用的其他文件。 可以选择“上传文件”以将文件上传到存储帐户。|
    |Spark 池| 作业将提交到选定的 Spark 池。|
    |Spark 版本| 正在运行 Spark 池的 Spark 版本。|
    |执行程序| 要在作业的指定 Spark 池中提供的执行程序数目。|
    |执行程序大小| 作业的指定 Spark 池中提供的执行程序要使用的核心数和内存量。|  
    |驱动程序大小| 作业的指定 Spark 池中提供的驱动程序要使用的核心数和内存量。|

    ![设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-py-definition.png)

   * 选择“.NET Spark(C#/F#)”作为“语言”。 

    |  properties   | 说明   |  
    | ----- | ----- |  
    |作业定义名称| 输入 Spark 作业定义的名称。  本教程使用 `job definition sample`。 在发布作业定义之前，随时可以更新此名称。|  
    |主定义文件| 用于作业的主文件。 从存储中选择包含你的 .NET for Spark 应用程序的 ZIP 文件（即，主可执行文件、包含用户定义的函数的 DLL 以及其他所需文件）。 可以选择“上传文件”以将文件上传到存储帐户。|
    |主可执行文件| 主定义 ZIP 文件中的主可执行文件。|
    |命令行参数| 作业的可选参数。|
    |引用文件| 工作器节点所需的其他文件（即，依赖的 jar、其他用户定义的函数 DLL 以及其他配置文件），用于执行不包含在主定义 ZIP 文件中的 .NET for Spark 应用程序。 可以选择“上传文件”以将文件上传到存储帐户。|
    |Spark 池| 作业将提交到选定的 Spark 池。|
    |Spark 版本| 正在运行 Spark 池的 Spark 版本。|
    |执行程序| 要在作业的指定 Spark 池中提供的执行程序数目。|
    |执行程序大小| 作业的指定 Spark 池中提供的执行程序要使用的核心数和内存量。|  
    |驱动程序大小| 作业的指定 Spark 池中提供的驱动程序要使用的核心数和内存量。|

    ![设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-net-definition.png)

6. 选择“发布”以保存 Spark 作业定义。

    ![发布 Spark 作业定义](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>提交 Spark 作业定义

创建 Spark 作业定义后，可将其提交到 Synapse Spark 池。 在尝试使用本部分中的示例之前，请确保已完成**入门**中的步骤。

### <a name="scenario-1-submit-spark-job-definition"></a>应用场景 1：提交 Spark 作业定义

1. 单击 Spark 作业定义来打开其窗口。

      ![打开要提交的 Spark 作业定义 ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. 单击“提交”图标以将项目提交到选定的 Spark 池。 可以单击“Spark 监视 URL”选项卡查看 Spark 应用程序的 LogQuery。

    ![单击“提交”按钮提交 Spark 作业定义](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![“Spark 提交”对话框](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>应用场景 2：查看 Spark 作业运行进度

1. 单击“监视”，然后选择“Spark 应用程序”选项。  可以找到已提交的 Spark 应用程序。

    ![查看 Spark 应用程序](./media/apache-spark-job-definitions/view-spark-application.png)

2. 然后单击该 Spark 应用程序，此时会显示“LogQuery”窗口。 可以在“LogQuery”中查看作业执行进度。

    ![查看 Spark 应用程序 - LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>应用场景 3：检查输出文件

 1. 单击“数据”，然后选择“存储帐户”。  成功运行后，可以转到 ADLS Gen2 存储，并检查是否生成了输出。

    ![查看输出文件](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>后续步骤

本教程演示了如何使用 Azure Synapse Analytics 创建 Spark 作业定义，然后将其提交到 Synapse Spark 池。 接下来，可以使用 Azure Synapse Analytics 创建 Power BI 数据集并管理 Power BI 数据。 

- [连接到 Power BI Desktop 中的数据](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [使用 Power BI 进行可视化](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
