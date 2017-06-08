---
title: "使用 Application Insights 监视 SharePoint site 站点"
description: "开始使用新检测密钥监视新应用程序"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/24/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 3977cbc0f111de39621a875cce2d6714559a73fd
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>使用 Application Insights 监视 SharePoint site 站点
Azure Application Insights 监视应用的可用性、性能和使用情况。 下面介绍如何为 SharePoint 站点设置它。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源
在 [Azure 门户](https://portal.azure.com)中，创建新的 Application Insights 资源。 选择 ASP.NET 作为应用程序类型。

![单击“属性”，选择密钥，然后按 Ctrl+C](./media/app-insights-sharepoint/01-new.png)

打开的边栏选项卡是查看关于应用的性能和用法数据的位置。 若要在下次登录 Azure 时返回到它，应在开始屏幕上查找它的磁贴。 或者单击“浏览”查找它。

## <a name="add-our-script-to-your-web-pages"></a>将脚本添加到网页
在“快速启动”中获取网页的脚本：

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

在要跟踪的每个页面的 &lt;/head&gt; 标记之前插入脚本。 如果网站有母版页，可以在那里插入脚本。 例如，在 ASP.NET MVC 项目中，将其放置在 View\Shared\_Layout.cshtml 中

该脚本包含将遥测定向到 Application Insights 资源的检测密钥。

### <a name="add-the-code-to-your-site-pages"></a>将代码添加到站点页面
#### <a name="on-the-master-page"></a>在母版页上
如果可以编辑站点的模板页，这将为站点中的每个页面提供监控。

签出母版页并使用 SharePoint Designer 或任何其他编辑器编辑它。

![](./media/app-insights-sharepoint/03-master.png)

在 </head> 标记之前添加代码。 

![](./media/app-insights-sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>或在个别页面上
若要监视一组有限的页面，请将脚本分别添加到每个页面。 

插入 Web 部分并在其中嵌入代码片段。

![](./media/app-insights-sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>查看关于应用的数据
重新部署应用。

返回到 [Azure 门户](https://portal.azure.com)中的应用程序边栏选项卡。

第一批事件将显示在“搜索”中。 

![](./media/app-insights-sharepoint/09-search.png)

如果需要更多数据，在几秒后单击“刷新”。

在“概述”边栏选项卡中，单击“使用情况分析”查看用户、会话和页面视图的图表：

![](./media/app-insights-sharepoint/06-usage.png)

单击任意图表查看更多详细信息，例如“页面视图”：

![](./media/app-insights-sharepoint/07-pages.png)

或用户：

![](./media/app-insights-sharepoint/08-users.png)

## <a name="capturing-user-id"></a>捕获用户 ID
标准网页代码片段不从 SharePoint 捕获用户 ID，但可在进行少量修改的情况下执行该操作。

1. 从 Application Insights 中的 Essentials 下拉列表中复制应用的检测密钥。 

    ![](./media/app-insights-sharepoint/02-props.png)

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
* 用于监视站点可用性的 [Web 测试](app-insights-monitor-web-app-availability.md)。
* 适用于其他应用类型的 [Application Insights](app-insights-overview.md)。

<!--Link references-->



