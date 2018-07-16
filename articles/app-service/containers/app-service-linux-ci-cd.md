---
title: 使用用于容器的 Web 应用从 Docker 容器注册表进行持续部署 - Azure | Microsoft Docs
description: 如何在用于容器的 Web 应用中设置从 Docker 容器注册表进行的持续部署。
keywords: azure 应用服务, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130953"
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

记下 Webhook URI。 在下一个部分中将要用到它。
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

可以通过使用 Azure 门户下载 Web 应用发布配置文件来获取 `publishingusername` 和 `publishingpwd`。

![屏幕截图：添加 Webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>添加 Webhook

若要添加 Webhook，请按照以下指南中的步骤操作：

- 使用 Webhook URL 的 [Azure 容器注册表](../../container-registry/container-registry-webhook.md)
- [用于 Docker 中心的 Webhook](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>后续步骤

* [Linux 上的 Azure 应用服务简介](./app-service-linux-intro.md)
* [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 应用服务中创建 .NET Core Web 应用](quickstart-dotnetcore.md)
* [使用 Linux 应用服务创建 Ruby Web 应用](quickstart-ruby.md)
* [在用于容器的 Web 应用中部署 Docker/Go Web 应用](quickstart-docker-go.md)
* [Linux 上的 Azure 应用服务常见问题解答](./app-service-linux-faq.md)
* [使用 Azure CLI 管理用于容器的 Web 应用](./app-service-linux-cli.md)
