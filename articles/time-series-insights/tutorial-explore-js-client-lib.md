---
title: 教程：探索 Azure 时序见解 JavaScript 客户端库 | Microsoft Docs
description: 了解 Azure 时序见解 JavaScript 客户端库和相关的编程模型。
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 7208c0d42cba0e7f04fc6876bf3ada9fa65a00d9
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991458"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>教程：探索 Azure 时序见解 JavaScript 客户端库

为了帮助 Web 开发人员查询并可视化时序见解中存储的数据，我们开发了基于 JavaScript D3 的 Azure 时序见解客户端库。 本教程将使用托管示例应用引导你完成时序见解客户端库和编程模型。

本教程将详细介绍如何使用库、如何访问时序见解数据以及如何使用图表控件来呈现和可视化数据。 还将介绍如何尝试使用不同类型的图形来可视化数据。 在本教程结束时，你将能够使用客户端库将时序见解功能合并到你自己的 Web 应用中。

具体来说，将学习以下内容：

> [!div class="checklist"]
> * 时序见解示例应用程序
> * 时序见解 JavaScript 客户端库
> * 示例应用程序如何使用该库来可视化时序见解数据

> [!NOTE]
> * 本教程使用免费的托管[时序见解 Web 演示](https://insights.timeseries.azure.com/clientsample)。
> * 时序见解示例应用源文件在 [GitHub 示例存储库](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)中提供。
> * 请阅读[时序见解客户端参考文档](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)。

## <a name="video"></a>视频

在本视频中，我们将介绍开源时序见解 JavaScript SDK：
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>先决条件

本教程使用浏览器的“开发人员工具”功能  。 现代 Web 浏览器（[Microsoft Edge](/microsoft-edge/devtools-guide)、[Chrome](https://developers.google.com/web/tools/chrome-devtools/)、[Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools)、[Safari](https://developer.apple.com/safari/tools/) 等）通常可以通过键盘上的 F12 热键访问“Web 检查器视图”  。 访问该视图的另一种方法是右键单击网页，然后选择“检查元素”  。

## <a name="time-series-insights-sample-application"></a>时序见解示例应用程序

整篇教程都使用免费的托管时序见解示例应用来探索应用程序背后的源代码和时序见解 JavaScript 客户端库。 通过使用示例应用，你将了解如何在 JavaScript 中与时序见解进行交互并通过图表和图形可视化数据。

1. 转到[时序见解示例应用程序](https://insights.timeseries.azure.com/clientsample)。 将显示以下登录提示：

   [![时序见解客户端示例登录提示](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. 选择“登录”，输入或选择凭据  。 可使用企业组织帐户 (Azure Active Directory) 或个人帐户（Microsoft 帐户）。

   [![时序见解客户端示例凭据提示](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. 登录后，将显示一个页面，其中显示填充了时序见解数据的图表。 右上角显示了你自己的用户帐户和“注销”选项： 

   [![登录后的时序见解客户端示例主页](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>页面源代码和结构

首先，让我们查看呈现的网页的 [HTML 和 JavaScript 源代码](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html)：

1. 在浏览器中打开“开发人员工具”。  查看构成当前页面的 HTML 元素（也称为 HTML 或 DOM 树）。

1. 展开 `<head>` 和 `<body>` 元素并观察以下节：

   * 在 `<head>` 元素下，你将找到允许应用运行的页面元数据和依赖项：
     * 用于引用 Azure Active Directory 身份验证库 (ADAL) 文件 *adal.min.js* 的 `<script>` 元素。 ADAL 是一个 JavaScript 库，它提供 OAuth 2.0 身份验证（登录），并获取用于访问 API 的令牌。
     * 样式表（也称为 *CSS*，例如 *sampleStyles.css* 和 *tsiclient.css*）的多个 `<link>` 元素。 样式表控制视觉页面样式详细信息，例如颜色、字体和间距。
     * 用于引用时序见解 JavaScript 客户端库 *tsiclient.js* 的 `<script>` 元素。 页面使用该库来调用时序见解服务 API，并在页面上呈现图表控件。

     >[!NOTE]
     > * [azure-activedirectory-library-for-js 存储库](https://github.com/AzureAD/azure-activedirectory-library-for-js)中提供了 ADAL JavaScript 库的源代码。
     > * [tsiclient 存储库](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)中提供了时序见解 JavaScript 客户端库的源代码。

   * 在 `<body>` 元素下，可看到帮助定义页面上项布局的 `<div>` 元素，以及另一个 `<script>` 元素：
     * 第一个 `<div>` 元素指定“登录”对话框 (`id="loginModal"`)  。
     * 第二个 `<div>` 元素充当以下对象的父级：
       * 一个标头 `<div>` 元素，用于在页面顶部附近显示状态消息和登录信息 (`class="header"`)。
       * 一个 `<div>` 元素，用于剩余的页面主体元素，包括图表 (`class="chartsWrapper"`)。
       * 一个 `<script>` 节，其中包含用于控制页面的 JavaScript。

   [![包含开发人员工具的时序见解客户端示例](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. 展开 `<div class="chartsWrapper">` 元素，将看到其他 `<div>` 子元素。 这些元素用于定位每个图表控件示例。 有好几对 `<div>` 元素，每个图表示例各有一对：

   * 第一个 (`class="rowOfCardsTitle"`) 元素包含描述性的标题，用于概括图表演示的内容。 例如： `Static Line Charts With Full-Size Legends.`
   * 第二个 (`class="rowOfCards"`) 元素是父级，包含用于在行中定位实际图表控件的其他 `<div>` 子元素。

   [![正文 div 元素](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. 展开紧靠在 `<div class="chartsWrapper">` 元素下面的 `<script type="text/javascript">` 元素。 页面级 JavaScript 节的开头部分用于处理所有页面逻辑（身份验证、调用时序见解服务 API、呈现图表控件等）：

   [![正文脚本](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>时序见解 JavaScript 客户端库概念

时序见解客户端库 (*tsclient.js*) 为两个重要的 JavaScript 功能提供抽象：

* **用于调用时序见解查询 API 的包装器方法**：可用于通过聚合表达式查询时序见解数据的 REST API。 这些方法组织在库的 TsiClient.Server 命名空间下。

* **用于创建和填充多种图表控件的方法**：可用于在网页中呈现时序见解聚合数据的方法。 这些方法组织在库的 TsiClient.UX 命名空间下。

通过这些简化，开发人员可以更轻松地生成使用时序见解数据提供支持的 UI 图形和图表组件。

### <a name="authentication"></a>Authentication

[时序见解示例应用程序](https://insights.timeseries.azure.com/clientsample)是具有 ADAL OAuth 2.0 用户身份验证支持的单页应用：

1. 使用 ADAL 进行身份验证时，必须在 Azure Active Directory (Azure AD) 中注册客户端应用。 注册单页应用是为了使用 [OAuth 2.0 隐式授权流](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow)。
1. 应用程序必须在运行时指定一些注册属性。 这些属性包括客户端 GUID (`clientId`) 和重定向 URI (`postLogoutRedirectUri`)。
1. 稍后，应用会从 Azure AD 请求*访问令牌*。 将会针对特定的服务或 API 标识符 (https:\//api.timeseries.azure.com) 颁发拥有一组有限权限的访问令牌。 令牌权限是代表登录用户颁发的。 服务或 API 的标识符是包含在应用的 Azure AD 注册中的另一个属性。
1. ADAL 向应用返回访问令牌后，在访问时序见解服务 API 时，系统会将该令牌作为*持有者令牌*传递。

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> 若要了解有关 Microsoft 支持的 Azure AD 身份验证库的详细信息，请参阅 [Azure Active Directory 身份验证库参考文档](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries)。

### <a name="control-identification"></a>控制标识

在提供的示例中，`<div>` 元素排列在父 `<body>` 元素中，以便为页面上呈现的图表控件提供合理的布局。

其中的每个 `<div>` 元素指定图表控件的位置和视觉对象特性的属性。 HTML 元素 `id` 属性用作唯一标识符，用于绑定到特定控件以呈现和更新可视化数据。

### <a name="aggregate-expressions"></a>聚合表达式

时序见解客户端库 API 使用聚合表达式：

* 使用聚合表达式可以构造一个或多个“搜索词”  。

* 客户端 API 的设计目的是提供与另一个演示应用（[时序见解资源管理器](https://insights.timeseries.azure.com/demo)）类似的功能，后者使用搜索范围、`where` 谓词、度量值和 `splitBy` 值。

* 大多数客户端库 API 都采用聚合表达式的数组，服务使用这些表达式来生成时序见解数据查询。

### <a name="call-pattern"></a>调用模式

图表控件的填充和呈现遵循一种常规模式。 你可以在整个示例应用中观察此常规模式，并在使用客户端库时获得帮助：

1. 声明一个 `array` 来保存一个或多个时序见解聚合表达式：

   ```javascript
   var aes =  [];
   ```

1. 构建“1”到“n”聚合表达式对象   。 然后将其添加到聚合表达式数组：

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression 参数**

   | 参数 | 说明 | 示例 |
   | --------- | ----------- | ------- |
   | `predicateObject` | 数据筛选表达式 |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | 所用度量的属性名称 | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | 所需度量属性的聚合 | `['avg', 'min']` |
   | `searchSpan`      | 聚合表达式的持续时间和间隔大小 | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | 用作拆分依据的字符串属性（可选：可为 null） | `{property: 'Station', type: 'String'}` |
   | `color`         | 要呈现的对象的颜色 | `'pink'` |
   | `alias`           | 聚合表达式的友好名称 | `'Factory3Temperature'` |
   | `contextMenuActions` | 要在可视化效果中绑定到时序对象的操作数组（可选） | 有关详细信息，请参阅[弹出上下文菜单](#pop-up-context-menus)。 |

1. 使用 TsiClient.Server API 调用时序见解查询以请求聚合数据：

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates 参数**

   | 参数 | 说明 | 示例 |
   | --------- | ----------- | ------- |
   | `token`     | 时序见解 API 的访问令牌 |  `authContext.getTsiToken()`<br />有关详细信息，请参阅[身份验证](#authentication)。 |
   | `envFQDN`   | 时序见解环境的完全限定域名 (FQDN) | 通过 Azure 门户。 例如：`10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`。 |
   | `aeTsxArray` | 时序见解查询表达式的数组 | 如前所述使用 `aes` 变量：`aes.map(function(ae){return ae.toTsx()}`。 |

1. 将时序见解查询返回的压缩结果转换为 JSON 以进行可视化：

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. 使用 TsiClient.UX API 创建图表控件。 将其绑定到页面上的某个 `<div>` 元素：

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. 在图表控件中填充转换后的 JSON 数据对象，并在页面上呈现该控件：

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>呈现控件

时序见解客户端库提供八个独特的、开箱即用的分析控件：

* **折线图**
* **饼图**
* <bpt id="p1">**</bpt>bar chart<ept id="p1">**</ept>
* **heatmap**
* **层次结构控件**
* **可访问网格**
* **离散事件时间线**
* **状态转换时间线**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>折线图、条形图和饼图示例

查看用于呈现某些标准图表控件的演示代码。 请注意用于创建这些控件的编程模型和模式。 具体而言，我们将查看 `// Example 3/4/5` 注释下面的 HTML，其中呈现了 HTML `id` 值为 `chart3`、`chart4` 和 `chart5` 的控件。

回顾[页面源代码和结构部分](#page-source-and-structure)的步骤 3，图表控件在页面上按行排列。 每个图表控件都有一个描述性的标题行。 在本示例中，要填充的三个图表都位于 `Multiple Chart Types From the Same Data` 标题 `<div>` 元素下面，并已绑定到标题下面的三个 `<div>` 元素：

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

以下 JavaScript 代码节使用前面所述的模式：生成时序见解聚合表达式，使用它们来查询时序见解数据，然后呈现三个图表。 使用 tsiClient.ux 命名空间中的三个图表类型：`LineChart`、`BarChart` 和 `PieChart`。 图表类型用于创建并呈现相应的图表。 所有三个图表都可以使用相同的聚合表达式数据 `transformedResult`：

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

在呈现时，三个图表如下所示：

[![基于相同数据的多个图表类型](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>高级功能

时序见解客户端库具有一些其他功能，你可以使用它们创造性地实现数据可视化。

### <a name="states-and-events"></a>状态和事件

有一个高级功能是将状态转换和离散事件添加到图表中。 此功能可用于突出显示事件、发出警报，以及创建状态开关（例如打开/关闭开关）。

查看 `// Example 10` 注释周围的代码。 该代码在标题 `Line Charts with Multiple Series Types` 下面呈现一个线条控件，并将其绑定到 HTML `id` 值为 `chart10` 的 `<div>` 元素。

以下步骤描述了该过程：

1. 定义一个名为 `events4` 的结构，用于保存要跟踪的状态更改元素。结构包含：

   * 名为 `Component States` 的字符串键。
   * 表示状态的值对象数组。 每个对象包括：
     * 包含 JavaScript ISO 时间戳的字符串键。
     * 包含状态特征的数组：颜色和说明。

1. 为保存要跟踪的事件元素数组的 `Incidents` 定义 `events5` 结构。数组结构的形状与概述的 `events4` 结构相同。

1. 呈现折线图，并结合图表选项参数传入两个结构：`events:` 和 `states:`。 请注意用于指定 `tooltip:`、`theme:` 或 `grid:` 的其他选项参数。

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

菱形标记/弹出窗口用于指示事件，沿时间刻度显示的彩色条块/弹出窗口指示状态更改：

[![具有多个序列类型的折线图](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>弹出上下文菜单

另一个高级功能是能够创建自定义上下文菜单（右键单击弹出菜单）。 自定义的上下文菜单可用于在应用程序范围内启用操作和逻辑后续步骤。

查看 `// Example 13/14/15` 注释周围的代码。 此代码最初在标题 `Line Chart with Context Menu to Create Pie/Bar Chart` 下面呈现折线图。 该图表已绑定到 HTML `id` 值为 `chart13` 的 `<div>` 元素。

使用上下文菜单可在折线图中动态创建绑定到 ID 为 `chart14` 和 `chart15` 的 `<div>` 元素的饼图和条形图。 饼图和条形图也使用上下文菜单来实现其自身的功能：分别是将数据从饼图复制到条形图，以及将条形图数据输出到浏览器控制台窗口中。

以下步骤描述了该过程：

1. 定义一系列自定义操作。 每个操作包含由一个或多个元素构成的数组。 每个元素定义单个上下文菜单项：

   * `barChartActions`：此操作定义饼图的上下文菜单，该菜单包含一个元素用于定义单个项：
     * `name`：用于菜单项的文本：“将参数输出到控制台。”
     * `action`：与菜单项关联的操作。 该操作始终是一个匿名函数，根据用于创建图表的聚合表达式采用三个参数。 在本例中，这些参数将写入浏览器控制台窗口：
       * `ae`：聚合表达式数组。
       * `splitBy`：`splitBy` 值。
       * `timestamp`：时间戳。

   * `pieChartActions`：此操作定义条形图的上下文菜单，该菜单包含一个元素用于定义单个项。 形状和架构与前面所述的 `barChartActions` 元素相同，但 `action` 函数明显不同：它实例化并呈现条形图。 `ae` 参数用于指定聚合表达式数组，在运行时打开菜单项期间，会传递该数组。 该函数还使用 `barChartActions` 上下文菜单设置 `ae.contextMenu` 属性。
   * `contextMenuActions`：此操作定义折线图的上下文菜单，其中包含用于定义三个菜单项的三个元素。 每个元素的形状和架构与前面所述的元素相同。 与 `barChartActions` 元素一样，第一个项将三个函数参数写入浏览器控制台窗口。 与 `pieChartActions` 元素一样，另外的两个项分别实例化并呈现饼图和条形图。 此外，另外的两个项分别使用 `pieChartActions` 和 `barChartActions` 上下文菜单设置其 `ae.contextMenu` 属性。

1. 将两个聚合表达式推送到 `aes` 聚合表达式数组。 它们为每个项指定 `contextMenuActions` 数组。 将在折线图控件中使用这些表达式。

1. 一开始只呈现折线图，在运行时，即可从折线图呈现饼图和条形图。

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

下面的屏幕截图显示了这些图表及其各自的弹出上下文菜单。 饼图和条形图是使用折线图上下文菜单选项动态创建的。

[![包含用于创建饼图和条形图的上下文菜单的折线图](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>画笔

可以使用画笔限定定义操作（例如缩放和浏览）的时间范围。

描述弹出上下文菜单的 `Line Chart with Context Menu to Create Pie/Bar Chart` 示例中显示了用于演示画笔的代码。

* 画笔操作类似于上下文菜单，为画笔定义一系列自定义操作。 每个操作均包含一个由一个或多个元素构成的数组。 每个元素定义单个上下文菜单项：
   * `name`：用于菜单项的文本：“将参数输出到控制台。”
   * `action`：与菜单项关联的操作，始终是采用两个参数的匿名函数。 在本例中，这些参数将写入浏览器控制台窗口：
     * `fromTime`：画笔选项的 `from` 时间戳。
     * `toTime`：画笔选项的 `to` 时间戳。

* 画笔操作将添加为另一个图表选项属性。 `brushContextMenuActions: brushActions` 属性传递给 `linechart.Render` 调用。

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![包含用于通过画笔创建饼图和条形图的上下文菜单的折线图](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 登录到时序见解示例应用程序并探索其源代码
> * 使用时序见解 JavaScript 客户端库中的 API
> * 使用 JavaScript 创建图表控件并在其中填充时序见解数据

时序见解示例应用程序使用演示数据集。 若要了解如何创建自己的时序见解环境和数据集，请阅读下文：

> [!div class="nextstepaction"]
> [教程：创建 Azure 时序见解环境](tutorial-create-populate-tsi-environment.md)

或查看时序见解示例应用程序源文件：

> [!div class="nextstepaction"]
> [时序见解示例应用存储库](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

请阅读时序见解客户端 API 参考文档：

> [!div class="nextstepaction"]
> [时序见解 API 参考文档](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
