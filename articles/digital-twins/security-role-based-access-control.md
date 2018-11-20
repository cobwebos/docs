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
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014782"
---
# <a name="role-based-access-control"></a>基于角色的访问控制

Azure 数字孪生支持对空间图中特定数据、资源和操作进行精确的访问控制。 它通过精细的角色和权限管理（称为基于角色的访问控制 (RBAC)）实现。 RBAC 包括“角色”和“角色分配”。 角色标识权限的级别。 角色分配将角色与用户或设备相关联。

使用 RBAC 可将权限授予：

- 用户。
- 设备。
- 服务主体。
- 用户定义的函数。 
- 属于某个域的所有用户。 
- 租户。
 
还可以微调访问权限的程度。

RBAC 具有唯一性，原因在于权限沿着空间图继承。

## <a name="what-can-i-do-with-rbac"></a>RBAC 的作用是什么？

开发人员可以使用 RBAC 来实现以下目的：

* 授予用户管理权限，使其能够管理整个建筑内或仅特定房间或楼层内的设备。
* 授予管理员全局访问权限，使其能够访问整个图或某部分图的所有空间图节点。
* 向支持专家授予图的读取访问权限，但访问密钥除外。
* 向域的每个成员授予所有图对象的读取访问权限。

## <a name="rbac-best-practices"></a>RBAC 最佳做法

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>角色

### <a name="role-definitions"></a>角色定义

角色定义是权限的集合，有时也称作角色。 角色定义列出了允许的操作，包括创建、读取、更新和删除。 它还指定了这些权限适用的对象类型。

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
> 若要检索上述角色的完整定义，请查询系统/角色 API。

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

若要向接收方授予权限，请创建角色分配。 若要撤消权限，请删除角色分配。 Azure 数字孪生角色分配将某个对象（例如用户或 Azure AD 租户）与角色和空间相关联。 向属于该空间的所有对象授予权限。 空间包括其下的整个空间图。

例如，向某用户分配某空间图（代表建筑）的根节点的 `DeviceInstaller` 角色。 然后，该用户可以读取和更新该节点的设备，以及读取和更新建筑中所有其他子空间的设备。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 数字孪生安全性的信息，请阅读[创建和管理角色分配](./security-create-manage-role-assignments.md)。
