---
title: "Azure Analysis Services 教程第 11 课：创建角色 | Microsoft Docs"
description: "介绍了在 Azure Analysis Services 教程项目中如何创建角色。"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 5fb0e2dd56e373ecf723a3672d9538bcc6dc68e3
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2018
---
# <a name="create-roles"></a>创建角色

在本课中，将创建角色。 角色通过仅允许属于角色成员的用户进行访问，提供模型数据库对象和数据安全性。 每个角色都定义有单一权限：无、读取、读取和处理、处理，或管理员。 在创作模型期间，可以使用角色管理器来定义角色。 在部署模型后，可以使用 SQL Server Management Studio (SSMS) 来管理角色。 若要了解详细信息，请参阅[角色](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular)。
  
> [!NOTE]  
> 创建角色不是完成本教程所必需的。 默认情况下，当前用来登录的帐户具有对模型的管理员权限。 不过，若要允许组织中的其他用户使用某个报告客户端来进行浏览，则必须至少创建一个具有读取权限的角色并将那些用户添加为其成员。  
  
需创建三个角色：  
  
-   **Sales Manager** – 此角色可以包括组织中希望其对所有模型对象和数据具有读取权限的用户。  
  
-   **Sales Analyst US** – 此角色可以包括组织中希望其只能浏览与在美国的销售相关的数据的用户。 对于此角色，使用 DAX 公式来定义行筛选器，该筛选器将成员限制为只能浏览有关美国的数据。  
  
-   **Administrator** – 此角色可以包括希望其具有管理员权限的用户，具有管理员权限时可以不受限制地进行访问并且有权对模型数据库执行管理任务。  
  
因为组织中的 Windows 用户和组帐户具有唯一性，所以可以将特定组织中的帐户添加为成员。 不过，对于本教程，也可以将成员保留为空。 可在后面的“第 12 课：在 Excel 中分析”中测试每个角色的效果。  
  
本课预计完成时间：**15 分钟**  
  
## <a name="prerequisites"></a>系统必备  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 10 课：创建分区](../tutorials/aas-lesson-10-create-partitions.md)。  
  
## <a name="create-roles"></a>创建角色  
  
#### <a name="to-create-a-sales-manager-user-role"></a>创建 Sales Manager 用户角色  
  
1.  在表格模型资源管理器中，右键单击“角色”，并单击“角色”。 >   
  
2.  在角色管理器中，单击“新建”。  
  
3.  单击新角色，然后在“名称”列中将角色重命名为“销售经理”。  
  
4.  在“权限”列中，单击下拉列表，并选择“读取”权限。 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  可选：单击“成员”选项卡，并单击“添加”。 在“选择用户或组”对话框中，输入组织中希望将其包括在该角色中的 Windows 用户或组。  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>创建 Sales Analyst US 用户角色  
  
1.  在角色管理器中，单击“新建”。    
  
2.  将角色重命名为“Sales Analyst US”。  
  
3.  向此角色授予“读取”权限。  
  
4.  单击“行筛选器”选项卡，然后仅针对“DimGeography”表在“DAX 筛选器”列中键入以下公式：  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    行筛选器公式必须解析为布尔 (TRUE/FALSE) 值。 通过此公式，指定只有国家/地区区域代码值为“US”的行对用户可见。  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  可选：单击“成员”选项卡，并单击“添加”。 在“选择用户或组”对话框中，输入组织中希望将其包括在该角色中的 Windows 用户或组。  
  
#### <a name="to-create-an-administrator-user-role"></a>创建 Administrator 用户角色  
  
1.  单击“新建” 。  
  
2.  将角色重命名为“Administrator”。  
  
3.  向此角色授予“管理员”权限。  
  
4.  可选：单击“成员”选项卡，并单击“添加”。 在“选择用户或组”对话框中，输入组织中希望将其包括在该角色中的 Windows 用户或组。 
  
  
## <a name="whats-next"></a>后续步骤
[第 12 课：在 Excel 中分析](../tutorials/aas-lesson-12-analyze-in-excel.md)。

  
  
