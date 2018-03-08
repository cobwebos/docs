---
title: "使用复制向导轻松复制数据 - Azure | Microsoft Docs"
description: "了解如何使用数据工厂复制向导将数据从支持的数据源复制到接收器。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: 
editor: 
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8f618c6efd96fd74e88f56a378b9d73cfa47b8da
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>使用 Azure 数据工厂复制向导轻松复制或移动数据
> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[版本 2 中的复制活动教程文档](../quickstart-create-data-factory-dot-net.md)。 


Azure 数据工厂复制向导用于简化数据引入过程，这通常是端到端数据集成方案中的第一步。 使用 Azure 数据工厂复制向导时，不需要了解有关链接服务、数据集和管道的任何 JSON 定义。 但是，完成向导中的所有步骤后，向导会自动创建管道，将数据从所选数据源复制到所选目标。 此外，复制向导可帮助用户在创作时验证正在引入的数据，从而节省大量时间（尤其是第一次从数据源引入数据时）。 若要启动复制向导，请单击数据工厂主页上的“复制数据”磁贴。

![复制向导](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>直观的数据复制向导
通过本向导，可在数分钟内轻松地将数据从各种源移动到目标。 完成向导步骤后，会自动创建带有复制活动的管道以及依赖数据工厂实体（链接的服务和数据集）。 创建管道不需要执行任何额外步骤。   

![选择数据源](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> 有关创建示例管道将数据从 Azure Blob 复制到 Azure SQL 数据库表的分步说明，请参阅[复制向导教程](data-factory-copy-data-wizard-tutorial.md)一文。 
> 
> 

设计之初，该向导便以大数据为理念。 它能简单高效地创作数据工厂管道，这些管道利用复制数据向导可以移动数百文件夹、文件或表。 该向导支持三种功能：自动数据预览、架构捕获和映射，以及筛选数据。 

## <a name="automatic-data-preview"></a>自动数据预览
通过复制向导可查看所选数据源的部分数据，以验证该数据是否是想要复制的数据。 此外，如果源数据在文本文件中，复制向导会自动分析文本文件，以了解行和列分隔符以及架构。 

![文件格式设置](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>架构捕获和映射
某些情况下，输入数据的架构可能与输出数据的架构不匹配。 在这种情况下，需要将源架构中的列映射到目标架构中的列。 

复制向导自动将源架构中的列映射到目标架构中的列。 可以通过下拉列表（或者）指定复制数据时是否需要跳过列来覆盖映射。   

![架构映射](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>筛选数据
该向导允许筛选源数据，从而仅选择需要复制到目标/接收器数据存储的数据。 筛选能够减少复制到接收器数据存储的数据量，从而提高复制操作的吞吐量。 它能够通过使用 SQL 查询语言灵活筛选关系数据库中的数据（或）通过使用[数据工厂函数和变量](data-factory-functions-variables.md)灵活筛选 Azure Blob 文件夹中的文件。   

### <a name="filtering-of-data-in-a-database"></a>数据库中的数据筛选
在该示例中，SQL 查询使用 `Text.Format` 函数和 `WindowStart` 变量。 

![验证表达式](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure Blob 文件夹中的数据筛选
可以使用文件夹路径中的变量，从运行时基于[系统变量](data-factory-functions-variables.md#data-factory-system-variables)确定的文件夹中复制数据。 支持的变量：**{year}**、**{month}**、**{day}**、**{hour}**、**{minute}** 和 **{custom}**。 示例：inputfolder/{year}/{month}/{day}。

假设输入文件夹格式如下：

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

单击“文件或文件夹”的“浏览”按钮，找到其中一个文件夹（例如，2016-> 03-> 01-> 02），并单击“选择”。 文本框中应该会显示 `2016/03/01/02`。 现在，请用 **{year}** 代替 **2016**、**{month}** 代替 **03**、**{day}** 代替 **01**、**{hour}** 代替 **02**，并按 Tab 键。可以看到用于选择这四个变量格式的下拉列表：

![使用系统变量](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

如以下屏幕截图所示，还可以使用“自定义”变量和任何[支持格式的字符串](https://msdn.microsoft.com/library/8kb3ddd4.aspx)。 若要选择具有该结构的文件夹，请首先使用“浏览”按钮。 然后将值替换为 **{custom}**，并按 Tab 键，查看可在其中键入格式字符串的文本框。     

![使用自定义变量](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>支持多种不同的数据和对象类型
利用复制向导，可以高效移动数百个文件夹、文件或表。

![选择要从其中复制数据的表](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>计划选项
可以一次性运行复制操作，也可按计划运行（每小时、每天等）。 这两种选项都可以广泛用于跨本地、云和本地桌面复制的连接器。

一次性复制操作仅支持一次性将数据从源移动到目标。 它适用于任何大小的数据和任何受支持的格式。 通过计划的复制，可以按规定的重复周期复制数据。 可使用丰富的设置（例如，重试、超时和警报）来配置计划的复制。

![计划属性](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>后续步骤
有关使用数据工厂复制向导创建具有复制活动的管道的快速演练，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

