---
title: "使用 Visual Studio 2015 部署到 Azure 应用服务 | Microsoft Docs"
description: "本文介绍如何使用 Visual Studio 2015 和 Azure SDK 将 Web 应用、API 应用或移动应用部署到 Azure Government。"
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 40e2eea5c1a11dadb232c6ac7c224ca5fc847c02
ms.openlocfilehash: 9a5efdd4c94c7cfc7a63c478f1f5b8124a8cb5c6


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>使用 Visual Studio 2015 部署到 Azure 应用服务
本文介绍如何使用 Visual Studio 2015 将 Azure 应用服务应用（API 应用、Web 应用、移动应用）部署到 Azure Government。

## <a name="prerequisites"></a>先决条件
* 请参阅 [Visual Studio 先决条件](../app-service-api/app-service-api-dotnet-get-started.md#prerequisites)，安装和配置 Visual Studio 2015 和 Azure SDK。
* 按照[这些说明](documentation-government-manage-subscriptions.md#connecting-via-visual-studio)配置 Visual Studio，以便连接到 Azure Government 帐户。

## <a name="open-app-project-in-visual-studio"></a>在 Visual Studio 中打开应用项目
* 在 Visual Studio 中打开现有应用解决方案\项目，按照[这些说明](../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application)创建一个项目，或按照[这些步骤](../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application)下载示例应用。
* 在 Visual Studio 中运行应用，确保它可在本地正常工作。

## <a name="deploy-to-azure-government"></a>部署到 Azure Government
* **配置 Visual Studio 以连接到 Azure Government 帐户**（已在先决条件部分完成）后，部署到应用服务的说明与部署到 Azure Public 的完全相同。
* 若要部署应用，请按照[这些步骤](../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it)操作。

### <a name="references"></a>参考
* [使用 Visual Studio 将 ASP.NET Web 应用部署到 Azure 应用服务](../app-service-web/web-sites-dotnet-get-started.md)
* 有关其他部署方法，请参阅[将应用部署到 Azure 应用服务](../app-service-web/web-sites-deploy.md)
* 有关常规应用服务文档，请参阅[应用服务 - API 应用文档](.../app-service-api/index.md)

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Microsoft Azure 政府版博客](https://blogs.msdn.microsoft.com/azuregov/)。



<!--HONumber=Jan17_HO1-->


