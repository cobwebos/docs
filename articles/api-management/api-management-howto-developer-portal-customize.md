---
title: 访问和自定义托管开发人员门户 - Azure API 管理 | Microsoft Docs
description: 了解如何使用 API 管理中的托管版开发人员门户。
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
ms.openlocfilehash: 8629d07830622770c3b30dacdd1fabc8417d7f52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454424"
---
# <a name="access-and-customize-developer-portal"></a>访问和自定义开发人员门户

开发人员门户是一个自动生成的、完全可自定义的网站，其中包含 API 的文档。 API 使用者可在其中找到 API、了解 API 的用法以及请求访问权限。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 访问托管版开发人员门户
> * 在门户管理界面中导航
> * 自定义内容
> * 发布更改
> * 查看发布的门户

可以在 [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)中找到有关开发人员门户的详细信息。

![API 管理开发人员门户 - 管理模式](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>以管理员身份访问门户

遵循以下步骤访问托管版本的门户。

1. 在 Azure 门户中转到你的 API 管理服务实例。
1. 单击顶部导航栏中的“开发人员门户”按钮。 此时会打开一个新的浏览器标签页，其中包含管理版本的门户。

## <a name="understand-the-portals-administrative-interface"></a>了解门户的管理界面

### <a name="default-content"></a>默认内容 

首次访问门户时，将自动在后台预配默认内容。 默认内容旨在展示门户的功能，并最大程度地减少个性化门户所要完成的自定义工作量。 可以在 [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)中详细了解具体的门户内容。

### <a name="visual-editor"></a>视觉编辑器

可以使用视觉编辑器来自定义门户的内容。 使用左侧的菜单部分可以创建或修改页面、媒体、布局、菜单、样式或网站设置。 使用底部的菜单项可以在视区之间切换（例如，切换为移动或桌面版）、查看门户中向已进行身份验证的用户或匿名用户显示的元素，或者保存或撤消操作。

单击带有加号的蓝色图标可以在页面中添加行。 按下带有加号的灰色图标可以添加小组件（例如文本、图像或 API 列表）。 可以使用拖放交互操作重新排列页面中的项。 

### <a name="layouts-and-pages"></a>布局和页面

![页面和布局](media/api-management-howto-developer-portal-customize/pages-layouts.png)

布局定义页面的显示方式。 例如，默认内容中有两个布局 - 一个布局应用于主页，另一个布局应用于所有剩余页面。

将布局的 URL 模板与页面的 URL 相匹配即可将该布局应用到该页面。 例如，使用 URL 模板 `/wiki/*` 的布局将应用到其 URL 包含 `/wiki/` 段的每个页面：`/wiki/getting-started`、`/wiki/styles`，等等。

在上面的图像中，属于该布局的内容标为蓝色，页面标为红色。 不同的菜单部分做了相应的标记。

### <a name="styling-guide"></a>样式指引

![样式指引](media/api-management-howto-developer-portal-customize/styling-guide.png)

样式指引是根据设计器创建的面板。 在此面板中可以检查门户中的所有视觉元素并设置其样式。 样式是分层的 - 许多元素从其他元素继承属性。 例如，按钮元素使用文本和背景的颜色。 若要更改按钮颜色，需要更改原始颜色变体。

若要编辑某个变体，请单击它，并选择其顶部显示的铅笔图标。 在弹出窗口中进行更改后将其关闭。

### <a name="save-button"></a>“保存”按钮

![“保存”按钮](media/api-management-howto-developer-portal-customize/save-button.png)

每当在门户中进行更改，都需要按下底部菜单中的“保存”按钮来手动保存更改。 保存更改时，修改的内容会自动上传到 API 管理服务。

## <a name="customize-the-portals-content"></a>自定义门户的内容

在将门户提供给访问者使用之前，应个性化自动生成的内容。 建议的更改包括主页的布局、样式和内容。

> [!NOTE]
> 由于集成方面的因素，无法删除以下页面或将其移到不同的 URL 下：`/404`、`/500`、`/captcha`、`/change-password`、`/config.json`、`/confirm/invitation`、`/confirm-v2/identities/basic/signup`、`/confirm-v2/password`、`/internal-status-0123456789abcdef`、`/publish`、`/signin`、`/signin-sso`、`/signup`。

### <a name="home-page"></a>主页

默认**主页**中已填充虚构内容。 可以删除包含这些内容的整个部分，或保留结构并逐个调整元素。 将生成的文本和图像替换为自己的内容，并确保链接指向所需的位置。

### <a name="layouts"></a>布局

将导航栏中自动生成的徽标替换为自己的图像。

### <a name="styling"></a>样式

尽管无需调整任何样式，但可以考虑调整特定的元素。 例如，更改主色，使之与自己的品牌色相匹配。

### <a name="customization-example"></a>自定义示例

以下视频演示了如何编辑门户的内容、自定义网站的外观，以及发布所做的更改。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish"></a>发布门户

若要将门户提供给访问者使用并反映其最新更改，需要发布门户。

1. 请务必单击“保存”图标保存更改。
1. 在菜单的“操作”部分单击“发布网站”。 此操作可能需要几分钟的时间。  
    ![发布门户](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> 需要在 API 管理服务配置更改后重新发布门户，如分配自定义域、更新标识提供者、设置委派、指定登录和产品条款等。

## <a name="visit-the-published-portal"></a>访问发布的门户

发布门户后，可以通过管理面板所用的相同 URL（例如 `https://contoso-api.developer.azure-api.net`）访问该门户。 在单独的浏览器会话（incognito/专用浏览模式）中以外部访问者身份查看它。

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)
