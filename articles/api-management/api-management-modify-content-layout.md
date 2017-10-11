---
title: "修改在 Azure API 管理开发人员门户中的页面内容 |Microsoft 文档"
description: "了解如何编辑在 Azure API 管理开发人员门户上的页面内容。"
services: api-management
documentationcenter: 
author: antonba
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
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>修改的内容和在 Azure API 管理开发人员门户上的页面布局
有三种基本的方法，以自定义在 Azure API 管理开发人员门户：

* [编辑静态页面和页面布局元素的内容][ modify-content-layout] （本指南中所述）
* [更新用于在开发人员门户的页面元素的样式][customize-styles]
* [修改用于门户生成的页面的模板][ portal-templates] （例如 API 文档、 产品、 用户身份验证等）

## <a name="page-structure"></a>结构的开发人员门户网站页

开发人员门户基于内容管理系统。 基于组称为小组件的较小的页面元素生成每个页的布局：

![开发人员门户页结构][api-management-customization-widget-structure]

所有小组件都是可编辑的。 
* 特定于每个单个页面的核心内容驻留在"内容"小组件。 编辑页面意味着编辑此小组件的内容。
* 所有页面布局元素都包含与剩余的小组件。 对这些小组件所做更改将应用于所有页面。 它们将简称为"布局小组件"。

在日常页中编辑一个通常只修改将具有每个单独页的不同内容的内容小组件。

## <a name="modify-layout-widget"></a>修改布局小组件的内容

通过可从 Azure 门户访问发布者门户修改开发人员门户中的内容。 若要访问它，请单击**发布者门户**服务工具栏的 API 管理实例。

![发布者门户][api-management-management-console]

若要编辑该小组件的内容，请单击**小组件**从**开发人员门户**在左侧菜单中的。 为此示例允许修改标头小组件的内容。 选择**标头**从列表中的小组件。

![小组件标头][api-management-widgets-header]

标头的内容是可从内编辑**正文**字段。 更改为所需的文本，然后单击**保存**位于页的底部。

现在，你应能够在开发人员门户内每一页上查看新的标头。

> 若要打开开发人员门户中发布者门户，单击**开发人员门户**顶栏中。
> 
> 

## <a name="edit-page-contents"></a>编辑页的内容

若要查看内容页中，单击所有现有列表**内容**从**开发人员门户**发布者门户中的菜单。

![管理内容][api-management-customization-manage-content]

单击**欢迎**页后，可以编辑开发人员门户的主页上显示的内容。 你想，有必要，预览它们，然后单击更改**立即发布**以使它们对每个人可见。

> 主页使用特殊的布局，以便它同时在顶部显示标题。 不可从编辑此横幅**内容**部分。 若要编辑此横幅，请单击**小组件**从**开发人员门户**菜单上，选择**主页**从**当前层**下拉列表，然后打开**横幅**项下面**特色部分**。 此小组件的内容是可编辑就像任何其他页一样。
> 
> 

## <a name="next-steps"></a>后续步骤
* [更新用于在开发人员门户的页面元素的样式][customize-styles]
* [修改用于门户生成的页面的模板][ portal-templates] （例如 API 文档、 产品、 用户身份验证等）

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
