---
title: "使用 JavaScript API 与报表交互 | Microsoft Docs"
description: "Power BI JavaScript API 可轻松地将 Power BI 报表嵌入到应用程序中。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 62a95807c35fcba15a8e5ffdf340a307dd22a642
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>使用 JavaScript API 与 Power BI 报表交互

Power BI JavaScript API 可轻松地将 Power BI 报表嵌入到应用程序中。 借助 API，应用程序可以以编程方式与不同的报表元素（如页和筛选器）进行交互。 这种交互性使 Power BI 报表成为应用程序更集成的一部分。

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月 或合同指示时可用。 建议规划迁移到 Power BI Embedded 以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

使用作为应用程序一部分托管的 IFrame 将 Power BI 报表嵌入到应用程序中。 IFrame 充当应用程序和报表之间的边界，如下图所示：

![Power BI 工作区集合 iframe，无 Javascript API](media/interact-with-reports/iframe-without-javacript.png)

IFrame 使得嵌入过程更容易，但如果没有 JavaScript API，报表和应用程序将不能彼此交互。 缺乏交互会让人觉得报表不是应用程序的一部分。 报表和应用程序确实需要进行相互通信，如下图中所示：

![Power BI 工作区集合 iframe，有 Javascript API](media/interact-with-reports/iframe-with-javascript.png)

Power BI JavaScript API 允许编写可以安全通过 IFrame 边界的代码。 这使应用程序可在报表中以编程方式执行某项操作，并侦听用户在报表中所做操作中的事件。

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>可以使用 Power BI JavaScript API 执行哪些操作？

使用 JavaScript API，可以管理报表、导航到报表中的页、筛选报表和处理嵌入事件。 下图显示了 API 的结构。

![Power BI JavaScript API 图示](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>管理报表
Javascript API 允许管理报表和页级别的行为：

* 在应用程序中安全地嵌入特定的 Power BI 报表 - 请尝试 [嵌入演示应用程序](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * 设置访问令牌
* 配置报表
  * 启用和禁用筛选器窗格和页面导航窗格 - 请尝试 [更新设置演示应用程序](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * 设置页面和筛选器的默认值 - 请尝试 [设置默认值演示](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* 进入和退出全屏模式

[了解有关嵌入报表的详细信息](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>导航到报表中的页
JavaScript API 能发现报表中的所有页并设置当前页。 请尝试 [导航演示应用程序](http://azure-samples.github.io/powerbi-angular-client/#/scenario3)。

[了解有关页面导航的详细信息](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>筛选报表
JavaScript API 为嵌入的报表和报表页提供基本和高级筛选功能。 请尝试 [筛选演示应用程序](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)，并查看此处的一些介绍性代码。

#### <a name="basic-filters"></a>基本筛选器
基本筛选器位于列或层次结构级别，包含要包括或排除的值的列表。

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>高级筛选器
高级筛选器使用逻辑运算符 AND 或 OR，接受一个或两个条件，每个条件都有其自己的运算符和值。 支持的条件有：

* 无
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* -StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[了解有关筛选的详细信息](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>处理事件

除了将信息发送到 IFrame，应用程序还可以接收来自 IFrame 的以下事件的信息：

* Embed
  * loaded
  * error
* 报告
  * pageChanged
  * dataSelected（即将推出）

[了解有关处理事件的详细信息](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>后续步骤

有关 Power BI JavaScript API 的详细信息，请查看以下链接：

* [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [对象模型参考](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [在线演示](https://microsoft.github.io/PowerBI-JavaScript/demo/)