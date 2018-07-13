---
title: Azure 容器注册表教程 - 将已更新的映像推送到区域部署
description: 将修改后的 Docker 映像推送到异地复制的 Azure 容器注册表，然后查看已自动部署到多个区域中运行的 Web 应用的更改。 由三个部分构成的教程系列的第三部分。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 8edb35b91327bde1fa824ec456b8a98962adb7ce
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634081"
---
# <a name="tutorial-push-an-updated-image-to-regional-deployments"></a>教程：将已更新的映像推送到区域部署

本文是由三个部分构成的教程系列的第三部分。 在[前一篇教程](container-registry-tutorial-deploy-app.md)中，已针对两个不同的区域性 Web 应用部署配置了异地复制。 在本教程中，我们首先修改应用程序，然后生成新的容器映像并将其推送到异地复制的注册表。 最后，查看 Azure 容器注册表 Webhook 在两个 Web 应用实例中自动部署的更改。

本教程（教程系列的最后一部分）的内容包括：

> [!div class="checklist"]
> * 修改 Web 应用程序 HTML
> * 生成并标记 Docker 映像
> * 将更改推送到 Azure 容器注册表
> * 查看两个不同区域中已更新的应用

如果尚未配置两个“用于容器的 Web 应用”区域部署，请返回到本教程系列的前一篇教程：[从 Azure 容器注册表部署 Web 应用](container-registry-tutorial-deploy-app.md)。

## <a name="modify-the-web-application"></a>修改 Web 应用程序

此步骤对 Web 应用程序进行更改。将更新的容器映像推送到 Azure 容器注册表之后，用户很容易发现这项更改。

在学习前一篇教程时[从 GitHub 克隆](container-registry-tutorial-prepare-registry.md#get-application-code)的应用程序源中找到 `AcrHelloworld/Views/Home/Index.cshtml` 文件，并在偏好的文本编辑器中将其打开。 在现有 `<h1>` 行下面添加以下行：

```html
<h1>MODIFIED</h1>
```

修改后的 `Index.cshtml` 应如下所示：

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>重新生成映像

更新 Web 应用程序后，请重新生成其容器映像。 如前所述，对标记使用完全限定的映像名称，包括登录服务器的完全限定域名 (FQDN)：

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

接下来，将更新的 *acr-helloworld* 容器映像推送到异地复制的注册表。 在这里，我们会执行一条 `docker push` 命令将更新的映像同时部署到“美国西部”和“美国东部”区域的注册表副本。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

`docker push` 输出应如下所示：

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>查看 Webhook 日志

复制映像时，可以看到 Azure 容器注册表 Webhook 正在触发。

若要查看在前一篇教程中将容器部署到“用于容器的 Web 应用”时创建的区域 Webhook，请在 Azure 门户中导航到容器注册表，选择“服务”下的“Webhook”。

![Azure 门户中的容器注册表 Webhook][tutorial-portal-01]

选择每个 Webhook 可查看其调用和响应的历史记录。 在两个 Webhook 的日志中，应会看到 **push** 操作有一个对应的行。 此处，位于“美国西部”区域的 Webhook 日志显示了由上一步骤中的 `docker push` 触发的 **push** 操作：

![Azure 门户中的容器注册表 Webhook 日志（美国西部）][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>查看已更新的 Web 应用

Webhook 通知 Web 应用，新的映像已推送到注册表，后者会自动将更新的容器自动部署到两个区域性 Web 应用。

在 Web 浏览器中导航到这两个区域性 Web 应用部署，验证应用程序是否已在这两个部署中更新。 请注意，可在每个“应用服务概述”选项卡的右上角找到已部署 Web 应用的 URL。

![Azure 门户中的“应用服务概述”][tutorial-portal-03]

若要查看已更新的应用程序，请在“应用服务概述”中选择相应的链接。 下面是在“美国西部”运行的应用的示例视图：

![在“美国西部”区域运行的已修改 Web 应用的浏览器视图][deployed-app-westus-modified]

在浏览器中查看已更新的容器映像，验证它是否同时已部署到“美国东部”部署。

![在“美国东部”区域运行的已修改 Web 应用的浏览器视图][deployed-app-eastus-modified]

使用单个 `docker push`，已自动更新这两个区域 Web 应用部署中运行的 Web 应用程序。 并且，Azure 容器注册表提供离每个部署最近的存储库中的容器映像。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已更新一个新版 Web 应用程序容器并将其推送到了异地复制的注册表。 Azure 容器注册表中的 Webhook 告知用于容器的 Web 应用已发生此项更新，更新触发了从最近的注册表副本进行的本地提取操作。

### <a name="acr-build-automated-image-build-and-patch"></a>ACR Build：自动映像生成和修补

除了异地复制外，ACR Build 是 Azure 容器注册表的另一项功能，可帮助优化容器部署管道。 从 ACR Build 概述着手，了解其功能：

[使用 ACR Build 自动执行 OS 和框架修补](container-registry-build-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png