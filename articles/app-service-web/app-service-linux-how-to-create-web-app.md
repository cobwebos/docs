---
title: "创建在 Linux 上运行的 Azure Web 应用 | Microsoft Docs"
description: "Linux 上 Azure Web 应用的 Web 应用创建工作流。"
keywords: "azure 应用服务, web 应用, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: f4bf4138ce3a9a2495a0a0fd46e149f6ac96d82d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>创建在 Linux 上运行的 Azure Web 应用

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


## <a name="use-the-azure-portal-to-create-your-web-app"></a>使用 Azure 门户创建 Web 应用
可以开始通过 [Azure 门户](https://portal.azure.com)创建 Linux Web 应用，如下图所示：

![开始在 Azure 门户上创建 Web 应用][1]

接着，会打开“创建边栏选项卡”，如下图所示：

![“创建”边栏选项卡][2]

1. 为 Web 应用提供一个名称。
2. 选择现有资源组，或创建一个新的资源组。 （请在[限制部分](app-service-linux-intro.md)中查看可用区域。）
3. 选择现有的 Azure 应用服务计划或创建新的计划。 （请在[限制部分](app-service-linux-intro.md)中查看应用服务计划说明。）
4. 选择要使用的应用程序堆栈。 可以在 Node.js、PHP、.Net Core 和 Ruby 的几个版本之间进行选择。

创建应用后，可以从应用程序设置中更改应用程序堆栈，如下图所示：

![应用程序设置][3]

## <a name="deploy-your-web-app"></a>部署 Web 应用
从管理门户选择“部署选项”后，即可使用本地 Git 或 GitHub 存储库部署应用程序。 说明的其余部分类似于非 Linux Web 应用。 可以按照[本地 Git 部署](app-service-deploy-local-git.md)或[连续部署](app-service-continuous-deployment.md)中的说明部署应用。

也可使用 FTP 将应用程序上载到站点。 可以从诊断日志部分获取 Web 应用的 FTP 终结点，如下图所示：

![诊断日志][4]

## <a name="next-steps"></a>后续步骤
* [什么是 Linux 上的 Azure Web 应用？](app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure App Service Web 应用中使用 Ruby](app-service-linux-ruby-get-started.md)
* [Linux 上的 Azure App Service Web 应用常见问题解答](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

