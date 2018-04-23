---
title: 专用模板入门 | Microsoft Docs
description: 使用 Azure 门户、Azure CLI 或 PowerShell 添加、管理和共享专用模板。
services: marketplace-customer
documentationcenter: ''
author: msmbaldwin
manager: asimm
editor: ''
tags: marketplace, azure-resource-manager
keywords: ''
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mbaldwin
ms.openlocfilehash: c716f54a1361d41dbad00e2e45562d5fbf8fd6ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Azure 门户中的专用模板入门
[Azure 资源管理器](../azure-resource-manager/resource-group-authoring-templates.md)模板是声明性模板，用于定义部署。 可以定义要为解决方案部署的资源，以及指定可让用户根据不同的环境输入值的参数和变量。 模板中包含可用于构造部署值的 JSON 和表达式。

可以将 [Azure 门户](https://portal.azure.com)中新的**模板**功能与作为 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 扩展的 **Microsoft.Gallery** 资源提供程序一起使用，使用户能够从个人库创建、管理和部署专用模板。

> [!NOTE]
> Microsoft 建议通过[托管应用程序](../managed-applications/overview.md)创建一个服务目录应用程序，而不是在门户中使用专用模板来这样做。 可以在将服务目录应用程序提供给组织中的用户。

本文介绍如何使用 Azure 门户添加、管理和共享专用**模板**。

## <a name="guidance"></a>指南
以下建议可帮助你在使用解决方案时充分利用**模板**：

* **模板**是一种封装的资源，其中包含资源管理器模板和其他元数据。 它的行为类似于 Marketplace 中的项目。 关键区别是，与公共 Marketplace 项目完全不同，它是私有项目。
* **模板** 库非常适合需要自定义部署的用户。
* **模板** 非常适合在 Azure 中需要简单存储库的用户。
* 从现有的 Resource Manager 模板开始。 在 [github](https://github.com/Azure/azure-quickstart-templates) 中查找模板，或者从现有的资源组[导出模板](../azure-resource-manager/resource-manager-export-template.md)。
* **模板** 与发布它们的用户相关联。 对模板具有读取权限的所有用户都可以看到发布者名称。
* **模板** 是 Resource Manager 资源，发布之后便不能重命名。

## <a name="add-a-template-resource"></a>添加模板资源
有两种方法可在 Azure 门户中创建 **模板** 资源。

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>方法 1：从正在运行的资源组新建模板资源
1. 导航到 Azure 门户中的现有资源组。 在“设置”中选择“导出模板”。
2. 导出 Resource Manager 模板后，使用“保存模板”按钮将其保存到“模板”存储库。 可在 [此处](../azure-resource-manager/resource-manager-export-template.md)找到有关导出模板的完整详细信息。
   <br /><br />
   ![导出资源组](media/rg-export-portal1.PNG)
3. 选择“保存到模板”  命令按钮。
   <br /><br />
4. 输入以下信息：
   
   * 名称 - 模板对象的名称（注意：此字段是基于 Azure 资源管理器的名称。 所有命名限制均适用，而且创建后便不能更改）。
   * 说明 - 有关模板的快速摘要。
     
     ![保存模板](media/save-template-portal1.PNG)
5. 单击“ **保存**”。
   
   > [!NOTE]
   > 当导出的资源管理器模板有错误时，门户会显示通知，但你仍然能够将此资源管理器模板保存到“模板”。 在重新部署导出的 Resource Manager 模板之前，请确保检查并修复任何 Resource Manager 模板问题。
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>方法 2：通过浏览添加新的模板资源
还可以使用“浏览”>“模板”中的“+添加”命令按钮从头开始新增一个模板。 提供名称、说明和资源管理器模板 JSON。

![添加模板](media/add-template-portal1.PNG)

> [!NOTE]
> Microsoft.Gallery 是基于租户的 Azure 资源提供程序。 模板资源与创建它的用户相关联， 而不与任何特定订阅相关联。 部署模板时，请选择一个订阅。
> 
> 

## <a name="view-template-resources"></a>查看模板资源
在“浏览”>“模板”中可以看到用户可用的所有模板。 可用模板包括已创建的模板，以及在各种权限级别与你共享的模板。 有关详细信息，请参阅[访问控制](#access-control-for-a-tenant-resource-provider)。

![查看模板](media/view-template-portal1.PNG)

单击列表中的项目即可查看 **模板** 的详细信息。

![查看模板](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>编辑模板资源
右键单击“浏览”列表中的项目或选择“编辑”命令按钮即可启动**模板**的编辑流。

![编辑模板](media/edit-template-portal1a.PNG)

可以编辑说明或 Resource Manager 模板文本。 不能编辑名称，因为它是资源管理器资源名称。 你在编辑资源管理器模板 JSON 时，我们会进行验证，确保它是有效的 JSON。 选择“确定”，并单击“保存”保存已更新的模板。

![编辑模板](media/edit-template-portal2a.PNG)

保存模板后，会看到确认通知。

![编辑模板](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>部署模板资源
用户可以部署他具有**读取**权限的任何**模板**。 填写 Resource Manager 模板参数的值，以便继续进行部署。

![部署模板](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>共享模板资源
可以与同事共享 **模板** 资源。 共享行为类似于 [Azure 上的任何资源的角色分配](../role-based-access-control/role-assignments-portal.md)。 **模板** 所有者向可以与模板资源进行交互的其他用户提供权限。 你与之共享**模板**的用户或用户组能够看到资源管理器模板及其库属性。

### <a name="access-control-for-the-microsoftgallery-resources"></a>对 Microsoft.Gallery 资源的访问控制
| 角色 | 权限 |
| --- | --- |
| 所有者 |允许对模板资源进行完全控制，包括共享 |
| 读取器 |允许对模板资源进行读取和执行（部署）操作 |
| 参与者 |允许对模板资源具有编辑和删除权限。 用户不能与其他人共享模板 |

可以通过右键单击针对浏览项选择“共享”，也可以在查看特定项时进行选择。

![共享模板](media/share-template-portal1a.png)

 现在可以选择角色和用户或组，以提供对特定 **模板**的访问权限。 可用的角色是“所有者”、“读者”和“参与者”。

![共享模板](media/share-template-portal2b.png)

![共享模板](media/share-template-portal3b.png)

单击“选择”和“确定”。 现在可以看到已添加到资源的用户或组。

![共享模板](media/share-template-portal4b.png)

> [!NOTE]
> 只能与同一 Azure Active Directory 租户中的用户和组共享模板。 如果与不在同一租户中的电子邮件地址共享模板，系统会发送一个邀请，请求该用户以来宾身份加入租户。
> 
> 

## <a name="next-steps"></a>后续步骤
* 若要了解有关创建 Resource Manager 模板的信息，请参阅 [创作模板](../azure-resource-manager/resource-group-authoring-templates.md)
* 若要了解可在资源管理器模板中使用的函数，请参阅[模板函数](../azure-resource-manager/resource-group-template-functions.md)

