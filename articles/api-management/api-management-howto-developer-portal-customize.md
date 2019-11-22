---
title: 访问和自定义托管开发人员门户-Azure API 管理 |Microsoft Docs
description: 了解如何在 API 管理中使用开发人员门户的托管版本。
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
ms.openlocfilehash: 28ef082d6aac5557998f7b67443d4dafd3a0fb15
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285581"
---
# <a name="access-and-customize-developer-portal"></a>访问和自定义开发人员门户

开发人员门户是一个自动生成的、可完全自定义的网站，其中包含 Api 的文档。 API 使用者可以在其中发现你的 Api、了解如何使用它们并请求访问。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 访问开发人员门户的托管版本
> * 导航其管理界面
> * 自定义内容
> * 发布更改
> * 查看发布的门户

可在[AZURE API 管理开发人员门户概述](api-management-howto-developer-portal.md)中找到开发人员门户的更多详细信息。

![API 管理开发人员门户-管理模式](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>以管理员身份访问门户

遵循以下步骤访问托管版本的门户。

1. 在 Azure 门户中转到你的 API 管理服务实例。
1. 单击顶部导航栏中的 "**开发人员门户**" 按钮。 此时会打开一个新的浏览器标签页，其中包含管理版本的门户。

## <a name="understand-the-portals-administrative-interface"></a>了解门户的管理界面

### <a name="default-content"></a>默认内容 

如果是首次访问门户，则会在后台自动预配默认内容。 默认内容旨在展示门户功能，并最大程度地减少自定义门户所需的自定义项的数量。 可以在[AZURE API 管理开发人员门户概述](api-management-howto-developer-portal.md)中了解有关门户内容的详细信息。

### <a name="visual-editor"></a>视觉对象编辑器

可以通过视觉对象编辑器自定义门户的内容。 利用左侧的菜单部分，您可以创建或修改页面、媒体、布局、菜单、样式或网站设置。 底部的菜单项使你可以在视区之间切换（例如，移动或桌面），查看门户中已进行身份验证或匿名用户可见的元素，或者保存或撤消操作。

您可以通过单击带有加号的蓝色图标向页面添加行。 可以通过按加号的灰色图标来添加小组件（例如，文本、图像或 Api 列表）。 您可以使用拖放交互重新排列页面中的项。 

### <a name="layouts-and-pages"></a>布局和页面

![页面和布局](media/api-management-howto-developer-portal-customize/pages-layouts.png)

布局定义页面的显示方式。 例如，在默认内容中，有两个布局-一种应用于主页，另一种用于所有剩余页面。

通过将布局的 URL 模板与页的 URL 相匹配，可将布局应用于页面。 例如，使用 url 模板 `/wiki/*` 的布局将应用于 URL 中具有 `/wiki/` 段的每个页面： `/wiki/getting-started`、`/wiki/styles`等。

在上面的图像中，属于布局的内容将以蓝色标记，而页面则标为红色。 菜单节分别标记。

### <a name="styling-guide"></a>样式指南

![样式指南](media/api-management-howto-developer-portal-customize/styling-guide.png)

样式指南是一个使用设计器创建的面板。 它允许监督和设置门户中所有视觉对象的样式。 样式是分层的，它是从其他元素继承属性的多个元素。 例如，button 元素使用文本和背景颜色。 若要更改按钮颜色，需要更改原始颜色变量。

若要编辑变量，请单击它，然后选择在其顶部显示的铅笔图标。 在弹出窗口中进行更改后，请将其关闭。

### <a name="save-button"></a>“保存”按钮

![“保存”按钮](media/api-management-howto-developer-portal-customize/save-button.png)

当你在门户中进行更改时，需要通过按底部菜单中的 "**保存**" 按钮来手动保存。 当你保存所做的更改时，修改后的内容会自动上载到你的 API 管理服务。

## <a name="customize-the-portals-content"></a>自定义门户内容

向访问者提供门户之前，应个性化自动生成的内容。 建议的更改包括主页的布局、样式和内容。

> [!NOTE]
> 由于集成方面的考虑，无法删除以下页面或将其移动到不同的 URL 下： `/404`、`/500`、`/captcha`、`/change-password`、`/config.json`、`/confirm/invitation`、`/confirm-v2/identities/basic/signup`、`/confirm-v2/password`、`/internal-status-0123456789abcdef`、`/publish`、`/signin`、`/signin-sso``/signup`。

### <a name="home-page"></a>主页

默认**主页**将填充虚拟内容。 您可以删除包含内容的整个部分或保留结构并逐个调整元素。 将生成的文本和图像替换为您自己的，并确保链接指向所需的位置。

### <a name="layouts"></a>布局

将导航栏中自动生成的徽标替换为自己的图像。

### <a name="styling"></a>样式

尽管不需要调整任何样式，但可以考虑调整特定元素。 例如，更改主色以匹配您的品牌颜色。

### <a name="customization-example"></a>自定义示例

以下视频演示了如何编辑门户的内容、自定义网站的外观，以及发布所做的更改。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a>发布门户

若要使你的门户及其最新更改可供访问者使用，你需要将其发布。

1. 单击 "**保存**" 图标，确保已保存所做的更改。
1. 单击菜单的 "**操作**" 部分中的 "**发布网站**"。 此操作可能需要几分钟的时间。  
    ![发布门户](media/api-management-howto-developer-portal-customize/publish-portal.png)

## <a name="visit-the-published-portal"></a>访问发布的门户

发布门户后，可以使用与管理面板相同的 URL 访问它，例如 `https://contoso-api.developer.azure-api.net`。 在单独的浏览器会话（incognito/专用浏览模式）中以外部访问者身份查看它。

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)
