---
title: 在基于文件的存储之间移动数据
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
ms.openlocfilehash: 81f072822226e4a573cf0086cac7e64ca1cfe45f
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628157"
---
# <a name="move-files-with-azure-data-factory"></a>使用 Azure 数据工厂移动文件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍一个解决方案模板，使用它可以在基于文件的存储之间，将一个文件夹中的文件移到另一个文件夹。 此模板的常见使用场景之一：不断地将文件放入源存储的登陆文件夹。 通过创建计划触发器，ADF 管道可以定期将这些文件从源存储移到目标存储。  ADF 管道实现“移动文件”的方式是从登陆文件夹中获取文件，将每个文件复制到目标存储中的另一个文件夹，然后从源存储中的登陆文件夹删除相同的文件。

> [!NOTE]
> 请注意，此模板旨在移动文件，而不是移动文件夹。  如果通过以下方式移动文件夹，则需要十分谨慎：更改数据集，使其仅包含文件夹路径，然后使用复制活动和删除活动引用表示某个文件夹的同一数据集。 因为必须确保在复制操作和删除操作之间不会有新文件进入文件夹。 如果在 Copy 活动刚完成复制作业，但 Delete 活动尚未开始时有新文件进入文件夹，则 Delete 活动可能将通过删除整个文件夹来删除尚未复制到目标的此新文件。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板从基于源文件的存储中获取文件。 然后，它将每个文件移到目标存储。

该模板包含五个活动：
- **GetMetadata** 获取对象列表，其中包括源存储上的文件夹中的文件和子文件夹。 它不会以递归方式检索对象。 
- **Filter** 筛选 **GetMetadata** 活动中的对象列表，以仅选择文件。 
- **ForEach** 获取 **Filter** 活动提供的文件列表，然后循环访问该列表并将每个文件传递到 Copy 活动和 Delete 活动。
- **Copy** 将源中的一个文件复制到目标存储。
- **Delete** 从源存储中删除同一个文件。

该模板定义了四个参数：
-  SourceStore_Location 是要从中移动文件的源存储的文件夹路径。 
-  SourceStore_Directory 是要从中移动文件的源存储的子文件夹路径。
-  DestinationStore_Location 是要将文件移到的目标存储的文件夹路径。 
-  DestinationStore_Directory 是要将文件移到的目标存储的子文件夹路径。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到“移动文件”模板。  选择现有的连接，或者与要从中移动文件的源文件存储建立**新**的连接。 请注意，**DataSource_Folder** 和 **DataSource_File** 是对源文件存储的相同连接的引用。

    ![与源建立新的连接](media/solution-template-move-files/move-files1.png)

2. 选择现有的连接，或者与要将文件移到的目标文件存储建立**新**的连接。

    ![与目标建立新的连接](media/solution-template-move-files/move-files2.png)

3. 选择“使用此模板”  选项卡。
    
4. 你将看到管道，如以下示例所示：

    ![显示管道](media/solution-template-move-files/move-files4.png)

5. 选择“调试”，输入**参数**，然后选择“完成”。     参数是要从中移动文件的文件夹路径，以及要将文件移到的文件夹路径。 

    ![运行管道](media/solution-template-move-files/move-files5.png)

6. 查看结果。

    ![查看结果](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂根据 LastModifiedDate 复制新的和已更改的文件](solution-template-copy-new-files-lastmodifieddate.md)

- [使用 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)