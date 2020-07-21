---
title: 解决 JavaScript web 应用程序的 SDK 加载失败问题-Azure 应用程序 Insights
description: 如何解决 JavaScript web 应用程序的 SDK 加载失败问题
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.openlocfilehash: a76ed65ebc1c56232d4fa42c6df20f619fe14ca3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517053"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>JavaScript web 应用的 SDK 加载失败疑难解答

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

当 JavaScript 代码段（v3 或更高版本）检测到 SDK 脚本下载或初始化失败时，将创建并报告 "SDK 加载失败" 异常。 简单，最终用户的客户端（浏览器）无法下载 Application Insights SDK，或从标识的宿主页面初始化，因此不会报告任何遥测数据或事件。

![Azure 门户 browser 故障概述](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> 支持 fetch （） API 或 XMLHttpRequest 的所有主要浏览器都支持此例外。 这不包括 IE 8 和更低的内容，因此你不会从这些浏览器中报告此类异常（除非你的环境包含提取填充代码）。

![浏览器异常详细信息](./media/javascript-sdk-load-failure/exception.png)

堆栈详细信息包括与最终用户使用的 Url 有关的基本信息。

| 名称                      | 说明                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN &nbsp; 终结点&gt; | 用于下载 SDK 的 URL （和失败）。                                                      |
| &lt;帮助 &nbsp; 链接&gt;    | 链接到疑难解答文档（本页）的 URL。                                              |
| &lt;主机 &nbsp; URL&gt;     | 最终用户正在使用的页面的完整 URL。                                                    |
| &lt;终结点 &nbsp; URL&gt; | 用于报告异常的 URL，此值可能有助于确定是否已从公共 internet 或私有云访问宿主页。

发生此异常的最常见原因如下：

- 间歇性网络连接故障。
- Application Insights CDN 中断。
- SDK 在加载脚本后未能初始化。
- 已阻止 Application Insights JavaScript CDN。

[间歇性网络连接故障](#intermittent-network-connectivity-failure)是导致此异常的最常见原因，尤其是在移动漫游方案中，用户间歇地断开网络连接。

以下部分将介绍如何对此错误的每个潜在根本原因进行故障排除。

> [!NOTE]
> 几个故障排除步骤假定您的应用程序能够直接控制代码段 &lt; 脚本/ &gt; 标记，以及作为托管 HTML 页的一部分返回的配置。 否则，这些确定的步骤不适用于你的方案。

## <a name="intermittent-network-connectivity-failure"></a>间歇性网络连接故障

**如果用户遇到间歇性的网络连接故障，则可能的解决方案越少，并且通常会在短时间内自行解决。** 例如，如果用户重新加载站点（刷新页面），则文件将（最终）在本地下载并缓存，直到发布更新版本。

> [!NOTE]
> 如果此异常是永久性的，并且在多个用户之间发生（通过报告的此异常的快速和持续级别进行诊断），并减少了正常的客户端遥测，则间歇性网络连接问题_不太可能_是问题的真正原因，应继续使用其他已知可能的问题进行诊断。

在这种情况下，将 SDK 托管在你自己的 CDN 上不太可能会提供或减少出现此异常，因为你自己的 CDN 会受到相同问题的影响。

通过 NPM 包解决方案使用 SDK 时也是如此。 但是，当发生这种情况时，你的整个应用程序无法加载/初始化（而不_只_是遥测 SDK），因此在完全加载之前，这些应用程序很可能会刷新你的站点。

还可以尝试使用[NPM 包](#use-npm-packages-to-embed-the-application-insight-sdk)嵌入 Application Insights SDK。

为了最大限度地减少间歇性网络连接故障，我们已在所有 CDN 文件上实现了缓存控制标头，因此，一旦最终用户的浏览器下载了最新版本的 SDK，就不需要再次下载，浏览器将重新使用以前获得的副本（请参阅[缓存的工作](../../cdn/cdn-how-caching-works.md)原理）。 如果缓存检查失败或已有新版本，最终用户的浏览器将需要下载更新的版本。 因此，在发生新版本时，可能会在检查失败情况下看到 _"噪音"_ 的背景级别，或者在出现新版本（部署到 CDN）时显示临时峰值。
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN 中断

你可以通过尝试直接从浏览器访问 CDN 终结点（例如，从你的 https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) 最终用户可能来自你自己的开发计算机的其他位置来确定是否存在 APPLICATION INSIGHTS cdn 中断（假设你的组织尚未阻止此域）。

如果确认发生了中断，可以[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)，或尝试更改用于下载 SDK 的 URL。

### <a name="change-the-cdn-endpoint"></a>更改 CDN 终结点
  
当应用程序在每个生成的页中返回代码段及其配置时，可以更改代码段配置， `src` 以使用不同的 SDK URL。 通过使用此方法，你可以绕过 CDN 阻止的问题，因为不应阻止新的 URL。

当前 Application Insights JavaScript SDK CDN 终结点
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> 此 `https://js.monitor.azure.com/` 终结点是一个别名，使我们能够在大约5分钟内切换 CDN 提供程序，而无需更改任何配置。这使我们能够更快地修复检测到的 CDN 相关问题，因为 CDN 提供商有区域或全局问题，无需用户调整其设置。

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>SDK 在加载脚本后未能初始化

当 SDK 未能初始化时， &lt; 脚本/ &gt; 已成功从 CDN 下载，但在初始化过程中失败。 这可能是由于依赖项缺失或无效或某些形式的 JavaScript 异常。

要检查的第一件事是是否已成功下载 SDK，如果脚本未下载，则这种情况__不__是导致 sdk 加载失败的异常。

快速检查：使用支持开发人员工具（F12）的浏览器，在 "网络" 选项卡上验证 ```src``` 是否已下载代码段配置中定义的脚本，响应代码为200（成功）或304（未更改）。 你还可以使用 fiddler 之类的工具来查看网络流量。

以下各节包含不同的报告选项，它将建议创建支持票证或在 GitHub 上提出问题。

 基本报告规则：

- 如果此问题仅影响少量用户以及浏览器版本的特定或子集（检查报告的异常的详细信息），则可能是最终用户或环境唯一问题，这可能会要求你的应用程序提供其他 `polyfill` 实现。 为此，请[在 GitHub 上提供问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)。
- 如果此问题影响整个应用程序，并且你的所有用户都受到影响，则可能是发布相关的问题，因此，你应[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)。

### <a name="javascript-exceptions"></a>JavaScript 异常

首先，让我们使用支持开发人员工具（F12）的浏览器来检查 JavaScript 异常，并检查是否发生了异常。

如果 SDK 脚本中报告了异常（例如 ai.2.min.js），则这可能表示传入 SDK 的配置包含意外或缺少所需的配置，或已将错误发布部署到 CDN。

若要检查错误的配置，请更改传递到代码段中的配置（如果尚未这样做），以便仅将检测密钥作为字符串值包括。

> src： " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js "、<br />
> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"<br />
> }});<br />

