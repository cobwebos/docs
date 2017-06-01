---
title: "在 Azure 中不到 5 分钟创建一个静态 HTML Web 应用 | Microsoft 文档"
description: "了解如何通过部署一个示例应用，轻松地在应用服务中运行 Web 应用。"
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>在 Azure 中不到 5 分钟创建一个静态 HTML Web 应用

本快速入门教程将指导你完成如何将基本 HTML+CSS 站点部署到 Azure。 你将使用 [Azure 应用服务计划](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)运行该应用，然后使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) 在其中创建一个 Web 应用。 使用 Git 将应用部署到 Azure。 安装先决条件后，需要大约五分钟可完成本教程。

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>先决条件

创建此示例之前，请下载并安装以下组件：

- [Git](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

在终端窗口中，将示例应用存储库克隆到本地计算机：

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>查看 HTML

导航到包含示例 HTML 的目录。 在浏览器中打开 *index.html* 文件。

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

在 `quickStartPlan` 应用服务计划中创建一个 [Web 应用](app-service-web-overview.md)。 该 Web 应用为代码提供托管空间，并提供一个 URL 用于查看已部署的应用。

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

该页作为 Azure 应用服务 Web 应用运行：

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>更新并重新部署应用

打开 *index.html* 文件。 对标记进行更改。 例如，将 `Hello world!` 更改为 `Hello Azure!`

提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated HTML"
git push azure master
```

完成部署后，刷新浏览器以查看所做的更改。

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

- 浏览示例 [Web 应用 CLI 脚本](app-service-cli-samples.md)。
- 了解如何[映射自定义域名](app-service-web-tutorial-custom-domain.md)（如 contoso.com）到[应用服务应用](app-service-web-tutorial-custom-domain.md)。
