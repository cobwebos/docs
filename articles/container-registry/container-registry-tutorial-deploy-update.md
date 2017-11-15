---
title: "Azure 容器注册表教程 - 将已更新的映像推送到区域部署"
description: "将修改后的 Docker 映像推送到异地复制的 Azure 容器注册表，然后查看已自动部署到多个区域中运行的 Web 应用的更改。 由三个部分构成的教程系列的第三部分。"
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker, 容器, 注册表, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 05c5149ed6c8502c31539f31bfff046f98dc633d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>将已更新的映像推送到区域部署

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

在学习前一篇教程时[从 GitHub 克隆](container-registry-tutorial-prepare-registry.md#get-application-code)的应用程序源中找到 `AcrHelloworld/Views/Home/Index.cshtml` 文件，并在偏好的文本编辑器中将其打开。 在 `<img>` 行的上面添加以下行：

```html
<h1>MODIFIED</h1>
```

修改后的 `Index.cshtml` 应如下所示：

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<h1>MODIFIED</h1>
<img width="700" src="~/images/@ViewData["MAPIMAGE"]" />
<ul>
<li>Registry URL: @ViewData["REGISTRYURL"]</li>
<li>Registry IP: @ViewData["REGISTRYIP"]</li>
<li>HostEntry: @ViewData["HOSTENTRY"]</li>
<li>Region: @ViewData["REGION"]</li>
<li>Map: @ViewData["MAPIMAGE"]</li>
</ul>
```

## <a name="rebuild-the-image"></a>重新生成映像

更新 Web 应用程序后，请重新生成其容器映像。 如前所述，为标记使用完全限定的映像名称，包括登录服务器 URL：

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="run-the-container-locally"></a>在本地运行容器

在部署到 Azure 容器注册表之前，请在本地运行该映像以验证生成是否成功。

```bash
docker run -d -p 8080:80 <acrName>.azurecr.io/acr-helloworld:v1
```

在 Web 浏览器中导航到 http://localhost:8080，确认容器是否正常运行并显示了所做的修改。

![本地容器映像][local-container-01]

## <a name="push-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

现在，将更新的 *acr-helloworld* 容器映像推送到异地复制的注册表。 在这里，我们会执行一条 `docker push` 命令将更新的映像同时部署到“美国西部”和“美国东部”区域的注册表副本。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

输出应如下所示：

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
c003ed6fc8b8: Pushed
02b11afef3fd: Layer already exists
cf17b6f921be: Layer already exists
c93ae914d31e: Layer already exists
2eea44510cee: Layer already exists
670f809bd6d5: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:e44c0956a21c91e1f5f7bc83f23f1de710c798246df1e0e508c0c88025449646 size: 1792
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

我们已使用一条 `docker push` 命令更新了两个区域性 Web 应用部署，并且 Azure 容器注册表已从靠近网络的存储库中提供了容器映像。

## <a name="next-steps"></a>后续步骤

在本教程中，我们已更新一个新版 Web 应用程序容器并将其推送到了异地复制的注册表。 Azure 容器注册表中的 Webhook 告知用于容器的 Web 应用已发生此项更新，更新触发了从注册表副本进行的本地提取操作。

在本教程（教程系列的最后一部分）中执行的操作包括：

> [!div class="checklist"]
> * 更新了 Web 应用程序 HTML
> * 生成并标记了 Docker 映像
> * 已将更改推送到 Azure 容器注册表
> * 已查看两个不同区域中的已更新应用

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png