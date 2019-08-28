---
title: 访问和自定义新开发人员门户-Azure API 管理 |Microsoft Docs
description: 了解如何在 API 管理中使用新的开发人员门户。
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
ms.openlocfilehash: da75ca43a2576e3214d4b67f9eb61c7bad3bd5cc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073520"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>在 Azure API 管理中访问和自定义新开发人员门户

本文介绍如何访问新的 Azure API 管理开发人员门户。 本教程将引导你完成可视化编辑器的体验-添加和编辑内容, 并自定义网站的外观。

![新的 API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>先决条件

- 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 新的开发人员门户目前处于预览阶段。

## <a name="managed-vs-self-hosted"></a>托管和自承载版本

可以通过两种方式生成开发人员门户:

- **托管版本**-通过编辑和自定义门户, 该门户内置于 API 管理实例, 并可通过 URL `<your-api-management-instance-name>.developer.azure-api.net`进行访问。
- **自承载版本**-通过在 API 管理实例之外部署和自我托管你的门户。 此方法允许您编辑门户的基本代码并扩展提供的核心功能。 有关详细信息和说明, 请参阅[GitHub 存储库, 其中包含门户的源代码][1]。

## <a name="managed-access"></a>访问门户的托管版本

按照以下步骤访问门户的托管版本。

1. 在 Azure 门户中转到 API 管理服务实例。
1. 单击顶部导航栏中的 "**新建开发人员门户 (预览)** " 按钮。 将打开新的浏览器选项卡, 其中包含门户的管理版本。 如果是首次访问门户, 将自动预配默认内容。

## <a name="managed-tutorial"></a>编辑和自定义门户的托管版本

在下面的视频中, 我们将演示如何编辑门户的内容, 自定义网站的外观并发布所做的更改。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a> 常见问题

在本部分中, 我们将回答有关全新开发人员门户的常见问题。 有关特定于自承载版本的问题, 请参阅[GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki)。

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>如何将内容从旧开发人员门户迁移到新的开发人员门户？

你不能。 门户不兼容。

### <a name="when-will-the-portal-become-generally-available"></a>门户何时会公开发布？

门户目前处于预览阶段, 将在日历年结束时 (2019) 正式发布。 不应将预览版本用于生产目的。

### <a name="will-the-old-portal-be-deprecated"></a>旧门户是否会被弃用？

是的, 在新的新项公开发布后, 将被弃用。 如果你有疑问, 请[在专用 GitHub 问题中](https://github.com/Azure/api-management-developer-portal/issues/121)提出。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新门户是否具有旧门户的所有功能？

公开上市的目标是通过旧门户提供基于方案的功能奇偶校验。 在此之前, 预览版本可能未实现所选功能。

例外情况是旧门户中的*应用程序*和*问题*, 无法在新门户中使用。 如果在旧门户中使用*问题*, 并在新门户中使用它们, 请在[专用 GitHub 问题](https://github.com/Azure/api-management-developer-portal/issues/122)中发布评论。

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>我发现了 bug, 并且/或者我想请求一项功能。

很好！ 你可以向我们提供反馈、提交功能请求, 或者通过[GitHub 存储库的问题部分](https://github.com/Azure/api-management-developer-portal/issues)提交 bug 报告。 在此过程中, 我们还会感谢你对标记有`community`标签的问题的反馈。

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>我想要在环境之间移动新门户的内容。 如何执行此操作, 是否需要与自承载版本配合工作？

可以在门户的管理版本和自承载版本中执行此操作。 新的开发人员门户支持通过 API 管理服务的管理 API 提取内容。 [GitHub 存储库的 wiki 部分](https://github.com/Azure/api-management-developer-portal/wiki/)介绍了这些 api。 我们还编写[了一个脚本](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat), 这可能会帮助你入门。

我们仍在努力将此过程与 API Management DevOps 资源工具包进行协调。

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>如何在创建新*页面*时选择*布局*？

通过将布局的 URL 模板与*页的*url 相匹配, 可将*布局*应用于页面。 例如, 使用 URL 模板的`/wiki/*`布局将应用于`/wiki/`每个页面, 每个*页面*都包含段`/wiki/styles`: `/wiki/getting-started`、等。

### <a name="why-doesnt-the-interactive-developer-console-work"></a>为什么交互开发人员控制台不起作用？

它可能与 CORS 相关。 交互式控制台从浏览器发出客户端 API 请求。 可以通过在 API 上添加[CORS 策略](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)来解决 CORS 问题。 您可以手动指定所有参数 (例如, 原为 https://contoso.com) 或使用通配符`*` 值。

## <a name="next-steps"></a>后续步骤

了解有关新开发人员门户的详细信息:

- [带有源代码的 GitHub 存储库][1]
- [有关自承载门户的说明][2]
- [项目的公共路线图][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects