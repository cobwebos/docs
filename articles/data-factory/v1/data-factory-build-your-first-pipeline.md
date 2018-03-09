---
title: "数据工厂教程：第一个数据管道 | Microsoft Docs"
description: "此 Azure 数据工厂教程介绍如何创建和计划数据工厂，使用 Hadoop 群集上的 Hive 脚本处理数据。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 374eebfe35207f6e5264159dcb72abddc6de4598
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>教程：使用 Hadoop 群集构建用于转换数据的第一个管道
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-build-your-first-pipeline.md)
> * [Azure 门户](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[快速入门：使用 Azure 数据工厂版本 2 创建数据工厂](../quickstart-create-data-factory-dot-net.md)。

在本教程中，将使用数据管道生成第一个 Azure 数据工厂。 管道通过在 Azure HDInsight (Hadoop) 群集上运行 Hive 脚本转换输入数据以生成输出数据。  

本文提供了此教程的概述和先决条件。 具备先决条件后，可以使用以下工具/SDK 之一完成此教程：Azure 门户、Visual Studio、PowerShell、Resource Manager 模板、REST API。 在本文开头的下拉列表中选择一个选项，或者选择本文末尾的链接以使用其中一个选项完成此教程。    

## <a name="tutorial-overview"></a>教程概述
将在本教程中执行以下步骤：

1. 创建**数据工厂**。 数据工厂可包含用于移动和转换数据的一个或多个数据管道。 

    在本教程中，会在数据工厂中创建一个管道。 
2. 创建**管道**。 管道可以具有一个或多个活动（示例：复制活动、HDInsight Hive 活动）。 此示例使用的是 HDInsight Hive 活动，在 HDInsight Hadoop 群集上运行 Hive 脚本。 该脚本首先创建表，以引用存储在 Azure Blob 存储中的原始 Web 日志数据，然后按年和月对原始数据进行分区。

    在本教程中，管道通过在 Azure HDInsight Hadoop 群集上运行 Hive 查询使用 Hive 活动转换数据。 
3. 创建**链接服务**。 创建一个链接服务，将数据存储或计算服务链接到数据工厂。 数据存储（如 Azure 存储）会将输入/输出数据保留在管道中。 计算服务（如 HDInsight Hadoop 群集）会处理/转换数据。

    在本教程中，将创建两个链接服务：**Azure 存储**和 **Azure HDInsight**。 Azure 存储链接服务将保存输入/输出数据的 Azure 存储帐户链接到数据工厂。 Azure HDInsight 链接服务用于转换数据的 Azure HDInsight 群集链接到数据工厂。 
3. 创建输入和输出**数据集**。 输入数据集表示管道中活动的输入，输出数据集表示活动的输出。

    在本教程中，输入和输出数据集指定 Azure Blob 存储中输入和输出数据的位置。 Azure 存储链接服务指定使用什么 Azure 存储帐户。 输入数据集指定输入文件所在位置，输出数据集指定输出文件所放置到的位置。 


有关 Azure 数据工厂的详细概述，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)一文。
  
下面是在本教程中构建的数据工厂示例的**图示视图**。 **MyFirstPipeline** 包含一个 Hive 类型的活动，该活动使用 **AzureBlobInput** 数据集作为输入并生成 **AzureBlobOutput** 数据集作为输出。 

![数据工厂教程中的图示视图](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


在本教程中，**adfgetstarted** Azure blob 容器的 **inputdata** 文件夹包含名为 input.log 的文件。 此日志文件包含三个月的条目：2016 年 1 月、2 月和 3 月。 以下是针对输入文件中的每个月的示例行。 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

当文件由具有 HDInsight Hive 活动的管道处理时，活动会在按年份和月份分区输入数据的 HDInsight 群集上运行 Hive 脚本。 该脚本会创建三个输出文件夹，其中包含具有每个月条目的文件。  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

如上所示的示例行中，第行（具有 2016-01-01）会写入 month = 1 文件夹中的 000000_0 文件。 同样，第二行会写入 month = 2 文件夹中的文件，第三行会写入 month = 3 文件夹中的文件。  

## <a name="prerequisites"></a>先决条件
开始本教程之前，必须具有以下先决条件：

1. **Azure 订阅** - 如果没有 Azure 订阅，只需几分钟就能创建一个免费试用帐户。 如需了解如何获取免费试用帐户，请参阅[免费试用](https://azure.microsoft.com/pricing/free-trial/)一文。
2. **Azure 存储** - 在本教程中，将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)一文。 创建存储帐户后，记下**帐户名称**和**访问密钥**。 请参阅[查看、复制和重新生成存储访问密钥](../../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys)。
3. 下载并查看位于以下网址的 Hive 查询文件 (**HQL**)：[https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql)。 此查询转换输入数据以生成输出数据。 
4. 下载并查看位于以下网址的示例输入文件 (**input.log**)：[https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. 在 Azure Blob 存储中创建一个名为 **adfgetstarted** 的 blob 容器。 
6. 将 **partitionweblogs.hql** 文件上传到 **adfgetstarted** 容器中的 **script** 文件夹。 使用 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)等工具。 
7. 将 **input.log** 文件上传到 **adfgetstarted** 容器中的 **inputdata** 文件夹。 

具备先决条件之后，选择以下工具/SDK 之一完成本教程： 

- [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Azure 门户和 Visual Studio 提供了生成数据工厂的 GUI 方式。 而 PowerShell、Resource Manager 模板和 REST API 选项提供了生成数据工厂的脚本/编程方式。

> [!NOTE]
> 本教程中的数据管道可以转换输入数据，以便生成输出数据。 它不是将数据从源数据存储复制到目标数据存储。 有关如何使用 Azure 数据工厂复制数据的教程，请参阅[教程：将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
> 
> 通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[数据工厂中的计划和执行情况](data-factory-scheduling-and-execution.md)。 





  
