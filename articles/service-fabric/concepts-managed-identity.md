---
title: Azure 的托管标识
description: 了解如何通过 Service Fabric 使用 Azure 的托管标识。
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 06ebcfdf3d6a3815908752153acb09437d745d15
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986744"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>将 Azure 托管标识与 Service Fabric （预览版）配合使用

构建云应用程序时，常见的难题是如何在代码中安全地管理凭据，以便对各种服务进行身份验证，而无需在开发人员工作站或源代码管理中将这些凭据保存到本地。 *Azure 的托管标识*通过在 Azure AD 内自动托管标识，为 Azure Active Directory （Azure AD）中的所有资源解决了此问题。 您可以使用服务的标识向支持 Azure AD 身份验证的任何服务进行身份验证，包括 Key Vault，而无需在代码中存储任何凭据。

Azure*资源的托管标识*免费，适用于 azure 订阅的 Azure AD。 不需额外付费。

> [!NOTE]
> *Azure 的托管标识*是以前称为托管服务标识（MSI）的服务的新名称。

## <a name="concepts"></a>概念

Azure 的托管标识基于几个关键概念：

- **客户端 ID** -由 Azure AD 生成的唯一标识符，该标识符在其初始设置期间绑定到应用程序和服务主体（另请参阅[应用程序 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)。）

- **主体 id** -用于授予对 Azure 资源的基于角色的访问权限的托管标识的服务主体对象的 id。

- **服务主体**-一种 Azure Active Directory 对象，该对象表示给定租户中 AAD 应用程序的投影（另请参阅[服务主体](../active-directory/develop/developer-glossary.md#service-principal-object)）。

托管标识分为两种类型：

- **系统分配的托管标识**直接在 Azure 服务实例上启用。  系统分配的标识的生命周期对于启用它的 Azure 服务实例是唯一的。
- **用户分配托管标识**是作为独立的 Azure 资源创建的。 可以将该标识分配给一个或多个 Azure 服务实例，并独立于这些实例的生命周期进行管理。

若要进一步了解托管标识类型之间的差异，请参阅[Azure 资源的托管标识如何工作？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 应用程序支持的方案

Service Fabric 的托管标识仅在 Azure 部署 Service Fabric 群集中受支持，并且仅适用于部署为 Azure 资源的应用程序;不能将未部署为 Azure 资源的应用程序分配给某个标识。 从概念上讲，Azure Service Fabric 群集中的托管标识支持包括两个阶段：

1. 将一个或多个托管标识分配给应用程序资源;可以分别向应用程序分配一个系统分配的标识和/或最多32个用户分配的标识。

2. 在应用程序的定义中，将分配给应用程序的标识之一映射到组成应用程序的任何单个服务。

系统分配的应用程序标识对于该应用程序是唯一的;用户分配的标识是独立的资源，可以分配给多个应用程序。 在应用程序中，可以将单个标识（无论是由系统分配还是由用户分配）分配给应用程序的多个服务，但每个单个服务只能分配一个标识。 最后，必须显式为服务分配标识，才能访问此功能。 实际上，应用程序的标识到其构成服务的映射允许应用程序内隔离，服务只能使用映射到它的标识。  

目前，此预览功能支持以下方案：

- 使用一个或多个服务以及一个或多个分配的标识部署新的应用程序

- 将一个或多个托管标识分配到现有（Azure 部署的）应用程序，以便访问 Azure 资源

不支持或不建议使用以下方案;请注意，这些操作可能不会被阻止，但可能导致应用程序中断：

- 删除或更改分配给应用程序的标识;如果必须进行更改，请提交单独的部署以首先添加新的标识分配，然后删除以前分配的标识。 从现有应用程序中删除标识可能会产生不良影响，包括使应用程序处于不可升级状态。 如果需要删除标识，则可以安全删除该应用程序;请注意，这会删除与应用程序关联的系统分配的标识（如果定义），并将删除与分配给该应用程序的用户分配的标识的任何关联。

- 目前不会将对托管标识的 Service Fabric 支持集成到[AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md)中;托管标识功能的预览期结束时，将实现集成。

>
> [!NOTE]
>
> 此功能为预览版。 可能经常发生更改，并且不适合生产部署。

## <a name="next-steps"></a>后续步骤

- [部署具有托管标识支持的新 Azure Service Fabric 群集](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [在现有 Azure Service Fabric 群集中启用托管标识支持](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [利用服务代码中 Service Fabric 应用程序的托管标识](./how-to-managed-identity-service-fabric-app-code.md)
- [向 Azure Service Fabric 应用程序授予其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)
- [声明并使用应用程序机密作为 KeyVaultReferences](./service-fabric-keyvault-references.md)
