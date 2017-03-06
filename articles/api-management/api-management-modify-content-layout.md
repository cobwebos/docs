---
title: "在 Azure API 管理中修改开发人员门户中的页面内容 | Microsoft Docs"
description: "了解如何在 Azure API 管理中编辑开发人员门户上的页面内容。"
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
translationtype: Human Translation
ms.sourcegitcommit: ecae1da20551d8372331124b07c4aca2e15f55bb
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.lasthandoff: 02/23/2017


---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>在 Azure API 管理中修改开发人员门户上的页面内容和页面布局
在 Azure API 管理中自定义开发人员门户有三种基本方法：

* [编辑静态页面内容和页面布局元素][modify-content-layout]（本指南中所述）
* [跨开发人员门户更新页面元素所用的样式][customize-styles]
* [修改门户生成的页面所用的模板][portal-templates]（例如 API 文档、产品和用户身份验证等）

## <a name="page-structure"></a>开发人员门户页面结构

开发人员门户基于内容管理系统。 每页的布局是基于一组小型页面元素（称为小组件）构建的：

![开发人员门户页面结构][api-management-customization-widget-structure]

所有小组件均可编辑。 
* 特定于每个单独页面的核心内容驻留在“内容”小组件中。 编辑页面意味着编辑此小组件的内容。
* 所有页面布局元素都包含在剩余的小组件中。 对这些小组件所做的更改会应用于所有页面。 它们称为“布局小组件”。

在日常页面编辑中，通常只修改“内容”小组件，此组件拥有针对每个单独页面的不同内容。

## <a name="modify-layout-widget"></a>修改布局小组件的内容

通过发布者门户修改开发人员门户中的内容，发布者门户可通过 Azure 门户进行访问。 若要访问它，请单击 API 管理实例的服务工具栏中的“发布者门户”。

![发布者门户][api-management-management-console]

若要编辑该小组件的内容，请单击左侧“开发人员门户”菜单中的“小组件”。 对于此示例，我们将修改“标头”小组件的内容。 从列表中选择“标头”小组件。

![小组件标头][api-management-widgets-header]

标题的内容可在“正文”字段中编辑。 根据需要更改文本，然后单击页面底部的“保存”。

现在，就能够在开发人员门户中的每一页上看见此新的标头。

> 若要在发布者门户中打开开发人员门户，请单击顶栏中的“开发人员门户”。
> 
> 

## <a name="edit-page-contents"> </a>编辑页面的内容

若要查看所有现有内容页的列表，请单击发布者门户的“开发人员门户”菜单中的“内容”。

![管理内容][api-management-customization-manage-content]

单击“欢迎”页以编辑开发人员门户主页上显示的内容。 按所需进行更改，如有必要可预览更改，然后单击“立即发布”使更改对每个人可见。

> 主页使用特殊的布局，可在顶部显示横幅。 从“内容”部分不能编辑此横幅。 若要编辑此横幅，请单击“开发人员门户”菜单中的“小组件”，从“当前层”下拉列表中选择“主页”，然后打开“特色”部分下的“横幅”项。 此小组件的内容就像任何其他页一样都可编辑。
> 
> 

## <a name="next-steps"> </a>后续步骤
* [跨开发人员门户更新页面元素所用的样式][customize-styles]
* [修改门户生成的页面所用的模板][portal-templates]（例如 API 文档、产品和用户身份验证等）

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

