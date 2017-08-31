---
title: "在 Azure API 管理中自定义开发人员门户中的样式 | Microsoft Docs"
description: "了解如何在 Azure API 管理中修改开发人员门户中的任意页面所用的样式。"
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.translationtype: Human Translation
ms.sourcegitcommit: 336d4f80f0357796fb29eb9314c11edfce831a69
ms.openlocfilehash: bd08eb476a4bd7298c5650977b88ba0b24deddec
ms.contentlocale: zh-cn
ms.lasthandoff: 02/23/2017

---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>在 Azure API 管理中自定义开发人员门户的样式
在 Azure API 管理中自定义开发人员门户有三种基本方法：

* [编辑静态页面内容和页面布局元素][modify-content-layout]
* [跨开发人员门户更新页面元素所用的样式][customize-styles]（本指南中所述）
* [修改门户生成的页面所用的模板][portal-templates]（例如 API 文档、产品和用户身份验证等）

## <a name="change-headers-styling"> </a>更改页面元素的样式

由样式规则定义门户上任何页面的颜色、字体、大小、间距和其他样式相关的元素。 

在以管理员身份登录时，可从“开发人员门户”编辑样式规则。 若要访问它，请先打开 Azure 门户，再单击 API 管理实例的服务工具栏中的“发布者门户”。

![发布者门户][api-management-management-console]

然后单击右上角的“开发人员门户”。 

![发布者门户上的开发人员门户链接][api-management-pp-dp-link]

若要打开自定义工具栏，请将鼠标移到自定义图标上（或选择它），然后从工具栏单击“样式”。

![自定义工具栏按钮][api-management-customization-toolbar-button]

有两种主要方式来编辑样式规则 - 可以查看任意位置使用的所有样式规则的列表（这些规则是默认显示的），然后根据需要修改样式；也可以选择“选择页面上的元素”，然后单击页面任意位置即可只查看该元素的样式。 

![自定义工具栏][api-management-customization-toolbar]

对于此示例，请单击“选择页面上的元素”选项。  现在将鼠标悬停在元素上方时元素将突出显示，以表示单击时可开始编辑的元素样式。 将鼠标移到标头中的文本上（此处通常是公司名称），并单击它。 样式编辑器中显示一组分类并命名的样式规则。 每个规则表示所选元素的样式属性。 例如，对于上面所选的标题文本，文本大小在 @font-size-h1 中，而带替代项的字体的名称在 @headings-font-family 中。

> 如果熟悉 [bootstrap][bootstrap]，这些规则实际上是开发人员门户中使用的 bootstrap 主题中的 [LESS 变量][LESS variables]。
> 
> 

让我们来更改标题文本的颜色。 选择 **@headings-color** 字段中的条目，并键入“#000000”。 这是黑色的十六进制代码。 执行此操作时，可看到一个正方形颜色指示符出现在文本框的末尾。 如果单击该指示符，可使用颜色选取器选择一种颜色。

![颜色选取器][api-management-customization-toolbar-color-picker]

所做更改是实时预览的，但仅对管理员可见。 要使这些更改对每个人都可见，请单击样式编辑器中的“发布”按钮，然后确认所做的更改。

![发布菜单][api-management-customization-toolbar-publish-form]

> 若要更改应用于页面上任何其他元素的样式规则，请按照对标题所执行的相同步骤进行操作。 在样式编辑器中单击“选择页面上的元素”，选择感兴趣的元素，然后开始修改显示在屏幕上的样式规则的值。
> 
> 


## <a name="next-steps"> </a>后续步骤
* 了解如何使用 [开发人员门户模板](api-management-developer-portal-templates.md)自定义开发人员门户页面的内容。

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/

