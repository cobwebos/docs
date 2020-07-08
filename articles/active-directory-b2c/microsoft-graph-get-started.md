---
title: 使用 Microsoft Graph 管理资源
titleSuffix: Azure AD B2C
description: 通过注册向其授予了所需 Graph API 权限的应用程序，准备使用 Microsoft Graph 管理 Azure AD B2C 资源。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d95b45b9be0893282a532bae9ec0278c3a141686
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385920"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C

利用 [Microsoft Graph][ms-graph]，你可以管理 Azure AD B2C 租户中的许多资源，包括客户用户帐户和自定义策略。 通过编写调用 [Microsoft Graph API][ms-graph-api] 的脚本或应用程序，你可以自动执行租户管理任务，例如：

* 将现有用户存储迁移到 Azure AD B2C 租户
* 使用 Azure DevOps 中的 Azure 管道部署自定义策略，以及管理自定义策略密钥
* 在你自己的页面上托管用户注册，并在后台在 Azure AD B2C 目录中创建用户帐户
* 自动执行应用程序注册
* 获取审核日志

以下各部分可帮助你为使用 Microsoft Graph API 自动管理 Azure AD B2C 目录中的资源做准备。

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API 交互模式

使用 Microsoft Graph API 管理 Azure AD B2C 租户中的资源时，可以使用两种通信模式：

* **交互式** - 适用于运行一次的任务，你可以使用 B2C 租户中的管理员帐户执行管理任务。 此模式要求管理员在调用 Microsoft Graph API 之前使用其凭据进行登录。

* **自动化** - 对于计划的或连续运行的任务，此方法使用你为其配置了执行管理任务所需权限的服务帐户。 在 Azure AD B2C 中创建 "服务帐户"，方法是注册应用程序和脚本用来通过其*应用程序（客户端） ID*和**OAuth 2.0 客户端凭据**grant 进行身份验证的应用程序。 在这种情况下，应用程序以自己的身份调用 Microsoft Graph API，而不是如前面的交互方式中所述使用管理员用户的身份。

可以通过创建以下各部分中所示的应用程序注册来启用**自动化**交互方案。

尽管 Azure AD B2C 身份验证服务当前不直接支持 OAuth 2.0 客户端凭据授予流，但你可以使用 Azure AD B2C 租户中的应用程序的 Azure AD 和 Microsoft 标识平台/token 终结点设置客户端凭据流。 Azure AD B2C 租户与 Azure AD 企业租户共享某些功能。

## <a name="register-management-application"></a>注册管理应用程序

你需要在你的 Azure AD B2C 租户中创建应用程序注册，以便能够与[MICROSOFT GRAPH api][ms-graph-api]交互，以管理 Azure AD B2C 资源，你需要在该租户中创建应用程序注册。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，*managementapp1*。
1. 选择“仅此组织目录中的帐户”****。
1. 在“权限”下，清除“授予对 openid 和 office_access 权限的管理员许可”复选框******。
1. 选择“注册”。
1. 记下应用“概述”页上显示的“应用程序(客户端) ID”****。 在稍后的步骤中会使用此值。

### <a name="grant-api-access"></a>授予 API 访问权限

接下来，向已注册的应用程序授予通过调用 Microsoft Graph API 来操作租户资源的权限。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>创建客户端机密

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

现在，你有一个应用程序，该应用程序有权在 Azure AD B2C 租户中*创建*、*读取*、*更新*和*删除*用户。 转到下一节以添加*密码更新*权限。

## <a name="enable-user-delete-and-password-update"></a>启用用户删除和密码更新

"*读取和写入目录数据*" 权限**不**包括删除用户或更新用户帐户密码的功能。

如果你的应用程序或脚本需要删除用户或更新其密码，请为你的应用程序分配 "*用户管理员*" 角色：

1. 登录到[Azure 门户](https://portal.azure.com)，并使用**目录 + 订阅**筛选器切换到 Azure AD B2C 租户。
1. 搜索并选择“Azure AD B2C”。
1. 在“管理”下，选择“角色和管理员”。********
1. 选择“用户管理员”角色。****
1. 选择 "**添加分配**"。
1. 在“选择”文本框中，输入前面注册的应用程序的名称，例如 *managementapp1*。**** 该应用程序显示在搜索结果中后，请将它选中。
1. 选择 **添加** 。 可能需要几分钟才能完全传播权限。

## <a name="next-steps"></a>后续步骤
现在，你已注册了管理应用程序并向其授予了必需的权限，你的应用程序和服务（例如 Azure Pipelines）可以使用其凭据和权限与 Microsoft Graph API 交互。 

* [从 Azure AD 获取访问令牌](https://docs.microsoft.com/graph/auth-v2-service#4-get-an-access-token)
* [使用访问令牌调用 Microsoft Graph](https://docs.microsoft.com/graph/auth-v2-service#4-get-an-access-token)
* [Microsoft Graph 支持的 B2C 操作](microsoft-graph-operations.md)
* [使用 Microsoft Graph 管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)
* [使用 Azure AD 报告 API 获取审核日志](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview