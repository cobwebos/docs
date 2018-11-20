---
title: 将 Power BI Desktop 文件导入到 Azure Analysis Services 中 | Microsoft Docs
description: 介绍了如何使用 Azure 门户导入 Power BI Desktop 文件 (pbix)。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3adf0c9c2e2b264904e66b82716447d634aaeee7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209643"
---
# <a name="import-a-power-bi-desktop-file"></a>导入 Power BI Desktop 文件

可以将 Power BI Desktop 文件 (pbix) 中的数据模型导入到 Azure Analysis Services 中。 将导入模型元数据、缓存的数据以及数据源连接。 不会导入报表和可视化效果。 从 Power BI Desktop 中导入的数据模型的兼容级别为 1400。

> [!IMPORTANT]
> 此功能已弃用。 将来的更新中可能删除或大大更改此功能。 建议在新项目和现有项目中停止使用此功能，以保持与未来更新兼容。 对于更高级的模型开发和测试，最好使用 Visual Studio (SSDT) 和 SQL Server Management Studio (SSMS)。

**限制**   


- 若数据模型是在 Power BI Desktop 2018 年 7 月更新 (2.60.5169.3201) 或更高版本中所创建的，则确保未启用任何预览功能。 Azure Analysis Services 尚不支持预览功能。 若在导入时收到以下错误，则 pbix 文件启用了 Azure Analysis Services 尚不支持的预览功能。

    ![兼容性级别警告](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)   
- 必须具有服务器管理员权限才能从 pbix 文件中进行导入。
- pbix 模型只能连接到“Azure SQL 数据库”和“Azure SQL 数据仓库”数据源。
- pbix 模型不能具有实时连接或 DirectQuery 连接。 


## <a name="to-import-from-pbix"></a>从 pbix 中导入

1. 在服务器的“概述” > “Web 设计器”中，单击“打开”。

    ![在 Azure 门户中创建模型](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. 在“Web 设计器” > “模型”中，单击“+ 添加”。

    ![在 Azure 门户中创建模型](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. 在“新建模型”中，键入模型名称，然后选择 Power BI Desktop 文件。

    ![Azure 门户中的“新建模型”对话框](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. 在“导入”中，找到并选择你的文件。

     ![Azure 门户中的“连接”对话框](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>更改凭据

从 pbix 文件中导入数据模型时，默认情况下，用来连接数据源的凭据设置为 ServiceAccount。 从 pbix 中导入模型后，可以使用以下方法来更改凭据：

- 使用 2018 年 7 月版（版本 17.8.1）或更高版本的 SSMS 来编辑凭据。 这是最简单的方法。
- 对 [DataSources 对象](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl)使用 TMSL [Alter 命令](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl)来修改连接字符串属性。 
- 在 Visual Studio 中打开模型，编辑数据源连接的凭据，然后重新部署该模型。

使用 SSMS 更改凭据 

1. 在 SSMS 中，展开数据库 >“连接”。 
2. 右键单击数据库连接，然后单击“刷新凭据”。 

    ![刷新凭据](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. 在凭据对话框中，选择凭据类型，然后输入凭据。 对于 SQL 身份验证，请选择“数据库”。 对于组织帐户 (OAuth)，请选择“Microsoft 帐户”。
    ![编辑凭据](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

2018 年 7 月版的 Power BI Desktop 包括了用于更改数据源权限的一项新功能。 在“主页”选项卡上，单击“编辑查询”  > “数据源设置”。 选择数据源连接，然后单击“编辑权限”。


## <a name="see-also"></a>另请参阅

[在 Azure 门户中创建模型](analysis-services-create-model-portal.md)   
[连接到 Azure Analysis Services](analysis-services-connect.md)  
