---
title: "Azure 托管应用程序概述 | Microsoft 文档"
description: "介绍 Azure 托管应用程序的概念"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/19/2017
ms.author: gauravbh
ms.openlocfilehash: 96b07bb3b923a5120e3d43c3fa60b3b1308010b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-overview"></a>Azure 托管应用程序概述

使用 Azure 的供应商可向世界各地的客户提供解决方案。 Azure Marketplace 是一个库，其中包含来自第一方和第三方供应商的数百种复杂的多资源模板。 客户可以在几分钟之内部署和启动使用平台即服务 (PaaS) 和服务型软件 (SaaS) 应用程序。 

虽然 Marketplace 提供了一种非常好的方法来快速部署产品/服务，但客户仍然需要负责维护和更新解决方案。 除了虚拟机映像计费外，供应商无法向客户收取应用程序的使用费用。 此外，供应商不能阻止客户修改关键应用程序资源， 也无法阻止对构成应用程序知识产权的访问。 Azure 托管应用程序针对这些问题提供了解决方案。 

托管应用程序类似于 Marketplace 中的解决方案模板，但有一个重大差异。 在托管应用程序中，资源被预配到由供应商管理的资源组。 资源组存在于客户的订阅中，但供应商租户中的标识有权访问该资源组。

## <a name="advantages-of-managed-applications"></a>托管应用程序的优点

托管服务提供商 (MSP)、ISV 和企业中心 IT 团队可以使用托管应用程序通过 Marketplace 或服务目录交付解决方案。 尽管客户将这些托管应用程序部署在他们自己的订阅中，但不需要维护、更新或修复这些应用程序。 由于供应商管理和支持应用程序，因此客户不需要培养应用程序特定的域方面的知识就能管理这些应用程序。 这样，客户便可以自动获取应用程序更新，而无需担心如何排查和诊断应用程序问题。

对于供应商和提供商，托管应用程序会创建一个通道，以通过 Marketplace 销售基础结构和软件。 另外，托管应用程序还提供了将服务和操作支持附加到 Azure 客户的方法。 供应商可以通过使用 Azure 计费系统向客户收取费用。 他们可以使用模板来管理已部署应用程序的生命周期。 这些解决方案具有自主性，经封装后提供给客户，使供应商能够提供优质服务。 此方法有利于 PaaS 和 SaaS 供应商。 它还可以为希望打包和转售其解决方案的企业中央平台团队和系统集成者 (SI) 提供帮助。

## <a name="managed-application-types"></a>托管应用程序的类型
Azure 托管应用程序有两种类型：服务目录和 Marketplace。
 
### <a name="service-catalog"></a>服务目录  

通过服务目录，客户可以创建适用于 Azure 的已批准解决方案的目录，以供其组织中的人员使用。 维护此类解决方案目录可为企业中的核心 IT 团队带来帮助。 他们可以使用该目录来确保符合特定的组织标准，同时为组织提供解决方案。 他们可以控制、更新和维护这些应用程序。 员工可以使用目录轻松发现 IT 部门推荐和批准的丰富应用程序集。 客户会看到他们创建的服务目录托管应用程序。 他们还可以看到组织中其他人员与他们共享的托管应用程序。
 
有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
 
有关使用服务目录托管应用程序的信息，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。
 
### <a name="marketplace"></a>应用商店

托管应用程序通过 Azure 门户中的 Marketplace 提供。 经供应商发布后，这些应用程序可供组织内部或外部的任何人使用。 MSP、ISV 和 SI 可通过这种方式向所有 Azure 客户提供其解决方案。 客户获得的好处是无需投入精力来了解和维护此类解决方案，即可使用这些复杂的解决方案。 

目前，发行商可作为托管应用程序或非托管解决方案模板的形式发布其产品/服务。 发布托管应用程序的主要组件包括模板文件和 UI 定义文件。 模板文件描述预配的资源。 UI 定义文件描述用于预配这些资源所需的输入如何显示在门户中。 所需的文件打包在 .zip 文件中，通过发布门户上传。
 
有关在 Marketplace 中发布托管应用程序的信息，请参阅 [Marketplace 中的 Azure 托管应用程序](managed-application-author-marketplace.md)。

有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。

## <a name="key-concepts"></a>关键概念

### <a name="managed-resource-group"></a>托管资源组
在其中创建了模板中预配的所有 Azure 资源的托管资源组。 例如，如果使用应用程序创建一个存储帐户，则此资源组将包含存储帐户资源。 它不包含应用程序资源。

### <a name="application-package"></a>应用程序包
发布者创建包含模板文件和 createUIDefinition 文件的包。 具体而言，它包含以下文件：

- **mainTemplate.json**：此模板文件定义了由应用程序预配的所有资源。 此文件是一个用于创建资源的常规模板文件。

- **createUIDefinition.json**：此文件介绍了如何呈现模板中定义的参数所需的 UI。

### <a name="authorization"></a>授权
发布者必须指定供应商代表客户来管理资源时所需的权限。 此权限应用于托管资源组。 设置以下值：

- PrincipalID：用来授予对托管资源组的访问权限的用户、组或应用程序的 Azure Active Directory (Azure AD) 标识符。 此标识符属于发布者的租户。

- RoleDefinitionID：分配给前面的 principalID 的角色的 Azure AD 标识符。 它可以是发布者租户中的任何基于角色的访问控制的内置角色。 有关详细信息，请参阅 [Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md)。

## <a name="next-steps"></a>后续步骤

* 有关将托管应用程序发布到 Marketplace 的信息，请参阅 [Marketplace 中的 Azure 托管应用程序](managed-application-author-marketplace.md)。
* 有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。
* 有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
* 有关使用服务目录托管应用程序的信息，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。
* 若要创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
