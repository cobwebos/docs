---
title: 在 Azure API 管理开发人员门户中自定义页面样式 | Microsoft Docs
description: 遵循本快速入门中的步骤，在 Azure API 管理开发人员门户中自定义元素的样式。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: d1f638c9825ea5eedf6eaee0e0ca2ccfd5a491bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>自定义开发人员门户页面的样式

在 Azure API 管理中自定义开发人员门户有三种最常见的方法：
 
* [编辑静态页面内容和页面布局元素](api-management-modify-content-layout.md)
* 跨开发人员门户更新页面元素所用的样式（本指南中所述）
* [修改门户生成的页面所用的模板](api-management-developer-portal-templates.md)（例如 API 文档、产品和用户身份验证）

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 自定义**开发人员**门户页面中元素的样式
> * 查看更改

![自定义样式](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>先决条件

+ 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入并发布第一个 API](import-and-publish.md)。

## <a name="customize-the-developer-portal"></a>自定义开发人员门户

1. 选择“概述”。
2. 单击“概述”窗口顶部的“开发人员门户”按钮。 或者，可以单击“开发人员门户 URL”链接。
3. 在屏幕的左上方，可以看到由两支画笔构成的图标。 将鼠标悬停在此图标上，打开门户自定义菜单。

    ![自定义样式](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. 从菜单中选择“样式”，打开样式自定义窗格。

    可以使用“样式”自定义的所有元素都显示在该页面上
5. 在“更改变量值以自定义开发人员门户外观:”字段中输入“headings-color”。

    **@headings-color** 元素将显示在页面上。 此变量控制文本的颜色。

    ![自定义样式](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. 单击 **@headings-color** 变量对应的字段。 
    
    此时会打开颜色选取器下拉列表。
7. 从颜色选取器下拉列表中选择一种新颜色。

    > [!TIP]
    > 可实时预览所有更改。 自定义窗格顶部会显示进度指示器。 几秒钟后，标题文本的颜色将更改为刚刚选择的颜色。

8. 选择自定义窗格菜单左下角的“发布”。
9. 选择“发布自定义项”，以公开所做的更改。

## <a name="view-your-change"></a>查看更改

1. 导航到开发人员门户。
2. 可以看到所做的更改。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 自定义**开发人员**门户页面中元素的样式
> * 查看更改

> [!div class="nextstepaction"]
> [使用模板自定义 Azure API 管理开发人员门户](api-management-developer-portal-templates.md)