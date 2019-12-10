---
title: 在基于文件的存储之间移动文件
description: 了解如何使用解决方案模板通过 Azure 数据工厂在基于文件的存储之间移动文件。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b3165daa06ed975df9ccb677699d3ceb449327ab
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941882"
---
# <a name="move-files-with-azure-data-factory"></a>通过 Azure 数据工厂移动文件

本文介绍可用于在基于文件的存储之间将文件从一个文件夹移到另一个文件夹的解决方案模板。 使用此模板的常见方案之一：文件会不断地删除到源存储的登陆文件夹中。 通过创建计划触发器，ADF 管道可以定期将这些文件从源迁移到目标存储。  ADF 管道实现 "移动文件" 的方式是从登陆文件夹中获取文件，将每个文件复制到目标存储中的另一个文件夹，然后从源存储中的登陆文件夹删除相同的文件。

> [!NOTE]
> 请注意，此模板旨在移动文件，而不是移动文件夹。  如果希望通过更改数据集来移动文件夹，使其仅包含文件夹路径，然后使用 "复制活动" 和 "删除" 活动引用同一数据集（表示文件夹），则需要小心。 因为必须确保在复制操作和删除操作之间不会有新文件进入文件夹。 如果在 Copy 活动刚完成复制作业，但 Delete 活动尚未开始时有新文件进入文件夹，则 Delete 活动可能将通过删除整个文件夹来删除尚未复制到目标的此新文件。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板从基于源文件的存储区中获取文件。 然后，将每个文件移动到目标存储区。

该模板包含五个活动：
- **GetMetadata**获取对象的列表，其中包括源存储区上的文件夹中的文件和子文件夹。 它不会以递归方式检索对象。 
- **Filter**筛选**GetMetadata**活动中的对象列表，以仅选择文件。 
- **ForEach**从**筛选器**活动中获取文件列表，然后循环访问该列表，并将每个文件传递给复制活动和删除活动。
- **将**源中的一个文件复制到目标存储区。
- **Delete**从源存储区中删除相同的文件。

该模板定义四个参数：
- *SourceStore_Location*是您要从中移动文件的源存储的文件夹路径。 
- *SourceStore_Directory*是您要从中移动文件的源存储的子文件夹路径。
- *DestinationStore_Location*是要将文件移动到的目标存储的文件夹路径。 
- *DestinationStore_Directory*是要将文件移动到的目标存储的子文件夹路径。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到 "**移动文件**" 模板。 选择 "现有连接" 或 "创建到你要在其中移动文件的源文件存储的**新**连接"。 请注意， **DataSource_Folder**和**DataSource_File**是对源文件存储的相同连接的引用。

    ![与源建立新的连接](media/solution-template-move-files/move-files1.png)

2. 选择 "现有连接" 或 "创建到目标文件存储的**新**连接"，以便将文件移动到该位置。

    ![与目标建立新的连接](media/solution-template-move-files/move-files2.png)

3. 选择 "**使用此模板**选项卡"。
    
4. 你会看到管道，如以下示例中所示：

    ![显示管道](media/solution-template-move-files/move-files4.png)

5. 选择 "**调试**"，输入**参数**，然后选择 "**完成**"。   参数是要将文件移入其中的文件夹路径，以及要将文件移动到的文件夹路径。 

    ![运行管道](media/solution-template-move-files/move-files5.png)

6. 查看结果。

    ![查看结果](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂通过 LastModifiedDate 复制新文件和更改的文件](solution-template-copy-new-files-lastmodifieddate.md)

- [通过 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)