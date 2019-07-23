---
title: 快速入门：使用 Azure Monitor Application Insights 监视网站 | Microsoft Docs
description: 提供使用 Azure Monitor Application Insights 快速设置客户端/浏览器端网站监视的说明
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/15/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 9845329d30da40243751c00f7c0fa62fdcfe5a0d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990095"
---
# <a name="start-monitoring-your-website"></a>开始监视网站

使用 Azure Monitor Application Insights，可轻松监视网站的可用性、性能和使用情况。 还可以快速确定并诊断应用程序中的错误，而无需等待用户报告这些错误。 Application Insights 提供服务器端监视和客户端/浏览器端监视功能。

本快速入门介绍如何添加[开源 Application Insights JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)，以便了解网站访客的客户端/浏览器端体验。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

- 需要一个 Azure 订阅。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="enable-application-insights"></a>启用 Application Insights

Application Insights 可以从任何连接 Internet 的应用程序收集遥测数据，不管是在本地运行还是在云中运行。 按照以下步骤开始查看此数据。

1. 选择“创建资源”   > “管理工具”   >   “Application Insights”。

   > [!NOTE]
   >如果这是你首次创建 Application Insights 资源，可以通过访问[创建 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)一文来了解更多信息。

   此时会显示配置对话框，请使用下表填写输入字段。

    | 设置        | 值           | 说明  |
   | ------------- |:-------------|:-----|
   | **名称**      | 全局唯一值 | 标识所监视的应用的名称 |
   | **资源组**     | myResourceGroup      | 用于托管 App Insights 数据的新资源组的名称 |
   | **位置** | 美国东部 | 选择离你近的位置或离托管应用的位置近的位置 |

2. 单击“创建”。 

## <a name="create-an-html-file"></a>创建一个 HTML 文件

1. 在本地计算机上创建名为 ``hello_world.html`` 的文件。 就此示例来说，该文件会放置在 C: 驱动器的根目录中，即 ``C:\hello_world.html``。
2. 将以下脚本复制到 ``hello_world.html`` 中：

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>配置 App Insights SDK

1. 选择“概述”   > “概要”  > 复制应用程序的**检测密钥**。

   ![“新建 App Insights 资源”窗体](media/website-monitoring/instrumentation-key-001.png)

2. 将以下脚本添加到 ``hello_world.html`` 的结束标记 ``</head>`` 之前：

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
         function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
      }({
         instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      });

      window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. 编辑 ``hello_world.html``，添加检测密钥。

4. 在本地浏览器会话中打开 ``hello_world.html``。 此时会创建单个页面视图。 可以刷新浏览器，生成多个测试性的页面视图。

## <a name="start-monitoring-in-the-azure-portal"></a>开始在 Azure 门户中监视

1. 现在可以在 Azure 门户中重新打开 Application Insights“概述”  页（已在其中检索到检测密钥），查看有关当前正在运行的应用程序的详细信息。 概览页上的四个默认图表局限于服务器端应用程序数据。 由于我们要检测客户端/浏览器端与 JavaScript SDK 的交互，因此该特定视图不会应用，除非我们也安装了服务器端 SDK。

2. 单击![“应用程序映射”图标](media/website-monitoring/006.png)  “Analytics”。  这将打开“Analytics”  ，该软件提供丰富的查询语言，可用于分析 Application Insights 收集的所有数据。 若要查看与客户端浏览器请求相关的数据，请运行以下查询：

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![一段时间内用户请求的分析图](./media/website-monitoring/analytics-query.png)

3. 返回到“概览”页。  在“调查”标题下单击“浏览器”，然后选择“性能”    。在此处可找到与网站性能相关的指标。 另外还有一个用于分析网站中故障和异常的相应视图。 可以单击“示例”，深入了解各个事务的详细信息。  在这里，可以访问[端到端事务详细信息](../../azure-monitor/app/transaction-diagnostics.md)体验。

   ![“服务器指标”图](./media/website-monitoring/browser-performance.png)

4. 若要开始探索[用户行为分析工具](../../azure-monitor/app/usage-overview.md)，请在 Application Insights 主菜单的“使用情况”标题下选择[**用户**](../../azure-monitor/app/usage-segmentation.md)。  由于我们是在单台计算机中进行测试，因此只会看到一个用户的数据。 对于实时网站，用户的分布可能如下所示：

     ![用户图](./media/website-monitoring/usage-users.png)

5. 如果我们已检测较复杂的包含多个页面的网站，则可使用另一有用的工具：[**用户流**](../../azure-monitor/app/usage-flows.md)。 使用**用户流**时，可以跟踪用户在访问网站不同部分时采用的路径。

   ![用户流可视化](./media/website-monitoring/user-flows.png)

若要了解更多用于监视网站的高级配置，请查看 [JavaScript SDK API 参考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)。

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习后续的快速入门或相关教程，请不要清除在本快速入门中创建的资源。 否则，如果不打算继续，请在 Azure 门户中执行以下步骤，删除本快速入门创建的所有资源。

1. 在 Azure 门户的左侧菜单中，单击“资源组”  ，然后单击“myResourceGroup”  。
2. 在资源组页上单击“删除”  ，在文本框中键入 **myResourceGroup**，然后单击“删除”  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查找和诊断性能问题](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
