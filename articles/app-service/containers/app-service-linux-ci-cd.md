---
title: "使用用于容器的 Web 应用从 Docker 容器注册表进行持续部署 - Azure | Microsoft Docs"
description: "如何在用于容器的 Web 应用中设置从 Docker 容器注册表进行的持续部署。"
keywords: "azure 应用服务, linux, docker, acr,oss"
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: cccbd4952c66d3d8140e2a03e3b76afaa5ba3fbf
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>使用用于容器的 Web 应用进行持续部署

在本教程中，通过托管 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)存储库或 [Docker 中心](https://hub.docker.com)为自定义容器映像配置持续部署。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>启用容器持续部署功能

你可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 并执行以下命令来启用连续部署功能

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

在 [Azure 门户](https://portal.azure.com/)中，单击页面左侧的“应用服务”选项。

单击要为其配置 Docker 中心持续部署的应用的名称。

在“应用设置”中，添加名为 `DOCKER_ENABLE_CI`、值为 `true` 的应用设置。

![插入应用设置的图像](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>准备 Webhook URL

你可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 并执行以下命令来获取 Webhook URL

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
```

对于 Webhook URL，需要使用以下终结点：`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

可以通过使用 Azure 门户下载 Web 应用发布配置文件来获取 `publishingusername` 和 `publishingpwd`。

![插入“添加 webhook 2”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>添加 Webhook

### <a name="azure-container-registry"></a>Azure 容器注册表

在注册表门户边栏选项卡中单击“Webhook”，然后单击“添加”创建新的 Webhook。 在“创建 Webhook”边栏选项卡中，为 Webhook 指定名称。 对于 Webhook URI，需要提供从步骤 3 中获得的 URL

请确保将范围定义为包含容器映像的存储库。

![插入 Webhook 的映像](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

更新映像时，会自动使用新映像更新 Web 应用。

### <a name="docker-hub"></a>Docker 中心

在“Docker 中心”页中，单击“Webhook”，并单击“创建 WEBHOOK”。

![插入“添加 webhook 1”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

对于 Webhook URL，需要提供从步骤 3 获得的 URL

![插入“添加 webhook 2”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

更新映像时，会自动使用新映像更新 Web 应用。

## <a name="next-steps"></a>后续步骤

* [什么是 Linux 上的 Azure 应用服务？](./app-service-linux-intro.md)
* [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 上的 Azure 应用服务中使用 .NET Core](quickstart-dotnetcore.md)
* [在 Linux 上的 Azure 应用服务中使用 Ruby](quickstart-ruby.md)
* [如何对用于容器的 Web 应用使用自定义 Docker 映像](quickstart-custom-docker-image.md)
* [用于容器的 Azure 应用服务 Web 应用常见问题解答](./app-service-linux-faq.md)
* [使用 Azure CLI 2.0 管理用于容器的 Web 应用](./app-service-linux-cli.md)