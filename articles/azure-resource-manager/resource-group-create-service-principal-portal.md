---
title: 在门户中为 Azure 应用创建标识 | Microsoft 文档
description: 介绍如何创建新的 Azure Active Directory 应用程序和服务主体，在 Azure 资源管理器中将此服务主体与基于角色的访问控制配合使用可以管理对资源的访问权限。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 7a1b6aa9afd26116253482a2e1a9c6a25bdf3c55
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441567"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体

当代码需要访问或修改资源时，必须设置 Azure Active Directory (AD) 应用程序。 将所需的权限分配给 AD 应用程序。 这种方法优于在自己的凭据下运行应用，因为可以将自己未拥有的权限分配给应用标识。 通常情况下，这些权限仅限于应用需执行的操作。

本文介绍了如何通过门户执行这些步骤。 重点介绍单租户应用程序，其中应用程序只应在一个组织内运行。 通常会将单租户应用程序作为在组织中运行的业务线应用程序使用。

> [!IMPORTANT]
> 请考虑使用 Azure AD 托管服务标识作为应用程序标识，而不是创建服务主体。 Azure AD MSI 是 Azure Active Directory 的公共预览版功能，它简化了创建代码标识的操作。 如果代码在支持 Azure AD MSI 的服务上运行并访问支持 Azure Active Directory 身份验证的资源，则 Azure AD MSI 是更好的选择。 若要详细了解 Azure AD MSI（包括哪些服务当前支持它），请参阅 [Azure 资源的托管服务标识](../active-directory/managed-service-identity/overview.md)。

## <a name="required-permissions"></a>所需的权限

若要完成本文，必须拥有足够的权限向 Azure AD 租户注册应用，并将应用分配给 Azure 订阅中的角色。 请确保拥有适当的权限来执行这些步骤。

### <a name="check-azure-active-directory-permissions"></a>检查 Azure Active Directory 权限

1. 选择“Azure Active Directory”。

   ![选择 azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. 在 Azure Active Directory 中，选择“用户设置”。

   ![选择用户设置](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. 检查“应用注册”设置。 如果设置为“是”，则非管理员用户可以注册 AD 应用。 此设置意味着 Active AD 租户中的任何用户都可以注册应用。 可继续转到[检查 Azure 订阅权限](#check-azure-subscription-permissions)。

   ![查看应用注册](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. 如果应用注册设置已设置为“否”，则只有[全局管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md)可以注册应用。 检查帐户是否为 Active AD 租户的管理员。 选择“概述”并查看用户信息。 如果将帐户分配到“用户”角色，但（前面步骤中设置的）应用注册设置仅限于管理员用户，请要求管理员为你分配“全局管理员”角色或允许用户注册应用。

   ![查找用户](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>检查 Azure 订阅权限

在 Azure 订阅中，帐户必须具有 `Microsoft.Authorization/*/Write` 访问权限才能向角色分配 AD 应用。 通过[所有者](../role-based-access-control/built-in-roles.md#owner)角色或[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)角色授权此操作。 如果将帐户分配到“参与者”角色，则没有足够权限。 尝试将服务主体分配到角色时，将收到错误。

检查订阅权限的方法如下：

1. 在右上角选择自己的帐户，然后选择“我的权限”。

   ![选择用户权限](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. 从下拉列表中选择订阅。 选择“单击此处查看此订阅的完整访问详细信息”。

   ![查找用户](./media/resource-group-create-service-principal-portal/view-details.png)

1. 查看分配到的角色，确定是否拥有足够的权限向角色分配 AD 应用。 如果没有，请要求订阅管理员你将添加到用户访问管理员角色。 在下图中，用户分配到了“所有者”角色，这意味着该用户具有足够的权限。

   ![显示权限](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

1. 通过 [Azure 门户](https://portal.azure.com)登录 Azure 帐户。
1. 选择“Azure Active Directory”。

   ![选择 azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. 选择“应用注册”。

   ![选择应用注册](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. 选择“新建应用程序注册”。

   ![添加应用](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. 为应用提供名称和 URL。 选择“Web 应用/API”作为要创建的应用类型。 无法创建[本机应用程序](../active-directory/manage-apps/application-proxy-configure-native-client-application.md)的凭据，因此这种类型不适用于自动化应用程序。 设置这些值后，选择“创建”。

   ![命名应用程序](./media/resource-group-create-service-principal-portal/create-app.png)

已创建应用程序。

## <a name="get-application-id-and-authentication-key"></a>获取应用程序 ID 和身份验证密钥

以编程方式登录时，需要使用应用程序的 ID 和身份验证密钥。 若要获取这些值，请使用以下步骤：

1. 从 Azure Active Directory 中的“应用注册”，选择应用程序。

   ![选择应用程序](./media/resource-group-create-service-principal-portal/select-app.png)

1. 复制“应用程序 ID”并将其存储在应用程序代码中。 某些[示例应用程序](#log-in-as-the-application)将此值作为客户端 ID。

   ![客户端 ID](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. 若要生成身份验证密钥，请选择“设置”。

   ![选择“设置”](./media/resource-group-create-service-principal-portal/select-settings.png)

1. 若要生成身份验证密钥，请选择“密钥”。

   ![选择密钥](./media/resource-group-create-service-principal-portal/select-keys.png)

1. 提供密钥说明和密钥持续时间。 完成后，选择“保存”。

   ![保存密钥](./media/resource-group-create-service-principal-portal/save-key.png)

   保存密钥后, 会显示密钥的值。 复制此值，因为稍后不能检索密钥。 提供密钥值及应用程序 ID 登录为该应用程序。 将密钥值存储在应用程序可检索的位置。

   ![保存的密钥](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>获取租户 ID

以编程方式登录时，需要随身份验证请求传递租户 ID。

1. 选择“Azure Active Directory”。

   ![选择 azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. 若要获取租户 ID，请选择 Azure AD 租户的“属性”。

   ![选择 Azure AD 属性](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. 复制“目录 ID”。 此值即为租户 ID。

   ![租户 ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>将应用程序分配到角色

要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](../role-based-access-control/built-in-roles.md)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其包含的所有资源。

1. 导航到要将应用程序分配到的作用域级别。 例如，若要在订阅范围内分配角色，选择“订阅”。 可改为选择资源组或资源。

   ![选择订阅](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. 选择特定订阅（资源组或资源），向其中分配应用程序。

   ![选择进行分配的订阅](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. 选择“访问控制 (IAM)”。

   ![选择访问权限](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. 选择 **添加** 。

   ![选择添加](./media/resource-group-create-service-principal-portal/select-add.png)

1. 选择要分配到应用程序的角色。 下图显示“读者”角色。

   ![选择角色](./media/resource-group-create-service-principal-portal/select-role.png)

1. 默认情况下，可用选项中不显示 Azure Active Directory 应用程序。 若要查找应用程序，必须在搜索字段中输入其名称。 选择它。

   ![搜索应用](./media/resource-group-create-service-principal-portal/search-app.png)

1. 选择“保存”完成角色分配。 该应用程序会显示在分配到该范围角色的用户列表中。

## <a name="next-steps"></a>后续步骤
* 若要设置多租户应用程序，请参阅[使用 Azure 资源管理器 API 进行授权的开发人员指南](resource-manager-api-authentication.md)。
* 若要了解如何指定安全策略，请参阅 [Azure 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)。  
* 有关可对用户授予或拒绝的可用操作的列表，请参阅 [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md)。
