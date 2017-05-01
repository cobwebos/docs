---
title: "在 Azure 应用服务上添加内容交付网络 | Microsoft Docs"
description: "在 Azure 应用服务上添加内容交付网络，以便从边缘节点交付静态文件。"
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>在 Azure 应用服务上添加内容交付网络

在本教程中，你需要将内容交付网络 (CDN) 添加到 Azure 应用服务，以便公开边缘服务器上的静态内容。 你需要创建一个 CDN 配置文件，该文件集合了多达 10 个 CDN 终结点。

内容交付网络将静态 Web 内容缓存在按策略布置好的位置，以便提供最大的吞吐量，方便将内容交付给用户。 使用 CDN 来缓存网站资产的优点包括：

* 为最终用户提供更好的性能和用户体验，尤其是在使用的应用程序需要多次往返来加载内容时。
* 大幅度缩放以更好地处理瞬间的高负载，例如在产品发布活动开始时。
* 通过分发用户请求并通过边缘服务器来提供内容，可以减少发送到源的流量。

> [!TIP]
> 查看 [Azure CDN pop 位置](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)的最新列表。
>

## <a name="deploy-the-sample"></a>部署示例

若要完成本教程，需要将应用程序部署到 Web 应用中。 请按[静态 HTML 快速入门](app-service-web-get-started-html.md)中的说明操作，该入门文档是本教程的基础。

## <a name="step-1---login-to-azure-portal"></a>步骤 1 - 登录到 Azure 门户

首先，打开偏好的浏览器并导航到 Azure [门户](https://portal.azure.com)。

## <a name="step-2---create-a-cdn-profile"></a>步骤 2 - 创建 CDN 配置文件

在左侧导航栏中单击 `+ New` 按钮，然后单击“Web + 移动”。 在“Web + 移动”类别下选择“CDN”。

指定以下字段：

| 字段 | 示例值 | 说明 |
|---|---|---|
| Name | myCDNProfile | CDN 配置文件的名称。 |
| 位置 | 欧洲西部 | 这是 Azure 位置，CDN 配置文件信息也将存储在该位置。 它不会影响 CDN 终结点位置。 |
| 资源组 | myResourceGroup | 有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md#resource-groups) |
| 定价层 | 标准 Akamai | 请参阅 [CDN 概述](../cdn/cdn-overview.md#azure-cdn-features)，了解定价层之间的对比。 |

单击“创建” 。

在左侧导航栏中打开资源组中心，然后选择“myResourceGroup”。 在资源列表中，选择“myCDNProfile”。

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>步骤 3 - 创建 CDN 终结点

在搜索框旁边的命令中单击“+ 终结点”，启动“创建终结点”边栏选项卡。

指定以下字段：

| 字段 | 示例值 | 说明 |
|---|---|
| Name |  | 此名称可用于访问在域 `<endpointname>.azureedge.net` 中缓存的资源 |
| 源服务器类型 | Web 应用 | 选择源服务器类型后，系统会为你提供针对其余字段的上下文菜单。 选择自定义源服务器后，系统会为你提供一个针对源服务器主机名的文本字段。 |
| 源服务器主机名 | |  下拉列表将列出所有可用的源服务器，其源服务器类型是你所指定的。 如果选择“自定义源服务器”作为“源服务器类型”，则需键入自定义源服务器的域  |

单击 **“添加”**。

随后将创建终结点。创建内容交付网络终结点后，状态将更新为“正在运行”。

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>步骤 4 - 从 Azure CDN 提供

CDN 终结点处于“正在运行”状态以后，应可从 CDN 终结点访问内容。

考虑到我们已使用[静态 HTML 快速入门](app-service-web-get-started-html.md)作为本教程的基础，我们应该可以在 CDN 上使用下述文件夹：`css`、`img`、`js`。

Web 应用 URL `http://<app_name>.azurewebsites.net/img/` 和 CDN 终结点 URL `http://<endpointname>.azureedge.net/img/` 之间的内容路径是相同的，这意味着你可以直接使用 CDN 终结点域来替换任何要通过 CDN 提供的静态内容。

让我们在你偏好的 Web 浏览器中导航到以下 URL，以便从 CDN 终结点拉取第一幅图像：

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

静态内容在 CDN 上可用以后，即可更新应用程序，以便使用 CDN 终结点将内容交付给最终用户。

也许可以通过多个框架进行 CDN 回退，具体取决于生成站点时所使用的语言。 例如，ASP.NET 提供[绑定和缩减](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn)支持，也方便你使用 CDN 回退功能。

如果你的语言没有内置的 CDN 回退支持，或者没有相应的库，则可使用 JavaScript 框架（例如 [FallbackJS](http://fallback.io/)），该框架支持加载[脚本](https://github.com/dolox/fallback/tree/master/examples/loading-scripts)、[样式表](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets)和[映像](https://github.com/dolox/fallback/tree/master/examples/loading-images)。

## <a name="step-5---purge-the-cdn"></a>步骤 5 - 清除 CDN

有时候，如果要在生存时间 (TTL) 过期之前让内容过期，则可能必须强制清除 CDN。

可以从“CDN 配置文件”边栏选项卡或“CDN 终结点”边栏选项卡手动清除 Azure CDN。 如果从“配置文件”页选择清除，则必须选择要清除的具体终结点。

若要清除内容，请键入要清除的内容路径。 可以传递一个完整的文件路径来清除单个文件，也可以传递一个路径片段来清除和刷新特定文件夹中的内容。

提供要清除的所有内容路径以后，即可单击“清除”。

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>步骤 6 - 映射自定义域

将自定义域映射到 CDN 终结点可以为 Web 应用程序提供统一的域。

若要将自定义域映射到 CDN 终结点，请在域注册机构中创建 CNAME 记录

> [!NOTE]
> CNAME 记录是一种 DNS 功能，可将源域（如 `www.contosocdn.com` 或 `static.contosocdn.com`）映射到目标域。

在本示例中，我们将添加一个 `static.contosocdn.com` 源域，其指向的目标域为 CDN 终结点。

| 源域 | 目标域 |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

在 CDN 终结点概览边栏选项卡中，单击`+ Custom domain`按钮。

在“添加自定义域”边栏选项卡的对话框中输入自定义域（包括子域）。 例如，按格式 `static.contosocdn.com` 输入域名。

单击 **“添加”**。

## <a name="step-7---version-content"></a>步骤 7 - 版本内容

在 CDN 终结点左侧导航窗格的“设置”标题下选择“缓存”。

可以通过“缓存”边栏选项卡来配置 CDN 如何处理请求中的查询字符串。

> [!NOTE]
> 有关查询字符串缓存行为选项的说明，请阅读[使用查询字符串控制 Azure CDN 缓存行为](../cdn/cdn-query-string.md)主题。

对于查询字符串缓存行为，请从下拉列表中选择“缓存每个唯一的 URL”。

单击“保存”。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure CDN](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [在 Azure CDN 自定义域上启用 HTTPS](../cdn/cdn-custom-ssl.md)
* [通过在 Azure CDN 中压缩文件来提高性能](../cdn/cdn-improve-performance.md)
* [在 Azure CDN 终结点上预加载资产](../cdn/cdn-preload-endpoint.md)

