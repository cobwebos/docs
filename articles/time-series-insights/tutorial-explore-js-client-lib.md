---
title: 探索 Azure 时序见解 JavaScript 客户端库
description: 了解 Azure 时序见解 JavaScript 客户端库和相关的编程模型。
author: BryanLa
manager: timlt
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: bryanla
ms.openlocfilehash: 6bd8b10100f8cdabca5f87addfea1690dc5fac6c
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809492"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>教程：探索 Azure 时序见解 JavaScript 客户端库

为了帮助 Web 开发人员查询并可视化时序见解 (TSI) 中存储的数据，我们开发了基于 JavaScript D3 的 TSI 客户端库。  本教程使用一个示例 Web 应用程序来逐步讲解如何探索 TSI 客户端库和相关的编程模型。

可以参考本教程中的主题来体验该库，以了解如何访问 TSI 数据，以及使用图表控件来呈现和可视化数据。 本教程的目标是提供足够的详细信息，使你能够在自己的 Web 应用程序中使用该库。

本教程的介绍内容包括：

> [!div class="checklist"]
> * TSI 示例应用程序。
> * TSI JavaScript 客户端库。
> * 示例应用程序如何使用该库来可视化 TSI 数据。

## <a name="prerequisites"></a>先决条件

本教程使用最新型 Web 浏览器（包括 [Edge](/microsoft-edge/devtools-guide)、[Chrome](https://developers.google.com/web/tools/chrome-devtools/)、[FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools)、[Safari](https://developer.apple.com/safari/tools/) 等）中的“开发人员工具”功能（也称为 DevTools 或 F12）。 如果你不熟悉此功能，请先在浏览器中探索此功能，然后再继续。

## <a name="time-series-insights-sample-application"></a>时序见解示例应用程序

整篇教程使用了时序见解示例应用程序来探索应用程序背后的源代码，包括 TSI JavaScript 客户端库的用法。 该示例是一个单页 Web 应用程序 (SPA)，演示如何使用该库。 该示例展示如何从一个示例 TSI 环境查询和可视化数据。

1. 浏览到[时序见解示例应用程序](https://insights.timeseries.azure.com/clientsample)。 此时会出现下图所示的页面，其中显示了登录提示：

   ![TSI 客户端示例登录提示](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. 选择“登录”并输入或选择凭据。 使用企业或组织帐户 (Azure Active Directory) 或个人帐户（Microsoft 帐户，简称 MSA）。

   ![TSI 客户端示例凭据提示](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. 成功登录后，会出现下图所示的页面。 该页显示填充了 TSI 数据的几款示例图表。 右上角显示了你自己的用户帐户和“注销”选项：

   ![登录后的 TSI 客户端示例主页](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>页面源代码和结构

首先，让我们查看浏览器中呈现的页面背后的 HTML 和 JavaScript 源代码。 我们不会逐个浏览所有的元素，而是了解主要的节，以便对页面工作原理有个大致的体会：

1. 在浏览器中打开“开发人员工具”。 查看构成当前页面的 HTML 元素（也称为 HTML 或 DOM 树）。

2. 展开 `<head>` 和 `<body>` 元素并注意以下节：

   - 在 `<head>` 元素下，可以看到提取其他文件以帮助页面正常运行的元素：
     - 用于引用 Azure Active Directory 身份验证库 **adal.min.js**（也称为 ADAL）的 `<script>` 元素。 ADAL 是一个 JavaScript 库，它提供 OAuth 2.0 身份验证（登录），并获取用于访问 API 的令牌。
     - 样式表（也称为 CSS）（例如 **sampleStyles.css** 和 **tsiclient.css**）的多个 `<link>` 元素。 样式表用于控制视觉页面样式详细信息，例如颜色、字体、间距，等等。
     - 用于引用 TSI 客户端 JavaScript 库 **tsiclient.js** 的 `<script>` 元素。 页面使用该库来调用 TSI 服务 API，并在页面上呈现图表控件。

     >[!NOTE]
     > [azure-activedirectory-library-for-js 存储库](https://github.com/AzureAD/azure-activedirectory-library-for-js)中提供了 ADAL JavaScript 库的源代码。
     > [tsiclient 存储库](https://github.com/Microsoft/tsiclient)中提供了 TSI 客户端 JavaScript 库的源代码。

   - 在 `<body>` 元素下，可以看到充当容器来定义页面上项布局的 `<div>` 元素，以及另一个 `<script>` 元素：
     - 第一个 `<div>` 元素指定“登录”对话框 (`id="loginModal"`)。
     - 第二个 `<div>` 元素充当以下对象的父级：
       - 一个标头 `<div>` 元素，用于在页面顶部附近显示状态消息和登录信息 (`class="header"`)。
       - 一个 `<div>` 元素，用于剩余的页面主体元素，包括所有图表 (`class="chartsWrapper"`)。
       - 一个 `<script>` 节，其中包含用于控制页面的所有 JavaScript。

   [![包含开发人员工具的 TSI 客户端示例](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. 展开 `<div class="chartsWrapper">` 元素，将会看到其他 `<div>` 子元素。 这些元素用于定位每个图表控件示例。 请注意，有好几对 `<div>` 元素，每个图表示例各有一对：

   - 第一个 (`class="rowOfCardsTitle"`) 元素包含描述性的标题，用于概括图表演示的内容。 例如：“包括完整大小图例的静态折线图。”
   - 第二个 (`class="rowOfCards"`) 元素是父级，包含用于在行中定位实际图表控件的 `<div>` 子元素。

   ![正文 div 元素](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. 现在，展开紧靠在 `<div class="chartsWrapper">` 元素下面的 `<script type="text/javascript">` 元素。 注意页面级 JavaScript 节的开头部分，它用于处理所有页面逻辑：身份验证、调用 TSI 服务 API、呈现图表控件，等等：

   ![正文脚本](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>TSI JavaScript 客户端库的概念

尽管本文不会详细介绍 TSI 客户端库 **tsclient.js**，但从本质而言，该库为以下两个重要类别提供抽象：

- **用于调用 TSI 查询 API 的包装器方法**：用于通过聚合表达式查询 TSI 数据的 REST API。 这些方法组织在库的 `TsiClient.Server` 命名空间下。
- **用于创建和填充多种图表控件的方法**：用于在网页中呈现 TSI 聚合数据的方法。 这些方法组织在库的 `TsiClient.UX` 命名空间下。

以下概念是通用的，广泛适用于 TSI 客户端库 API。

### <a name="authentication"></a>身份验证

如前所述，本示例是一个 SPA，它使用 ADAL 中的 OAuth 2.0 支持进行用户身份验证。 下面是此脚本节中的一些关注点：

1. 使用 ADAL 进行身份验证时，客户端应用程序必须在 Azure Active Directory (Azure AD) 应用程序注册表中注册自身。 作为一个 SPA，此应用程序会注册为使用“隐式”OAuth 2.0 授权授予流。 相应地，此应用程序会在运行时指定一些注册属性（例如客户端 ID GUID (`clientId`) 和重定向 URI (`postLogoutRedirectUri`)）以参与流。

2. 稍后，应用程序会从 Azure AD 请求“访问令牌”。 将会针对特定的服务/API 标识符 https://api.timeseries.azure.com 颁发拥有有限权限集的访问令牌。 服务/API 标识符也称为令牌“受众”。 令牌权限是代表登录用户颁发的。 服务/API 的标识符是包含在应用程序的 Azure AD 注册中的另一个属性。 ADAL 向应用程序返回访问令牌后，在访问 TSI 服务 API 时，系统会将该令牌作为“持有者令牌”传递。

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>控制标识

如前所述，`<body>` 元素中的 `<div>` 元素为页面上演示的所有图表控件提供布局。 其中的每个 `<div>` 元素指定图表控件的位置和视觉特性的属性，包括 `id` 属性。 `id` 属性提供唯一的标识符，将在 JavaScript 代码中使用该标识符来标识和绑定到用于呈现和更新的每个控件。

### <a name="aggregate-expressions"></a>聚合表达式

TSI 客户端库 API 重度使用聚合表达式。 使用聚合表达式可以构造一个或多个“搜索词”。 API 在设计上类似于[时序见解资源管理器](https://insights.timeseries.azure.com/demo)使用搜索范围、where 谓词、度量和 split-by 值。 大多数库 API 采用聚合表达式的数组，服务使用这些表达式来生成 TSI 数据查询。

### <a name="call-pattern"></a>调用模式

图表控件的填充和呈现遵循一种通用模式。 在实例化和加载 TSI 示例应用程序控件的整个页面 JavaScript 中，都会发现使用了此模式：

1. 声明一个 `array` 用于保存一个或多个 TSI 聚合表达式：

   ```javascript
   var aes =  [];
   ```

2. 生成 1 到 n 个聚合表达式对象，并将其添加到聚合表达式数组：

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression 参数**

   | 参数 | 说明 | 示例 |
   | --------- | ----------- | ------- |
   | `predicateObject` | 数据筛选表达式。 |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | 所用度量的属性名称。 | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | 度量属性的所需聚合。 | `['avg', 'min']` |
   | `searchSpan`      | 聚合表达式的持续时间和间隔大小。 | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | 用作拆分依据的字符串属性（可选 – 可为 null）。 | `{property: 'Station', type: 'String'}` |
   | `color`         | 要呈现的对象的颜色。 | `'pink'` |
   | `alias`           | 聚合表达式的友好名称。 | `'Factory3Temperature'` |
   | `contextMenuActions` | 要在可视化效果中绑定到时序对象的操作数组（可选）。 | 有关详细信息，请参阅[“高级功能”部分中的“弹出上下文菜单”](#popup-context-menus)。 |

3. 使用 `TsiClient.Server` API 调用 TSI 查询以请求聚合数据：

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates 参数**

   | 参数 | 说明 | 示例 |
   | --------- | ----------- | ------- |
   | `token`     | TSI API 的访问令牌。 |  `authContext.getTsiToken()` 有关详细信息，请参阅[身份验证部分](#authentication)。 |
   | `envFQDN`   | TSI 环境的完全限定域名 (FQDN)。 | 例如，Azure 门户中的 `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`。 |
   | `aeTsxArray` | TSI 查询表达式的数组。 | 如前所述使用 `aes` 变量：`aes.map(function(ae){return ae.toTsx()}`。 |

4. 将 TSI 查询返回的压缩结果转换为 JSON 以进行可视化。

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. 使用 `TsiClient.UX` API 创建图表控件，并将其绑定到页面上的某个 `<div>` 元素。

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. 在图表控件中填充转换后的 JSON 数据对象，并在页面上呈现该控件。

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>呈现控件

TSI 客户端库目前公开 8 个独特的分析控件：折线图、饼图、条形图、热度地图、层次结构控件、可访问的网格、离散事件时间线和状态转换时间线。

### <a name="line-bar-pie-chart-examples"></a>折线图、饼图、条形图示例

让我们看看应用程序中演示的某些标准图表控件背后的代码，以及用于创建这些控件的编程模型/模式。 具体而言，我们将查看 HTML 中 `// Example 3/4/5` 注释下面的节，其中呈现了 ID 值为 `chart3`、`chart4` 和 `chart5` 的控件。

回顾[“页面源代码和结构”部分](#page-source-and-structure)所述的步骤 #3，图表控件在页面上按行排列，每个控件都有一个描述性的标题行。 在本示例中，要填充的三个图表都位于“同一数据中的多个图表类型”标题 `<div>` 元素下面，并已绑定到标题下面的三个 `<div>` 元素：

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

以下 JavaScript 代码节使用前面所述的模式：生成 TSI 聚合表达式，使用它们来查询 TSI 数据，然后呈现三个图表。 请注意使用了 `tsiClient.ux` 命名空间中的三个类型 `LineChart`、`BarChart` 和 `PieChart` 创建并呈现了相应图表。 另请注意，所有三个图表都可以使用相同的聚合表达式数据 `transformedResult`：

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

在呈现时，三个图表如下所示：

[![基于相同数据的多个图表类型](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>高级功能

TSI 客户端库还会公开一些可按需使用的可选高级功能。

### <a name="states-and-events"></a>状态和事件

提供的高级功能的一个示例是，将状态转换和离散事件添加到图表。 此功能可用于突出显示事件、警报，以及“开/关”等状态开关。

查看 HTML 节中 `// Example 10` 注释后面的代码。 该代码在“具有多个序列类型的折线图”标题下面呈现一个线条控件，并将其绑定到 ID 值为 `chart10` 的 `<div>` 元素。

1. 首先定义一个名为 `events4` 的结构，用于保存要跟踪的状态更改元素。结构包含：

   - 名为 `Component States` 的字符串键。
   - 表示状态的值对象数组。 每个对象包括：
     - 包含 JavaScript ISO 时间戳的字符串键。
     - 包含状态特征的数组：颜色和说明。

2. 接下来，为保存要跟踪的事件元素数组的“事件”定义 `events5` 结构。数组结构的形状与概述的 `events4` 结构相同。

3. 最后呈现折线图，并结合图表选项参数传入两个结构：`events:` 和 `states:`。 请注意用于指定 `tooltip:`、`theme:` 或 `grid:` 的其他选项参数。

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

在视觉上，菱形标记/弹出窗口用于指示事件，沿时间刻度显示的彩色条块/弹出窗口指示状态更改：

[![具有多个序列类型的折线图](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>弹出上下文菜单

高级功能的另一个示例是自定义的上下文菜单（右键单击弹出菜单）。 自定义的上下文菜单可用于在应用程序范围内启用操作和逻辑后续步骤。

查看 HTML 节中 `// Example 13/14/15` 注释后面的代码。 此代码最初在“包含用于创建饼图/条形图的上下文菜单的折线图”标题下面呈现折线图，该图表已绑定到 ID 值为 `chart13` 的 `<div>` 元素。 使用上下文菜单可在折线图中动态创建绑定到 ID 为 `chart14` 和 `chart15` 的 `<div>` 元素的饼图和条形图。 此外，饼图和条形图也使用上下文菜单来实现其自身的功能：分别是将数据从饼图复制到条形图，以及在浏览器控制台窗口中列显饼图数据。

1. 首先定义一系列自定义操作。 每个操作包含由一个或多个元素构成的数组。 每个元素定义单个上下文菜单项：

   - `barChartActions`：此操作定义饼图的上下文菜单，该菜单包含一个元素用于定义单个项：
     - `name`：用于菜单项的文本：“在控制台上列显参数。”
     - `action`：与菜单项关联的操作。 该操作始终是一个匿名函数，根据用于创建图表的聚合表达式采用三个参数。 在本例中，这些参数将写入浏览器控制台窗口：
       - `ae`：聚合表达式数组。
       - `splitBy`：splitBy 值。
       - `timestamp`：时间戳。

   - `pieChartActions`：此操作定义条形图的上下文菜单，该菜单包含一个元素用于定义单个项。 形状和架构是前面所述的 `barChartActions` 元素相同，但请注意，`action` 函数明显不同：它实例化并呈现条形图。 另请注意，使用了 `ae` 参数指定聚合表达式数组，在运行时打开菜单项期间，会传递该数组。 该函数还使用 `barChartActions` 上下文菜单设置 `ae.contextMenu` 属性。
   - `contextMenuActions`：此操作定义折线图的上下文菜单，其中包含用于定义三个菜单项的三个元素。 每个元素的形状和架构与前面所述的元素相同。 与 `barChartActions` 元素一样，第一个项将三个函数参数写入浏览器控制台窗口。 与 `pieChartActions` 元素一样，另外的两个项分别实例化并呈现饼图和条形图。 此外，另外的两个项分别使用 `pieChartActions` 和 `barChartActions` 上下文菜单设置其 `ae.contextMenu` 属性。

2. 接下来，将两个聚合表达式推送到 `aes` 聚合表达式数组，并为每个项指定 `contextMenuActions` 数组。 将在折线图控件中使用这些表达式。

3. 最后，一开始只呈现折线图，在运行时，即可从折线图呈现饼图和条形图。

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

屏幕截图显示了这些图表及其各自的弹出上下文菜单。 饼图和条形图是使用折线图上下文菜单选项动态创建的。

[![包含用于创建饼图/条形图的上下文菜单的折线图](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>画笔

画笔用于限定定义操作（例如缩放和浏览）的时间范围。

前一示例“包含用于创建饼图/条形图的上下文菜单的折线图”（描述[弹出上下文菜单](#popup-context-menus-section)）中显示了用于演示画笔的代码。

1. 画笔操作类似于上下文菜单，为画笔定义一系列自定义操作。 每个操作包含由一个或多个元素构成的数组。 每个元素定义单个上下文菜单项：
   - `name`：用于菜单项的文本：“在控制台上列显参数。”
   - `action`：与菜单项关联的操作，始终是采用两个参数的匿名函数。 在本例中，这些参数将写入浏览器控制台窗口：
      - `fromTime`：画笔选项的“起始时间”时间戳。
      - `toTime`：画笔选项的“截止时间”时间戳。

2. 画笔操作将添加为另一个图表选项属性。 请注意传递给 `linechart.Render` 调用的 `brushContextMenuActions: brushActions` 属性。

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![包含用于通过画笔创建饼图/条形图的上下文菜单的折线图](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 登录到 TSI 示例应用程序并探索其源代码。
> * 使用 TSI JavaScript 客户端库中的 API。
> * 使用 JavaScript 创建图表控件并在其中填充 TSI 数据。

如前所述，TSI 示例应用程序使用演示数据集。 若要了解如何创建自己的 TSI 环境和数据集，请继续学习以下文章：

> [!div class="nextstepaction"]
> [教程：创建 Azure 时序见解环境](tutorial-create-populate-tsi-environment.md)


