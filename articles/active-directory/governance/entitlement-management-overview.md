---
title: 什么是 Azure AD 授权管理？ （预览版）-Azure Active Directory
description: 获取 Azure Active Directory 权利管理以及如何使用它来管理内部和外部用户的组、 应用程序和 SharePoint Online 网站的访问权限的概述。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb4001e9496d31d9c2879721f8cf8e26b74ddf3
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204555"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>什么是 Azure AD 授权管理？ （预览版）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在组织中的员工需要访问各种组、 应用程序和站点来开展其工作。 管理此访问权限是一个挑战。 在大多数情况下，没有组织的用户需要在项目的所有资源的列表。 项目管理器具有所需的资源，涉及，以及多长的个人项目将持续更好地理解。 但是，项目经理通常没有权限来批准或授予对其他人访问权限。 当你尝试使用外部个人或公司时，这种情况下变得更为复杂。

Azure Active Directory (Azure AD) 授权管理可以帮助您管理的内部用户以及你的组织外部的用户组、 应用程序和 SharePoint Online 网站的访问权限。

## <a name="why-use-entitlement-management"></a>为什么要使用授权管理？

企业组织通常面临着挑战，如管理资源的访问权限时：

- 用户可能不知道它们应具有什么访问权限
- 用户很难找到适当的人员或适当的资源
- 一旦用户查找和接收对资源的访问，可能保留时间超过是业务所需的访问

这些问题会更加复杂的用户需要从另一个目录，例如来自供应链组织或其他业务合作伙伴的外部用户的访问。 例如：

- 组织可能不知道所有其他目录中的特定个体可以邀请
- 即使组织能够邀请这些用户，可能不记得组织一致地管理所有用户的访问权限

Azure AD 授权管理可帮助解决这些难题。

## <a name="what-can-i-do-with-entitlement-management"></a>使用授权管理可以做什么？

下面是一些的权利管理功能：

- 创建用户可以请求的相关资源的包
- 请求资源和访问的到期时定义的规则
- 管理内部和外部用户的访问权限的生命周期
- 委托管理的资源
- 指定审批者批准请求
- 创建报表来跟踪历史记录

有关标识监管和权利管理的概述，请观看以下视频来自 Ignite 2018 大会：

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>可以管理哪些资源？

下面是可以管理的权利管理到访问的资源的类型：

- Azure AD 安全组
- Office 365 组
- Azure AD 企业应用程序，包括 SaaS 应用程序和自定义集成的应用程序支持联合身份验证或预配
- SharePoint Online 站点集合和站点

此外可以控制依赖于 Azure AD 安全组或 Office 365 组的其他资源的访问权限。  例如：

- 可以通过访问包中使用的 Azure AD 安全组和配置 Microsoft Office 365 提供的用户许可证[基于组的许可](../users-groups-roles/licensing-groups-assign.md)该组
- 可通过访问包中使用的 Azure AD 安全组和创建管理 Azure 资源的访问权限授予用户[Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)该组

## <a name="what-are-access-packages-and-policies"></a>访问包和策略是什么？

权利管理引入的概念*访问包*。 访问包是一个用户需要在处理项目或执行其作业的所有资源的捆绑包。 这些资源包括组、 应用程序，或站点的访问权限。 访问包用于在内部员工，以及你的组织外部的用户控制的访问。 在名为 *catalogs* 的容器中定义访问包。

访问包还包括一个或多个*策略*。 策略定义的规则或 guardrails 访问包具有访问权限。 如果启用策略强制实施正确的用户被授予访问权限，到适当的资源，以及适当数量的时间。

![访问包和策略](./media/entitlement-management-overview/elm-overview-access-package.png)

访问包和其策略，访问包管理器定义：

- 资源
- 角色的用户需要的资源
- 内部用户和外部用户有资格请求的访问权限
- 审批过程和用户可以批准或拒绝访问
- 用户的访问权限的持续时间

下图显示在授权管理中的不同元素的示例。 它显示了两个示例访问包。

