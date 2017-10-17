---
title: "使用模板自定义 API 管理开发人员门户 - Azure | Microsoft 文档"
description: "如何使用模板自定义 Azure API 管理开发人员门户。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2434762c2a1174a40d9c6daad6c317bc3ca8f374
ms.openlocfilehash: 40d25726d31d2018785b77d169a8811c565316bf
ms.contentlocale: zh-cn
ms.lasthandoff: 02/23/2017

---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>如何使用模板自定义 Azure API Management 开发人员门户

在 Azure API 管理中自定义开发人员门户有三种基本方法：

* [编辑静态页面内容和页面布局元素][modify-content-layout]
* [跨开发人员门户更新页面元素所用的样式][customize-styles]
* [修改门户生成的页面所用的模板][portal-templates]（在本指南中说明）

模板用于自定义系统生成的开发人员门户页的内容（例如，API 文档、产品、用户身份验证等）。 使用 [DotLiquid](http://dotliquidmarkup.org/) 语法和一组提供的本地化字符串资源、图标和页面控件，可根据需要非常灵活地配置页面内容。

## <a name="developer-portal-templates-overview"></a>开发人员门户模板概述
在以管理员身份登录时，可从“开发人员门户”编辑模板。 若要访问它，请先打开 Azure 门户，再单击 API 管理实例的服务工具栏中的“发布者门户”。

![发布者门户][api-management-management-console]

然后单击右上角的“开发人员门户”。 

![开发人员门户菜单][api-management-developer-portal-menu]

若要访问开发人员门户模板，请单击左侧的自定义图标显示自定义菜单，然后单击“模板”。

![开发人员门户模板][api-management-customize-menu]

模板列表显示模板的多个类别，涵盖开发人员门户中的不同页面。 每个模板都不同，但编辑它们并发布更改的步骤均相同。 若要编辑模板，请单击模板的名称。

![开发人员门户模板][api-management-templates-menu]

单击模板转到由该模板自定义的开发人员门户页面。 在此示例中，显示“产品列表”模板。 “产品列表”模板控制红色矩形所示的屏幕区域。 

![产品列表模板][api-management-developer-portal-templates-overview]

某些模板（如“用户配置文件”模板）自定义同一页面的不同部分。 

![用户配置文件模板][api-management-user-profile-templates]

每个开发人员门户模板的编辑器都具有两个在页面底部显示的部分。 左侧显示模板的编辑窗格，右侧显示模板的数据模型。 

模板编辑窗格包含标记，用于控制开发人员门户中的对应页面的外观和行为。 模板中的标记使用 [DotLiquid](http://dotliquidmarkup.org/) 语法。 DotLiquid 的一个流行编辑器是 [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)。 在编辑期间对模板所作的任何更改都实时显示在浏览器中，但在[保存](#to-save-a-template)并[发布](#to-publish-a-template)该模板之前对客户不可见。

![模板标记][api-management-template]

“模板数据”窗格提供可在特定模板中使用的实体的数据模型指南。 它通过显示当前在开发人员门户中显示的实时数据提供此指南。 可通过单击“模板数据”窗格右上角的矩形展开模板窗格。

![模板数据模型][api-management-template-data]

在前面的示例中，开发人员门户中显示两个从“模板数据”窗格中显示的数据检索的产品，如以下示例所示。

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

**产品列表**模板中的标记处理数据以显示所需的输出，方法是循环访问产品集合以显示信息和指向每个单独产品的链接。 请注意标记中的 `<search-control>` 和 `<page-control>` 元素。 这些元素控制页面上的搜索和分页控件的显示。 `ProductsStrings|PageTitleProducts` 是一个本地化字符串参考，包含页面的 `h2` 标题文本。 有关可在开发人员门户模板中使用的字符串资源、页面控件和图标的列表，请参阅 [API 管理开发人员门户模板参考](api-management-developer-portal-templates-reference.md)。

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>    
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>保存模板
若要模板，请在模板编辑器中单击“保存”。

![保存模板][api-management-save-template]

已保存的更改在开发人员门户中不生效，直到它们发布。

## <a name="to-publish-a-template"></a>发布模板
已保存的模板可单独或全部一起发布。 若要发布个别模板，请在模板编辑器中单击“发布”。

![发布模板][api-management-publish-template]

单击“是”确认并使模板在开发人员门户中生效。

![确认发布][api-management-publish-template-confirm]

若要发布所有当前未发布的模板版本，请单击模板列表中的“发布”。 未发布的模板通过模板名称后跟星号来指定。 在此示例中，将发布“产品列表”和“产品”模板。

![发布模板][api-management-publish-templates]

单击“发布自定义”确认。

![确认发布][api-management-publish-customizations]

新发布的模板在开发人员门户中立即生效。

## <a name="to-revert-a-template-to-the-previous-version"></a>将模板还原到以前版本
若要将模板还原到以前发布的版本，请单击模板编辑器中的“还原”。

![还原模板][api-management-revert-template]

单击“是”确认。

![确认][api-management-revert-template-confirm]

还原操作完成后，模板的以前发布的版本在开发人员门户中生效。

## <a name="to-restore-a-template-to-the-default-version"></a>将模板还原到默认版本
将模板还原到其默认版本的过程分为两步。 首先必须还原模板，然后必须发布已还原的版本。

若要将单个模板还原到默认版本，请在模板编辑器中单击“还原”。

![还原模板][api-management-reset-template]

单击“是”确认。

![确认][api-management-reset-template-confirm]

若要将所有模板还原到其默认版本，请单击模板列表中的“还原默认模板”。

![还原模板][api-management-restore-templates]

然后，还原的模板必须按照[发布模板](#to-publish-a-template)中的步骤单独或全部同时发布。

## <a name="next-steps"></a>后续步骤
有关开发人员门户模板、字符串资源、图标和页面控件的参考信息，请参阅 [API 管理开发人员门户模板参考](api-management-developer-portal-templates-reference.md)。

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png








