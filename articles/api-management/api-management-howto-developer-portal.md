---
title: Azure API 管理开发人员门户概述
titleSuffix: Azure API Management
description: 了解 API 管理中的开发人员门户。 开发人员门户是使用者可以在其中找到你的 Api 的位置。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/28/2020
ms.author: apimpm
ms.openlocfilehash: 6a8c4c3fa2bd73fa689458d6877d09900ea86938
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852151"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理开发人员门户概述

开发人员门户是一个自动生成的、完全可自定义的网站，其中包含 API 的文档。 API 使用者可在其中找到 API、了解 API 的用法、请求访问权限以及试用这些 API。

本文介绍了 API 管理中开发人员门户的自承载版本与托管版本之间的差异。 此外介绍此门户的体系结构，并提供常见问题的解答。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> <a name="migrate-from-legacy"></a>新的开发人员门户与旧版开发人员门户不兼容，因此不能自动迁移。 需要手动重新创建 (页面、文本、媒体文件) 的内容，并自定义新门户的外观。 有关指南，请参阅[开发人员门户教程](api-management-howto-developer-portal-customize.md)。

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a> 托管版本和自承载版本

可通过两种方式构建开发人员门户：

- **托管版本** - 通过编辑和自定义 API 管理实例中内置的、可通过 URL `<your-api-management-instance-name>.developer.azure-api.net` 访问的门户。 请参阅[此文档](api-management-howto-developer-portal-customize.md)了解如何访问和自定义托管门户。
- **自承载版本** - 通过在 API 管理实例外部部署和自承载门户。 使用此方法可编辑门户的基本代码并扩展提供的核心功能，例如实现自定义小组件以便与第三方系统集成。 在此方案中，你是门户的维护人员，负责将门户升级到最新版本。 有关详细信息和说明，请参阅[包含门户源代码的 GitHub 存储库][1]和[有关实现小组件的教程][3]。 [托管版本的教程](api-management-howto-developer-portal-customize.md)演练了门户的管理面板，该面板在托管版本和自承载版本中很常见。

## <a name="portal-architectural-concepts"></a>门户体系结构概念

门户组件在逻辑上可以划分为两个类别：代码和内容。 

代码在 [GitHub 存储库][1]中维护，包括：

- 小组件 - 呈现视觉元素并组合了 HTML、JavaScript、样式功能、设置和内容映射。 例如，图像、文本段落、表单、API 列表等。
- 样式定义 - 指定如何设置小组件的样式
- 引擎 - 基于门户内容生成静态网页，是以 JavaScript 编写的
- 视觉编辑器 - 用于浏览器内部的自定义和创作体验

内容划分为两个子类别：门户内容和 API 管理内容。  

门户内容特定于门户，包括：

- 页面 - 例如登陆页、API 教程和博客文章
- 媒体 - 图像、动画和其他基于文件的内容
- 布局 - 与 URL 匹配的模板，定义页面显示方式
- 样式 - 样式定义值，例如字体、颜色和边框
- 设置 - 网站图标、网站元数据等配置

门户内容（媒体除外）以 JSON 文档的形式表示。

API 管理内容包括 API、操作、产品和订阅等实体。

