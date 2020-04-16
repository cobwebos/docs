---
title: 从文件向数据库进行批量复制
description: 了解如何使用解决方案模板将数据批量从 Azure 数据存储第 2 代复制到 Azure 同步分析/Azure SQL 数据库。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414866"
---
# <a name="bulk-copy-from-files-to-database"></a>从文件向数据库进行批量复制

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了一个解决方案模板，可用于将数据批量从 Azure 数据存储第 2 代复制到 Azure 同步分析/Azure SQL 数据库。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板从 Azure 数据存储源第 2 代源检索文件。 然后，它遍遍了源中的每个文件，并将文件复制到目标数据存储。 

目前，此模板仅支持以 **"分隔文本"** 格式复制数据。 也可以从源数据存储中检索其他数据格式的文件，但不能复制到目标数据存储。  

该模板包含三个活动：
- **获取元数据**活动从 Azure 数据存储第 2 代检索文件，并将它们传递到后续*的 ForEach*活动。
- **ForEach 每个**活动从 *"获取元数据"活动获取*文件，并将每个文件重新复制到 *"复制"* 活动。
- **复制**活动驻留在*ForEach*活动中，用于将每个文件从源数据存储复制到目标数据存储。

模板定义了以下两个参数：
- *源容器*是从 Azure 数据湖存储 Gen2 中复制数据的根容器路径。 
- *SourceDirectory*是根容器下的目录路径，其中从 Azure 数据湖存储 Gen2 中复制数据。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到**从文件到数据库模板的批量复制**。 创建到源 Gen2 存储**的新**连接。 请注意，"GetMetadataDataset"和"源数据集"是源文件存储的相同连接的引用。

    ![创建到源数据存储的新连接](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. 创建要将数据复制到的接收器数据存储**的新**连接。

    ![创建到接收器数据存储的新连接](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. 选择“使用此模板”****。

    ![使用此模板](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 您将看到创建管道，如以下示例所示：

    ![查看管道](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 如果在上述**步骤 2**中选择**Azure 突触分析（以前为 SQL DW）** 作为数据目标，则必须按照 SQL 数据仓库库库的要求，输入到 Azure Blob 存储进行暂存的连接。 如下图所示，模板将自动为 Blob 存储生成*存储路径*。 检查是否在管道运行后创建了容器。
        
    ![Polybase 设置](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. 选择“调试”，输入**参数**，然后选择“完成”。********

    ![单击“调试”****](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. 当管道运行成功完成时，您将看到类似于以下示例的结果：

    ![查看结果](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)