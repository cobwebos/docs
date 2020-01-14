---
title: 从文件大容量复制到数据库
description: 了解如何使用解决方案模板批量将数据从 Azure Data Lake Storage Gen2 复制到 Azure Synapse Analytics/Azure SQL 数据库。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921140"
---
# <a name="bulk-copy-from-files-to-database"></a>从文件大容量复制到数据库

本文介绍了一个解决方案模板，你可以使用该模板将数据批量从 Azure Data Lake Storage Gen2 复制到 Azure Synapse Analytics/Azure SQL 数据库。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板从源检索文件 Azure Data Lake Storage Gen2。 然后，它将循环访问源中的每个文件，并将文件复制到目标数据存储区。 

此模板当前仅支持以**DelimitedText**格式复制数据。 还可以从源数据存储中检索其他数据格式的文件，但不能将这些文件复制到目标数据存储。  

该模板包含三个活动：
- **获取元数据**活动从 Azure Data Lake Storage Gen2 检索文件，并将其传递给后续的*ForEach*活动。
- **ForEach**活动从*Get Metadata*活动获取文件，并将每个文件迭代到*复制*活动。
- **复制**活动驻留在*ForEach*活动中，以便将源数据存储中的每个文件复制到目标数据存储区。

该模板定义以下两个参数：
- *SourceContainer*是从 Azure Data Lake Storage Gen2 中的数据复制到的根容器路径。 
- *SourceDirectory*是根容器下的目录路径，从 Azure Data Lake Storage Gen2 中的数据复制到该容器。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 请参阅**从文件到数据库的大容量复制**模板。 创建与源 Gen2 存储区的**新**连接。 请注意，"GetMetadataDataset" 和 "SourceDataset" 是对源文件存储的相同连接的引用。

    ![创建与源数据存储区的新连接](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 创建与要向其复制数据的接收器数据存储的**新**连接。

    ![创建到接收器数据存储的新连接](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. 选择“使用此模板”。

    ![使用此模板](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 你会看到创建的管道，如以下示例中所示：

    ![查看管道](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 如果在上述**步骤 2**中选择**azure Synapse Analytics （以前称为 SQL DW）** 作为数据目标，则必须根据 SQL 数据仓库 Polybase 的要求，输入与 azure Blob 存储的连接以进行过渡。 如以下屏幕截图所示，模板会自动为 Blob 存储生成*存储路径*。 检查是否已在管道运行后创建了容器。
        
    ![Polybase 设置](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. 选择 "**调试**"，输入**参数**，然后选择 "**完成**"。

    ![单击 "调试"。](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 管道运行成功完成后，会看到类似于以下示例的结果：

    ![查看结果](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)