---
title: 使用用于容器的 Web 应用从 Docker 容器注册表进行持续部署 - Azure | Microsoft Docs
description: 如何在用于容器的 Web 应用中设置从 Docker 容器注册表进行的持续部署。
keywords: azure 应用服务, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30168318"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>使用用于容器的 Web 应用进行持续部署

在本教程中，通过托管 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)存储库或 [Docker 中心](https://hub.docker.com)为自定义容器映像配置持续部署。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="enable-the-continuous-deployment-feature"></a>启用持续部署功能

可使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 并执行以下命令来启用连续部署功能：

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

在 [Azure 门户](https://portal.azure.com/)中，选择页面左侧的“应用服务”选项。

选择要为其配置 Docker 中心持续部署的应用的名称。

在“Docker 容器”页上，选择“启用”，然后选择“保存”以启用持续部署。

![应用设置的屏幕截图](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>准备 Webhook URL

可使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 并执行以下命令来获取 Webhook URL：

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

对于 Webhook URL，需要以下终结点：`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

可以通过使用 Azure 门户下载 Web 应用发布配置文件来获取 `publishingusername` 和 `publishingpwd`。

![屏幕截图：添加 Webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>添加 Webhook

### <a name="azure-container-registry"></a>Azure 容器注册表

1. 在注册门户页上，选择“Webhook”。
2. 若要创建一个新的 Webhook，请选择“添加”。 
3. 在“创建 Webhook”窗格中，为 Webhook 指定名称。 对于 Webhook URL，请提供前面部分中获取的 URL。

请确保将范围定义为包含容器映像的存储库。

![Webhook 的屏幕截图](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

更新映像时，会自动使用新映像更新 Web 应用。

### <a name="docker-hub"></a>Docker 中心

在“Docker 中心”页上，选择“Webhook”，并选择“创建 WEBHOOK”。

![屏幕截图：添加 Webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

对于 webhook URL，提供之前获取的 URL。

![屏幕截图：添加 Webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

更新映像时，会自动使用新映像更新 Web 应用。

## <a name="next-steps"></a>后续步骤

* [Linux 上的 Azure 应用服务简介](./app-service-linux-intro.md)
* [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 应用服务中创建 .NET Core Web 应用](quickstart-dotnetcore.md)
* [使用 Linux 应用服务创建 Ruby Web 应用](quickstart-ruby.md)
* [在用于容器的 Web 应用中部署 Docker/Go Web 应用](quickstart-docker-go.md)
* [Linux 上的 Azure 应用服务常见问题解答](./app-service-linux-faq.md)
* [使用 Azure CLI 管理用于容器的 Web 应用](./app-service-linux-cli.md)