门户基于 [Paperbits 框架](https://paperbits.io/)的改编分叉。 原始 Paperbits 功能已经过扩展，提供特定于 API 管理的小组件（例如 API 列表、产品列表）以及与 API 管理服务之间的连接器，可以保存和检索内容。

## <a name="frequently-asked-questions"></a><a name="faq"></a> 常见问题

本部分解答有关开发人员门户的一般性常见问题。 有关自承载版本的特定问题，请参阅 [GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a>如何从门户预览版迁移？

你已使用开发人员门户预览版在 API 管理服务中预配了预览内容。 为了提供更好的用户体验，默认内容已在正式版中进行重大修改。 正式版中还包括新的小组件。

如果使用的是托管版本，请通过单击“操作”菜单部分中的“重置内容”来重置门户内容。  确认此操作会删除门户的所有内容并预配新的默认内容。 门户引擎已在 API 管理服务中自动更新。

![重置门户内容](media/api-management-howto-developer-portal/reset-content.png)

如果使用的是自承载版本，请使用 GitHub 存储库中的 `scripts/cleanup.bat` 和 `scripts/generate.bat` 删除现有内容并预配新内容。 确保提前将门户代码升级到 GitHub 存储库中的最新版本。

如果不想要重置门户内容，可以考虑在整个页面中使用新的可用小组件。 现有的小组件已自动更新到最新版本。

如果门户是宣布推出正式版后预配的，则它应已具有新的默认内容。 你无需在自己的一端执行任何操作。

### <a name="does-the-portal-have-all-the-features-of-the-legacy-portal"></a>门户是否包含旧版门户的所有功能？

开发人员门户不再支持“应用程序”和“问题” 。

### <a name="has-the-legacy-portal-been-deprecated"></a>旧门户是否已弃用？

旧的开发人员和发布者门户现在是*旧*功能，它们仅接收安全更新。 新功能只会在新开发人员门户中实现。

旧门户弃用时，我们会另行通告。 若有问题、疑虑或意见，请提出[相关的 GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/121)。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>门户不支持我需要的功能

可以打开[功能请求](https://aka.ms/apimwish)或 [自行实现缺失的功能”][3]。 如果自行实现此功能，则可以自承载开发人员门户，或在 GitHub 上提交拉取请求，以便在托管版本中包含所需更改。

### <a name="how-can-i-automate-portal-deployments"></a>如何自动部署门户？

不管使用的是托管版本还是自承载版本，都可以通过 REST API 以编程方式访问和管理开发人员门户的内容。

[GitHub 存储库的 Wiki 部分][2]介绍了该 API。 可以使用该 API 在环境之间自动迁移门户内容（例如，从测试环境迁移到生产环境）。 可以在 GitHub 上的[此文档](https://aka.ms/apimdocs/migrateportal)中详细了解此过程。

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>如何实现从托管版本迁移到自承载版本？

请参阅[GitHub 上的开发人员门户存储库的 Wiki 部分][2]中的详细文章。

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>能否在一个 API 管理服务中有多个开发人员门户？

可以有一个托管门户和多个自承载门户。 所有门户的内容都存储在同一个 API 管理服务中，因此它们是相同的。 如果你想要区分门户的外观和功能，你可以将它们自行托管为你自己的自定义小组件，以便在运行时上动态自定义页面（例如基于 URL）。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>门户是否支持 Azure 资源管理器模板，和/或是否与 API 管理 DevOps 资源工具包兼容？

否。

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>门户的内容是否随 API 管理中的备份/还原功能一起保存？

否。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>是否需要为托管门户依赖项启用额外的 VNet 连接？

大多数情况下是不需要的。

如果 API 管理服务位于内部 VNet 中，则只能从网络内部访问开发人员门户。 管理终结点的主机名必须解析成用于访问门户管理界面的计算机中的服务的内部 VIP。 请确保管理终结点已在 DNS 中注册。 如果配置不当，将会出现以下错误：`Unable to start the portal. See if settings are specified correctly in the configuration (...)`。

如果 API 管理服务位于内部 VNet 中，并且是通过 Internet 上的应用程序网关访问它，请确保启用与开发人员门户和“API 管理”的管理终结点的连接。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我已分配一个自定义 API 管理域，但发布的门户无法正常工作

更新域后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我已添加一个标识提供者，但门户中未显示它

配置标识提供者（例如 AAD、AAD B2C）之后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已设置委托，但门户不使用它

设置委托后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理配置更改未传播到开发人员门户中

大多数配置更改（例如，VNet、登录和产品条款）都需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>使用交互式控制台时出现 CORS 错误

交互式控制台从浏览器发出客户端 API 请求。 通过在 API 中添加 [CORS 策略](api-management-cross-domain-policies.md#CORS)解决 CORS 问题。

可以在 Azure 门户的 API 管理服务的“门户概述”部分中检查 CORS 策略的状态。 警告框指示缺少策略或策略配置不正确。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cors-azure-portal.png)

通过单击“启用 CORS”按钮自动应用 CORS 策略。

还可以手动启用 CORS。

1. 单击“在全局级别上手动应用”链接以查看生成的策略代码。
2. 在 Azure 门户中导航到 API 管理服务的“API”部分的“所有 API” 。
3. 在“入站处理”部分中，单击 </> 图标 。
4. 将策略插入到 XML 文件的 <inbound> 部分中。 请确保 <origin> 值与开发人员门户的域匹配。

> [!NOTE]
> 
> 如果在产品范围而不是 API 范围内应用 CORS 策略，并且 API 通过标头使用订阅密钥身份验证，则控制台无法正常工作。
>
> 浏览器会自动发出 OPTIONS HTTP 请求，但该请求不包含带有订阅密钥的标头。 由于缺少订阅密钥，API 管理无法将 OPTIONS 调用与产品相关联，因此也就无法应用 CORS 策略。
>
> 解决方法之一是在查询参数中传递订阅密钥。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>编辑开发人员门户需要哪些权限？

如果在管理模式下打开门户时出现 `Oops. Something went wrong. Please try again later.` 错误，则原因可能是缺少所需的权限 (RBAC)。

旧门户需要服务范围 (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) 的 `Microsoft.ApiManagement/service/getssotoken/action` 权限，以允许用户管理员访问门户。 新门户需要 `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` 范围的 `Microsoft.ApiManagement/service/users/token/action` 权限。

可以使用以下 PowerShell 脚本创建拥有所需权限的角色。 请记得更改 `<subscription-id>` 参数。 

```powershell
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
 
创建角色后，可以通过 Azure 门户中的“访问控制(IAM)”部分将其授予任何用户。 将此角色分配给用户会在服务范围分配权限。 该用户可以代表服务中的任何用户生成 SAS 令牌。 最起码需要将此角色分配给服务的管理员。 以下 PowerShell 命令演示如何在最低范围将角色分配给用户 `user1`，以避免向该用户授予不必要的权限： 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

向用户授予权限后，该用户必须注销并重新登录到 Azure 门户，才能使新权限生效。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>出现 `Unable to start the portal. See if settings are specified correctly (...)` 错误

对 `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` 发出 `GET` 调用失败时会显示此错误。 该调用是在浏览器中通过门户的管理界面发出的。

如果 API 管理服务位于 VNet 中，请参阅前面的 VNet 连接问题。

调用失败也可能是 TLS/SSL 证书引起的，该证书已分配到自定义域，且不受浏览器信任。 作为缓解措施，可以删除管理终结点自定义域 - API 管理将回退到包含受信任证书的默认终结点。

### <a name="whats-the-browser-support-for-the-portal"></a>门户对浏览器的支持是什么？

| 浏览者                     | 支持       |
|-----------------------------|-----------------|
| Apple Safari                | 是<sup>1</sup> |
| Google Chrome               | 是<sup>1</sup> |
| Microsoft Edge              | 是<sup>1</sup> |
| Microsoft Internet Explorer | 否              |
| Mozilla Firefox             | 是<sup>1</sup> |

 <small><sup>1</sup> 在两个最新的生产版本中受支持。</small>

## <a name="next-steps"></a>后续步骤

详细了解新开发人员门户：

- [访问和自定义托管开发人员门户](api-management-howto-developer-portal-customize.md)
- [设置自承载版本的门户][2]
- [实现自己的小组件][3]

浏览其他资源：

- [包含源代码的 GitHub 存储库][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
