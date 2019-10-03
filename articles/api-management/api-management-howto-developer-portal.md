---
title: 访问和自定义新的开发人员门户 - Azure API 管理 | Microsoft Docs
description: 了解如何使用 API 管理中的新开发人员门户。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: c015b1afbc61e1501e656aaa480ee2a4e19ba094
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672791"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>访问和自定义 Azure API 管理中的新开发人员门户

本文介绍如何访问新的 Azure API 管理开发人员门户。 其中逐步介绍了视觉编辑器体验（添加和编辑内容），以及如何自定义网站的外观。

![新的 API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>先决条件

- 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 新的开发人员门户目前以预览版提供。

## <a name="managed-vs-self-hosted"></a> 托管版本和自承载版本

可通过两种方式构建开发人员门户：

- **托管版本** - 通过编辑和自定义 API 管理实例中内置的、可通过 URL `<your-api-management-instance-name>.developer.azure-api.net` 访问的门户。
- **自承载版本** - 通过在 API 管理实例外部部署和自承载门户。 使用此方法可以编辑门户的基代码并扩展所提供的核心功能。 有关详细信息和说明，请参阅[包含门户源代码的 GitHub 存储库][1]。

## <a name="managed-access"></a> 访问托管版本的门户

遵循以下步骤访问托管版本的门户。

1. 在 Azure 门户中转到你的 API 管理服务实例。
1. 单击顶部导航栏中的“新开发人员门户(预览版)”按钮。 此时会打开一个新的浏览器标签页，其中包含管理版本的门户。 首次访问门户时，将自动预配默认内容。

## <a name="managed-tutorial"></a> 编辑和自定义托管版本的门户

以下视频演示了如何编辑门户的内容、自定义网站的外观，以及发布所做的更改。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a> 常见问题

本部分解答了有关新开发人员门户的一般性常见问题。 有关自承载版本的特定问题，请参阅 [GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>如何将旧开发人员门户中的内容迁移到新开发人员门户？

无法迁移。 这两个门户不兼容。

### <a name="when-will-the-portal-become-generally-available"></a>该门户何时推出正式版？

该门户目前为预览版，将在 2019 年日历年底推出正式版。 请不要将预览版用于生产目的。

### <a name="will-the-old-portal-be-deprecated"></a>将来是否会弃用旧门户？

是的，在新门户推出正式版后，即会弃用旧门户。 如果你有疑虑，请提出[相关的 GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/121)。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新门户是否拥有旧门户的所有功能？

正式版的目标是提供与旧门户相当的基于方案的功能。 在此之前，预览版中可能未实现某些功能。

旧门户中的“应用程序”和“问题”除外，新门户中不会提供这些功能。 如果你在使用旧门户中的“问题”，并需要在新门户中继续使用该功能，请在[相关的 GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/122)中留言。

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>我发现了 bug，并且/或者想要请求某项功能。

很好！ 你可以向我们提供反馈、提交功能请求，或者通过 [GitHub 存储库的“问题”部分](https://github.com/Azure/api-management-developer-portal/issues)提交 bug 报告。 在此过程中，我们还会感谢你对带有 `community` 标签的问题提供反馈。

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>我想在不同的环境之间移动新门户的内容。 如何做到这一点，是否需要使用自承载版本？

在托管版本和自承载版本的门户中都可以做到这一点。 新开发人员门户支持通过 API 管理服务的管理 API 提取内容。 [GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki/)介绍了这些 API。 我们还编写了一个可帮助你入门的[脚本](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)。

我们仍在努力使此过程与 API 管理 DevOps 资源工具包相一致。

### <a name="what-do-i-need-to-configure-for-the-new-portal-to-work-in-my-api-management-service-in-vnet"></a>需要为新门户配置哪些内容才能在 VNET 中的 API 管理服务中工作？

尽管新开发人员门户处于预览阶段，但你需要允许连接到美国西部区域中的 Azure 存储服务，才能在 VNET 中的 API 管理服务中使用托管门户。 [存储文档](../storage/common/storage-network-security.md#available-virtual-network-regions)中提供了详细信息。

新门户公开发布后，将不再需要上述设置。

门户的自承载版本可能需要其他连接配置，具体取决于您的设置。

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>创建新的页面时如何选择布局？

将布局的 URL 模板与页面的 URL 相匹配即可将该布局应用到该页面。 例如，使用 URL 模板 `/wiki/*` 的布局将应用到包含 `/wiki/` 段的每个页面：`/wiki/getting-started`、`/wiki/styles`，等等。

### <a name="why-doesnt-the-interactive-developer-console-work"></a>交互式开发人员控制台为何无法正常工作？

这有可能与 CORS 相关。 交互式控制台从浏览器发出客户端 API 请求。 在 API 中添加 [CORS 策略](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)可以解决 CORS 问题。 可以手动指定所有参数 (例如，来源为 https://contoso.com) ，或使用通配符值 `*` 。

## <a name="next-steps"></a>后续步骤

详细了解新开发人员门户：

- [包含源代码的 GitHub 存储库][1]
- [有关自承载门户的说明][2]
- [项目的公开路线图][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects