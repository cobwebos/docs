---
title: 使用 Application Insights 监视 SharePoint site 站点
description: 开始使用新检测密钥监视新应用程序
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 395e8d667985318f4a084428c6fd4c395ee8b956
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671437"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>使用 Application Insights 监视 SharePoint site 站点
Azure Application Insights 监视应用的可用性、性能和使用情况。 下面介绍如何为 SharePoint 站点设置它。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源
在 [Azure 门户](https://portal.azure.com)中，创建新的 Application Insights 资源。 选择 ASP.NET 作为应用程序类型。

![单击“属性”，选择密钥，并按 Ctrl+C](./media/sharepoint/001.png)

打开的窗口是查看关于应用的性能和用法数据的位置。 若要在下次登录 Azure 时返回到它，应在开始屏幕上查找它的磁贴。 或者单击“浏览”查找它。

## <a name="add-the-script-to-your-web-pages"></a>将脚本添加到网页

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

在要跟踪的每个页面的 &lt;/头&gt; 标记之前插入脚本。如果你的网站有母版页，你可以将该脚本放在此处。 例如，在 ASP.NET MVC 项目中，将其放置在 View\Shared\_Layout.cshtml 中

该脚本包含将遥测定向到 Application Insights 资源的检测密钥。

### <a name="add-the-code-to-your-site-pages"></a>将代码添加到站点页面
#### <a name="on-the-master-page"></a>在母版页上
如果可以编辑站点的模板页，这会为站点中的每个页面提供监控。

签出母版页并使用 SharePoint Designer 或任何其他编辑器编辑它。

![](./media/sharepoint/03-master.png)

在 </head> 标记之前添加代码。 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>或在个别页面上
要监视一组有限的页面，请将脚本分别添加到每个页面。 

插入 Web 部分并在其中嵌入代码片段。

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>查看关于应用的数据
重新部署应用。

返回到 [Azure 门户](https://portal.azure.com)中的应用程序边栏选项卡。

第一批事件会显示在“搜索”中。 

![](./media/sharepoint/09-search.png)

如果需要更多数据，在几秒后单击“刷新”。

## <a name="capturing-user-id"></a>捕获用户 ID
标准网页代码片段不从 SharePoint 捕获用户 ID，但可在进行少量修改的情况下执行该操作。

1. 从 Application Insights 中的 Essentials 下拉列表中复制应用的检测密钥。 

    ![](./media/sharepoint/02-props.png)

1. 在下面代码片段中将“XXXX”替换为检测密钥。 
2. 将脚本嵌入 SharePoint 应用，而不是从门户获取的代码片段。

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>后续步骤
* 用于监视站点可用性的 [Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)。
* 适用于其他应用类型的 [Application Insights](../../azure-monitor/app/app-insights-overview.md)。

<!--Link references-->


