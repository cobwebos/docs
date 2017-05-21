---
title: "Azure 托管应用程序概述 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的概念"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a4348a4d2348d744c03ad3a89d0548526fa2f9f8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="azure-managed-applications-overview"></a>Azure 托管应用程序概述

目前，Azure 提供了一个可靠的应用商店，ISV 和创业公司可以在其中向世界各地的客户提供其解决方案。 Azure 应用商店是一个库，其中包含来自第一方和第三方供应商的数百种复杂的多资源模板。 客户可以在数分钟内使用 PaaS 和 SaaS 应用程序进行部署和启动。 虽然它提供了一种非常好的方法来快速部署产品，但客户仍然需要负责维护和更新解决方案。 对于供应商，除了虚拟机映像计费外，无法向客户收取应用程序的使用费用。 而且，供应商无法阻止客户修改关键的应用程序资源，并且无法阻止他人访问构成应用程序的知识产权。 Azure 托管应用程序针对这些问题提供了一个解决方案。 

## <a name="advantages-of-managed-applications"></a>托管应用程序的优点

Azure 托管应用程序提供了一个生态系统，使得供应商能够以自包含应用程序形式提供 PaaS 或 SaaS 服务。 客户在其订阅中部署托管应用程序，但供应商可以管理它们。 它使得供应商能够使用 Azure 的计费系统向客户收费，使用模板来管理所部署的应用程序的生命周期。 另一方面，它使得客户能够自动获取更新，以及支付支持和维护费用。 他们不需要自己维护或更新应用程序，也不需要在应用程序失败时自己诊断和解决问题。

Azure 中的这种生态系统不仅会使 PaaS 和 SaaS 供应商受益，还会使希望打包和转售其解决方案的公司中央平台团队和系统集成商受益。

## <a name="how-managed-applications-work"></a>托管应用程序如何工作
使用托管应用程序时有两种体验：

1. 供应商或独立软件供应商 (ISV) 创建托管应用程序并使其可供更广泛的使用。 
2. 客户和使用者希望创建并使用发布的应用程序。 

本文涵盖了这两种体验。 首先，让我们来了解一下托管应用程序如何工作。 

托管应用程序类似于应用商店解决方案，但有一个重大差异。 在托管应用程序中，资源预配到由 ISV/供应商管理的资源组。 资源组存在于客户的订阅中，但 ISV 的租户中的用户、用户组或应用程序对资源组具有访问权限。 为管理应用程序并为其提供服务，供应商的标识将添加到 Active Directory 所有者、参与者、读取者或任何其他内置角色中。 

## <a name="key-concepts"></a>关键概念

### <a name="managed-resource-group"></a>托管资源组
在其中创建了模板中预配的所有 Azure 资源的资源组。 例如，如果设备创建一个存储帐户，则此资源组将包含存储帐户资源。 它不包含设备资源。

### <a name="appliance-package"></a>设备包
发布者创建包含模板文件和 createUIDefinition 文件的包。 具体而言，它包含以下文件：

- **applianceMainTemplate.json** - 一个模板文件，它定义了由设备预配的所有资源。 此文件是一个用于创建资源的常规模板文件。

- **MainTemplate.json** - 一个模板文件，它定义了设备资源 (Microsoft.Solutions/appliances)。 此资源中定义的一个关键属性是 ManagedResourceGroupId。 此属性指示哪个资源组用于承载 applianceMainTemplate.json 中定义的实际资源。

- **createUIDefinition.json** - 此文件描述了如何呈现模板中定义的参数所需的 UI。

### <a name="authorization"></a>授权
发布者需要指定供应商代表客户来管理资源时所需的权限。 此权限应用于托管资源组。 需要设置以下值：

- **PrincipalID** - 用来授予对托管资源组的访问权限的用户、组或应用程序的 Azure AD 标识符。 此标识符属于发布者的租户。

- **RoleDefinitionID** - 分配给前面的 principalID 的角色的 Azure AD 标识符。 它可以是发布者的租户中的任何内置 RBAC 角色。

## <a name="next-steps"></a>后续步骤

* 若要了解供应商体验，请参阅[创建和发布 Azure 托管应用程序](managed-application-publishing.md)。
* 若要了解使用者体验，请参阅[使用 Azure 托管应用程序](managed-application-consumption.md)。
* 若要创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