如果使用此最小配置时，你仍会在 SDK 脚本中看到 JavaScript 异常，请[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)，因为这将需要回滚错误的生成，因为它可能是新部署的版本的问题。

如果异常消失，则问题可能是由类型不匹配或意外值导致的。 开始逐个添加配置选项并进行测试，直到再次出现异常。 然后，查看导致问题的项目的文档。 如果文档不明确或需要帮助，请[在 GitHub 上记录问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

如果配置以前已部署且工作正常，但刚刚开始报告此例外，则可能是新部署的版本存在问题，请检查它是否仅影响一小部分用户/浏览器，并[在 GitHub 上提交问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)，或者[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)。

### <a name="enable-console-debugging"></a>启用控制台调试

假设没有引发异常，下一步是通过向配置中添加设置来启用控制台调试 `loggingLevelConsole` ，这会将所有初始化错误和警告发送到浏览器控制台（通常通过开发人员工具（F12）提供）。 任何报告的错误都应一目了然，并且如果需要进一步的帮助[文件，请在 GitHub 上遇到问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"，<br />
> loggingLevelConsole：2<br />
> }});<br />

> [!NOTE]
> 在初始化期间，SDK 对已知的主要依赖项执行一些基本检查。 如果当前运行时未提供这些值，则它会将失败的警告消息报告给控制台，但前提 `loggingLevelConsole` 是大于零。

如果仍无法初始化，请尝试启用此 ```enableDebug``` 配置设置。 这将导致所有内部错误作为异常引发（这将导致遥测丢失）。 因为这是开发人员的唯一设置，所以它可能会干扰作为某些内部检查的一部分引发的异常，因此您需要检查每个异常以确定导致 SDK 失败的问题。 使用脚本的非缩小版本（请注意，它的扩展名为 ".js"，而不是 ".min.js"），否则例外将不可读。

> [!WARNING]
> 这是一项仅限开发人员的设置，不应在整个生产环境中启用，因为你会丢失遥测数据。

> src： " https://az416426.vo.msecnd.net/scripts/b/ai.2.js "、<br />
> cfg： {<br />
> instrumentationKey： "INSTRUMENTATION_KEY"，<br />
> enableDebug： true<br />
> }});<br />

