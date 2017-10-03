---
title: "在 Azure 成本管理中分配访问权限 | Microsoft Docs"
description: "使用定义对实体的访问级别的用户帐户分配对成本管理数据的访问权限。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/19/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 32e5c2c7c058267c7a2c43d5264922c8a4cab83a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---


# <a name="assign-access-to-cost-management-data"></a>分配对成本管理数据的访问权限

对成本管理数据的访问权限通过用户或实体管理提供。 Cloudyn 用户帐户确定对实体和管理功能的访问权限。 有两种类型的访问权限：管理员和用户。 除非按用户进行修改，否则管理员访问权限允许用户在 Cloudyn 门户中不受限制地使用所有功能，包括：用户管理、收件人列表管理以及对所有实体数据的根实体访问权限。 用户访问权限旨在供最终用户使用他们对实体数据拥有的访问权限来查看报表和创建报表。

实体用于反映业务组织的层次结构。 它们在 Cloudyn 中标识组织中的部门、分支机构和团队。 实体层次结构可帮助准确跟踪实体的花费。

当你注册了 Azure 协议或帐户时，拥有管理员权限的帐户会在 Cloudyn 中进行创建，以便你可以执行本教程中的所有步骤。 本教程介绍对成本管理数据（包括用户管理和实体管理）的访问权限。 学习如何：

> [!div class="checklist"]
> * 创建拥有管理员访问权限的用户
> * 创建拥有用户访问权限的用户
> * 创建实体



## <a name="create-a-user-with-admin-access"></a>创建拥有管理员访问权限的用户

虽然你已经拥有管理员访问权限，但是组织中的同事可能也需要拥有管理员访问权限。 在 Cloudyn 门户中，单击右上方的齿轮符号，然后选择“用户管理”。 单击“添加新用户”以添加新用户。

输入有关用户的必需信息。 可以将密码字段留空，以便用户可以在首次登录时设置新密码。 当你选择“通过电子邮件通知用户”时，一个包含登录信息的链接会从 Cloudyn 通过电子邮件发送给用户。 选择允许进行用户管理的权限，以便用户可以创建和修改其他用户。 收件人列表管理允许用户编辑收件人列表。

在“用户拥有管理员访问权限”下，组织的根实体处于选择状态。 保留选择根，然后保存用户信息。 选择根实体使用户不仅对树中的根实体，而且对位于其下的所有实体都拥有管理员权限。  
  ![添加拥有管理员访问权限的新用户](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>创建拥有用户访问权限的用户
需要访问成本管理数据（如仪表板和报表）的典型用户应拥有用户访问权限来查看这些内容。 创建拥有用户访问权限的新用户，这与使用管理员访问权限创建的用户类似，不过具有以下差异：

- 清除“允许用户管理”、“允许收件人列表管理”，然后清除**用户拥有管理员访问权限**列表中的所有内容。
- 在“用户拥有用户访问权限”列表中选择用户需要访问的实体。
- 还可以根据需要允许管理员访问特定实体。

![添加拥有用户访问权限的新用户](.\media\tutorial-user-access\new-user-access.png)

## <a name="create-entities"></a>创建实体

定义成本实体层次结构时，最佳做法是标识组织的结构。

构建树时，请考虑希望或需要如何查看按业务部门、成本中心、环境和销售部门分隔的成本。 Cloudyn 中的实体树由于实体继承而十分灵活。 云帐户的各个订阅会链接到特定实体。 因此，实体是多租户。 可以使用实体仅向特定用户分配对其企业部门的访问权限。 这样做可使数据保持隔离（即使是在企业的较大部分中，如子公司）。 并且数据隔离有助于进行治理。  

向 Cloudyn 注册你的 Azure 协议或帐户时，来自你的订阅的 Azure 资源数据（包括使用情况、性能、帐单和标记数据）会复制到 Cloudyn 帐户。 但是，你必须手动创建实体树。 如果你跳过 Azure 资源管理器注册，则 Cloudyn 门户中只提供帐单数据和几个资产报表。

在 Cloudyn 门户中，单击右上方的“设置”，然后“云帐户”。 从单个实体（根）开始，在根下构建实体树。 下面是树完成之后可能类似于许多 IT 组织的实体层次结构示例：

![实体树](.\media\tutorial-user-access\entity-tree.png)

在“实体”旁，单击“添加实体”。 输入有关要添加的人员或部门的信息。 “全名”和“电子邮件”字段不必与现有用户匹配。 如果要查看访问级别列表，请在帮助中搜索“添加实体”。

![添加实体](.\media\tutorial-user-access\add-entity.png)

完成后，“保存”实体。


## <a name="next-steps"></a>后续步骤

在本教程中，已学习了如何执行以下操作：

> [!div class="checklist"]
> * 创建拥有管理员访问权限的用户
> * 创建拥有用户访问权限的用户
> * 创建实体

前进到 Cloudyn 文档以详细了解 Cloudyn 入门和功能使用。

> [!div class="nextstepaction"]
> [Cloudyn 文档](https://support.cloudyn.com/hc/)

