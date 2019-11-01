---
title: Service Fabric 托管标识概述 |Microsoft Docs
description: 本文概述了托管标识。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: e2f05ba28109a9b3c88d80eb218bf94014f0f082
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243027"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Service Fabric 应用程序的托管标识（预览）

生成云应用程序时需要应对的常见挑战是，如何管理代码中用于云服务身份验证的凭据。 保护凭据安全是一项重要任务，因为它们从不出现在开发人员工作站上，并且未签入到源代码管理中。 Azure Active Directory （Azure AD）中的 Azure 资源的托管标识功能解决了此问题。 此功能为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

Azure 资源的托管标识功能可免费用于 Azure 订阅 Azure AD。 没有任何额外费用。

> [!NOTE]
> Azure 资源的托管标识是以前称为托管服务标识（MSI）的服务的新名称。

## <a name="terminology"></a>术语

在 Azure 资源的托管标识文档集中使用以下术语：

- **客户端 ID** -由 Azure AD 生成的唯一标识符，该标识符在其初始设置期间绑定到应用程序和服务主体（另请参阅[应用程序 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)。）

- **主体 id** -用于授予对 Azure 资源的基于角色的访问权限的托管标识的服务主体对象的 id。

- **服务主体**-一种 Azure Active Directory 对象，该对象表示给定租户中 AAD 应用程序的投影（另请参阅[服务主体](../active-directory/develop/developer-glossary.md#service-principal-object)）。


## <a name="about-managed-identities-in-azure"></a>关于 Azure 中的托管标识

- [Azure 中托管标识（MI）的类型](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [系统分配的托管标识在 Azure 中的工作原理](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [用户定义的托管标识（MI）在 Azure 中的工作原理](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 应用程序支持的方案

仅在 Azure 中部署 Service Fabric 群集并仅在部署为 Azure 资源的应用程序中支持 Service Fabric 的托管标识;不能将未部署为 Azure 资源的应用程序分配有标识。 从概念上讲，Azure Service Fabric 群集中的托管标识支持包括两个阶段：

1. 将一个或多个托管标识分配给应用程序资源;可以分别向应用程序分配一个系统分配的标识和/或最多32个用户分配的标识。

2. 在应用程序的定义中，将分配给应用程序的标识之一映射到组成应用程序的任何单个服务。

系统分配的应用程序标识对于该应用程序是唯一的;用户分配的标识是独立的资源，可以分配给多个应用程序。 在应用程序中，可以将单个标识（无论是由系统分配还是由用户分配）分配给应用程序的多个服务，但每个单个服务只能分配一个标识。 最后，必须显式为服务分配标识，才能访问此功能。 实际上，应用程序的标识到其构成服务的映射允许应用程序内隔离-一种服务只能使用映射到该服务的标识（如果未显式分配一个服务，则不能使用此标识）。  

预览版本支持的方案列表如下所示：

   - 部署具有一个或多个服务的新应用程序，以及一个或多个已分配标识

   - 向现有应用程序分配一个或多个托管标识，以便访问 Azure 资源;必须已将应用程序部署为 Azure 资源本身


不支持或不建议使用以下方案;请注意，这些操作可能不会被阻止，但可能导致应用程序中断：

   - 删除或更改分配给应用程序的标识;如果必须进行更改，请提交单独的部署以首先添加新的标识分配，然后删除以前分配的标识。 从现有应用程序中删除标识可能会产生不良影响，包括使应用程序处于不可升级状态。 如果需要删除标识，则可以安全删除该应用程序;请注意，这会删除与应用程序关联的系统分配的标识（如果定义），并将删除与分配给该应用程序的用户分配的标识的任何关联。

   - 目前不会将对托管标识的 SF 支持集成到[AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md)中;托管标识功能的预览期结束时，将实现集成。

>
> [!NOTE]
>
> 此功能处于预览阶段;因此，可能会经常发生更改，并且可能不适合生产部署。

## <a name="next-steps"></a>后续步骤
* [部署具有托管标识支持的新 Azure Service Fabric 群集](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [在现有 Azure Service Fabric 群集中启用托管标识支持](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [利用服务代码中 Service Fabric 应用程序的托管标识](./how-to-managed-identity-service-fabric-app-code.md)
* [向 Azure Service Fabric 应用程序授予其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)
* [声明并使用应用程序机密作为 KeyVaultReferences](./service-fabric-keyvault-references.md) 
