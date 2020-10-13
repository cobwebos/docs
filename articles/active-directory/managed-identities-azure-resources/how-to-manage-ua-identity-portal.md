---
title: 在 Azure 门户中管理用户分配的托管标识 - Azure AD
description: 分步说明如何创建、列出和删除用户分配的托管标识以及如何为其分配角色。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ad91c916a6134f2507e74df6e87478421a00f43
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977419"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>使用 Azure 门户创建、列出和删除用户分配的托管标识以及为其分配角色

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

本文介绍如何使用 Azure 门户创建、列出和删除用户分配的托管标识以及如何为其分配角色。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以创建用户分配的托管标识。
2. 在搜索框中，键入托管标识，然后在“服务”下单击“托管标识” 。
3. 单击“添加”，然后在“创建用户分配的托管标识”窗格下的以下字段中输入值 ：
    - **订阅**：选择要在其下创建用户分配的托管标识的订阅。
    - **资源组**：选择要在其中创建用户分配的托管标识的资源组，或单击“新建”来创建新资源组。
    - **区域**：选择一个区域以部署用户分配的托管标识，例如 " **美国西部**"。
    - **名称**：这是用户分配的托管标识的名称，例如 UAI1。
    ![创建用户分配的托管标识](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. 单击 " **查看 + 创建** " 查看更改。
5. 单击“创建”。

## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出/读取用户分配的托管标识，你的帐户需要[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以列出用户分配的托管标识。
2. 在搜索框中，键入托管标识，然后在“服务”下单击“托管标识”。
3. 这会返回一个列表，其中包含订阅中的用户分配的托管标识。  要查看用户分配的托管标识的详细信息，请单击其名称。

![列出用户分配的托管标识](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

删除用户分配的标识不会将其从 VM 或其他已分配给的资源中删除。  要从 VM 中删除用户分配的标识，请参阅[从 VM 中删除用户分配的托管标识](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm)。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以删除用户分配的托管标识。
2. 选择用户分配的托管标识，然后单击“删除”。
3. 在确认框中选择“是”。

![删除用户分配的托管标识](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>为用户分配的托管标识分配角色 

若要为用户分配的托管标识分配角色，你的帐户需要[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色分配。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以列出用户分配的托管标识。
2. 在搜索框中，键入托管标识，然后在“服务”下单击“托管标识”。
3. 这会返回一个列表，其中包含订阅中的用户分配的托管标识。  选择要为其分配角色的用户分配托管标识。
4. 依次选择“访问控制(IAM)”、“添加角色分配” 

   ![用户分配的托管标识启动](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. 在“添加角色分配”边栏选项卡中，配置以下值，然后单击“保存”：
   - **角色** - 要分配的角色
   - **将访问权限分配到** - 要将用户分配的托管标识分配到的资源
   - **选择** - 要将访问权限分配到的成员
   
   ![用户分配的托管标识 IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)
