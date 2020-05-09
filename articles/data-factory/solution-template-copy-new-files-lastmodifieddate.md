---
title: 按 LastModifiedDate 复制新文件和更改的文件
description: 了解如何在 Azure 数据工厂中使用解决方案模板根据 LastModifiedDate 复制新的和已更改的文件。
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
ms.openlocfilehash: 363af1012b327a55703cc6e35a02dc1f27869bd0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629059"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>使用 Azure 数据工厂根据 LastModifiedDate 复制新的和已更改的文件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍一个解决方案模板。使用该模板可以根据 LastModifiedDate，仅将新的和已更改的文件从基于文件的存储复制到目标存储。 

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板首先根据新文件和已更改文件的 **LastModifiedDate** 属性选择这些文件（且仅限这些文件），然后将所选的文件从数据源存储复制到数据目标存储。

该模板包含一个活动：
- **Copy** - 仅根据新文件和已更改文件的 LastModifiedDate 将其从文件存储复制到目标存储。

该模板定义六个参数：
-  *FolderPath_Source* 是可从中读取源存储中的文件的文件夹路径。 需将默认值替换为自己的文件夹路径。
-  *Directory_Source* 是可从中读取源存储中的文件的子文件夹路径。 需将默认值替换为自己的子文件夹路径。
-  *FolderPath_Destination* 是目标存储中要将文件复制到的文件夹路径。 需将默认值替换为自己的文件夹路径。
-  *Directory_Destination* 是目标存储中要将文件复制到的子文件夹路径。 需将默认值替换为自己的子文件夹路径。
-  *LastModified_From* 用于选择其 LastModifiedDate 属性迟于或等于此日期时间值的文件。  若要仅选择上次未复制的新文件，此日期时间值可以是上次触发管道的时间。 可将默认值“2019-02-01T00:00:00Z”替换为 UTC 时区中的预期 LastModifiedDate。 
-  *LastModified_To* 用于选择其 LastModifiedDate 属性早于此日期时间值的文件。 若要仅选择上次未复制的新文件，此日期时间值可以是当前时间。  可将默认值“2019-02-01T00:00:00Z”替换为 UTC 时区中的预期 LastModifiedDate。 

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到模板“仅根据 LastModifiedDate 复制新文件”。  创建与源存储的**新**连接。 源存储是要从中复制文件的存储。

    ![与源建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 与目标存储建立**新的**连接。 目标存储是要将文件复制到的存储。 

    ![与目标建立新的连接](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. 选择“使用此模板”  。

    ![使用此模板](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. 面板中会显示可用的管道，如以下示例所示：

    ![显示管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. 选择“调试”，写入“参数”的值，然后选择“完成”。     下图显示了我们的参数设置。
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = subfolder
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = subfolder
   - **LastModified_From** =  2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    此示例表明，最后一次在 **2019-02-01T00:00:00Z** 到 **2019年-03-01T00:00:00Z** 时间跨度内修改的文件将从源路径 **sourcefolder/subfolder** 复制到目标路径 **destinationfolder/subfolder**。  可将这些参数替换为你自己的参数。

    ![运行管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. 查看结果。 你将看到，只有其上次修改时间处于配置的时间跨度内的文件已复制到目标存储。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. 现在，可以添加一个翻转窗口触发器来自动执行此管道，使管道始终可以定期根据 LastModifiedDate，仅复制新文件和已更改的文件。  依次选择“添加触发器”、“新建/编辑”。  

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. 在“添加触发器”窗口中，选择“+ 新建”。  

9. 选择“翻转窗口”作为触发器类型，设置“每隔 15 分钟”作为重复周期（可更改为任意间隔时间）。   对于“已激活”框，请选择“是”，然后选择“确定”。  

    ![创建触发器](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. 按如下所示设置“触发器运行参数”的值，然后选择“完成”。  
    - **FolderPath_Source** = **sourcefolder**。  可替换为源数据存储中的文件夹。
    - **Directory_Source** = **subfolder**。  可替换为源数据存储中的子文件夹。
    - **FolderPath_Destination** = **destinationfolder**。  可替换为目标数据存储中的文件夹。
    - **Directory_Destination** = **subfolder**。  可替换为目标数据存储中的子文件夹。
    - **LastModified_From** =   **\@trigger().outputs.windowStartTime**。  这是触发器中的一个系统变量，确定上次触发管道的时间。
    - **LastModified_To** =  **\@trigger().outputs.windowEndTime**。  这是触发器中的一个系统变量，确定本次触发管道的时间。
    
    ![输入参数](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. 选择“全部发布”。 
    
    ![全部发布](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. 在数据源存储的源文件夹中创建新文件。  现在，等待自动触发的管道，并仅将新文件复制到目标存储。

13. 在左侧导航面板中选择“监视”选项卡；如果触发器的重复周期设置为每隔 15 分钟，请等待大约 15 分钟。  

14. 查看结果。 你将看到，管道每隔 15 分钟自动触发，并且在每个管道运行中，只会将源存储中新的或已更改的文件复制到目标存储。

    ![查看结果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
