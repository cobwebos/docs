---
title: "专用模板入门 | Microsoft Docs"
description: "使用 Azure 门户、Azure CLI 或 PowerShell 添加、管理和共享专用模板。"
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cada217a967597ecb44d84c469367157c7bc785e


---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Azure 门户中的专用模板入门
[Azure Resource Manager](../resource-group-authoring-templates.md) 模板是声明性模板，用于定义部署。 可以定义要为解决方案部署的资源，以及指定可让用户根据不同的环境输入值的参数和变量。 模板中包含可用于构造部署值的 JSON 和表达式。

可以将 [Azure 门户](https://portal.azure.com)中新的**模板**功能与作为 [Azure 应用商](https://azure.microsoft.com/marketplace/)店扩展的 **Microsoft.Gallery** 资源提供程序一起使用，使用户能够从个人库创建、管理和部署专用模板。

本文介绍如何使用 Azure 门户添加、管理和共享专用 **模板** 。

## <a name="guidance"></a>指南
以下建议可帮助用户在使用解决方案时充分利用 **模板** ：

* **模板** 是一种封装的资源，其中包含 Resource Manager 模板和其他元数据。 它的行为非常类似于应用商店中的项目。 关键区别是，与公共应用商店项目完全不同，它是私有项目。
* **模板** 库非常适合需要自定义部署的用户。
* **模板** 非常适合在 Azure 中需要简单存储库的用户。
* 从现有的 Resource Manager 模板开始。 在 [github](https://github.com/Azure/azure-quickstart-templates) 中查找模板，或者从现有的资源组[导出模板](../resource-manager-export-template.md)。
* **模板** 与发布它们的用户相关联。 对模板具有读取权限的所有用户都可以看到发布者名称。
* **模板** 是 Resource Manager 资源，发布之后便不能重命名。

## <a name="add-a-template-resource"></a>添加模板资源
有两种方法可在 Azure 门户中创建 **模板** 资源。

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>方法 1：从正在运行的资源组新建模板资源
1. 导航到 Azure 门户中的现有资源组。 在“设置”中选择“导出模板”。
2. 导出 Resource Manager 模板后，使用“保存模板”按钮将其保存到“模板”存储库。 可在 [此处](../resource-manager-export-template.md)找到有关导出模板的完整详细信息。
   <br /><br />
   ![导出资源组](media/rg-export-portal1.PNG)  <br />
3. 选择“保存到模板”  命令按钮。
   <br /><br />
4. 输入以下信息：
   
   * 名称 - 模板对象的名称（请注意：这是基于 Azure Resource Manager 的名称。 所有命名限制均适用，而且创建后便不能更改）。
   * 说明 - 有关模板的快速摘要。
     
     ![保存模板](media/save-template-portal1.PNG)  <br />
5. 单击“保存” 。
   
   > [!NOTE]
   > 当导出的 Resource Manager 模板有错误时，“导出模板”边栏选项卡将显示通知，但用户仍能够将此 Resource Manager 模板保存到“模板”。 在重新部署导出的 Resource Manager 模板之前，请确保检查并修复任何 Resource Manager 模板问题。
   > 
   > 

### <a name="b-method-2-add-a-new-template-resource-from-browse"></a>B. 方法 2：通过浏览新增模板资源
还可以使用“浏览”>“模板”中的“+添加”命令按钮从头开始新增一个模板。 需要提供名称、说明和 Resource Manager 模板 JSON。

![添加模板](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery 是基于租户的 Azure 资源提供程序。 模板资源与创建它的用户相关联， 而不与任何特定订阅相关联。 仅在部署模板时，才需要选择订阅。
> 
> 

## <a name="view-template-resources"></a>查看模板资源
在“浏览”>“模板”中可以看到用户可用的所有模板。 这包括用户已创建的**模板**以及在各种权限级别与用户共享的模板。 在下面的[访问控制](#access-control-for-a-tenant-resource-provider)部分中提供更多详细信息。

![查看模板](media/view-template-portal1.PNG)  <br />

单击列表中的项目即可查看 **模板** 的详细信息。

![查看模板](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>编辑模板资源
右键单击“浏览”列表中的项目或选择“编辑”命令按钮即可启动 **模板** 的编辑流程。

![编辑模板](media/edit-template-portal1a.PNG)  <br />

可以编辑说明或 Resource Manager 模板文本。 不能编辑名称，因为它是 Resource Manager 资源名称。 用户编辑 Resource Manager 模板 JSON 时，我们将进行验证以确保它是有效的 JSON。 选择“确定”，然后单击“保存”保存已更新的模板。

![编辑模板](media/edit-template-portal2a.PNG)  <br />

保存 **模板** 后，用户将看到确认通知。

![编辑模板](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>部署模板资源
用户可以部署他具有**读取**权限的任何**模板**。 部署流将启动标准的“Azure 模板部署”边栏选项卡。 填写 Resource Manager 模板参数的值，以便继续进行部署。

![部署模板](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>共享模板资源
可以与同事共享 **模板** 资源。 共享行为类似于 [Azure 上的任何资源的角色分配](../active-directory/role-based-access-control-configure.md)。 **模板** 所有者向可以与模板资源进行交互的其他用户提供权限。 与之共享 **模板** 的用户或用户组将能够看到 Resource Manager 模板及其库属性。

### <a name="access-control-for-the-microsoftgallery-resources"></a>对 Microsoft.Gallery 资源的访问控制
| 角色 | 权限 |
| --- | --- |
| 所有者 |允许对模板资源进行完全控制，包括共享 |
| 读取器 |允许对模板资源进行读取和执行（部署）操作 |
| 参与者 |允许对模板资源具有编辑和删除权限。 用户不能与其他人共享模板 |

通过右键单击或在特定项的视图边栏选项卡上可对浏览项选择“共享”  。 这将启动共享体验。

![共享模板](media/share-template-portal1a.png)  <br />

 现在可以选择角色和用户或组，以提供对特定 **模板**的访问权限。 可用的角色是“所有者”、“读者”和“参与者”。 在下面的 [访问控制](#access-control-for-a-tenant-resource-provider) 部分中提供更多详细信息。

![共享模板](media/share-template-portal2b.png)  <br />

![共享模板](media/share-template-portal3b.png)  <br />

单击“选择”和“确定”。 现在可以看到已添加到资源的用户或组。

![共享模板](media/share-template-portal4b.png)  <br />

> [!NOTE]
> 只能与同一 Azure Active Directory 租户中的用户和组共享模板。 如果与不在同一租户中的电子邮件地址共享模板，将发送邀请请求该用户以来宾身份加入该租户。
> 
> 

## <a name="next-steps"></a>后续步骤
* 若要了解有关创建 Resource Manager 模板的信息，请参阅 [创作模板](../resource-group-authoring-templates.md)
* 若要了解可在 Resource Manager 模板中使用的函数，请参阅 [模板函数](../resource-group-template-functions.md)
* 有关如何设计你的模板的指南，请参阅 [设计 Azure 资源管理器模板的最佳实践](../best-practices-resource-manager-design-templates.md)




<!--HONumber=Nov16_HO2-->


