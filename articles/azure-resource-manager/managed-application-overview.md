---
title: "Azure 托管应用程序概述 | Microsoft Docs"
description: "介绍 Azure 托管应用程序的概念"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 51732474e7983827988f950d344d36745564dfd2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---

# <a name="azure-managed-applications-overview"></a>Azure 托管应用程序概述

Azure 可让供应商向世界各地的客户提供解决方案。 Microsoft Azure Marketplace 是一个库，其中包含来自第一方和第三方供应商的数百种复杂的多资源模板。 客户可以在数分钟内使用 PaaS 和 SaaS 应用程序进行部署和启动。 虽然它提供了一种非常好的方法来快速部署产品，但客户仍然需要负责维护和更新解决方案。 对于供应商，除了虚拟机映像计费外，无法向客户收取应用程序的使用费用。 而且，供应商无法阻止客户修改关键的应用程序资源，并且无法阻止他人访问构成应用程序的知识产权。 Azure 托管应用程序针对这些问题提供了一个解决方案。 

托管应用程序类似于 Marketplace 中的解决方案模板，但有一个重大差异。 在托管应用程序中，资源预配到由供应商管理的资源组。 资源组存在于客户的订阅中，但供应商租户中的标识有权访问该资源组。

## <a name="advantages-of-managed-applications"></a>托管应用程序的优点

托管应用程序可让托管服务提供商 (MSP)、独立软件供应商 (ISV) 和企业中心 IT 团队通过 Marketplace 或服务目录交付解决方案。 尽管客户将这些托管应用程序部署在他们自己的订阅中，但不需要维护、更新或修复这些应用程序。 供应商会管理和支持这些应用程序。 因此，客户不需要培养应用程序特定的域方面的知识就能管理这些应用程序。 这样，客户便可以自动获取应用程序更新，而无需担心如何排查和诊断应用程序问题。

对于供应商和提供商而言，托管应用程序不仅建立了一个可以通过 Marketplace 销售基础结构和软件的渠道，而且还提供了一种为 Azure 客户附送服务与运营支持的方式。 托管应用程序可让供应商使用 Azure 的计费系统向客户收费，使用模板来管理所部署的应用程序的生命周期。 这些解决方案具有自主性，经封装后提供给客户，使供应商能够提供优质服务。 这种方式不仅会使 PaaS 和 SaaS 供应商受益，还会使希望打包和转售其解决方案的公司中央平台团队和系统集成商受益。

## <a name="managed-application-types"></a>托管应用程序的类型
Azure 托管应用程序采用两种形式 - 服务目录和 Marketplace。
 
### <a name="service-catalog"></a>服务目录  

组织可以使用服务目录创建适用于 Azure 的已批准解决方案的目录，供该组织中的人员使用。 维护此类解决方案目录可为企业中的核心 IT 团队带来帮助。 他们可以确保符合特定的组织标准，同时为组织提供优异的解决方案。 他们可以控制、更新和维护这些应用程序。 组织中的员工可以轻松发现 IT 部门推荐和批准的丰富应用程序集。 客户只能看到他们自己创建的服务目录托管应用程序，或者组织中其他人员与他们共享的托管应用程序。
 
有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
 
有关使用服务目录托管应用程序的信息，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。
 
### <a name="marketplace"></a>Marketplace

托管应用程序通过 Azure 门户中的 Marketplace 提供。 经供应商发布后，这些应用程序可供组织内部或外部的任何人使用。 MSP、ISV 和系统集成商 (SI) 可通过这种方式向所有 Azure 客户提供其解决方案。 客户获得的好处是无需投入精力来了解和维护此类解决方案，即可利用这些解决方案。 目前，发行商可以托管应用程序或非托管解决方案模板的形式发布其产品/服务。 发布的托管应用程序的主要组件包括模板文件（描述预配的资源）和 UI 定义文件（描述如何在门户中显示预配这些资源所需的输入）。 所需的文件打包在 .zip 文件中，通过发布门户上传。
 
有关将托管应用程序发布到 Marketplace 的信息，请参阅 [Marketplace 中的 Azure 托管应用程序](managed-application-author-marketplace.md)。

有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。

## <a name="key-concepts"></a>关键概念

### <a name="managed-resource-group"></a>托管资源组
在其中创建了模板中预配的所有 Azure 资源的资源组。 例如，如果设备创建一个存储帐户，则此资源组将包含存储帐户资源。 它不包含设备资源。

### <a name="appliance-package"></a>设备包
发布者创建包含模板文件和 createUIDefinition 文件的包。 具体而言，它包含以下文件：

- **applianceMainTemplate.json** - 一个模板文件，它定义了由设备预配的所有资源。 此文件是一个用于创建资源的常规模板文件。

- **MainTemplate.json** - 一个模板文件，它定义了设备资源 (Microsoft.Solutions/appliances)。 此资源中定义的一个关键属性是 ManagedResourceGroupId。 此属性指示哪个资源组用于承载 applianceMainTemplate.json 中定义的实际资源。

- **applianceCreateUIDefinition.json** - 此文件描述了如何呈现模板中定义的参数所需的 UI。

### <a name="authorization"></a>授权
发布者需要指定供应商代表客户来管理资源时所需的权限。 此权限应用于托管资源组。 需要设置以下值：

- **PrincipalID** - 用来授予对托管资源组的访问权限的用户、组或应用程序的 Azure AD 标识符。 此标识符属于发布者的租户。

- **RoleDefinitionID** - 分配给前面的 principalID 的角色的 Azure AD 标识符。 它可以是发布者的租户中的任何内置 RBAC 角色。 有关详细信息，请参阅 [Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md)。

## <a name="next-steps"></a>后续步骤

* 有关将托管应用程序发布到 Marketplace 的信息，请参阅 [Marketplace 中的 Azure 托管应用程序](managed-application-author-marketplace.md)。
* 有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。
* 有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
* 有关使用服务目录托管应用程序的信息，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。
* 若要创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
