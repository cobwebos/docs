---
title: Azure API 管理开发人员门户概述
titleSuffix: Azure API Management
description: 了解 API 管理中的开发人员门户。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335912"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理开发人员门户概述

开发人员门户是一个自动生成的、完全可自定义的网站，其中包含 API 的文档。 API 使用者可在其中找到 API、了解 API 的用法、请求访问权限以及试用这些 API。

本文介绍了 API 管理中开发人员门户的自承载版本与托管版本之间的差异。 此外介绍此门户的体系结构，并提供常见问题的解答。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a> 托管版本和自承载版本

可通过两种方式构建开发人员门户：

- **托管版本** - 通过编辑和自定义 API 管理实例中内置的、可通过 URL `<your-api-management-instance-name>.developer.azure-api.net` 访问的门户。 请参阅[此文档](api-management-howto-developer-portal-customize.md)了解如何访问和自定义托管门户。
- **自承载版本** - 通过在 API 管理实例外部部署和自承载门户。 此方法允许您编辑门户的代码库并扩展提供的核心功能 - 例如，实现自定义小部件以便与第三方系统集成。 在这种情况下，您是门户的维护者，您负责将门户升级到最新版本。 有关详细信息和说明，请参阅[包含门户源代码的 GitHub 存储库][1]以及[有关实现小部件的教程][3]。 [托管版本的教程](api-management-howto-developer-portal-customize.md)将介绍门户的管理面板，这是托管版本和自托管版本常见的。

## <a name="portal-architectural-concepts"></a>门户体系结构概念

门户组件在逻辑上可以划分为两个类别：代码和内容。****

代码在 [GitHub 存储库][1]中维护，包括：**

- 小组件 - 呈现视觉元素并组合了 HTML、JavaScript、样式功能、设置和内容映射。 例如，图像、文本段落、表单、API 列表等。
- 样式定义 - 指定如何设置小组件的样式
- 引擎 - 基于门户内容生成静态网页，是以 JavaScript 编写的
- 视觉编辑器 - 用于浏览器内部的自定义和创作体验

内容划分为两个子类别：门户内容和 API 管理内容。******

门户内容特定于门户，包括：**

- 页面 - 例如登陆页、API 教程和博客文章
- 媒体 - 图像、动画和其他基于文件的内容
- 布局 - 与 URL 匹配的模板，定义页面显示方式
- 样式 - 样式定义值，例如字体、颜色和边框
- 设置 - 网站图标、网站元数据等配置

门户内容（媒体除外）以 JSON 文档的形式表示。**

API 管理内容包括 API、操作、产品和订阅等实体。**

