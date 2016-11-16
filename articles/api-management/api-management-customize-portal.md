---
title: "在 Azure API 管理中自定义开发人员门户 | Microsoft Docs"
description: "了解如何在 Azure API 管理中自定义开发人员门户。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 398b845a6549341d6afba101acec9eefd146d810


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>在 Azure API 管理中自定义开发人员门户
本指南演示如何在 Azure API 管理中修改开发人员门户的外观和感觉，从而与自己的品牌保持一致。

## <a name="change-page-headers"> </a>更改页面标头中的文本/徽标
门户自定义的关键之一是用您公司名称或徽标来替换所有页面顶部的文本。

通过发布者门户修改开发人员门户中的内容，发布者门户可通过 Azure 门户进行访问。 若要访问它，请单击 API 管理实例的服务工具栏中的“发布者门户”。

![发布者门户][api-management-management-console]

开发人员门户基于内容管理系统或 CMS。 每一页显示的标题是内容的一种特殊类型，成为小组件。 若要编辑该小组件的内容，请单击左侧“开发人员门户”菜单上的“小组件”，然后从列表中选择“标题”小组件。

![小组件标头][api-management-widgets-header]

标题的内容可在“正文”字段中编辑。 将文本更改为“Fabrikam 开发人员门户”，然后单击页面底部的“保存”。

现在，就能够在开发人员门户中的每一页上看见此新的标头。

> 若要在发布者门户中打开开发人员门户，请单击顶栏中的“开发人员门户”。
> 
> 

## <a name="change-headers-styling"> </a>更改标头的样式
由样式规则定义门户上任何页面的颜色、字体、大小、间距和其他样式相关的元素。 若要编辑样式，请单击发布者门户中“开发人员门户”菜单中的“外观”，然后单击“开始自定义”以启用样式编辑器。

浏览器切换到包含内容示例的开发人员门户中的隐藏页面，其中显示了可在站点的任何位置使用的所有样式规则的示例。 要打开样式编辑器，请将光标移到页面最左侧部分的灰色细竖线上。 应显示编辑器工具栏。

![自定义工具栏][api-management-customization-toolbar]

编辑样式规则有两种主要模式 - **编辑所有规则**显示任何位置使用的所有样式规则的列表；而**选择元素**则允许从所在的页上选择一个元素，并仅对该元素显示样式。

本节中我们只想更改标头的样式。 单击样式编辑器工具栏中的“选择元素”选项，然后单击“选择要自定义的元素”。 现在将鼠标悬停在元素上方时元素将突出显示，以表示单击时可开始编辑的元素样式。 将鼠标移到标头中表示公司名称的文本（如果遵循上一节中的说明，则为“Fabrikam 开发人员门户”），然后单击它。 样式编辑器中显示一组分类并命名的样式规则。

每个规则表示所选元素的样式属性。 例如，对于上面所选的标题文本，文本大小在 @font-size-h1 中，而带替代项的字体的名称在 @headings-font-family. 中

> 如果熟悉 [bootstrap][bootstrap]，这些规则实际上是开发人员门户使用的 bootstrap 主题中的 [LESS 变量][LESS 变量]。
> 
> 

让我们来更改标题文本的颜色。 选择 **@headings-color** 字段中的条目，并键入“#000000”。 这是黑色的十六进制代码。 执行此操作时，可看到一个正方形颜色指示符出现在文本框的末尾。 如果单击该指示符，可使用颜色选取器选择一种颜色。

![颜色选取器][api-management-customization-toolbar-color-picker]

完成对所选元素的样式更改后，单击“预览更改”可在屏幕上查看结果。 这次它们只对管理员可见。 要使这些更改对每个人都可见，请单击样式编辑器中的“发布”按钮，然后确认所做的更改。

![发布菜单][api-management-customization-toolbar-publish-form]

> 若要更改应用于页面上任何其他元素的样式规则，请按照对标题所执行的相同步骤进行操作。 在样式编辑器中单击“选择元素”，选择感兴趣的元素，然后开始修改屏幕上显示的样式规则的值。
> 
> 

## <a name="edit-page-contents"> </a>编辑页面的内容
开发人员门户由自动生成的页面组成，如 API、产品、应用程序、问题和手动编写的内容。 由于它基于内容管理系统，因此可根据需要创建此类的内容。

若要查看所有现有内容页的列表，请单击发布者门户的“开发人员门户”菜单中的“内容”。

![管理内容][api-management-customization-manage-content]

单击“欢迎”页以编辑开发人员门户主页上显示的内容。 按所需进行更改，如有必要可预览更改，然后单击“立即发布”使更改对每个人可见。

> 主页使用特殊的布局，可在顶部显示横幅。 从“内容”部分不能编辑此横幅。 若要编辑此横幅，请单击“开发人员门户”菜单中的“小组件”，从“当前层”下拉列表中选择“主页”，然后打开“特色”部分下的“横幅”项。 此小组件的内容就像任何其他页一样都可编辑。
> 
> 

## <a name="next-steps"> </a>后续步骤
* 了解如何使用 [开发人员门户模板](api-management-developer-portal-templates.md)自定义开发人员门户页面的内容。

[更改页面标题中的文本/徽标]: #change-page-headers
[更改标头的样式]: #change-headers-styling
[编辑页面的内容]: #edit-page-contents
[后续步骤]: #next-steps

[Azure 经典门户]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS 变量]: http://getbootstrap.com/css/



<!--HONumber=Nov16_HO2-->


