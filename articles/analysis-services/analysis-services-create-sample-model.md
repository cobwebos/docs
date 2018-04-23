---
title: 为 Azure Analysis Services 服务器添加示例表格模型 | Microsoft Docs
description: 了解如何在 Azure Analysis Services 中添加示例模型。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>教程：添加示例模型

在本教程中，将向服务器添加示例 Adventure Works 模型。 此示例模型是 Adventure Works Internet Sales (1200) 数据建模教程的完整版本。 示例模型可用于测试模型管理、与工具和客户端应用程序进行连接，以及查询模型数据。

## <a name="before-you-begin"></a>开始之前

要完成本教程，需要：

- Azure Analysis Services 服务器
- 服务器管理员权限

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-sample-model"></a>创建示例模型

1. 在服务器“概述”中，单击“新建模型”。

    ![创建示例模型](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. 在“新建模型” > “选择数据源”中，确认已选中“示例数据”，然后单击“添加”。

    ![选择“示例数据”](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. 在“概述”中，确认是否创建了 `adventureworks` 示例。

    ![选择“示例数据”](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>清理资源

示例模型使用了缓存内存资源。 如果不使用示例模型进行测试，则应将其从服务器中删除。

> [!NOTE]
> 下面的步骤介绍了如何使用 SSMS 从服务器中删除模型。 也可以使用预览版 Web 设计器功能来删除模型。

1. 在 SSMS >“对象资源管理器”中，单击“连接” > “Analysis Services”。

2. 在“连接到服务器”中，粘贴服务器名称，然后在“身份验证”中选择“Active Directory - 支持 MFA 的通用身份验证”，输入你的用户名，然后单击“连接”。

    ![登录](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. 在“对象资源管理器”中，右键单击 `adventureworks` 示例数据库，然后单击“删除”。

    ![删除示例数据库](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>后续步骤 

[在 Power BI Desktop 中连接](analysis-services-connect-pbi.md)   
[管理数据库角色和用户](analysis-services-database-users.md)


