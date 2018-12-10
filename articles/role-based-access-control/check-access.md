---
title: 快速入门 - 使用 Azure 门户查看分配给用户的角色 | Microsoft Docs
description: 了解如何使用 Azure 门户查看分配给用户、组、服务主体或托管标识的基于角色的访问控制 (RBAC) 权限。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52641862"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>快速入门：使用 Azure 门户查看分配给用户的角色

可以使用[基于角色的访问控制 (RBAC)](overview.md) 中的“访问控制(IAM)”边栏选项卡查看多个用户、组、服务主体和托管标识的角色分配，但有时你只需要快速查看单个用户、组、服务主体或托管标识的角色分配。 执行此操作的最简单方法是使用 Azure 门户中的**检查访问权限**功能。

## <a name="view-role-assignments"></a>查看角色分配

按照以下步骤查看订阅范围内单个用户、组、服务主体或托管标识的角色分配。

1. 在 Azure 门户中，依次单击“所有服务”、“订阅”。

1. 单击你的订阅。

1. 单击“访问控制(IAM)”。

1. 单击“检查访问权限”选项卡。

    ![“访问控制”-“检查访问权限”选项卡](./media/check-access/access-control-check-access.png)

1. 在“查找”列表中，选择要检查访问权限的安全主体类型。

1. 在搜索框中，输入字符串以在目录中搜索显示名称、电子邮件地址或对象标识符。

    ![“检查访问权限”选择列表](./media/check-access/check-access-select.png)

1. 单击安全主体以打开“分配”窗格。

    ![分配窗格](./media/check-access/check-access-assignments.png)

    在此窗格中，可以看到分配给所选安全主体和范围的角色。 如果此范围内有任何拒绝分配或继承到此范围的角色，则会将其列出。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 RBAC 和 Azure 门户为用户授予访问权限](quickstart-assign-role-user-portal.md)
