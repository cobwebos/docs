---
title: Application Insights JavaScript SDK 的响应插件
description: 如何安装和使用 Application Insights JavaScript SDK 的响应插件。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 677810c21f9ea6151e2ffe7a4e2b9cdc8473a09f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227093"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 的响应插件

Application Insights JavaScript SDK 的响应插件，启用：

- 跟踪路由更改
- 反应组件使用统计信息

## <a name="getting-started"></a>入门

安装 npm 包：

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>基本用法

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>配置

| 名称    | 默认 | 说明                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | 对路由器历史记录做出响应。 有关详细信息，请参阅 [响应路由器包文档](https://reactrouter.com/web/api/history)。 若要了解如何访问组件以外的历史记录对象，请参阅 [响应-路由器常见问题解答](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>对组件使用情况跟踪做出反应

若要检测各种反应组件使用情况跟踪，请应用 `withAITracking` 高阶组件函数。

它将 `ComponentDidMount` 通过事件度量事件的时间 `ComponentWillUnmount` 。 但是，为了使此方法更精确，它会减去用户处于空闲状态的时间 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` 。

若要在 Azure 门户中查看此指标，你需要导航到 Application Insights 资源，选择 "指标" 选项卡，并配置空图表以显示自定义指标名称 "响应组件占用时间 (秒") "，选择聚合 (总和，avg 等，并将 split 应用为" Component Name "。

![显示自定义指标的图表屏幕截图，"响应组件占用时间 (秒) " 拆分为 "组件名称"](./media/javascript-react-plugin/chart.png)

您还可以运行自定义查询，以根据您的要求来划分 Application Insights 数据以生成报表和可视化效果。 在 Azure 门户导航到 Application Insights 资源，从 "概述" 选项卡的顶部菜单中选择 "分析"，然后运行查询。

![自定义指标查询结果的屏幕截图。](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 新的自定义指标可能需要长达10分钟的时间才能显示在 Azure 门户中。

## <a name="sample-app"></a>示例应用

查看 [Application Insights 反应演示](https://github.com/Azure-Samples/application-insights-react-demo)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 JavaScript SDK 的详细信息，请参阅 [Application Insights JAVASCRIPT sdk 文档](javascript.md)。
- 若要了解 Kusto 查询语言和查询 Log Analytics 中的数据，请参阅 [日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。
