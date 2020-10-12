---
title: 关于自助服务注册流中的 API 连接器-Azure AD
description: 使用 Azure Active Directory (Azure AD) API 连接器，通过使用 web Api 自定义和扩展自助注册用户流。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb6e85b6d81de3d4b88ba315ddd35bd5b37ae7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165203"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>使用 API 连接器自定义和扩展自助注册 

## <a name="overview"></a>概述 
作为开发人员或 IT 管理员，你可以使用 API 连接器通过利用 web Api 将 [自助服务注册用户流](self-service-sign-up-overview.md) 与外部系统集成。 例如，可以使用 API 连接器执行以下操作：

- [**与自定义批准工作流集成**](self-service-sign-up-add-approvals.md)。 连接到用于管理帐户创建的自定义审批系统。
- [**执行身份验证**](code-samples-self-service-sign-up.md#identity-verification)。 使用身份验证服务为帐户创建决策添加额外的安全级别。
- 验证用户输入数据。 针对格式不正确或无效的用户数据进行验证。 例如，可以根据外部数据存储或允许值列表中的现有数据验证用户提供的数据。 如果无效，你可以要求用户提供有效数据或阻止用户继续注册流。
- **覆盖用户属性**。 重新格式化或为从用户收集的属性赋值。 例如，如果用户使用全小写或全大写字母输入了名字，则你可以设置该名字的格式，只将第一个字母大写。 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- 运行自定义业务逻辑。 可以在云系统中触发下游事件，以发送推送通知、更新企业数据库、管理权限、审核数据库以及执行其他自定义操作。

API 连接器通过定义 HTTP 终结点 URL 和身份验证向 Azure Active Directory 提供调用 API 终结点所需的信息。 配置 API 连接器后，可以为用户流中的特定步骤启用它。 当用户在注册流中到达该步骤时，将调用 API 连接器并将其具体化为 API 的 HTTP POST 请求，并将用户信息作为 JSON 正文中的键值对发送 ( "声明" ) 。 API 响应可能会影响用户流的执行。 例如，API 响应会阻止用户进行注册，要求用户重新输入信息，或覆盖并追加用户属性。

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>可在其中启用用户流中的 API 连接器

用户流中有两个位置可用于启用 API 连接器：

- 使用标识提供者登录后
- 创建用户之前

> [!IMPORTANT]
> 在这两种情况下，将在用户 **注册**过程中调用 API 连接器，而不是登录。

### <a name="after-signing-in-with-an-identity-provider"></a>使用标识提供者登录后

在用户使用标识提供者进行身份验证 (Google、Facebook、Azure AD) 后，立即调用注册过程中的 API 连接器。 此步骤优先于 " ***属性集合" 页***，它是向用户显示的用于收集用户属性的窗体。 下面是可以在此步骤中启用的 API 连接器方案示例：

- 使用用户提供的电子邮件或联合标识在现有系统中查找声明。 从现有系统返回这些声明，预先填充 "属性集合" 页，并使它们可在标记中返回。
- 验证用户是否包含在允许或拒绝列表中，并控制是否可以继续注册流。

### <a name="before-creating-the-user"></a>创建用户之前

如果包含 "属性集合" 页，则在注册过程中的此步骤调用 API 连接器。 在 Azure AD 中创建用户帐户之前，始终会调用此步骤。 下面是你在注册过程中可能会启用的方案示例：

- 验证用户输入数据并要求用户重新提交数据。
- 阻止用户根据用户输入的数据进行注册。
- 执行身份验证。
- 在外部系统中查询有关用户的现有数据，以在应用程序令牌中返回它，或将其存储在 Azure AD 中。

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="next-steps"></a>后续步骤
- 了解如何向 [用户流添加 API 连接器](self-service-sign-up-add-api-connector.md)
- 了解如何 [将自定义审批系统添加到自助注册](self-service-sign-up-add-approvals.md)