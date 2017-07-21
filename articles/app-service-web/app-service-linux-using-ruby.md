---
title: "在 Linux 上的 Azure 应用服务 Web 应用中使用 Ruby | Microsoft Docs"
description: "在 Linux 上的 Azure 应用服务 Web 应用中使用 Ruby。"
keywords: "azure 应用服务, web 应用, faq, linux, oss, ruby"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: ad724b99426c1873c173da905c89b471ee64ffa6
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017


---

# <a name="using-ruby-in-web-app-on-linux"></a>在 Linux Web 应用中使用 Ruby #

我们在后端的最新更新中引入了对 Ruby v.2.3 的支持。 通过设置 Linux Web 应用的配置，可更改应用程序堆栈。

## <a name="using-the-azure-portal"></a>使用 Azure 门户 ##

从 [Azure 门户](https://portal.azure.com)的新菜单中，可以从“Web + 移动”选项选择在 Linux 上创建 Web 应用，如下图所示：

![开始在 Azure 门户上创建 Web 应用][1]

接着，会打开“创建边栏选项卡”，如下图所示：

![“创建”边栏选项卡][2]

1. 为 Web 应用提供一个名称。
2. 选择现有资源组，或创建一个新的资源组。 （请在[限制部分](app-service-linux-intro.md)中查看可用区域。）
3. 选择现有的 Azure 应用服务计划或创建新的计划。 （请在[限制部分](app-service-linux-intro.md)中查看应用服务计划说明。）
4. 从内置运行时堆栈中选择 Ruby。

创建 Ruby Web 应用后，可以使用 Git 或 FTP 对其进行部署。

了解有关创建 Ruby 应用的详细信息，请查阅[入门指南](app-service-linux-ruby-get-started.md)

## <a name="next-steps"></a>后续步骤
* [什么是 Linux Web 应用？](app-service-linux-intro.md)
* [在 Linux Web 应用中创建 Web 应用](app-service-linux-how-to-create-web-app.md)
* [从本地 Git 部署到 Azure 应用服务](app-service-deploy-local-git.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)
* [使用 Linux 上的 Azure Web 应用创建 Ruby 应用](app-service-linux-ruby-get-started.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
