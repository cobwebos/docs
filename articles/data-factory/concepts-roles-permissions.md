---
title: Azure 数据工厂的角色和权限 | Microsoft Docs
description: 介绍创建数据工厂并使用子资源所需的角色和权限。
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 19666eb668dd120c1705c6a62a8ba1abd2321026
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61261801"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Azure 数据工厂的角色和权限

本文介绍创建和管理 Azure 数据工厂资源所需的角色，以及由这些角色授予的权限。

## <a name="roles-and-requirements"></a>角色和要求

若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于参与者或所有者角色，或者是 Azure 订阅的管理员。    若要查看你在订阅中拥有的权限，请在 Azure 门户中，选择右上角的用户名，然后选择“权限”。  如果可以访问多个订阅，请选择相应的订阅。 

若要为数据工厂创建和管理子资源（包括数据集、链接服务、管道、触发器和集成运行时），以下要求适用：
- 若要在 Azure 门户中创建和管理子资源，你必须属于资源组级别或更高级别的**数据工厂参与者**角色。
- 若要使用 PowerShell 或 SDK 创建和管理子资源，资源级别或更高级别的**参与者**角色已足够。

有关如何将用户添加到角色的示例说明，请参阅[添加角色](../billing/billing-add-change-azure-subscription-administrator.md)一文。

## <a name="set-up-permissions"></a>设置权限

创建数据工厂之后，可能需要让其他用户使用该数据工厂。 若要将该访问权限授予其他用户，必须将这些用户添加到数据工厂所在资源组中的内置“数据工厂参与者”角色。 

### <a name="scope-of-the-data-factory-contributor-role"></a>“数据工厂参与者”角色的权限范围

“数据工厂参与者”角色中的成员身份允许用户执行以下操作： 
- 创建、编辑和删除数据工厂和子资源，包括数据集、链接服务、管道、触发器和集成运行时。
- 部署资源管理器模板。 资源管理器部署是数据工厂在 Azure 门户中使用的部署方法。
- 管理数据工厂的 App Insights 警报。
- 创建支持票证。

有关此角色的详细信息，请参阅[“数据工厂参与者”角色](../role-based-access-control/built-in-roles.md#data-factory-contributor)。

### <a name="resource-manager-template-deployment"></a>资源管理器模板部署

资源组级别或更高级别的“数据工厂参与者”  角色允许用户部署资源管理器模板。 因此，此角色的成员可以使用资源管理器模板来部署数据工厂及其子资源，包括数据集、链接服务、管道、触发器和集成运行时。 不过，此角色的成员身份不允许用户创建其他资源。

Azure Repos 和 GitHub 上的权限独立于数据工厂权限。 因此，具有存储库权限但只具有读者角色的用户可以编辑数据工厂子资源并向存储库提交更改，但不能发布这些更改。

> [!IMPORTANT]
> 使用“数据工厂参与者”角色进行资源管理器模板部署不会提升你的权限。  例如，如果你部署一个可以创建 Azure 虚拟机的模板，而你没有创建虚拟机的权限，则部署会失败并出现授权错误。

### <a name="custom-scenarios-and-custom-roles"></a>自定义方案和自定义角色

有时候，可能需要为不同的数据工厂用户授予不同的访问权限级别。 例如：
- 可能需要一个其中的用户只有特定数据工厂的权限的组。
- 或者可能需要一个其中的用户只能监视一个或多个数据工厂但不能对其进行修改的组。

若要实现这些自定义方案，可以创建自定义角色，然后向这些角色分配用户。 有关自定义角色的详细信息，请参阅 [Azure 中的自定义角色](..//role-based-access-control/custom-roles.md)。

下面是一些示例，演示了自定义角色的功能：

- 让用户通过 Azure 门户对资源组中的任何数据工厂执行创建、编辑或删除操作。

  在资源组级别为用户分配内置的“数据工厂参与者”角色。  若需允许用户访问订阅中的任何数据工厂，请在订阅级别分配角色。

- 允许用户查看（读取）和监视数据工厂，但不允许对其进行编辑或更改。

  为用户分配内置的“读者”角色，其权限范围为数据工厂资源。 

- 允许用户在 Azure 门户中编辑单个数据工厂。

  此方案需要两个角色分配。

  1. 在数据工厂级别分配内置的“参与者”角色。 
  2. 创建权限为 **Microsoft.Resources/deployments/** 的自定义角色。 将此自定义角色分配给资源组级别的用户。

- 允许用户通过 PowerShell 或 SDK 更新数据工厂，但不允许其在 Azure 门户中进行更新。

  为用户分配内置的“参与者”角色，其权限范围为数据工厂资源。  此角色允许用户在 Azure 门户中查看资源，但不允许其访问“发布”和“全部发布”按钮。  

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的角色 - [了解角色定义](../role-based-access-control/role-definitions.md)

- 详细了解“数据工厂参与者”角色  - [“数据工厂参与者”角色](../role-based-access-control/built-in-roles.md#data-factory-contributor)。