- **访问包 1**包含作为资源的单个组。 访问使用允许用户在目录中请求访问一组的策略定义。
- **访问包 2**作为资源包括一个组、 应用程序和 SharePoint Online 站点。 使用两个不同的策略定义访问权限。 第一个策略启用的目录中用户请求访问一组。 第二个策略允许外部目录中的用户请求的访问权限。

![授权管理概述](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>外部用户

使用时[Azure AD 企业到企业 (B2B)](../b2b/what-is-b2b.md)邀请的经验，您必须已经知道你想要将引入资源目录，并使用外部来宾用户的电子邮件地址。 此方法效果非常好，当您在处理较小或短期项目和您已经知道所有参与者，但这很难管理如果有大量你想要使用的用户或参与者随时间而变化。  例如，你可能会使用另一个组织并具有与该组织的联系点，但随着时间的推移该组织中的其他用户还需要访问权限。

借助授权管理，可以定义允许您指定的组织也使用 Azure AD 中，可以请求访问包中的用户的策略。 您可以指定是否将需要批准和访问的到期日期。 如果需要审批，还可以指定为审批者你以前受邀-因为它们可能知道其组织的外部用户需要访问外部组织中的一个或多个用户。 配置访问包后，可以将链接到访问包发送到外部组织联系人。 该联系人可以与其他用户共享在外部组织中，并且它们可以使用此链接来请求访问包。  从该组织已邀请到你的目录的用户还可以使用该链接。

在请求得到批准，权利管理将所需的访问，其中可能包括邀请用户，如果它们尚不在目录中与用户预配。 Azure AD 会自动为其创建 B2B 帐户。  请注意，管理员可能具有以前限制哪些组织进行协作、 允许通过设置[B2B 允许或拒绝列表](../b2b/allow-deny-list.md)允许或阻止邀请到其他组织。  如果用户不允许通过允许或阻止列表，然后它们将不能邀请。

由于不希望外部用户的访问权限会永远持续存在，因此在策略中，如 180 天指定过期日期。 180 天后，如果不续订他们的访问权限，则授权管理将删除与该访问包相关联的所有访问。  如果权利管理通过已邀请的用户不具有任何其他访问权限的包分配，然后时，他们会失去其最后一个分配，其 B2B 帐户将被阻止登录的 30 天，并且随后删除。  这可以防止不必要的帐户的发展壮大。  

## <a name="terminology"></a>术语

若要更好地了解授权管理和其文档，应查看以下术语。

| 术语或概念 | 描述 |
| --- | --- |
| 权利管理 | 服务分配，撤消，并且管理访问包。 |
| 访问包 | 权限和策略，以便用户可以请求的资源的集合。 访问包始终包含在目录中。 |
| 访问请求 | 访问访问包的请求。 请求通常可以顺利工作流。 |
| policy | 定义访问生命周期，如用户如何获取访问权限、 谁可以批准和多长时间，用户可以访问的规则集。 示例策略包括员工和外部访问权限。 |
| catalog | 相关的资源和访问包的容器。 |
| 常规目录 | 始终是可用的内置目录。 若要将资源添加到常规的目录中，需要特定权限。 |
| resource | 资产或用户可以被授予访问权限的服务 （例如组、 应用程序或站点）。 |
| 资源类型 | 资源，其中包括组、 应用程序和 SharePoint Online 网站的类型。 |
| 资源角色 | 与资源关联的权限集合。 |
| 资源目录 | 具有一个或多个资源共享的目录。 |
| 已分配的用户 | 向用户或组访问包的工作分配。 |
| enable | 用户可以请求提供访问包的过程。 |

## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

专用的云，例如 Azure 政府、 Azure Germany 和 Azure 中国 21Vianet 不是当前可在此预览版中使用。

## <a name="next-steps"></a>后续步骤

- [教程：创建第一个访问包](entitlement-management-access-package-first.md)
- [常见方案](entitlement-management-scenarios.md)
