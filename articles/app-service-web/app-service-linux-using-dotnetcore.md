---
title: "在 Linux Azure 应用服务 Web 应用中使用 .NET Core | Microsoft Docs"
description: "在 Linux Azure 应用服务 Web 应用中使用 .NET Core。"
keywords: "azure app service、web 应用、dotnet、core、linux、oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>在 Linux Web 应用中使用 .NET Core #

我们在后端的最新更新中引入了对 .NET Core v.1.0 的支持。 通过设置 Linux Web 应用的配置，可更改应用程序堆栈。


## <a name="using-xplat-cli"></a>使用 XPlat CLI ##

通过最新 Azure 跨平台 CLI，可使用 **azure webapp config set** 命令更改应用程序堆栈。 以下是上述操作的示例：

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

请注意，**aspnetcore.dll** 文件是应用的 dll。 可以在应用中使用任何喜欢的名称。

这将加载 .Net Core 映像并启动 Web 应用。 可通过使用 **azure webapp config show** 来检查设置是否正确。 以下是上述操作的示例：

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>后续步骤
* [Linux 应用服务简介](./app-service-linux-intro.md) 
* [什么是 Linux 应用服务？](app-service-linux-intro.md)
* [在 Linux 应用服务中创建 Web 应用](./app-service-linux-how-to-create-a-web-app.md)
* [Azure Web 应用跨平台 CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)



<!--HONumber=Nov16_HO3-->


