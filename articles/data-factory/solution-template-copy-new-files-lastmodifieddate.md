---
title: 按 LastModifiedDate 复制新文件和更改的文件
description: 了解如何使用解决方案模板通过 Azure 数据工厂 LastModifiedDate 复制新文件和更改的文件。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 971d311dfb54d417a8f66f504d01f08f8bcfc48b
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941992"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>使用 Azure 数据工厂通过 LastModifiedDate 复制新文件和更改的文件

本文介绍了一个解决方案模板，你可以使用该模板将新文件和更改的文件从基于文件的存储中 LastModifiedDate 复制到目标存储。 

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板首先仅通过属性**LastModifiedDate**选择新文件和更改的文件，然后将这些选定文件从数据源存储复制到数据目标存储。

该模板包含一个活动：
- **复制**以将新文件和更改的文件仅从文件存储区中的 LastModifiedDate 复制到目标存储。

该模板定义六个参数：
-  *FolderPath_Source*是文件夹路径，可以从中读取源存储区中的文件。 您需要用您自己的文件夹路径替换默认值。
-  *Directory_Source*是子文件夹路径，可以从中读取源存储区中的文件。 您需要用您自己的子文件夹路径替换默认值。
-  *FolderPath_Destination*是要将文件复制到目标存储的文件夹路径。 您需要用您自己的文件夹路径替换默认值。
-  *Directory_Destination*是要将文件复制到目标存储的目标文件夹路径。 您需要用您自己的子文件夹路径替换默认值。
-  *LastModified_From*用于选择其 LastModifiedDate 属性晚于或等于此 datetime 值的文件。  为了仅选择最近一次未复制的新文件，此 datetime 值可以是上次触发管道的时间。 可以将默认值 "2019-02-01T00：00： 00Z" 替换为 UTC 时区中的预期 LastModifiedDate。 
-  *LastModified_To*用于选择其 LastModifiedDate 属性在此 datetime 值之前的文件。 为了仅选择最近一次未复制的新文件，此 datetime 值可以是当前时间。  可以将默认值 "2019-02-01T00：00： 00Z" 替换为 UTC 时区中的预期 LastModifiedDate。 

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 中转到模板**仅复制 LastModifiedDate 的新文件**。 创建到源存储存储的**新**连接。 源存储存储是要从中复制文件的位置。

    ![与源建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 创建与目标存储区的**新**连接。 目标存储是要将文件复制到的位置。 

    ![与目标建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. 选择“使用此模板”。

    ![使用此模板](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. 面板中会显示可用的管道，如以下示例所示：

    ![显示管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. 选择 "**调试**"，为**参数**编写值并选择 "**完成**"。  在下图中，我们按如下所示设置参数。
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = 子文件夹
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = 子文件夹
   - **LastModified_From** = 2019-02-01T00：00：00Z
   - **LastModified_To** = 2019-03-01T00：00：00Z
    
    该示例表明，在 timespan 中最后修改的文件（**2019-02-01T00：00： 00Z**到**2019-03-03-01T00：00： 00Z**）将从源路径**sourcefolder/子**文件夹复制到目标路径**destinationfolder/子文件夹**。  可以将这些参数替换为自己的参数。

    ![运行管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. 查看结果。 你将仅看到在配置的 timespan 内最后修改的文件已复制到目标存储区。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. 现在，你可以添加翻转 windows 触发器来自动执行此管道，使管道始终可以定期仅通过 LastModifiedDate 复制新文件和更改的文件。  选择 "**添加触发器**"，然后选择 "**新建/编辑**"。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. 在“添加触发器”窗口中，选择“+ 新建”。

9. 为触发器类型选择**翻转窗口**，将**每隔15分钟**设置为重复周期（可以更改为任意间隔时间）。 选择 **"** 已激活" 复选框，然后选择 **"确定"** 。

    ![创建触发器](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. 按如下所示设置**触发器运行参数**的值，然后选择 "**完成**"。
    -  = **sourcefolder** **FolderPath_Source** 。  您可以将替换为您在源数据存储区中的文件夹。
    - **Directory_Source** = **子文件夹**。  你可以将替换为源数据存储中的子文件夹。
    -  = **destinationfolder** **FolderPath_Destination** 。  您可以将替换为您在目标数据存储区中的文件夹。
    - **Directory_Destination** = **子文件夹**。  你可以将替换为目标数据存储中的子文件夹。
    - **LastModified_From** =   **\@触发器（）。 .windowstarttime**。  它是触发器中用于确定上次触发管道的时间的系统变量。
    - **LastModified_To** =  **\@触发器（）。 .windowendtime**。  它是触发器的系统变量，用于确定此时间触发管道的时间。
    
    ![输入参数](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. 选择“全部发布”。
    
    ![全部发布](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. 在数据源存储的源文件夹中创建新文件。  你现在正在等待自动触发管道，并且只有新文件会被复制到目标存储中。

13. 选择左侧导航面板中的 "**监视器**" 选项卡，如果触发器的重复周期设置为每15分钟，则等待大约15分钟。 

14. 查看结果。 你将看到，每15分钟会自动触发一次管道，并且只有源存储中的新文件或已更改的文件将复制到每个管道运行的目标存储中。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
