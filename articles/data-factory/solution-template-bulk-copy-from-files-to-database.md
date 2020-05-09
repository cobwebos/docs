---
title: 从文件向数据库进行批量复制
description: 了解如何使用解决方案模板将数据从 Azure Data Lake Storage Gen2 批量复制到 Azure Synapse Analytics/Azure SQL 数据库。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 468bd838237e076aacb9dee0ccacfdcc1ea940af
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629110"
---
# <a name="bulk-copy-from-files-to-database"></a>从文件向数据库进行批量复制

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍了一个解决方案模板，你可以使用该模板将数据从 Azure Data Lake Storage Gen2 批量复制到 Azure Synapse Analytics/Azure SQL 数据库。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板从 Azure Data Lake Storage Gen2 源检索文件。 然后，它会循环访问源数据库中的每个文件，并将文件复制到目标数据存储。 

当前，此模板仅支持复制 **DelimitedText** 格式的数据。 还可以从源数据存储中检索其他数据格式的文件，但不能将这些文件复制到目标数据存储。  

该模板包含三个活动：
- **获取元数据**活动从 Azure Data Lake Storage Gen2 中检索文件，然后将其传递给后续 *ForEach* 活动。
- **ForEach** 活动从*获取元数据*活动获取文件，并以迭代方式将每个文件传递给*复制*活动。
- **复制**活动位于 *ForEach* 活动中，将源数据存储中的每个文件复制到目标数据存储区。

此模板定义了下面两个参数：
- *SourceContainer* 是将数据从 Azure Data Lake Storage Gen2 中复制到的根容器路径。 
- *SourceDirectory* 是 Azure Data Lake Storage Gen2 中根容器下要从中复制数据的目录路径。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到“从文件批量复制到数据库”模板。  创建与源 Gen2 存储的新连接。  请注意，“GetMetadataDataset”和“SourceDataset”是对源文件存储的同一连接的引用。

    ![创建与源数据存储的新连接](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 创建与接收器数据存储（要将数据复制到其中）的新  连接。

    ![创建与接收器数据存储的新连接](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. 选择“使用此模板”  。

    ![使用此模板](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 你会看到所创建的管道，如以下示例中所示：

    ![查看管道](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 如果在上面提到的**步骤 2** 中选择了“Azure Synapse Analytics（以前为 SQL DW）”  作为数据目标，需要按 SQL 数据仓库 Polybase 的要求，输入用于暂存的 Azure Blob 存储的连接。 如以下屏幕截图所示，模板会自动为 Blob 存储生成*存储路径*。 检查是否在管道运行后创建了容器。
        
    ![Polybase 设置](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. 选择“调试”，输入**参数**，然后选择“完成”。  

    ![单击“调试”****](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 管道运行成功完成后，你会看到类似于以下示例的结果：

    ![查看结果](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)