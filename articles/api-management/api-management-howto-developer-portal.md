---
title: Azure API 管理开发人员门户概述-Azure API 管理 |Microsoft Docs
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
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1a8fb8140fd2f58556e4a64bca0f4a052a84c2bb
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285605"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API 管理开发人员门户概述

开发人员门户是一个自动生成的、可完全自定义的网站，其中包含 Api 的文档。 API 使用者可以在其中发现你的 Api、了解如何使用它们、如何请求访问以及如何进行试用。

本文介绍了 API 管理中开发人员门户的自承载和托管版本之间的差异。 它还介绍了其体系结构，并提供常见问题的解答。

> [!WARNING]
>
> [了解如何从预览版迁移到开发人员门户的正式发布版本](#preview-to-ga)。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a> 托管版本和自承载版本

可通过两种方式构建开发人员门户：

- **托管版本** - 通过编辑和自定义 API 管理实例中内置的、可通过 URL `<your-api-management-instance-name>.developer.azure-api.net` 访问的门户。 请参阅[此文档一文](api-management-howto-developer-portal-customize.md)，了解如何访问和自定义托管门户。
- **自承载版本** - 通过在 API 管理实例外部部署和自承载门户。 使用此方法可以编辑门户的基代码并扩展所提供的核心功能。 还需要自己将门户升级到最新版本。 有关详细信息和说明，请参阅[包含门户源代码的 GitHub 存储库][1]。 [托管版本教程](api-management-howto-developer-portal-customize.md)介绍了门户的管理面板，该面板还在自承载版本中提供。

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

## <a name="faq"></a> 常见问题

在本部分中，我们将回答有关新开发人员门户的常见问题，这些问题是一般的。 有关自承载版本的特定问题，请参阅 [GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> 如何从门户预览版本进行迁移？

通过使用开发人员门户的预览版本，你在 API 管理服务中预配了预览内容。 为了获得更好的用户体验，已在正式发布的版本中对默认内容进行了重大修改。 它还包括新的小组件。

如果使用的是托管版本，请通过单击 "**操作**" 菜单部分中的 "**重置内容**" 来重置门户的内容。 确认此操作将删除门户的所有内容并预配新的默认内容。 门户引擎已在 API 管理服务中自动更新。

![重置门户内容](media/api-management-howto-developer-portal/reset-content.png)

如果使用自承载版本，请使用 GitHub 存储库中的 `scripts/cleanup.bat` 和 `scripts/generate.bat` 删除现有内容并预配新内容。 请确保事先将门户的代码升级到 GitHub 存储库中的最新版本。

如果你不希望重置门户的内容，则可以考虑在页面中使用新的可用小组件。 现有小组件已自动更新到最新版本。

如果你的门户是在公开发布后预配的，则它应已具有新的默认内容。 不需要执行任何操作。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>如何从旧开发人员门户迁移到新的开发人员门户？

门户不兼容，需要手动迁移内容。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新门户是否拥有旧门户的所有功能？

新的开发人员门户不支持*应用程序*和*问题*。 如果在旧门户中使用了问题，并在新门户中使用了这些*问题*，请在[专用 GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/122)中发布评论。

### <a name="has-the-old-portal-been-deprecated"></a>旧门户是否已弃用？

旧的开发人员和发布者门户现在是*旧*功能，它们仅接收安全更新。 新功能将仅在新的开发人员门户中实现。

旧门户的弃用将单独发布。 如果你有疑问、问题或意见，请[在专用 GitHub 问题中](https://github.com/Azure/api-management-developer-portal/issues/121)提出。

### <a name="how-can-i-automate-portal-deployments"></a>如何自动部署门户？

你可以通过 REST API 以编程方式访问和管理开发人员门户的内容，而不考虑你使用的是托管版本还是自承载版本。

[GitHub 存储库的 wiki 部分][2]介绍了该 API。 它还可用于在环境之间自动迁移门户内容（例如从测试环境到生产环境）。 有关此过程的详细信息，请参阅 GitHub 上的[此文档文章](https://aka.ms/apimdocs/migrateportal)。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>门户是否支持 Azure 资源管理器模板和/或它是否与 API 管理 DevOps 资源工具包兼容？

不。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>是否需要为托管门户依赖项启用附加 VNET 连接？

不。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>使用交互式控制台时，出现 CORS 错误。 我该怎么办？

交互式控制台从浏览器发出客户端 API 请求。 在 API 中添加 [CORS 策略](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)可以解决 CORS 问题。 可以手动指定所有参数，也可以使用通配符 `*` 值。 例如：

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

## <a name="next-steps"></a>后续步骤

详细了解新开发人员门户：

- [访问和自定义托管开发人员门户](api-management-howto-developer-portal-customize.md)
- [设置门户的自承载版本][2]

浏览其他资源：

- [包含源代码的 GitHub 存储库][1]
- [项目的公开路线图][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects