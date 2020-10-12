---
title: 排查 JavaScript Web 应用程序的 SDK 加载失败问题 - Azure Application Insights
description: 如何排查 JavaScript Web 应用程序的 SDK 加载失败问题
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 30c7caef4143b1a7cdba959971ff7689f986cb9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333250"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>排查 JavaScript Web 应用的 SDK 加载失败问题

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

当 JavaScript 代码片段（v3 或更高版本）检测到 SDK 脚本下载或初始化失败时，会创建并报告 SDK 加载失败异常。 简单说来，最终用户的客户端（浏览器）无法下载 Application Insights SDK 或无法从所标识的托管页初始化，因此不会报告任何遥测数据或事件。

![Azure 门户浏览器失败概述](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> 支持 fetch() API 或 XMLHttpRequest 的所有主要浏览器都支持此异常。 这不包括 IE 8 及更低版本，因此不会从这些浏览器报告此类型的异常（除非你的环境包含 fetch polyfill）。

![浏览器异常详细信息](./media/javascript-sdk-load-failure/exception.png)

堆栈详细信息包括与最终用户使用的 URL 有关的基本信息。

| 名称                      | 说明                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN&nbsp;终结点&gt; | 用于下载 SDK（但下载失败）的 URL。                                                      |
| &lt;帮助&nbsp;链接&gt;    | 链接到故障排除文档（本页）的 URL。                                              |
| &lt;主机&nbsp;URL&gt;     | 最终用户使用的页面的完整 URL。                                                    |
| &lt;终结点&nbsp;URL&gt; | 用于报告异常的 URL。此值可能有助于确定是从公共 Internet 还是从私有云访问了托管页。

发生此异常的最常见原因如下：

- 间歇性网络连接故障。
- Application Insights CDN 中断。
- SDK 在加载脚本后未能初始化。
- Application Insights JavaScript CDN 已被阻止。

[间歇性网络连接故障](#intermittent-network-connectivity-failure)是导致此异常的最常见原因，尤其是在移动漫游场景中，用户会间歇地丢失网络连接。

以下各部分将介绍如何对此错误的每个潜在根本原因进行故障排除。

> [!NOTE]
> 几个故障排除步骤假定你的应用程序能够直接控制作为 HTML 托管页的一部分返回的代码片段 &lt;script /&gt; 标记及其配置。 否则，所列出的步骤不适用于你的方案。

## <a name="intermittent-network-connectivity-failure"></a>间歇性网络连接失败

**如果用户遇到间歇性网络连接失败，则可能的解决方案较少，它们通常可以在短时间内自行解决。** 例如，如果用户重新加载你的站点（刷新页面），则文件（最终）会下载并缓存到本地，直到有更新的版本发布。

> [!NOTE]
> 如果此异常持续存在，并且许多用户遇到过（根据报告此异常的用户数快速持续增加判断得出）且正常的客户端遥测数据减少，则间歇性网络连接问题不太可能是问题的真正原因，应继续诊断是否存在其他已知的可能问题。

在这种情况下，将 SDK 托管在你自己的 CDN 上不太可能会增加或减少此异常的数目，因为你自己的 CDN 会受相同问题的影响。

通过 NPM 包解决方案使用 SDK 时也是如此。 但从最终用户的角度来看，发生这种情况时，整个应用程序（而不仅仅是他们在视觉上看不到的遥测 SDK）都无法加载/初始化，因此他们很可能会刷新你的站点，直至加载完毕。

你还可以尝试使用 [NPM 包](#use-npm-packages-to-embed-the-application-insight-sdk)来嵌入 Application Insights SDK。

为了最大限度地减少间歇性网络连接失败，我们在所有 CDN 文件上实现了 Cache-Control 标头。因此，一旦最终用户的浏览器下载了最新版本的 SDK，就不需再次下载，浏览器会重新使用以前获得的副本（请参阅[缓存工作原理](../../cdn/cdn-how-caching-works.md)）。 如果缓存检查失败或存在新版本，则最终用户的浏览器将需要下载更新的版本。 因此，你在检查失败的情况下可能会看到背景级别的“噪音”，在新版本正式发布（部署到 CDN）的情况下可能会看到临时峰值。
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN 中断

若要确认是否存在 Application Insights CDN 中断，可以尝试直接从与最终用户的位置不同的位置（可能是你自己的开发计算机，假定你的组织尚未阻止该域）通过浏览器来访问 CDN 终结点（例如 https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) ）。

如果确认发生了中断，可以[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)，或尝试更改用于下载 SDK 的 URL。

### <a name="change-the-cdn-endpoint"></a>更改 CDN 终结点
  
由于应用程序在每个生成的页面中都会返回该代码片段及其配置，因此你可以更改代码片段 `src` 的配置，为 SDK 使用另一 URL。 使用此方法可以绕过 CDN 被阻止的问题，因为新 URL 应该不会被阻止。

当前的 Application Insights JavaScript SDK CDN 终结点
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/` 终结点是一个别名，它使我们能够在大约 5 分钟内切换 CDN 提供商，无需更改任何配置。这是为了在 CDN 提供商出现区域性或全局性问题的情况下，使我们能够更快地修复检测到的 CDN 相关问题，而无需每个用户调整其设置。

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>SDK 在加载脚本后未能初始化

SDK 未能初始化是指 &lt;script /&gt; 已成功从 CDN 下载，但在初始化过程中失败。 这可能是由于依赖项缺失或无效，或存在某种形式的 JavaScript 异常。

要检查的第一件事是 SDK 是否已成功下载，如果脚本未下载，则此情况不是导致“SDK 加载失败”异常的原因。

快速检查：使用一个支持开发人员工具 (F12) 的浏览器，在网络选项卡上验证 ```src``` 代码片段配置中定义的脚本是否已下载且响应代码为“200 (成功)”或“304 (未更改)”。 还可以使用 Fiddler 之类的工具来查看网络流量。

以下各部分包括不同的报告选项，它们会建议你创建支持票证或在 GitHub 上提出问题。

 基本报告规则：

- 如果此问题仅影响少量用户以及特定或部分浏览器版本（请检查报告的异常的相关详细信息），则它可能是一个仅限最终用户或环境的问题，因此，应用程序可能需要提供其他的 `polyfill` 实现。 为此，请[在 GitHub 上提出问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)。
- 如果此问题影响整个应用程序，并且你的所有用户都受到影响，则可能是与版本相关的问题，因此应[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)。

### <a name="javascript-exceptions"></a>JavaScript 异常

首先，让我们使用支持开发人员工具 (F12) 的浏览器来加载页面并查看是否发生了任何异常，通过这种方式检查是否存在 JavaScript 异常。

如果 SDK 脚本（例如 ai.2.min.js）中报告了异常，则可能表示传入 SDK 的配置包含意外的配置或缺少所需的配置，或已将错误的版本部署到 CDN。

若要查找错误的配置，请更改传递到代码片段中的配置（如果尚未这样做），使之仅包含字符串值形式的检测密钥。

> src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ",<br />
> cfg:{<br />
> instrumentationKey:"INSTRUMENTATION_KEY"<br />
> }});<br />

如果在使用此最小配置后仍然在 SDK 脚本中看到 JavaScript 异常，则请[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)，因为这将需要回滚有错误的生成（因为可能是新部署的版本存在问题）。

如果异常消失，则问题可能是由类型不匹配或意外值导致的。 开始逐个将配置选项添加回来并进行测试，直到再次出现异常。 然后，查看导致问题的项的文档。 如果文档不明确或者你需要帮助，请[在 GitHub 上提出问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

如果配置之前已部署且工作正常，但刚刚开始报告此异常，则可能是新部署的版本存在问题，请检查它是否只影响一小部分用户/浏览器，然后[在 GitHub 上提出问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)或[创建新的支持票证](https://azure.microsoft.com/support/create-ticket/)。

### <a name="enable-console-debugging"></a>启用控制台调试

假设没有引发异常，则下一步是通过将 `loggingLevelConsole` 设置添加到配置来启用控制台调试，这会将所有初始化错误和警告发送到浏览器控制台（通常通过开发人员工具 (F12) 提供）。 所报告的任何错误应当都一目了然。如果需要进一步的帮助，请[在 GitHub 上提出问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)。

> cfg:{<br />
> instrumentationKey:"INSTRUMENTATION_KEY",<br />
> loggingLevelConsole:2<br />
> }});<br />

> [!NOTE]
> 在初始化期间，SDK 会对已知的主要依赖项执行一些基本检查。 如果当前运行时未提供这些值，则它会将失败作为警告消息报告到控制台，但前提是 `loggingLevelConsole` 大于零。

如果仍无法初始化，请尝试启用 ```enableDebug``` 配置设置。 这会导致所有内部错误作为异常引发（这会导致遥测数据丢失）。 因为这是仅限开发人员使用的设置，所以在某些内部检查过程中引发异常时，它可能会存在干扰因素。因此，你需要查看每个异常以确定导致 SDK 失败的具体问题。 请使用该脚本的非缩小版本（请注意下面的扩展名，是“.js” 而不是“.min.js”），否则异常将无法读取。

> [!WARNING]
> 这是一个仅限开发人员使用的设置，不应在完整的生产环境中启用，因为你会丢失遥测数据。

> src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.js ",<br />
> cfg:{<br />
> instrumentationKey:"INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

如果这样仍未提供任何见解，则应[在 GitHub 上提出问题](https://github.com/Microsoft/ApplicationInsights-JS/issues)，并提供详细信息和示例站点（如果有）。 请提供浏览器版本、操作系统和 JS 框架详细信息，以帮助确定问题。

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Application Insights JavaScript CDN 已被阻止

当 Application Insights JavaScript SDK CDN 终结点被报告和/或标识为不安全时，此 CDN 可能会被阻止。 发生这种情况时，会导致终结点被公开加入阻止列表中，这些列表的使用者将开始阻止所有访问。

若要解决此问题，CDN 终结点的所有者必须与将终结点标记为不安全的阻止列表实体联系，让其从相关列表中删除该终结点。

请检查 CDN 终结点是否已被标识为不安全。
- [Google 透明度报告](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

取决于应用程序、防火墙或环境更新这些列表的本地副本的频率，最终用户或公司 IT 部门可能需要花费相当长的时间并/或需要手动干预，以便强制进行更新或显式允许 CDN 终结点，这样才能解决问题。

如果 CDN 终结点被标识为不安全，请[创建支持票证](https://azure.microsoft.com/support/create-ticket/)，以确保尽快解决此问题。

在可能情况下，若要更快地绕过此问题，可以[更改 SDK CDN 终结点](#change-the-cdn-endpoint)。

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript CDN 被阻止（被最终用户阻止，即被浏览器、已安装的阻止程序、个人防火墙阻止）

检查最终用户是否存在以下情况：

- 安装了浏览器插件（通常是某种形式的广告、恶意软件或弹出窗口阻止程序）。
- 在其浏览器或代理中阻止了（或不允许）Application Insights CDN 终结点。
- 所配置的某个防火墙规则导致 SDK 的 CDN 域被阻止（或 DNS 条目无法解析）。

如果他们已配置这些选项中的任何一个，你需要与他们联系（或提供文档），让他们允许 CDN 终结点。

他们安装的插件可能在使用公共阻止列表。 如果不是这样，则使用的可能是某个其他手动配置的解决方案，或者使用的是专用域阻止列表。

#### <a name="add-exceptions-for-cdn-endpoints"></a>为 CDN 终结点添加例外

与最终用户联系或提供文档，告知他们应该将 Application Insights CDN 终结点中的脚本包括到浏览器插件或防火墙规则的例外列表中（具体取决于最终用户的环境），以便允许下载这些脚本。

下面是一个示例，说明了如何配置 [Chrome 以允许或阻止对网站的访问。](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN 被阻止（被企业防火墙阻止）

如果你的最终用户在公司网络上，则出现问题很可能因为其防火墙解决方案，并且其 IT 部门实现了某种形式的 Internet 筛选系统。 在这种情况下，你需要与他们联系，以便为你的最终用户启用必要的规则。

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>针对公司为 CDN 终结点添加例外

  这类似于[针对最终用户添加例外](#add-exceptions-for-cdn-endpoints)，但你需要与公司的 IT 部门合作，让他们通过在任何域阻止列表或允许列表服务中包括（或删除）Application Insights CDN 终结点来配置要下载的终结点。

  > [!WARNING]
  > 如果你的公司用户使用的是[私有云](https://azure.microsoft.com/overview/what-is-a-private-cloud/)，并且无法启用任何形式的例外来向其内部用户提供 CDN 终结点的公共访问权限，则你需要使用 [Application Insights NPM 包](https://www.npmjs.com/package/applicationinsights)或在你自己的 CDN 上托管 Application Insights SDK。  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>针对已阻止的 CDN 的其他故障排除方法

> [!NOTE]
> 如果你的用户使用的是[私有云](https://azure.microsoft.com/overview/what-is-a-private-cloud/)，并且无法访问公共 Internet，则你需要在自己的 CDN 上托管 SDK 或使用 NPM 包。

#### <a name="host-the-sdk-on-your-own-cdn"></a>在你自己的 CDN 上托管 SDK

 你可以通过自己的 CDN 终结点托管 Application Insights SDK，而不是让最终用户从公共 CDN 下载 Application Insights SDK。 建议你使用特定版本 (ai.2.#.#.min.js)，以便更轻松地标识你使用的版本。 另请定期将其更新到最新版本 (ai.2.min.js)，以便利用已发布的任何 bug 修复和新功能。

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>使用 NPM 包来嵌入 Application Insights SDK

可以使用 [NPM 包](https://www.npmjs.com/package/applicationinsights)将 SDK 包括为你自己的 JavaScript 文件的一部分，而不是使用代码片段和公共 CDN 终结点。 SDK 将仅成为你自己的脚本中的另一个包。

> [!NOTE]
> 建议在使用 NPM 包时，还应使用某种形式的 [JavaScript 捆绑程序](https://www.bing.com/search?q=javascript+bundler)来帮助进行代码拆分和缩减。

与代码片段一样，你自己的脚本（不管使用还是不使用 SDK NPM 包）也可能会受此处列出的相同阻止性问题的影响，因此，你可能需要根据应用程序、用户和框架来考虑实现与代码片段中的逻辑类似的东西来检测和报告这些问题。


## <a name="next-steps"></a>后续步骤 
- [通过在 GitHub 上提出问题获取更多帮助](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [监视网页的使用情况](javascript.md)