如果这仍未提供任何见解，你应该[在 GitHub 上](https://github.com/Microsoft/ApplicationInsights-JS/issues)使用详细信息和示例站点（如果有）来处理问题。 包括浏览器版本、操作系统和 JS framework 详细信息，以帮助确定问题。

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>已阻止 Application Insights JavaScript CDN

当 Application Insights 的 JavaScript SDK CDN 终结点报告为不安全时，可能会阻止 CDN。 发生这种情况时，将导致终结点公开阻止，这些列表的使用者将开始阻止所有访问。

若要解决此问题，它要求 CDN 终结点的所有者与将终结点标记为不安全的块列表实体一起使用，以便可以从相关列表中删除该终结点。

检查是否已将 CDN 终结点识别为不安全。
- [Google 透明度报表](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

根据应用程序、防火墙或环境更新这些列表的本地副本的频率，最终用户或公司 IT 部门可能需要花费相当长的时间和/或需要手动干预，才能强制进行更新或显式允许 CDN 终结点解决问题。

如果 CDN 终结点被识别为不安全，请[创建支持票证](https://azure.microsoft.com/support/create-ticket/)，以确保尽快解决此问题。

为了*更*快地绕过此问题，你可以[更改 SDK CDN 终结点](#change-the-cdn-endpoint)。

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript CDN 被阻止（由浏览器阻止的最终用户; 已安装的阻止程序; 个人防火墙）

检查最终用户是否具有：

- 安装了浏览器插件（通常是某种形式的 ad、恶意软件或弹出窗口阻止程序）。
- 阻止（或不允许）在其浏览器或代理中 Application Insights CDN 终结点。
- 配置了一个防火墙规则，该规则导致 SDK 阻止 CDN 域（或无法解析 DNS 条目）。

如果他们已配置这些选项中的任何一个，则需要使用它们（或提供文档）来允许 CDN 终结点。

它们安装的插件可能使用的是公共阻止列表。 如果不是这种情况，则很可能是其他一些手动配置的解决方案，或者它使用的是专用域阻止列表。

#### <a name="add-exceptions-for-cdn-endpoints"></a>为 CDN 终结点添加例外

与最终用户联系或提供文档，告知他们应该允许通过将 Application Insights CDN 终结点中的脚本下载到浏览器的插件或防火墙规则例外列表中来下载这些脚本（具体取决于最终用户的环境）。

下面的示例演示如何将 Chrome 配置[为允许或阻止对网站的访问。](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN 被阻止（通过企业防火墙）

如果你的最终用户在公司网络上，则很可能是其防火墙解决方案，并且其 IT 部门实现了某种形式的 internet 筛选系统。 在这种情况下，您将需要与它们一起使用，以便为您的最终用户提供必要的规则。

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>为公司的 CDN 终结点添加例外

  这与为[最终用户添加例外](#add-exceptions-for-cdn-endpoints)类似，但你将需要与公司的 IT 部门合作，让他们通过在任何域块列表或允许列表服务中包括（或删除）来配置要下载的 Application Insights CDN 终结点。

  > [!WARNING]
  > 如果你的公司用户使用[私有云](https://azure.microsoft.com/overview/what-is-a-private-cloud/)，并且他们不能启用任何形式的异常来向其内部用户提供 CDN 终结点的公共访问权限，则你需要使用[Application Insights NPM 包](https://www.npmjs.com/package/applicationinsights)或在你自己的 CDN 上托管 Application Insights SDK。  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>阻止的 CDN 的其他疑难解答

> [!NOTE]
> 如果用户使用的是[私有云](https://azure.microsoft.com/overview/what-is-a-private-cloud/)，并且他们无权访问公共 internet，则需要在你自己的 CDN 上承载 SDK 或使用 NPM 包。

#### <a name="host-the-sdk-on-your-own-cdn"></a>在你自己的 CDN 上托管 SDK

 你可以从你自己的 CDN 终结点承载 Application Insights SDK，而不是最终用户从公共 CDN 下载 Application Insights SDK。 建议使用特定版本（ai. #. # .min.js），以便更轻松地识别所使用的版本。 还要定期将其更新到最新版本（ai.2.min.js），以便你可以利用任何 bug 修复和新功能。

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>使用 NPM 包嵌入 Application insights SDK

你可以使用[NPM 包](https://www.npmjs.com/package/applicationinsights)将 SDK 作为你自己的 JavaScript 文件的一部分包含在内，而不是使用代码片段和公共 CDN 终结点。 SDK 只会成为你自己的脚本中的其他包。

> [!NOTE]
> 建议在使用 NPM 包时，还应使用某种形式的[JavaScript 捆绑程序](https://www.bing.com/search?q=javascript+bundler)来帮助代码拆分和缩减。

与代码片段一样，你自己的脚本（带有或不使用 SDK NPM 包）可能会受到此处列出的相同阻止性问题的影响，因此，根据你的应用程序、用户和框架，你可能需要考虑实现与代码段中的逻辑类似的操作来检测和报告这些问题。


## <a name="next-steps"></a>后续步骤 
- [通过在 GitHub 上存档问题获取其他帮助](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [监视网页的使用情况](javascript.md)
