---
title: "开始使用专用模板 |Microsoft 文档"
description: "添加、 管理和共享专用模板使用 Azure 门户、 Azure CLI 或 PowerShell。"
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
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>开始使用 Azure 门户上的专用模板
[Azure 资源管理器](../azure-resource-manager/resource-group-authoring-templates.md)模板是用于定义你的部署的声明性模板。 你可以定义资源来部署有关的解决方案，并指定参数和变量，您可以输入不同的环境的值。 模板中包含的 JSON 和可用于构造部署值的表达式。

你可以使用新**模板**中的功能[Azure 门户](https://portal.azure.com)连同**Microsoft.Gallery**资源提供程序中的扩展作为[Azure 应用商店](https://azure.microsoft.com/marketplace/)以使用户能够创建、 管理和部署个人库中的专用模板。

本文档将指导你完成添加、 管理和共享私有**模板**使用 Azure 门户。

## <a name="guidance"></a>指导
以下建议将帮助你充分利用**模板**使用你的解决方案时：

* A**模板**是一种封装资源包含资源管理器模板和其他元数据。 它的行为非常类似于应用商店中的项。 主要区别是它是专用项而不是公共应用商店项。
* **模板**库非常适用于需要自定义其部署的用户。
* **模板**适用于需要在 Azure 中的简单存储库的用户工作。
* 开始使用现有的资源管理器模板。 查找中的模板[github](https://github.com/Azure/azure-quickstart-templates)或[导出模板](../azure-resource-manager/resource-manager-export-template.md)从现有资源组。
* **模板**绑定到的用户将其发布。 发布服务器名称是对有权读取它的每个人可见。
* **模板**是资源管理器资源，不能一次发布重命名。

## <a name="add-a-template-resource"></a>添加模板资源
有两种方法来创建**模板**在 Azure 门户中的资源。

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>方法 1： 从正在运行的资源组中创建新的模板资源
1. 导航到 Azure 门户上的现有资源组。 选择**导出模板**中**设置**。
2. 资源管理器模板导出后，使用**另存为模板**按钮以将其保存到**模板**存储库。 找到完整的详细信息导出模板[此处](../azure-resource-manager/resource-manager-export-template.md)。
   <br /><br />
   ![资源组导出](media/rg-export-portal1.PNG)  <br />
3. 选择**将保存到模板**命令按钮。
   <br /><br />
4. 输入以下信息：
   
   * 名称 – 模板对象的名称 (请注意： 这是基于 Azure 资源管理器名称。 所有命名限制，而且无法更改一次创建）。
   * 说明 – 有关该模板的快速摘要。
     
     ![保存模板](media/save-template-portal1.PNG)  <br />
5. 单击 **“保存”**。
   
   > [!NOTE]
   > 导出模板边栏选项卡显示通知时已导出的资源管理器模板出现错误，但你仍将能够将此资源管理器模板保存到模板。 确保你检查并修复任何资源管理器模板问题，然后重新部署已导出的资源管理器模板。
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>方法 2： 从浏览添加新的模板资源
你还可以添加一个新**模板**从暂存使用 + 中的添加命令按钮**浏览 > 模板**。 你将需要提供名称、 描述和资源管理器模板 JSON。

![添加模板](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery 是租户基于 Azure 资源提供程序。 模板资源与创建它的用户。 它位于未与任何特定的订阅。 订阅需要仅在部署模板时，才选择。
> 
> 

## <a name="view-template-resources"></a>查看模板资源
所有**模板**供可以观察到的**浏览 > 模板**。 这包括**模板**已创建以及那些使用不同的权限级别与你共享。 中的更多详细信息[访问控制](#access-control-for-a-tenant-resource-provider)下面一节。

![查看模板](media/view-template-portal1.PNG)  <br />

你可以查看的详细信息**模板**通过单击列表中的一个项。

![查看模板](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>编辑模板资源
你可以启动的编辑流**模板**通过右键单击浏览列表上的项或通过选择编辑命令按钮。

![编辑模板](media/edit-template-portal1a.PNG)  <br />

你可以编辑说明或资源管理器模板文本。 您无法编辑该名称，因为它是资源管理器资源名称。 当您编辑资源管理器模板 JSON 我们将对其进行验证以确保它是有效的 JSON。 选择**确定**然后**保存**以保存你更新的模板。

![编辑模板](media/edit-template-portal2a.PNG)  <br />

一次**模板**保存你将看到确认通知。

![编辑模板](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>部署模板资源
你可以部署任何**模板**必须**读取**上的权限。 部署流将启动标准 Azure 模板部署边栏选项卡。 填写要继续进行部署的资源管理器模板参数的值。

![部署模板](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>共享的模板资源
A**模板**可以与你的同事共享资源。 共享的行为方式类似于[Azure 上的任何资源的角色分配](../active-directory/role-based-access-control-configure.md)。 **模板**所有者提供给其他用户可以进行交互的模板资源的权限。 个人或组共享的人员**模板**与将能够看到资源管理器模板和其库属性。

### <a name="access-control-for-the-microsoftgallery-resources"></a>Microsoft.Gallery 资源的访问控制
| 角色 | 权限 |
| --- | --- |
| 所有者 |允许包括共享的模板资源的完全控制 |
| 读取器 |允许读取和 Execute(Deploy) 上的模板资源 |
| Contributor |允许在模板资源上编辑和删除权限。 用户不能与其他人共享的模板 |

选择**共享**浏览项通过右键单击或上的特定项的视图边栏选项卡上。 这将启动共享体验。

![共享模板](media/share-template-portal1a.png)  <br />

 你现在可以选择一个角色的用户或组，以提供对特定的访问**模板**。 可用的角色是所有者、 读取和参与者。 中的更多详细信息[访问控制](#access-control-for-a-tenant-resource-provider)上面一节。

![共享模板](media/share-template-portal2b.png)  <br />

![共享模板](media/share-template-portal3b.png)  <br />

单击**选择**和**确定**。 你现在可以看到的用户或组添加到资源。

![共享模板](media/share-template-portal4b.png)  <br />

> [!NOTE]
> 在相同的 Azure Active Directory 租户中仅与用户和组共享一个模板。 如果你不在你的租户的电子邮件地址共用一个模板，邀请将发送询问用户加入作为来宾租户。
> 
> 

## <a name="next-steps"></a>后续步骤
* 若要了解如何创建资源管理器模板，请参阅[创作模板](../azure-resource-manager/resource-group-authoring-templates.md)
* 若要了解可以使用资源管理器模板中的函数，请参阅[模板函数](../azure-resource-manager/resource-group-template-functions.md)
* 有关如何设计你的模板的指南，请参阅[用于设计 Azure 资源管理器模板的最佳做法](../azure-resource-manager/best-practices-resource-manager-design-templates.md)

