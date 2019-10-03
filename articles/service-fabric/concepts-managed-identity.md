---
title: Service Fabric 托管标识概述 |Microsoft Docs
description: 本文提供托管标识的概述。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: d63fd3d1b778c691d053f13fbf0fbb2ed5ccb3e3
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968281"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Service Fabric 应用程序的托管标识（预览版）

生成云应用程序时需要应对的常见挑战是，如何管理代码中用于云服务身份验证的凭据。 保护凭据的安全是一项重要任务，因为凭据从来不会显示在开发人员工作站上，并且不会签入源代码管理中。 Azure Active Directory (Azure AD) 中的 Azure 资源托管标识功能可以解决此问题。 此功能为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

如果你有 Azure 订阅，Azure AD 中的 Azure 资源托管标识功能是免费的。 没有任何额外费用。

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

## <a name="terminology"></a>术语

Azure 资源托管标识的整个文档集中都使用了以下术语：

- **客户端 ID** - Azure AD 生成的唯一标识符，在其初始预配期间与应用程序和服务主体绑定（另请参阅[应用程序 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)。）

- **主体 ID** - 托管标识的服务主体对象的对象 ID，用于授予对 Azure 资源的基于角色的访问权限。

- **服务主体** - 一个 Azure Active Directory 对象，表示给定租户中 AAD 应用程序的投影（另请参阅[服务主体](../active-directory/develop/developer-glossary.md#service-principal-object)。）


## <a name="about-managed-identities-in-azure"></a>关于 Azure 中的托管标识

- [Azure 中的托管标识 (MI) 类型](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [系统分配的托管标识在 Azure 中的工作原理](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [用户定义的托管标识 (MI) 在 Azure 中的工作原理](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 应用程序支持的方案

Service Fabric 的托管标识仅在 Azure 部署的 Service Fabric 群集中受支持，并且仅适用于部署为 Azure 资源的应用程序；无法对部署为 Azure 资源的应用程序分配标识。 从概念上讲，Azure Service Fabric 群集中的托管标识支持包括两个阶段：

1. 将一个或多个托管标识分配到应用程序资源；可以分别为某个应用程序分配单个系统分配的标识和/或最多 32 个用户分配的标识。

2. 在应用程序的定义中，可将分配给应用程序的某个标识映射到构成该应用程序的任意单个服务。

应用程序的系统分配的标识是该应用程序特有的；用户分配的标识是独立的资源，可分配到多个应用程序。 在应用程序中，可将单个标识（无论是系统分配的还是用户分配的）分配到该应用程序的多个服务，但对于每个服务，只能为其分配一个标识。 最后，必须显式为服务分配标识才能访问此功能。 应用程序标识到其构成服务的映射可以有效实现应用程序内部隔离 - 一个服务只能使用映射到其自身的标识（如果未显式分配标识，则该服务根本没有可用的标识。）  

预览版支持的方案列表如下：

   - 部署包含一个或多个服务以及一个或多个已分配标识的新应用程序

   - 将一个或多个托管标识分配到现有应用程序以访问 Azure 资源；应用程序本身必须部署为 Azure 资源


不支持，也不建议使用以下方案；请注意，这些操作不一定会遭到阻止，但可能会导致应用程序中断：

   - 删除或更改分配给应用程序的标识;如果必须进行更改，请提交单独的部署以首先添加新的标识分配，然后删除以前分配的标识。 从现有应用程序中删除标识可能会产生不良影响，包括使应用程序处于一种不可升级的状态。 如果有必要删除标识，安全的做法是连同应用程序一起删除；请注意，这会删除与该应用程序关联的系统分配的标识（如果已定义），并会删除与分配给该应用程序的用户分配的标识之间的所有关联。

>
> [!NOTE]
>
> 此功能目前为预览版；因此，将来可能会经常更改，并可能不适合用于生产部署。

## <a name="next-steps"></a>后续步骤
* [部署支持托管标识的新 Azure Service Fabric 群集](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [在现有 Azure Service Fabric 群集中启用托管标识支持](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [从服务代码中利用 Service Fabric 应用程序的托管标识](./how-to-managed-identity-service-fabric-app-code.md)
* [为 Azure Service Fabric 应用程序授予对其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)
