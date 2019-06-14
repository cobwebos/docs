---
title: 将新的和已更改文件通过使用 Azure 数据工厂 LastModifiedDate 复制 |Microsoft Docs
description: 了解如何使用解决方案模板将通过使用 Azure 数据工厂 LastModifiedDate 的新功能和更改文件复制。
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
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312651"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>通过使用 Azure 数据工厂 LastModifiedDate 的新功能和更改文件复制

本文介绍可用于将新的和已更改文件只能由 LastModifiedDate 基于文件的存储复制到目标存储的解决方案模板。 

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板第一次选择新的和已更改文件仅由其属性**LastModifiedDate**，然后将这些所选的文件从数据源存储复制到数据目标存储区。

该模板包含一个活动：
- **复制**将仅由 LastModifiedDate 的新功能和更改文件从文件存储复制到目标存储区。

模板定义四个参数：
-  *FolderPath_Source*是其中源存储中读取文件的文件夹路径。 需要默认值替换为你自己的文件夹路径。
-  *FolderPath_Destination*是你想要将文件复制到目标存储区的文件夹路径。 需要默认值替换为你自己的文件夹路径。
-  *LastModified_From*用于选择其 LastModifiedDate 属性是后的文件或等于该日期时间值。  若要选择的新文件，不具有已复制上一次，该日期时间值可以是管道时触发最后一次的时间。 您可以替换默认值"2019年-02-01T00:00:00Z 到您预期 LastModifiedDate UTC 时区中。 
-  *LastModified_To*用于选择其 LastModifiedDate 特性是在该日期时间值之前的文件。 若要选择的新文件，不具有已复制上一次，该日期时间值可以是当前时间。  您可以替换默认值"2019年-02-01T00:00:00Z 到您预期 LastModifiedDate UTC 时区中。 

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到模板**复制新文件只能由 LastModifiedDate**。 创建**新建**到源存储存储的连接。 源存储存储是你想要从中复制文件。

    ![与源建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 首先选择的存储空间**类型**。 输入存储之后**帐户名**并**帐户密钥**。 最后，选择**完成**。

    ![输入连接字符串](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. 创建**新建**连接到目标存储区。 目标存储区是你想要将文件复制到。 因为你未在第 2 步，您还需要输入类似的数据目标存储区的连接信息。

    ![与目标建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. 选择**使用此模板**。

    ![使用此模板](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. 面板中会显示可用的管道，如以下示例所示：

    ![显示管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. 选择**调试**，写入的值**参数**，然后选择**完成**。  在下图中，我们可以将参数设置如下所示。
   - **FolderPath_Source** =  **/source/**
   - **FolderPath_Destination** =  **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     该示例，它指示的文件的上次修改之间的时间跨度内*2019年-02-01T00:00:00Z*并*2019年-03-01T00:00:00Z*将从文件夹复制 */source/* 的文件夹 */destination/* 。  可以将其自己的参数替换为。
    
     ![运行管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. 查看结果。 你将看到上次修改时间内的已配置文件已将 timespan 复制到目标存储区。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. 现在可以添加翻转窗口触发器来自动执行此管道，以便管道可以始终会定期复制只能由 LastModifiedDate 的新功能和更改文件。  选择**添加触发器**，然后选择**新建/编辑**。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. 在“添加触发器”窗口中，选择“+ 新建”。  

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. 选择**Tumbling Window**对于触发器类型，设置**每隔 15 分钟**作为定期 （可以更改为任何间隔时间），然后选择**下一步**。

    ![创建触发器](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. 写入的值**触发器运行参数**作为以下内容，然后选择**完成**。
    - **FolderPath_Source** =  **/source/** 。  您可以替换为你的源数据存储中的文件夹。
    - **FolderPath_Destination** =  **/destination/** 。  您可以替换为你在目标数据存储中的文件夹。
    - **LastModified_From** =   **@trigger（)。 outputs.windowStartTime**。  它是系统变量确定当最后一次触发管道的时间的触发器中。
    - **LastModified_To** =  **@trigger（)。 outputs.windowEndTime**。  它是系统变量确定管道触发这一次时的时间的触发器中。
    
    ![输入参数](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. 选择“全部发布”。 
    
    ![全部发布](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. 数据源存储在源文件夹中创建新文件。  你现在正在等待自动触发的管道，并仅将新文件将复制到目标存储区。

14. 选择**监视**选项卡上，在左侧的导航窗格中，并等待大约 15 分钟，如果触发器的重复周期设置为每隔 15 分钟。 

    ![选择监视](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. 查看结果。 你将看到将自动触发每隔 15 分钟，你的管道，并且仅新增或更改存储中的文件源将复制到目标存储区中每个管道运行。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)