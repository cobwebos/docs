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
ms.date: 11/22/2019
ms.author: apimpm
ms.openlocfilehash: a206ef6eb529fc396ec4ecb82d468c19f9e54b8a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108257"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理开发人员门户概述

开发人员门户是一个自动生成的、可完全自定义的网站，其中包含 Api 的文档。 API 使用者可以在其中发现你的 Api、了解如何使用它们、如何请求访问以及如何进行试用。

本文介绍了 API 管理中开发人员门户的自承载和托管版本之间的差异。 它还介绍了其体系结构，并提供常见问题的解答。

> [!WARNING]
>
> [了解如何从预览版迁移到开发人员门户的正式发布版本](#preview-to-ga)。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>托管和自承载版本

可以通过两种方式生成开发人员门户：

- **托管版本**-通过编辑和自定义门户，该门户内置于 API 管理实例，并可通过 URL `<your-api-management-instance-name>.developer.azure-api.net`进行访问。 请参阅[此文档一文](api-management-howto-developer-portal-customize.md)，了解如何访问和自定义托管门户。
- **自承载版本**-通过在 API 管理实例之外部署和自我托管你的门户。 此方法允许您编辑门户的基本代码并扩展提供的核心功能。 还需要自己将门户升级到最新版本。 有关详细信息和说明，请参阅[GitHub 存储库中的门户源代码][1]和[实现小组件教程][3]。 [托管版本教程](api-management-howto-developer-portal-customize.md)介绍了门户的管理面板，该面板还在自承载版本中提供。

## <a name="portal-architectural-concepts"></a>门户体系结构概念

门户组件可以逻辑划分为两个类别：*代码*和*内容*。

*代码*保留在[GitHub 存储库][1]中，其中包括：

- 小组件-表示可视元素并组合 HTML、JavaScript、样式设置功能、设置和内容映射。 例如，图像、文本段落、表单、Api 列表等。
- 样式定义-指定如何设置小组件的样式
- 引擎-从门户内容生成静态网页并以 JavaScript 编写
- 可视编辑器-允许浏览器自定义和创作体验

*内容*分为两个子类别：*门户内容*和*API 管理内容*。

*门户内容*特定于门户，包括：

- 页面-例如登陆页面、API 教程、博客文章
- 媒体-图像、动画和其他基于文件的内容
- 布局-模板与 URL 匹配并定义页面显示方式
- 样式-样式定义的值，例如字体、颜色、边框
- 设置-配置，例如 favicon、网站元数据

*门户内容*（媒体除外）以 JSON 文档的形式表示。

*Api 管理内容*包括 Api、操作、产品和订阅等实体。

门户基于[Paperbits 框架](https://paperbits.io/)的改编分叉。 已对原始 Paperbits 功能进行了扩展，以提供特定于 API 管理的小组件（例如，Api 列表、产品列表）和 API 管理服务的连接器，用于保存和检索内容。

## <a name="faq"></a>常见问题

在本部分中，我们将回答有关新开发人员门户的常见问题，这些问题是一般的。 有关特定于自承载版本的问题，请参阅[GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> 如何从门户预览版本进行迁移？

通过使用开发人员门户的预览版本，你在 API 管理服务中预配了预览内容。 为了获得更好的用户体验，已在正式发布的版本中对默认内容进行了重大修改。 它还包括新的小组件。

如果使用的是托管版本，请通过单击 "**操作**" 菜单部分中的 "**重置内容**" 来重置门户的内容。 确认此操作将删除门户的所有内容并预配新的默认内容。 门户引擎已在 API 管理服务中自动更新。

![重置门户内容](media/api-management-howto-developer-portal/reset-content.png)

如果使用自承载版本，请使用 GitHub 存储库中的 `scripts/cleanup.bat` 和 `scripts/generate.bat` 删除现有内容并预配新内容。 请确保事先将门户的代码升级到 GitHub 存储库中的最新版本。

如果你不希望重置门户的内容，则可以考虑在页面中使用新的可用小组件。 现有小组件已自动更新到最新版本。

如果你的门户是在公开发布后预配的，则它应已具有新的默认内容。 不需要执行任何操作。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>如何从旧开发人员门户迁移到新的开发人员门户？

门户不兼容，需要手动迁移内容。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新门户是否具有旧门户的所有功能？

新的开发人员门户不支持*应用程序*和*问题*。 如果在旧门户中使用了问题，并在新门户中使用了这些*问题*，请在[专用 GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/122)中发布评论。

目前尚不支持在交互式开发人员控制台中通过 OAuth 进行身份验证。 可以通过[GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/208)跟踪进度。

### <a name="has-the-old-portal-been-deprecated"></a>旧门户是否已弃用？

旧的开发人员和发布者门户现在是*旧*功能，它们仅接收安全更新。 新功能将仅在新的开发人员门户中实现。

旧门户的弃用将单独发布。 如果你有疑问、问题或意见，请[在专用 GitHub 问题中](https://github.com/Azure/api-management-developer-portal/issues/121)提出。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>门户中不支持我需要的功能

使用自承载版本并[实现你自己的小组件][3]。

### <a name="how-can-i-automate-portal-deployments"></a>如何自动部署门户？

你可以通过 REST API 以编程方式访问和管理开发人员门户的内容，而不考虑你使用的是托管版本还是自承载版本。

[GitHub 存储库的 wiki 部分][2]介绍了该 API。 它还可用于在环境之间自动迁移门户内容（例如从测试环境到生产环境）。 有关此过程的详细信息，请参阅 GitHub 上的[此文档文章](https://aka.ms/apimdocs/migrateportal)。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>门户是否支持 Azure 资源管理器模板和/或它是否与 API 管理 DevOps 资源工具包兼容？

No。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-new-managed-portal-dependencies"></a>是否需要为新的托管门户依赖项启用附加 VNet 连接？

在大多数情况下-否。

如果 API 管理服务在内部 VNet 中，则只能从网络内部访问开发人员门户。 管理终结点的主机名必须从用于访问门户的管理界面的计算机解析为服务的内部 VIP。 请确保在 DNS 中注册了管理终结点。 如果配置不正确，你将看到错误： `Unable to start the portal. See if settings are specified correctly in the configuration (...)`。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我分配了自定义 API 管理域，但发布的门户不起作用

更新域后，需要重新[发布门户](api-management-howto-developer-portal-customize.md#publish)以使更改生效。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我添加了一个标识提供程序，但在门户中看不到它

配置标识提供者（例如 AAD、AAD B2C）后，需要重新[发布门户](api-management-howto-developer-portal-customize.md#publish)以使更改生效。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已经设置了委派，但门户不使用它

设置委派后，需要重新[发布门户](api-management-howto-developer-portal-customize.md#publish)以使更改生效。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理配置更改尚未在开发人员门户中传播

大多数配置更改（例如，VNet、登录和产品条款）都需要重新[发布门户](api-management-howto-developer-portal-customize.md#publish)。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a>使用交互式控制台时遇到 CORS 错误

交互式控制台从浏览器发出客户端 API 请求。 可以通过在 API 上添加[CORS 策略](api-management-cross-domain-policies.md#CORS)来解决 CORS 问题。 可以手动指定所有参数，也可以使用通配符 `*` 值。 例如：

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

> [!NOTE]
> 
> 如果你在产品范围内应用 CORS 策略，而不是在 API 范围内应用，并且你的 API 通过标头使用订阅密钥身份验证，则你的控制台将无法工作。
>
> 浏览器自动发出 HTTP 请求选项，该请求不包含带有订阅密钥的标头。 由于缺少订阅密钥，API 管理无法将选项调用与产品相关联，因此不能应用 CORS 策略。
>
> 一种解决方法是，可以在查询参数中传递订阅密钥。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>编辑开发人员门户需要哪些权限？

如果在管理模式下打开门户时出现 `Oops. Something went wrong. Please try again later.` 错误，则可能缺少所需的权限（RBAC）。

旧门户要求在服务范围（`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`） `Microsoft.ApiManagement/service/getssotoken/action` 权限，以允许用户管理员访问门户。 新门户需要 `Microsoft.ApiManagement/service/users/token/action` 范围 `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`的权限。

你可以使用以下 PowerShell 脚本创建具有所需权限的角色。 请记住更改 `<subscription-id>` 参数。 

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
 
创建角色后，可以从 Azure 门户中的 "**访问控制（IAM）** " 部分向任何用户授予该角色。 将此角色分配给用户将在服务范围内分配权限。 用户将可以代表服务中的*任何*用户生成 SAS 令牌。 至少需要将此角色分配给服务的管理员。 以下 PowerShell 命令演示了如何将角色分配给最低范围内的用户 `user1`，以避免向用户授予不必要的权限： 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

向用户授予权限后，用户必须注销并再次登录到 Azure 门户，才能使新权限生效。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>我看到 `Unable to start the portal. See if settings are specified correctly (...)` 错误

当对 `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` 的 `GET` 调用失败时，将显示此错误。 从浏览器通过门户的管理界面发出调用。

如果 API 管理服务在 VNet 中，请参阅上述 VNet 连接问题。

调用失败也可能由将分配给自定义域且不受浏览器信任的 SSL 证书引起。 作为缓解措施，你可以删除管理终结点自定义域-API 管理将回退到具有受信任证书的默认终结点。

### <a name="whats-the-browser-support-for-the-portal"></a>门户的浏览器支持有哪些？

| 浏览器                     | 支持       |
|-----------------------------|-----------------|
| Apple Safari                | 是<sup>1</sup> |
| Google Chrome               | 是<sup>1</sup> |
| Microsoft Edge              | 是<sup>1</sup> |
| Microsoft Internet Explorer | 是              |
| Mozilla Firefox             | 是<sup>1</sup> |

 <small><sup>1</sup>在两个最新的生产版本中受支持。</small>

## <a name="next-steps"></a>后续步骤

了解有关新开发人员门户的详细信息：

- [访问和自定义托管开发人员门户](api-management-howto-developer-portal-customize.md)
- [设置门户的自承载版本][2]
- [实现自己的小组件][3]

浏览其他资源：

- [带有源代码的 GitHub 存储库][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend