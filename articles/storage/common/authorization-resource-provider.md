---
title: 使用 Azure 存储资源提供程序访问管理资源
description: Azure 存储资源提供程序是一种服务，可用于访问 Azure 存储空间的管理资源。 可以使用 Azure 存储资源提供程序创建、更新、管理和删除资源，例如存储帐户、专用终结点和帐户访问密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d632de0a6274ebb2ede950ca610dfd5e94310d28
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613867"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>使用 Azure 存储资源提供程序访问管理资源

Azure 资源管理器是 Azure 的部署和管理服务。 Azure 存储资源提供程序是一种基于 Azure 资源管理器的服务，可用于访问 Azure 存储空间的管理资源。 可以使用 Azure 存储资源提供程序创建、更新、管理和删除资源，例如存储帐户、专用终结点和帐户访问密钥。 有关 Azure 资源管理器的详细信息，请参阅[azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。

可以使用 Azure 存储资源提供程序执行一些操作，例如创建或删除存储帐户，或获取订阅中的存储帐户列表。 若要对 Azure 存储资源提供程序的请求进行授权，请使用 Azure Active Directory （Azure AD）。 本文介绍如何将权限分配给管理资源，并指向演示如何对 Azure 存储资源提供程序发出请求的示例。

## <a name="management-resources-versus-data-resources"></a>管理资源与数据资源

Microsoft 提供了两个用于处理 Azure 存储资源的 REST Api。 这些 Api 构成可以对 Azure 存储空间执行的所有操作的基础。 使用 Azure 存储 REST API 可以处理存储帐户中的数据，包括 blob、队列、文件和表数据。 使用 Azure 存储资源提供程序 REST API 可以处理存储帐户和相关资源。

读取或写入 blob 数据的请求所需的权限与执行管理操作的请求不同。 RBAC 提供对两种类型资源的权限的精细控制。 向安全主体分配 RBAC 角色时，请确保你了解将授予主体的权限。 有关说明与每个内置 RBAC 角色关联的操作的详细参考信息，请参阅[Azure 资源的内置角色](../../role-based-access-control/built-in-roles.md)。

Azure 存储支持使用 Azure AD 对针对 Blob 和队列存储的请求进行授权。 有关用于 blob 和队列数据操作的 RBAC 角色的信息，请参阅[使用 Active Directory 授予对 blob 和队列的访问权限](storage-auth-aad.md)。

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>使用基于角色的访问控制（RBAC）分配管理权限

每个 Azure 订阅都有一个关联的 Azure Active Directory，用于管理用户、组和应用程序。 用户、组或应用程序在[Microsoft 标识平台](/azure/active-directory/develop/)的上下文中也称为安全主体。 你可以通过使用基于角色的访问控制（RBAC）向在 Active Directory 中定义的安全主体授予对订阅中资源的访问权限。

向安全主体分配 RBAC 角色时，还会指示该角色授予的权限生效的作用域。 对于管理操作，你可以在订阅、资源组或存储帐户级别分配角色。 可以使用[Azure 门户](https://portal.azure.com/)、 [Azure CLI 工具](../../cli-install-nodejs.md)、 [PowerShell](/powershell/azureps-cmdlets-docs)或[Azure 存储资源提供程序 REST API](/rest/api/storagerp)向安全主体分配 RBAC 角色。

有关 RBAC 的详细信息，请参阅[什么是适用于 Azure 资源的基于角色的访问控制（RBAC）](../../role-based-access-control/overview.md)以及[经典订阅管理员角色、azure RBAC 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

### <a name="built-in-roles-for-management-operations"></a>用于管理操作的内置角色

Azure 提供了内置角色，可授予调用管理操作的权限。 Azure 存储还提供专用于 Azure 存储资源提供程序的内置角色。

授予调用存储管理操作权限的内置角色包括下表中所述的角色：

|    RBAC 角色    |    Description    |    包括对帐户密钥的访问权限？    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **所有者** | 可以管理所有存储资源和资源的访问权限。  | 是，提供查看和重新生成存储帐户密钥的权限。 |
| **参与者**  | 可以管理所有存储资源，但不能管理分配给资源的资源。 | 是，提供查看和重新生成存储帐户密钥的权限。 |
| **读取者** | 可以查看有关存储帐户的信息，但无法查看帐户密钥。 | 不。 |
| **存储帐户参与者** | 可以管理存储帐户、获取订阅资源组和资源的相关信息，以及创建和管理订阅资源组部署。 | 是，提供查看和重新生成存储帐户密钥的权限。 |
| **用户访问管理员** | 可以管理对存储帐户的访问。   | 是，允许安全主体将任何权限分配给自身和其他人。 |
| **虚拟机参与者** | 可以管理虚拟机，但不能管理其连接到的存储帐户。   | 是，提供查看和重新生成存储帐户密钥的权限。 |

表中的第三列指示内置角色是否支持**storageAccounts/listkeys/action**。 此操作授予读取和重新生成存储帐户密钥的权限。 访问 Azure 存储管理资源的权限还不包括访问数据的权限。 但是，如果用户有权访问帐户密钥，则他们可以使用帐户密钥通过共享密钥授权访问 Azure 存储数据。

### <a name="custom-roles-for-management-operations"></a>用于管理操作的自定义角色

Azure 还支持定义用于访问管理资源的自定义 RBAC 角色。 有关自定义角色的详细信息，请参阅[Azure 资源的自定义角色](../../role-based-access-control/custom-roles.md)。

## <a name="code-samples"></a>代码示例

有关演示如何从 Azure 存储管理库授权和调用管理操作的代码示例，请参阅以下示例：

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure 资源管理器与经典部署

Resource Manager 和经典部署模型表示部署和管理 Azure 解决方案的两种不同方式。 当你创建新的存储帐户时，Microsoft 建议使用 Azure 资源管理器部署模型。 如果可能，Microsoft 还建议你重新创建具有资源管理器模型的现有经典存储帐户。 尽管可以使用经典部署模型创建存储帐户，但经典模型比较不灵活，最终将不推荐使用。

有关 Azure 部署模型的详细信息，请参阅[资源管理器部署和经典部署](../../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)
- [什么是 Azure 资源的基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)
- [Azure 存储资源提供程序的可伸缩性目标](scalability-targets-resource-provider.md)
