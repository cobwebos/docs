---
title: 将 CDN 添加到 Azure 应用服务 | Microsoft Docs
description: 向 Azure 应用服务添加内容交付网络 (CDN)，以便在全球靠近客户的服务器缓存和交付静态文件。
services: app-service\web
author: syntaxc4
ms.author: cfowler
ms.date: 05/31/2017
ms.topic: tutorial
ms.service: app-service-web
manager: erikre
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 131424b4a09beeca7c2e318a04e454bea7dab6a7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>教程：向 Azure 应用服务添加内容交付网络 (CDN)

[Azure 内容交付网络 (CDN)](../cdn/cdn-overview.md) 将静态 Web 内容缓存在按特定策略布置好的位置，以便提供最大的吞吐量，方便将内容交付给用户。 CDN 还会降低 Web 应用的服务器负载。 本教程介绍如何向 [Azure 应用服务中的 Web 应用](app-service-web-overview.md)添加 Azure CDN。 

下面是要使用的示例性静态 HTML 站点的主页：

![示例应用主页](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

学习内容：

> [!div class="checklist"]
> * 创建 CDN 终结点。
> * 刷新缓存资产。
> * 使用查询字符串控制缓存的版本。
> * 将自定义域用于 CDN 终结点。

## <a name="prerequisites"></a>先决条件

完成本教程：

- [安装 Git](https://git-scm.com/)
- [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>创建 Web 应用

若要创建将使用的 Web 应用，通过“浏览到应用”步骤，按照[静态 HTML 快速入门](app-service-web-get-started-html.md)的说明操作。

### <a name="have-a-custom-domain-ready"></a>准备自定义域

若要完成本教程的自定义域步骤，需要拥有自定义域并能够访问域提供商的 DNS 注册表。 例如，若要添加 `contoso.com` 和 `www.contoso.com` 的 DNS 条目，必须有权配置 `contoso.com` 根域的 DNS 设置。

如果还没有域名，请考虑遵照[应用服务域教程](custom-dns-web-site-buydomains-web-app.md)使用 Azure 门户购买一个域。 

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

打开浏览器并导航到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-cdn-profile-and-endpoint"></a>创建 CDN 配置文件和终结点

在左侧导航窗格中，选择“应用服务”，并选择在[静态 HTML 快速入门](app-service-web-get-started-html.md)中创建的应用。

![在门户中选择应用服务应用](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

在“应用服务”页的“设置”部分，选择“网络”>“为应用配置 Azure CDN”。

![在门户中选择 CDN](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

在“Azure 内容交付网络”页中，按表中的指定提供“新建终结点”设置。

![在门户中创建配置文件和终结点](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| 设置 | 建议的值 | 说明 |
| ------- | --------------- | ----------- |
| **CDN 配置文件** | myCDNProfile | 选择“新建”创建 CDN 配置文件。 CDN 配置文件是具有同一定价层的 CDN 终结点的集合。 |
| **定价层** | 标准 Akamai | [定价层](../cdn/cdn-overview.md#azure-cdn-features)指定提供商和可用功能。 在本教程中，将使用 Standard Akamai。 |
| **CDN 终结点名称** | azureedge.net 域中的任何唯一名称 | 可在 \<endpointname>.azureedge.net 域中访问缓存的资源。

选择**创建**。

Azure 将创建配置文件和终结点。 新的终结点显示在同一页的“终结点”列表中，在预配后其状态为“正在运行”。

![列表中的新建终结点](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>测试 CDN 终结点

如果选择了 Verizon 定价层，则进行终结点传播通常需要约 90 分钟。 对于 Akamai，传播需要数分钟

示例应用有一个 `index.html` 文件，此外还有 css、img 和 js 文件夹，其中包含其他静态资产。 在 CDN 终结点上，所有这些文件的内容路径是相同的。 例如，以下两个 URL 都可以访问 css 文件夹中的 bootstrap.css 文件：

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

在浏览器中导航到以下 URL：

```
http://<endpointname>.azureedge.net/index.html
```

![从 CDN 提供的示例应用主页](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 将看到先前在 Azure web 应用中运行的同一页面。 Azure CDN 已检索源 Web 应用的资产，并且是从 CDN 终结点提供这些资产

若要确保此页缓存在 CDN 中，请刷新页面。 有时候，需要通过两个请求来请求同一资产才能让 CDN 缓存请求的内容。

有关创建 Azure CDN 配置文件和终结点的详细信息，请参阅 [Azure CDN 入门](../cdn/cdn-create-new-endpoint.md)。

## <a name="purge-the-cdn"></a>清除 CDN

CDN 定期根据生存时间 (TTL) 配置刷新其在源 Web 应用中的资源。 默认 TTL 为 7 天。

有时候，可能需要在 TTL 过期之前刷新 CDN -- 例如，在将更新的内容部署到 Web 应用时。 若要触发刷新，可以手动清除 CDN 资源。 

在本教程的此部分，请将更改部署到 Web 应用并清除 CDN，以便触发 CDN 刷新其缓存。

### <a name="deploy-a-change-to-the-web-app"></a>将更改部署到 Web 应用

打开 `index.html` 文件，将“- V2”添加到 H1 标题，如以下示例所示： 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

提交所做的更改并将其部署到 Web 应用。

```bash
git commit -am "version 2"
git push azure master
```

完成部署后，浏览到 Web 应用 URL 并查看所做的更改。

```
http://<appname>.azurewebsites.net/index.html
```

![Web 应用标题中包含 V2](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

浏览到主页的 CDN 终结点 URL，此时看不到所做的更改，因为 CDN 中的缓存版本尚未过期。 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN 标题中无 V2](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>在门户中清除 CDN

若要触发 CDN 更新其缓存的版本，请清除该 CDN。

在门户的左侧导航窗格中选择“资源组”，并选择为 Web 应用创建的资源组 (myResourceGroup)。

![选择资源组](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

在资源列表中，选择 CDN 终结点。

![选择终结点](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

在“终结点”页顶部，单击“清除”。

![选择“清除”](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

输入要清除的内容路径。 可以传递一个完整的文件路径来清除单个文件，也可以传递一个路径片段来清除和刷新文件夹中的所有内容。 由于更改了 `index.html`，请确保这是其中的一个路径。

在页面底部，选择“清除”。

![“清除”页](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>验证是否已更新 CDN

等待清除请求完成相关处理，通常需数分钟。 若要查看当前状态，请选择页面顶部的钟形图标。 

![清除通知](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

浏览到 `index.html` 的 CDN 终结点 URL，此时会看到添加到主页标题的 V2。 这表明 CDN 缓存已刷新。

```
http://<endpointname>.azureedge.net/index.html
```

![CDN 标题中包含 V2](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

有关详细信息，请参阅[清除 Azure CDN 终结点](../cdn/cdn-purge-endpoint.md)。 

## <a name="use-query-strings-to-version-content"></a>使用版本内容的查询字符串

Azure CDN 提供以下缓存行为选项：

* 忽略查询字符串
* 绕过查询字符串的缓存
* 缓存每个唯一的 URL 

这些 URL中的第一个为默认值，这意味着，不管在 URL 中使用什么查询字符串，资产的缓存版本只有一个。 

在本教程的此部分，请将缓存行为更改为缓存每个唯一的 URL。

### <a name="change-the-cache-behavior"></a>更改缓存行为

在 Azure 门户的“CDN 终结点”页上，选择“缓存”。

从“查询字符串缓存行为”下拉列表中选择“缓存每个唯一的 URL”。

选择“保存”。

![选择查询字符串缓存行为](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>验证唯一 URL 是否已单独缓存

在浏览器中，导航到 CDN 终结点的主页，但请包括查询字符串： 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

CDN 返回当前的 Web 应用内容，其在标题中包含“V2”。 

若要确保此页缓存在 CDN 中，请刷新页面。 

打开 `index.html`，将“V2”更改为“V3”，并部署所做的更改。 

```bash
git commit -am "version 3"
git push azure master
```

在浏览器中，转到包含新的查询字符串（例如 `q=2`）的 CDN 终结点 URL。 CDN 获取当前的 `index.html` 文件并显示“V3”。  但如果使用 `q=1` 查询字符串导航到 CDN 终结点，则会显示“V2”。

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN 标题中包含 V3（查询字符串 2）](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN 标题中包含 V2（查询字符串 1）](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

此输出显示每个查询字符串的处理方式不同：

* 之前使用的是 q = 1 ，因此将返回缓存内容 (V2)。
* 最近使用的是 q = 2，因此将检索最新的 Web 应用内容并将其返回 (V3)。

有关详细信息，请参阅[使用查询字符串控制 Azure CDN 缓存行为](../cdn/cdn-query-string.md)。

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>将自定义域映射到 CDN 终结点

需要通过创建 CNAME 记录，将自定义域映射到 CDN 终结点。 CNAME 记录是一种 DNS 功能，用于将源域映射到目标域。 例如，可以将 `cdn.contoso.com` 或 `static.contoso.com` 映射到 `contoso.azureedge.net`。

如果没有自定义域，请考虑遵照[应用服务域教程](custom-dns-web-site-buydomains-web-app.md)使用 Azure 门户购买一个域。 

### <a name="find-the-hostname-to-use-with-the-cname"></a>找到与 CNAME 配合使用的主机名

在 Azure 门户的“终结点”页中，确保选中左侧导航窗格中的“概览”，并选择页面顶部的“+ 自定义域”按钮。

![选择“添加自定义域”](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

在“添加自定义域”页中，会看到创建 CNAME 记录时需要使用的终结点主机名。 主机名派生自 CDN 终结点 URL：**&lt;EndpointName>.azureedge.net**。 

![“添加域”页](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>通过域注册机构配置 CNAME

导航到域注册机构的网站，找到创建 DNS 记录的部分。 可能会在“**域名**”、“**DNS**”或“**名称服务器管理**”等部分中找到此页。

找到用于管理 CNAME 的部分。 可能需要转至高级设置页面，并找到“CNAME”、“别名”或“子域”字样。

创建一个 CNAME 记录，将所选子域（例如 static 或 cdn）映射到此前在门户中显示的“终结点主机名”。 

### <a name="enter-the-custom-domain-in-azure"></a>在 Azure 中输入自定义域

返回到“添加自定义域”页，在对话框中输入自定义域（包括子域）。 例如，输入 `cdn.contoso.com`。   
   
Azure 会验证所输入的域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。

将 CNAME 记录传播到 Internet 上的名称服务器需要一定的时间。 如果没有立即验证域，请等待几分钟，然后重试。

### <a name="test-the-custom-domain"></a>测试自定义域

在浏览器中，使用自定义域（例如 `cdn.contoso.com/index.html`）导航到 `index.html` 文件，验证其结果是否与直接访问 `<endpointname>azureedge.net/index.html` 相同。

![使用自定义域 URL 的示例应用主页](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

有关详细信息，请参阅[将 Azure CDN 内容映射到自定义域](../cdn/cdn-map-content-to-custom-domain.md)。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 创建 CDN 终结点。
> * 刷新缓存资产。
> * 使用查询字符串控制缓存的版本。
> * 将自定义域用于 CDN 终结点。

阅读以下文章，了解如何优化 CDN 性能：

> [!div class="nextstepaction"]
> [通过在 Azure CDN 中压缩文件来提高性能](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [在 Azure CDN 终结点上预加载资产](../cdn/cdn-preload-endpoint.md)
