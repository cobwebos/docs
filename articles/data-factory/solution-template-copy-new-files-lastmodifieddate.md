---
title: 按 LastModifiedDate 复制新文件和更改的文件
description: 了解如何使用解决方案模板通过 Azure 数据工厂 LastModifiedDate 复制新文件和更改的文件。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: ca752fb75b8e151de925d3b5604a7e7182d82e92
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896293"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>使用 Azure 数据工厂通过 LastModifiedDate 复制新文件和更改的文件

本文介绍了一个解决方案模板，你可以使用该模板将新文件和更改的文件从基于文件的存储中 LastModifiedDate 复制到目标存储。 

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板首先仅通过属性**LastModifiedDate**选择新文件和更改的文件，然后将这些选定文件从数据源存储复制到数据目标存储。

该模板包含一个活动：
- **复制**以将新文件和更改的文件仅从文件存储区中的 LastModifiedDate 复制到目标存储。

该模板定义四个参数：
-  *FolderPath_Source*是文件夹路径，可以从中读取源存储区中的文件。 您需要用您自己的文件夹路径替换默认值。
-  *FolderPath_Destination*是要将文件复制到目标存储的文件夹路径。 您需要用您自己的文件夹路径替换默认值。
-  *LastModified_From*用于选择其 LastModifiedDate 属性晚于或等于此 datetime 值的文件。  为了仅选择最近一次未复制的新文件，此 datetime 值可以是上次触发管道的时间。 可以将默认值 "2019-02-01T00：00： 00Z" 替换为 UTC 时区中的预期 LastModifiedDate。 
-  *LastModified_To*用于选择其 LastModifiedDate 属性在此 datetime 值之前的文件。 为了仅选择最近一次未复制的新文件，此 datetime 值可以是当前时间。  可以将默认值 "2019-02-01T00：00： 00Z" 替换为 UTC 时区中的预期 LastModifiedDate。 

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 中转到模板**仅复制 LastModifiedDate 的新文件**。 创建到源存储存储的**新**连接。 源存储存储是要从中复制文件的位置。

    ![与源建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 首先选择存储**类型**。 完成后，输入存储**帐户名称**和**帐户密钥**。 最后，选择 "**完成**"。

    ![输入连接字符串](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. 创建与目标存储区的**新**连接。 目标存储是要将文件复制到的位置。 还需要输入数据目标存储的连接信息，类似于在步骤2中执行的操作。

    ![与目标建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. 选择“使用此模板”。

    ![使用此模板](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. 面板中会显示可用的管道，如以下示例所示：

    ![显示管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. 选择 "**调试**"，为**参数**编写值并选择 "**完成**"。  在下图中，我们按如下所示设置参数。
   - **FolderPath_Source** =  **/source/**
   - **FolderPath_Destination** =  **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     该示例指示在*2019-02-01T00：00： 00Z*和*2019-03-01T00：00： 00Z*之间的 timespan 内最后修改的文件将从文件夹 */source/* 复制到文件夹 */destination/* 。  可以将这些参数替换为自己的参数。
    
     ![运行管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. 查看结果。 你将仅看到在配置的 timespan 内最后修改的文件已复制到目标存储区。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. 现在，你可以添加翻转 windows 触发器来自动执行此管道，使管道始终可以定期仅通过 LastModifiedDate 复制新文件和更改的文件。  选择 "**添加触发器**"，然后选择 "**新建/编辑**"。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. 在“添加触发器”窗口中，选择“+ 新建”。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. 为触发器类型选择**翻转窗口**，将**每隔15分钟**设置为定期（可以更改为任意间隔时间），然后选择 "**下一步**"。

    ![创建触发器](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. 按如下所示为**触发器运行参数**编写值，然后选择 "**完成**"。
    -  =  **/source/** **FolderPath_Source** 。  您可以将替换为您在源数据存储区中的文件夹。
    -  =  **/destination/** **FolderPath_Destination** 。  您可以将替换为您在目标数据存储区中的文件夹。
    - **LastModified_From** =   **\@触发器（）。 .windowstarttime**。  它是触发器中用于确定上次触发管道的时间的系统变量。
    - **LastModified_To** =  **\@触发器（）。 .windowendtime**。  它是触发器的系统变量，用于确定此时间触发管道的时间。
    
    ![输入参数](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. 选择“全部发布”。
    
    ![全部发布](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. 在数据源存储的源文件夹中创建新文件。  你现在正在等待自动触发管道，并且只有新文件会被复制到目标存储中。

14. 如果触发器的 "重复周期" 设置为每15分钟，请在左侧导航窗格中选择 "**监视**" 选项卡，并等待大约15分钟。 

    ![选择监视](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. 查看结果。 你将看到，每15分钟会自动触发一次管道，并且只有源存储中的新文件或已更改的文件将复制到每个管道运行的目标存储中。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
