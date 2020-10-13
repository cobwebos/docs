---
title: 关于 Azure AD B2C 中的 API 连接器
description: 使用 Azure Active Directory (Azure AD) API 连接器通过使用 web Api 自定义和扩展注册用户流。
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 09/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 195101a432d16c2236ea2d164416e75df33b12e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828337"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>使用 API 连接器自定义和扩展注册用户流

## <a name="overview"></a>概述 
作为开发人员或 IT 管理员，你可以使用 API 连接器将注册用户流与 web Api 集成，以自定义注册体验。 利用 API 连接器，你可以：

- **执行身份验证**。 使用身份验证服务为帐户创建决策添加额外的安全级别。
- 验证用户输入数据。 针对格式不正确或无效的用户数据进行验证。 例如，可以根据外部数据存储或允许值列表中的现有数据验证用户提供的数据。 如果无效，你可以要求用户提供有效数据或阻止用户继续注册流。
- **与自定义批准工作流集成**。 连接到用于管理和限制帐户创建的自定义审批系统。
- **覆盖用户属性**。 重新格式化或为从用户收集的属性赋值。 例如，如果用户使用全小写或全大写字母输入了名字，则你可以设置该名字的格式，只将第一个字母大写。 
- 运行自定义业务逻辑。 可以在云系统中触发下游事件，以发送推送通知、更新企业数据库、管理权限、审核数据库以及执行其他自定义操作。

API 连接器为 Azure Active Directory 提供调用 API 所需的信息，包括终结点 URL 和身份验证。 配置 API 连接器后，可以为用户流中的特定步骤启用它。 当用户在注册流中到达该步骤时，将调用 API 连接器并将其具体化为 API 的 HTTP POST 请求，并将用户信息作为 JSON 正文中的键值对发送 ( "声明" ) 。 API 响应可能会影响用户流的执行。 例如，API 响应会阻止用户进行注册，要求用户重新输入信息，或覆盖并追加用户属性。

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>可在其中启用用户流中的 API 连接器

用户流中有两个位置可用于启用 API 连接器：

- 使用标识提供者登录后
- 创建用户之前

> [!IMPORTANT]
> 在这两种情况下，将在用户 **注册**过程中调用 API 连接器，而不是登录。

### <a name="after-signing-in-with-an-identity-provider"></a>使用标识提供者登录后

在用户使用标识 (提供者（如 Google、Facebook、& Azure AD) 进行身份验证后，会立即调用注册过程中的 API 连接器。 此步骤优先于 " ***属性集合" 页***，它是向用户显示的用于收集用户属性的窗体。 如果用户正在使用本地帐户注册，则不会调用此步骤。 下面是可以在此步骤中启用的 API 连接器方案示例：

- 使用用户提供的电子邮件或联合标识在现有系统中查找声明。 从现有系统返回这些声明，预先填充 "属性集合" 页，并使它们可在标记中返回。
- 根据社交标识实现允许或阻止列表。

### <a name="before-creating-the-user"></a>创建用户之前

如果包含 "属性集合" 页，则在注册过程中的此步骤调用 API 连接器。 始终在创建用户帐户之前调用此步骤。 下面是你在注册过程中可能会启用的方案示例：

- 验证用户输入数据并要求用户重新提交数据。
- 阻止用户根据用户输入的数据进行注册。
- 执行身份验证。
- 在外部系统中查询有关用户的现有数据，以在应用程序令牌中返回它，或将其存储在 Azure AD 中。


## <a name="next-steps"></a>后续步骤
- 了解如何向 [用户流添加 API 连接器](add-api-connector.md)
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->