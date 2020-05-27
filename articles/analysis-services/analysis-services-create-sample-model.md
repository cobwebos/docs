---
title: 教程 - 添加示例模型 - Azure Analysis Services | Microsoft Docs
description: 本教程介绍如何在 Azure Analysis Services 中添加示例模型。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 88ed58ea1eea80b4b890c31260fc65153377eaaa
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697519"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>教程：从门户添加示例模型

在本教程中，将向服务器添加示例 Adventure Works 表格模型数据库。 此示例模型是 Adventure Works Internet Sales (1200) 示例数据模型的完整版本。 示例模型可用于测试模型管理、与工具和客户端应用程序进行连接，以及查询模型数据。 本教程使用 [Azure 门户](https://portal.azure.com)和 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) 执行以下操作： 

> [!div class="checklist"]
> * 将已完成的示例表格数据模型添加到服务器 
> * 使用 SSMS 连接到模型

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>开始之前

要完成本教程，需要：

- Azure Analysis Services 服务器。 若要了解更多信息，请参阅[创建服务器 - 门户](analysis-services-create-server.md)。
- 服务器管理员权限
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录[门户](https://portal.azure.com/)。

## <a name="add-a-sample-model"></a>添加示例模型

1. 在服务器“概述”中，单击“新建模型”。

    ![创建示例模型](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. 在“新建模型” > “选择数据源”中，确认已选中“示例数据”，然后单击“添加”。   

    ![选择“示例数据”](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. 在“概述”中，确认是否添加了 `adventureworks` 示例模型。

    ![选择“示例数据”](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>清理资源

示例模型使用了缓存内存资源。 如果不使用示例模型进行测试，则应将其从服务器中删除。

下面的步骤介绍了如何使用 SSMS 从服务器中删除模型。

1. 在 SSMS >“对象资源管理器”中，单击“连接” > “Analysis Services”。

2. 在“连接到服务器”中，粘贴服务器名称，然后在“身份验证”中选择“Active Directory - 支持 MFA 的通用身份验证”，输入你的用户名，然后单击“连接”。

    ![登录](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. 在“对象资源管理器”中，右键单击 `adventureworks` 示例数据库，然后单击“删除”。 

    ![删除示例数据库](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>后续步骤 

本教程介绍了如何将基本示例模型添加到服务器。 现在，已有模型数据库，可以从 SQL Server Management Studio 连接到它，并添加用户角色。 若要了解详细信息，请继续阅读下一个教程。

> [!div class="nextstepaction"]
> [教程：配置服务器管理员和用户角色](tutorials/analysis-services-tutorial-roles.md)


