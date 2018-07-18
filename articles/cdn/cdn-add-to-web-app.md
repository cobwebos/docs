---
title: 教程 - 向 Azure 应用服务 Web 应用添加Azure CDN | Microsoft Docs
description: 本教程演示如何向 Azure 应用服务 Web 应用添加 Azure 内容分发网络 (CDN)，以便在全球靠近客户的服务器缓存和分发静态文件。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: efd8e93f32020d1ef3695e7fc6b9907374275848
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608383"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>教程：向 Azure 应用服务 Web 应用添加 Azure CDN

本教程介绍如何向 [Azure 应用服务中的 Web 应用](../app-service/app-service-web-overview.md)添加 [Azure 内容分发网络 (CDN)](cdn-overview.md)。 Web 应用是用于托管 Web 应用程序、REST API 和移动后端的服务。 

下面是要使用的示例性静态 HTML 站点的主页：

![示例应用主页](media/cdn-add-to-web-app/sample-app-home-page.png)

学习内容：

> [!div class="checklist"]
> * 创建 CDN 终结点。
> * 刷新缓存资产。
> * 使用查询字符串控制缓存的版本。


## <a name="prerequisites"></a>先决条件

完成本教程：

- [安装 Git](https://git-scm.com/)
- [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>创建 Web 应用

若要创建将使用的 Web 应用，通过“浏览到应用”步骤，按照[静态 HTML 快速入门](../app-service/app-service-web-get-started-html.md)的说明操作。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

打开浏览器并导航到 [Azure 门户](https://portal.azure.com)。

### <a name="dynamic-site-acceleration-optimization"></a>动态站点加速优化
如果想要针对动态站点加速 (DSA) 优化 CDN 终结点，则应使用 [CDN 门户](cdn-create-new-endpoint.md)创建配置文件和终结点。 通过 [DSA 优化](cdn-dynamic-site-acceleration.md)，显著改进了包含动态内容的网页性能。 有关如何从 CDN 门户针对 DSA 优化 CDN 终结点的说明，请参阅[用于加速传送动态文件的 CDN 终结点配置](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files)。 否则，如果不想优化新的终结点，则可以按照下一节中的步骤使用 Web 应用门户来创建它。 请注意，对于“来自 Verizon 的 Azure CDN”配置文件，无法在创建 CDN 终结点后更改其优化设置。

## <a name="create-a-cdn-profile-and-endpoint"></a>创建 CDN 配置文件和终结点

在左侧导航窗格中，选择“应用服务”，并选择在[静态 HTML 快速入门](../app-service/app-service-web-get-started-html.md)中创建的应用。

![在门户中选择应用服务应用](media/cdn-add-to-web-app/portal-select-app-services.png)

在“应用服务”页的“设置”部分，选择“网络”>“为应用配置 Azure CDN”。

![在门户中选择 CDN](media/cdn-add-to-web-app/portal-select-cdn.png)

在“Azure 内容分发网络”页中，按表中的指定提供“新建终结点”设置。

![在门户中创建配置文件和终结点](media/cdn-add-to-web-app/portal-new-endpoint.png)

| 设置 | 建议的值 | 说明 |
| ------- | --------------- | ----------- |
| **CDN 配置文件** | myCDNProfile | CDN 配置文件是具有同一定价层的 CDN 终结点的集合。 |
| **定价层** | 标准 Akamai | [定价层](cdn-features.md)指定提供商和可用功能。 本教程使用标准 Akamai。 |
| **CDN 终结点名称** | azureedge.net 域中的任何唯一名称 | 可在 *&lt;endpointname&gt;*.azureedge.net 域中访问缓存的资源。

选择“创建”创建 CDN 配置文件。

Azure 将创建配置文件和终结点。 新的终结点显示在“终结点”列表中，预配后其状态为“正在运行”。

![列表中的新建终结点](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>测试 CDN 终结点

 终结点不会立即可供使用，因为注册传播需花时间： 
   - 对于 **Microsoft 推出的 Azure CDN 标准版**配置文件，传播通常可在 10 分钟内完成。 
   - 对于 **Akamai 的 Azure CDN 标准版**配置文件，传播通常可在一分钟内完成。 
   - 对于 Verizon 的 Azure CDN 标准版和 Verizon 的 Azure CDN 高级版配置文件，传播通常可在 90 分钟内完成。 

示例应用有一个 index.html 文件，此外还有 css、img 和 js 文件夹，其中包含其他静态资产。 在 CDN 终结点上，所有这些文件的内容路径是相同的。 例如，以下两个 URL 都可以访问 css 文件夹中的 bootstrap.css 文件：

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

![从 CDN 提供的示例应用主页](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 将看到先前在 Azure web 应用中运行的同一页面。 Azure CDN 已检索源 Web 应用的资产，并且是从 CDN 终结点提供这些资产

若要确保此页缓存在 CDN 中，请刷新页面。 有时候，需要通过两个请求来请求同一资产才能让 CDN 缓存请求的内容。

有关创建 Azure CDN 配置文件和终结点的详细信息，请参阅 [Azure CDN 入门](cdn-create-new-endpoint.md)。

## <a name="purge-the-cdn"></a>清除 CDN

CDN 定期根据生存时间 (TTL) 配置刷新其在源 Web 应用中的资源。 默认 TTL 为 7 天。

有时候，可能需要在 TTL 过期之前刷新 CDN；例如，在将更新的内容部署到 Web 应用时。 若要触发刷新，可以手动清除 CDN 资源。 

在本教程的此部分，请将更改部署到 Web 应用并清除 CDN，以便触发 CDN 刷新其缓存。

### <a name="deploy-a-change-to-the-web-app"></a>将更改部署到 Web 应用

打开 index.html 文件，将“- V2”添加到 H1 标题，如以下示例所示： 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

提交所做的更改并将其部署到 Web 应用。

```bash
git commit -am "version 2"
git push azure master
```

完成部署后，浏览到 Web 应用 URL 并查看更改。

```
http://<appname>.azurewebsites.net/index.html
```

![Web 应用标题中包含 V2](media/cdn-add-to-web-app/v2-in-web-app-title.png)

浏览到主页的 CDN 终结点 URL 时，看不到所做的更改，因为 CDN 中的缓存版本尚未过期。 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN 标题中无 V2](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>在门户中清除 CDN

若要触发 CDN 更新其缓存的版本，请清除该 CDN。

在门户的左侧导航窗格中选择“资源组”，并选择为 Web 应用创建的资源组 (myResourceGroup)。

![选择资源组](media/cdn-add-to-web-app/portal-select-group.png)

在资源列表中，选择 CDN 终结点。

![选择终结点](media/cdn-add-to-web-app/portal-select-endpoint.png)

在“终结点”页顶部，选择“清除”。

![选择“清除”](media/cdn-add-to-web-app/portal-select-purge.png)

输入要清除的内容路径。 可以传递一个完整的文件路径来清除单个文件，也可以传递一个路径片段来清除和刷新文件夹中的所有内容。 由于更改了 index.html，因此请确保它位于其中一个路径中。

在页面底部，选择“清除”。

![“清除”页](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>验证是否已更新 CDN

等待清除请求完成相关处理，这通常需要数分钟。 若要查看当前状态，请选择页面顶部的钟形图标。 

![清除通知](media/cdn-add-to-web-app/portal-purge-notification.png)

浏览到 index.html 的 CDN 终结点 URL 时，可以看到之前向主页标题添加的 V2，这表示 CDN 缓存已刷新。

```
http://<endpointname>.azureedge.net/index.html
```

![CDN 标题中包含 V2](media/cdn-add-to-web-app/v2-in-cdn-title.png)

有关详细信息，请参阅[清除 Azure CDN 终结点](../cdn/cdn-purge-endpoint.md)。 

## <a name="use-query-strings-to-version-content"></a>使用版本内容的查询字符串

Azure CDN 提供以下缓存行为选项：

* 忽略查询字符串
* 绕过查询字符串的缓存
* 缓存每个唯一的 URL 

第一个选项为默认值，这意味着，不管在 URL 中使用什么查询字符串，资产的缓存版本只有一个。 

在本教程的此部分，请将缓存行为更改为缓存每个唯一的 URL。

### <a name="change-the-cache-behavior"></a>更改缓存行为

在 Azure 门户的“CDN 终结点”页上，选择“缓存”。

从“查询字符串缓存行为”下拉列表中选择“缓存每个唯一的 URL”。

选择“保存”。

![选择查询字符串缓存行为](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>验证唯一 URL 是否已单独缓存

在浏览器中，导航到 CDN 终结点的主页并包含一个查询字符串： 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN 返回当前的 Web 应用内容，其在标题中包含“V2”。 

若要确保此页缓存在 CDN 中，请刷新页面。 

打开“index.html”，将“V2”更改为“V3”，然后部署该更改。 

```bash
git commit -am "version 3"
git push azure master
```

在浏览器中，转到包含新的查询字符串（例如 `q=2`）的 CDN 终结点 URL。 Azure CDN 获取当前的 index.html 文件并显示“V3”。 但是，如果使用 `q=1` 查询字符串导航到 CDN 终结点，则会显示“V2”。

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN 标题中包含 V3（查询字符串 2）](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN 标题中包含 V2（查询字符串 1）](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

此输出显示每个查询字符串的处理方式不同：

* 之前使用的是 q = 1 ，因此将返回缓存内容 (V2)。
* 最近使用的是 q = 2，因此将检索最新的 Web 应用内容并将其返回 (V3)。

有关详细信息，请参阅[使用查询字符串控制 Azure CDN 缓存行为](../cdn/cdn-query-string.md)。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 创建 CDN 终结点。
> * 刷新缓存资产。
> * 使用查询字符串控制缓存的版本。

阅读以下文章，了解如何优化 CDN 性能：

> [!div class="nextstepaction"]
> [教程：将自定义域添加到 Azure CDN 终结点](cdn-map-content-to-custom-domain.md)


