---
title: 使用 Microsoft 图形管理资源
titleSuffix: Azure AD B2C
description: 通过注册被授予所需图形 API 权限的应用程序，准备使用 Microsoft 图形管理 Azure AD B2C 资源。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184329"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>使用微软图形管理 Azure AD B2C

[Microsoft 图形][ms-graph]允许您管理 Azure AD B2C 租户中的许多资源，包括客户用户帐户和自定义策略。 通过编写调用 Microsoft 图形[API][ms-graph-api]的脚本或应用程序，可以自动执行租户管理任务，例如：

* 将现有用户存储迁移到 Azure AD B2C 租户
* 在 Azure DevOps 中使用 Azure 管道部署自定义策略，并管理自定义策略键
* 在您自己的页面上托管用户注册，并在后台在 Azure AD B2C 目录中创建用户帐户
* 自动注册应用程序
* 获取审核日志

以下各节可帮助您准备使用 Microsoft 图形 API 自动管理 Azure AD B2C 目录中的资源。

## <a name="microsoft-graph-api-interaction-modes"></a>微软图形API交互模式

使用 Microsoft 图形 API 管理 Azure AD B2C 租户中的资源时，可以使用两种通信模式：

* **交互式**- 适合运行一次的任务，您可以使用 B2C 租户中的管理员帐户来执行管理任务。 此模式要求管理员在调用 Microsoft 图形 API 之前使用其凭据登录。

* **自动化**- 对于计划或连续运行的任务，此方法使用您配置的服务帐户，该服务帐户具有执行管理任务所需的权限。 通过注册应用程序和脚本使用应用程序 *（客户端） ID*和 OAuth 2.0 客户端凭据授予进行身份验证的应用程序，在 Azure AD B2C 中创建"服务帐户"。 在这种情况下，应用程序充当自身来调用 Microsoft 图形 API，而不是管理员用户，如前面描述的交互式方法所示。

通过创建以下各节中所示的应用程序注册，可以启用**自动**交互方案。

## <a name="register-management-application"></a>注册管理应用程序

在脚本和应用程序可以与 Microsoft[图形 API][ms-graph-api]进行交互以管理 Azure AD B2C 资源之前，需要在 Azure AD B2C 租户中创建应用程序注册，该租户授予所需的 API 权限。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>授予 API 访问权限

接下来，授予注册的应用程序权限，以便通过调用 Microsoft Graph API 来操作租户资源。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>创建客户端机密

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

现在，您拥有在 Azure AD B2C 租户中*创建*、*读取*、*更新*和*删除*用户的权限的应用程序。 继续下一节添加*密码更新*权限。

## <a name="enable-user-delete-and-password-update"></a>启用用户删除和密码更新

*读取和写入目录数据*权限**不包括**删除用户或更新用户帐户密码的功能。

如果应用程序或脚本需要删除用户或更新其密码，请为应用程序分配*用户管理员*角色：

1. 登录到 Azure[门户](https://portal.azure.com)并使用**目录 + 订阅**筛选器切换到 Azure AD B2C 租户。
1. 搜索并选择**Azure AD B2C**。
1. 在“管理”下，选择“角色和管理员”。********
1. 选择“用户管理员”角色。****
1. 选择 **"添加分配**"。
1. 在“选择”文本框中，输入前面注册的应用程序的名称，例如 *managementapp1*。**** 该应用程序显示在搜索结果中后，请将它选中。
1. 选择“添加”****。 可能需要几分钟才能完全传播权限。

## <a name="next-steps"></a>后续步骤

现在，您已注册管理应用程序并授予它所需的权限，您的应用程序和服务（例如，Azure 管道）可以使用其凭据和权限与 Microsoft 图形 API 进行交互。

* [微软图形支持的 B2C 操作](microsoft-graph-operations.md)
* [使用微软图形管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)
* [使用 Azure AD 报告 API 获取审核日志](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
