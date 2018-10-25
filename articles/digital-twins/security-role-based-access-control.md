---
title: 了解 Azure 数字孪生基于角色的访问控制 | Microsoft Docs
description: 了解如何使用基于角色的访问控制在数字孪生中进行身份验证。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323700"
---
# <a name="role-based-access-control"></a>基于角色的访问控制

Azure 数字孪生支持对空间图中特定数据、资源和操作进行精确的访问控制。 它通过精细的角色和权限管理（称为基于角色的访问控制）实现。 基于角色的访问控制包括“角色”或权限级别以及“角色分配”或角色与用户或设备的关联。

使用基于角色的访问控制，可以向用户、设备、服务主体、用户定义函数、属于某个域的所有用户或者租户授予权限。 此外，也可以对访问程度进行微调。

基于角色的访问控制具有唯一性，原因在于权限沿着空间图继承。

## <a name="what-can-i-do-with-role-based-access-control"></a>我可以使用基于角色的访问控制做什么？

开发人员可以使用基于角色的访问控制进行以下操作：

* 授予用户管理权限，使其能够管理整个建筑内或仅特定房间或楼层内的设备。
* 授予管理员全局访问权限，使其能够访问整个图或某部分图的所有空间图节点。
* 向支持专家授予图的读取访问权限，但访问密钥除外。
* 向域的每个成员授予所有图对象的读取访问权限。

## <a name="role-based-access-control-best-practices"></a>基于角色的访问控制最佳做法

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>角色

### <a name="role-definitions"></a>角色定义

角色定义是权限的集合，有时也称作“角色”。 角色定义列出了允许的操作，包括“创建”、“读取”、“更新”和“删除”。 它还指定了这些权限适用的对象类型。

Azure 数字孪生中提供了以下角色：

* **空间管理员**：指定空间和其下所有节点的创建、读取、更新和删除权限。 全局权限。
* **用户管理员**：用户及与用户相关对象的创建、读取、更新和删除权限。 空间的读取权限。
* **设备管理员**：设备及与设备相关对象的创建、读取、更新和删除权限。 空间的读取权限。
* **密钥管理员**：访问密钥的创建、读取、更新和删除权限。 空间的读取权限。
* **令牌管理员**：访问密钥的读取和更新权限。 空间的读取权限。
* **用户**：空间、传感器和用户（包括其相应的相关对象）的读取权限。
* **支持专家**：除访问密钥之外的所有内容的读取权限。
* **设备安装程序**：设备和传感器（包括其相应的相关对象）的读取和更新权限。 空间的读取权限。
* **网关设备**：传感器的创建权限。 设备和传感器（包括其相应的相关对象）的读取权限。

>[!NOTE]
> 可以通过查询系统/角色 API 来检索上述内容的完整定义。

### <a name="object-types"></a>对象类型

`ObjectIdType` 表示要赋予其角色的标识的类型。 除了 `DeviceId` 和 `UserDefinedFunctionId` 类型，以下类型分别对应 Azure Active Directory (Azure AD) 对象的属性：
  
* `UserId` 类型将角色分配给用户。
* `DeviceId` 类型将角色分配给设备。
* `DomainName` 类型将角色分配给域名。 具有指定域名的每位用户都将拥有相应角色的访问权限。
* `TenantId` 类型将角色分配给租户。 属于指定 Azure AD 租户 ID 的每位用户都将拥有相应角色的访问权限。
* `ServicePrincipalId` 类型将角色分配给服务主体对象 ID。
* `UserDefinedFunctionId` 类型将角色分配给用户定义的函数 (UDF)。

> [!div class="nextstepaction"]
> [查询用户的对象 ID](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [获取服务主体的对象 ID](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [检索 Azure AD 租户的对象 ID](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>角色分配

通过创建角色分配向接收方授予权限，通过删除角色分配来撤销权限。 Azure 数字孪生角色分配与对象（用户、Azure AD 租户等）、角色和空间相关联。 然后向属于该空间（包括其下整个空间图）的所有对象授予权限。

例如，向某用户分配某空间图（代表建筑）的根节点的 `DeviceInstaller` 角色。 然后，该用户不仅可以读取和更新该节点的设备，还可以读取和更新建筑中所有其他子空间的设备。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 数字孪生安全性的信息，请阅读[创建和管理角色分配](./security-create-manage-role-assignments.md)。
