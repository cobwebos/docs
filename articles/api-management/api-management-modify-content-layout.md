---
title: 在 Azure API 管理中修改开发人员门户中的页面内容 | Microsoft Docs
description: 了解如何在 Azure API 管理中编辑开发人员门户上的页面内容。
services: api-management
documentationcenter: ''
author: antonba
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: bcf48ab8dd3b57ace70fa713074b13a992940002
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29377793"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>在 Azure API 管理中修改开发人员门户上的页面内容和页面布局
在 Azure API 管理中自定义开发人员门户有三种基本方法：

* [编辑静态页面内容和页面布局元素][modify-content-layout]（本指南中所述）
* [跨开发人员门户更新页面元素所用的样式][customize-styles]
* [修改门户生成的页面所用的模板][portal-templates]（例如 API 文档、产品、用户身份验证等）

## <a name="page-structure"> </a>开发人员门户页面结构

开发人员门户基于内容管理系统。 每页的布局是基于一组小型页面元素（称为小组件）构建的：

![开发人员门户页面结构][api-management-customization-widget-structure]

所有小组件均可编辑。 
* 特定于每个单独页面的核心内容驻留在“内容”小组件中。 编辑页面意味着编辑此小组件的内容。
* 所有页面布局元素都包含在剩余的小组件中。 对这些小组件所做的更改应用于所有页面。 它们称为“布局小组件”。

在日常页面编辑中，通常只修改“内容”小组件，此组件拥有针对每个单独页面的不同内容。

## <a name="modify-layout-widget"> </a>修改布局小组件的内容

开发人员门户可从 Azure 门户进行访问。 

1. 单击 API 管理实例的工具栏中的“开发人员门户”。
2. 若要编辑小组件的内容，请单击左侧“开发人员门户”菜单中由两个画刷组成的图标。 
3. 若要修改标头的内容，请滚动到左侧列表中的“标头”部分。
    
    可以在字段中对小组件进行编辑。
4. 做好发布更改的准备以后，请单击页面底部的“发布”。

现在，就能够在开发人员门户中的每一页上看见此新的标头。

## <a name="next-steps"></a>后续步骤
* [跨开发人员门户更新页面元素所用的样式][customize-styles]
* [修改门户生成的页面所用的模板][portal-templates]（例如 API 文档、产品、用户身份验证等）

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
