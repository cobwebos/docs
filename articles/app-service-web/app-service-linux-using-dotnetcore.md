---
title: "在 Linux 上的 Azure App Service Web 应用中使用 .NET Core | Microsoft Docs"
description: "在 Linux 上的 Azure App Service Web 应用中使用 .NET Core。"
keywords: "azure 应用服务、web 应用、dotnet、core、linux、oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3608ddf86c3d8010b577e6f745dcd5cef016acd9
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>在 Linux 上的 Azure Web 应用中使用 .NET Core #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


我们在后端的最新更新中引入了对 .NET Core v.1.0 的支持。 通过设置 Linux Web 应用的配置，可更改应用程序堆栈。


## <a name="using-the-azure-cli"></a>使用 Azure CLI ##

通过[最新 Azure 命令行接口 (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli)，可使用 **azure webapp config set** 命令更改应用程序堆栈。 下面是一个示例：

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

**aspnetcore.dll** 文件是应用的 dll。 可以在应用中使用任何喜欢的名称。

这将加载 .Net Core 映像并启动 Web 应用。 可通过使用 **azure webapp config show** 来检查设置是否正确。 下面是一个示例：

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>后续步骤
* [什么是 Linux 上的 Azure Web 应用？](app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 应用中创建 Web 应用](./app-service-linux-how-to-create-web-app.md)
* [Azure Web 应用跨平台 CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Linux 上的 Azure App Service Web 应用常见问题解答](app-service-linux-faq.md)
