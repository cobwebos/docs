---
title: 使用 Azure 存储资源提供程序访问管理资源
description: Azure 存储资源提供程序是提供对 Azure 存储管理资源的访问的服务。 可以使用 Azure 存储资源提供程序来创建、更新、管理和删除存储帐户、专用终结点和帐户访问密钥等资源。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e71e56b9da06bfd8f3be24481efd619b788a8839
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822280"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>使用 Azure 存储资源提供程序访问管理资源

Azure 资源管理器是 Azure 的部署和管理服务。 Azure 存储资源提供程序是基于 Azure 资源管理器的服务，提供对 Azure 存储管理资源的访问。 可以使用 Azure 存储资源提供程序来创建、更新、管理和删除存储帐户、专用终结点和帐户访问密钥等资源。 有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。

可以使用 Azure 存储资源提供程序执行多种操作，例如，创建或删除存储帐户，或者获取订阅中的存储帐户列表。 若要授权针对 Azure 存储资源提供程序发出的请求，请使用 Azure Active Directory (Azure AD)。 本文介绍如何向管理资源分配权限，并通过示例来演示如何对 Azure 存储资源提供程序发出请求。

## <a name="management-resources-versus-data-resources"></a>管理资源与数据资源

Microsoft 提供了两个用于处理 Azure 存储资源的 REST Api。 这些 API 构成了可对 Azure 存储执行的所有操作的基础。 使用 Azure 存储 REST API 可以处理存储帐户中的数据，包括 Blob、队列、文件和表数据。 使用 Azure 存储资源提供程序 REST API 可以处理存储帐户和相关的资源。

读取或写入 Blob 数据的请求所需的权限不同于执行管理操作的请求。 Azure RBAC 提供对这两种类型资源的权限的精细控制。 向安全主体分配 Azure 角色时，请确保了解要向主体授予的权限。 有关与每个 Azure 内置角色关联的操作的详细参考，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

Azure 存储支持使用 Azure AD 来授权对 Blob 和队列存储的请求。 有关用于 Blob 和队列数据操作的 Azure 角色的信息，请参阅[使用 Active Directory 授权访问 Blob 和队列](storage-auth-aad.md)。

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>使用 azure RBAC)  (基于角色的访问控制分配管理权限

每个 Azure 订阅都有一个用于管理用户、组和应用程序的关联 Azure Active Directory。 在 [Microsoft 标识平台](/azure/active-directory/develop/)的上下文中，用户、组或应用程序也称为安全主体。 可以使用 Azure RBAC)  (azure 基于角色的访问控制，向在 Active Directory 中定义的安全主体授予对资源的访问权限。

向安全主体分配某个 Azure 角色时，还需要指明该角色授予的权限在哪个范围生效。 对于管理操作，可以在订阅、资源组或存储帐户级别分配角色。 可以使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI 工具](../../cli-install-nodejs.md)、[PowerShell](/powershell/azure/) 或 [Azure 存储资源提供程序 REST API](/rest/api/storagerp) 向安全主体分配 Azure 角色。

有关详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md) 和[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

### <a name="built-in-roles-for-management-operations"></a>用于管理操作的内置角色

Azure 提供用于授权调用管理操作的内置角色。 Azure 存储还提供专用于 Azure 存储资源提供程序的内置角色。

授权调用存储管理操作的内置角色包括下表中所述的角色：

|    Azure 角色    |    说明    |    是否包括对帐户密钥的访问权限？    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **所有者** | 可以管理所有存储资源和访问资源。  | 是，提供查看和重新生成存储帐户密钥的权限。 |
| **参与者**  | 可以管理所有存储资源，但不能管理对资源的访问权限。 | 是，提供查看和重新生成存储帐户密钥的权限。 |
| **读者** | 可以查看有关存储帐户的信息，但不能查看帐户密钥。 | 否。 |
| **存储帐户参与者** | 可以管理存储帐户、获取有关订阅的资源组和资源的信息，以及创建和管理订阅资源组部署。 | 是，提供查看和重新生成存储帐户密钥的权限。 |
| **用户访问管理员** | 可以管理对存储帐户的访问权限。   | 是，允许安全主体将任何权限分配给自身和其他对象。 |
| **虚拟机参与者** | 可以管理虚拟机，但不能管理虚拟机连接到的存储帐户。   | 是，提供查看和重新生成存储帐户密钥的权限。 |

表中的第三列指示内置角色是否支持 **Microsoft.Storage/storageAccounts/listkeys/action**。 此操作授予读取和重新生成存储帐户密钥的权限。 访问 Azure 存储管理资源的权限不同时包括访问数据的权限。 但是，如果用户有权访问帐户密钥，则他们可以使用帐户密钥通过共享密钥授权来访问 Azure 存储数据。

### <a name="custom-roles-for-management-operations"></a>用于管理操作的自定义角色

Azure 还支持定义用于访问管理资源的 Azure 自定义角色。 有关自定义角色的详细信息，请参阅 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。

## <a name="code-samples"></a>代码示例

有关演示如何从 Azure 存储管理库授权和调用管理操作的代码示例，请参阅以下示例：

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure 资源管理器与经典部署

Resource Manager 部署模型和经典部署模型代表两种不同的 Azure 解决方案部署和管理方式。 当你创建新的存储帐户时，Microsoft 建议使用 Azure 资源管理器部署模型。 如果可能，Microsoft 还建议你重新创建具有资源管理器模型的现有经典存储帐户。 尽管可以使用经典部署模型创建存储帐户，但经典模型较不灵活，且最终将被弃用。

有关 Azure 部署模型的详细信息，请参阅[资源管理器和经典部署](../../azure-resource-manager/management/deployment-models.md)。

## <a name="next-steps"></a>后续步骤

- [Azure Resource Manager 概述](/azure/azure-resource-manager/resource-group-overview)
- [什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [Azure 存储资源提供程序的可伸缩性目标](scalability-targets-resource-provider.md)
