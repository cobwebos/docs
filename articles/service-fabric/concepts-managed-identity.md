---
title: Azure 的托管标识
description: 了解如何将托管标识用于 Azure 与服务结构。
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 06ebcfdf3d6a3815908752153acb09437d745d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986744"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>使用具有服务结构的 Azure 的托管标识（预览）

构建云应用程序时面临的一个常见挑战是，如何安全地管理代码中的凭据，以便对各种服务进行身份验证，而无需将它们保存在开发人员工作站或源代码管理中。 *Azure 的托管标识*通过在 Azure AD 中自动托管标识为 Azure 活动目录 （Azure AD） 中的所有资源提供此问题。 可以使用服务标识对支持 Azure AD 身份验证（包括密钥保管库）的任何服务进行身份验证，而无需在代码中存储任何凭据。

*Azure 资源的托管标识*对于 Azure 订阅的 Azure AD 是免费的。 不需额外付费。

> [!NOTE]
> *Azure 的托管标识*是以前称为托管服务标识 （MSI） 的服务的新名称。

## <a name="concepts"></a>概念

Azure 的托管标识基于几个关键概念：

- **客户端 ID** - Azure AD 生成的唯一标识符，在其初始预配期间与应用程序和服务主体绑定（另请参阅[应用程序 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)。）

- **主体 ID** - 托管标识的服务主体对象的对象 ID，用于授予对 Azure 资源的基于角色的访问权限。

- **服务主体** - 一个 Azure Active Directory 对象，表示给定租户中 AAD 应用程序的投影（另请参阅[服务主体](../active-directory/develop/developer-glossary.md#service-principal-object)。）

托管标识分为两种类型：

- 直接在 Azure 服务实例上启用**系统分配的托管标识**。  系统分配的标识的生命周期对于启用它的 Azure 服务实例是唯一的。
- **用户分配的托管标识**将创建为独立的 Azure 资源。 标识可以分配给一个或多个 Azure 服务实例，并独立于这些实例的生命周期进行管理。

为了进一步了解托管标识类型之间的区别，请参阅[Azure 资源的托管标识如何工作？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 应用程序支持的方案

服务结构的托管标识仅在 Azure 部署的服务结构群集中受支持，并且仅适用于作为 Azure 资源部署的应用程序;无法为未部署为 Azure 资源的应用程序分配标识。 从概念上讲，对 Azure 服务结构群集中的托管标识的支持包括两个阶段：

1. 将一个或多个托管标识分配到应用程序资源；可以分别为某个应用程序分配单个系统分配的标识和/或最多 32 个用户分配的标识。

2. 在应用程序的定义中，可将分配给应用程序的某个标识映射到构成该应用程序的任意单个服务。

应用程序的系统分配的标识是该应用程序特有的；用户分配的标识是独立的资源，可分配到多个应用程序。 在应用程序中，可将单个标识（无论是系统分配的还是用户分配的）分配到该应用程序的多个服务，但对于每个服务，只能为其分配一个标识。 最后，必须显式为服务分配标识才能访问此功能。 实际上，将应用程序的身份映射到其组成服务允许应用程序内隔离 - 服务只能使用映射到它的标识。  

目前，此预览功能支持以下方案：

- 使用一个或多个服务和一个或多个分配标识部署新应用程序

- 将一个或多个托管标识分配给现有（Azure 部署）应用程序，以便访问 Azure 资源

不支持，也不建议使用以下方案；请注意，这些操作不一定会遭到阻止，但可能会导致应用程序中断：

- 删除或更改分配给应用程序的标识；如果必须进行更改，请提交单独的部署，以先添加新的标识分配，然后删除以前分配的标识。 从现有应用程序中删除标识可能会产生不良影响，包括使应用程序处于一种不可升级的状态。 如果有必要删除标识，安全的做法是连同应用程序一起删除；请注意，这会删除与该应用程序关联的系统分配的标识（如果已定义），并会删除与分配给该应用程序的用户分配的标识之间的所有关联。

- 此时未将托管标识的服务交换矩阵支持集成到[AzureServiceToken 提供程序](../key-vault/service-to-service-authentication.md)中;集成将在托管标识功能的预览期结束时完成。

>
> [!NOTE]
>
> 此功能为预览版。 它可能经常更改，不适合生产部署。

## <a name="next-steps"></a>后续步骤

- [部署支持托管标识的新 Azure Service Fabric 群集](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [在现有 Azure Service Fabric 群集中启用托管标识支持](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [从服务代码中利用 Service Fabric 应用程序的托管标识](./how-to-managed-identity-service-fabric-app-code.md)
- [为 Azure Service Fabric 应用程序授予对其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)
- [声明应用程序机密并将其用作 KeyVaultReferences](./service-fabric-keyvault-references.md)