门户基于 [Paperbits 框架](https://paperbits.io/)的改编分叉。 原始 Paperbits 功能已经过扩展，提供特定于 API 管理的小组件（例如 API 列表、产品列表）以及与 API 管理服务之间的连接器，可以保存和检索内容。

## <a name="frequently-asked-questions"></a><a name="faq"></a>常见问题

在本节中，我们将回答有关开发人员门户的常见问题，这些问题具有一般性质。 有关自承载版本的特定问题，请参阅 [GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> 如何从门户预览版迁移？

你已使用开发人员门户预览版在 API 管理服务中预配了预览内容。 为了提供更好的用户体验，默认内容已在正式版中进行重大修改。 正式版中还包括新的小组件。

如果使用的是托管版本，请通过单击“操作”菜单部分中的“重置内容”来重置门户内容。******** 确认此操作会删除门户的所有内容并预配新的默认内容。 门户引擎已在 API 管理服务中自动更新。

![重置门户内容](media/api-management-howto-developer-portal/reset-content.png)

如果使用的是自承载版本，请使用 GitHub 存储库中的 `scripts/cleanup.bat` 和 `scripts/generate.bat` 删除现有内容并预配新内容。 确保提前将门户代码升级到 GitHub 存储库中的最新版本。

如果不想要重置门户内容，可以考虑在整个页面中使用新的可用小组件。 现有的小组件已自动更新到最新版本。

如果门户是宣布推出正式版后预配的，则它应已具有新的默认内容。 你无需在自己的一端执行任何操作。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>如何从旧的开发人员门户迁移到开发人员门户？

这两个门户不兼容，需要手动迁移内容。

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>门户是否具有旧门户的所有功能？

开发人员门户不再支持*应用程序*和*问题*。

目前尚不支持在交互式开发人员控制台中使用 OAuth 进行身份验证。 可以通过 [GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/208)跟踪进度。

### <a name="has-the-old-portal-been-deprecated"></a>旧门户是否已弃用？

旧的开发人员和发布者门户现在属于旧版功能 - 它们只会接收安全更新。** 新功能只会在新开发人员门户中实现。

旧门户弃用时，我们会另行通告。 若有问题、疑虑或意见，请提出[相关的 GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/121)。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>门户不支持我需要的功能

您可以打开[功能请求](https://aka.ms/apimwish)或[自行实现缺少的功能][3]。 如果自己实现该功能，则可以自行托管开发人员门户，或在 GitHub 上打开拉取请求，以在托管版本中包含更改。

### <a name="how-can-i-automate-portal-deployments"></a>如何自动部署门户？

不管使用的是托管版本还是自承载版本，都可以通过 REST API 以编程方式访问和管理开发人员门户的内容。

[GitHub 存储库的 Wiki 部分][2]介绍了该 API。 它可用于自动在环境之间迁移门户内容，例如，从测试环境到生产环境。 可以在 GitHub 上的[此文档](https://aka.ms/apimdocs/migrateportal)中详细了解此过程。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>门户是否支持 Azure 资源管理器模板，和/或是否与 API 管理 DevOps 资源工具包兼容？

不是。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>是否需要为托管门户依赖项启用其他 VNet 连接？

大多数情况下是不需要的。

如果 API 管理服务位于内部 VNet 中，则只能从网络内部访问开发人员门户。 管理终结点的主机名必须解析成用于访问门户管理界面的计算机中的服务的内部 VIP。 请确保管理终结点已在 DNS 中注册。 如果配置不当，将会出现以下错误：`Unable to start the portal. See if settings are specified correctly in the configuration (...)`。

如果您的 API 管理服务位于内部 VNet 中，并且您正在通过 Internet 应用程序网关访问该服务，请确保启用与开发人员门户和 API 管理管理终结点的连接。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我已分配一个自定义 API 管理域，但发布的门户无法正常工作

更新域后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我已添加一个标识提供者，但门户中未显示它

配置标识提供者（例如 AAD、AAD B2C）之后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已设置委托，但门户不使用它

设置委托后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理配置更改未传播到开发人员门户中

大多数配置更改（例如，VNet、登录和产品条款）都需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>使用交互式控制台时，我收到 CORS 错误

交互式控制台从浏览器发出客户端 API 请求。 通过在 API 上添加[CORS 策略来解决 CORS](api-management-cross-domain-policies.md#CORS)问题。

您可以在 Azure 门户中的 API 管理服务的**门户概述**部分中检查 CORS 策略的状态。 警告框指示策略不存在或配置错误。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cors-azure-portal.png)

单击启用 CORS 按钮，自动应用**CORS**策略。

您还可以手动启用 CORS。

1. 单击"**手动将其应用于全局级别**"链接以查看生成的策略代码。
2. 导航到 Azure 门户中 API 管理服务**API**部分中的所有**API。**
3. 单击**</>****"入站处理**"部分中的图标。
4. 将策略插入 XML**<inbound>** 文件部分。 确保**<origin>** 该值与开发人员门户的域匹配。

> [!NOTE]
> 
> 如果在产品范围而不是 API 范围内应用 CORS 策略，并且 API 通过标头使用订阅密钥身份验证，则控制台无法正常工作。
>
> 浏览器会自动发出选项 HTTP 请求，该请求不包含包含订阅密钥的标头。 由于缺少订阅密钥，API 管理无法将 OPTIONS 调用与产品相关联，因此无法应用 CORS 策略。
>
> 解决方法之一是在查询参数中传递订阅密钥。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>编辑开发人员门户需要哪些权限？

如果在管理模式下打开门户时出现 `Oops. Something went wrong. Please try again later.` 错误，则原因可能是缺少所需的权限 (RBAC)。

旧门户需要服务范围 (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) 的 `Microsoft.ApiManagement/service/getssotoken/action` 权限，以允许用户管理员访问门户。 新门户需要 `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` 范围的 `Microsoft.ApiManagement/service/users/token/action` 权限。

可以使用以下 PowerShell 脚本创建拥有所需权限的角色。 请记得更改 `<subscription-id>` 参数。 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
创建角色后，可以通过 Azure 门户中的“访问控制(IAM)”部分将其授予任何用户。**** 将此角色分配给用户会在服务范围分配权限。 该用户可以代表服务中的任何用户生成 SAS 令牌。** 最起码需要将此角色分配给服务的管理员。 以下 PowerShell 命令演示如何在最低范围将角色分配给用户 `user1`，以避免向该用户授予不必要的权限： 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

向用户授予权限后，该用户必须注销并重新登录到 Azure 门户，才能使新权限生效。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>出现 `Unable to start the portal. See if settings are specified correctly (...)` 错误

对 `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` 发出 `GET` 调用失败时会显示此错误。 该调用是在浏览器中通过门户的管理界面发出的。

如果 API 管理服务位于 VNet 中，请参阅前面的 VNet 连接问题。

呼叫失败也可能由 TLS/SSL 证书引起，该证书分配给自定义域，并且不受浏览器信任。 作为缓解措施，可以删除管理终结点自定义域 - API 管理将回退到包含受信任证书的默认终结点。

### <a name="whats-the-browser-support-for-the-portal"></a>浏览器对门户的支持是什么？

| 浏览者                     | 支持       |
|-----------------------------|-----------------|
| Apple Safari                | 是<sup>1</sup> |
| Google Chrome               | 是<sup>1</sup> |
| Microsoft Edge              | 是<sup>1</sup> |
| Microsoft Internet Explorer | 否              |
| Mozilla Firefox             | 是<sup>1</sup> |

 <small><sup>1</sup>在两个最新的生产版本中支持。</small>

## <a name="next-steps"></a>后续步骤

详细了解新开发人员门户：

- [访问和自定义托管开发人员门户](api-management-howto-developer-portal-customize.md)
- [设置自承载版本的门户][2]
- [实现您自己的小部件][3]

浏览其他资源：

- [包含源代码的 GitHub 存储库][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend