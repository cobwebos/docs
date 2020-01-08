---
title: 通过 Visual Studio App Center 和 Azure 服务自动部署和发布移动应用程序
description: 了解 App Center 的服务，这些服务可帮助为你的移动应用程序设置持续交付管道。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 6a8c8d9fc535d973c70eb2e477051dbd1dd1f6fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454472"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>通过持续交付服务自动部署和发布移动应用程序

作为开发人员，你编写代码并将其签入代码存储库，但签入到存储库中的提交可能并不总是一致的。 当多个开发人员处理同一个项目时，可能会出现问题。 团队可能会遇到这样的情况：工作不起作用、错误堆积以及项目开发已延迟。 开发人员必须等待，直到生成并测试整个软件代码，以检查是否有错误，这会使处理速度变慢且迭代更少。

通过持续交付，你可以自动执行移动应用程序的部署和发布。 无论你是要将应用程序分发到一组测试人员还是公司员工（对于测试测试）或应用商店（用于生产），都不重要。 持续交付使部署更不具风险，并鼓励快速迭代。 你还可以通过连续方式向客户发布新的更改。

## <a name="distribute-application-binaries-to-beta-testers"></a>将应用程序二进制文件分发给 beta 版测试人员
测试您的移动应用程序的测试是应用程序开发过程中的关键步骤之一。 它可帮助你及早查找应用程序中的错误和问题。 当你的应用程序可供生产使用时，反馈会提高你的应用程序质量。

使用以下服务在移动应用中启用持续交付管道。

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 分发](/appcenter/distribution/)是一种工具，可供开发人员快速将版本发布到设备。 通过完整的安装门户体验，App Center 分发是适用于 beta 应用程序测试程序分发的功能强大的解决方案。 它也是通过公用应用商店分发的便利方法。 开发人员还可以通过 App Center 生成和公用应用商店集成，进一步自动化其分发工作流。

**主要功能**
- 将你的应用分发给 beta 版测试人员和用户，并确保所有测试人员都在最新版本的应用程序上。
- 向测试人员通知新版本，而无需测试人员再次经历下载流。
- 管理不同版本的应用程序的通讯组。
- 分发到存储： 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- 获得适用于 iOS、Android、macOS、tvOS、Xamarin、响应本机、Unity 和 Cordova 的平台支持。
- 自动将 iOS 设备注册到预配配置文件。

**参考**
- [注册 Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 分发入门](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是一种功能全面的持续集成（CI）和持续交付（CD）服务，可与首选的 Git 提供程序一起使用。 Azure Pipelines 可以部署到最主要的云服务，如 Azure 服务。 你可以在 GitHub、GitHub Enterprise Server、GitLab、Bitbucket 云和 Azure Repos 上开始编写代码。 然后，可以将代码的生成、测试和部署自动化到 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services （AWS）。

**主要功能**
- **简化了设置 CI 服务器的基于任务的体验：** 为本机（Android、iOS 和 Windows）和跨平台（Xamarin、Cordova 和响应本机）移动应用程序设置 CI 服务器。
- **任何语言、平台和云：** 生成、测试和部署 node.js、Python、Java、PHP、Ruby、中转、C/C++、 C#、Android 和 iOS 应用。 在 Linux、macOS 和 Windows 上并行运行。 部署到 Azure、AWS 和 Google Cloud Platform 等云提供程序。 通过 beta 通道和应用商店分发移动应用程序。
- **本机容器支持：** 轻松创建新容器，并将其推送到任何注册表。 将容器部署到独立的主机或 Kubernetes。
- **高级工作流和功能：** 轻松创建生成链和 multiphased 生成。 获取对 YAML、测试集成、发布门、报表等的支持。
- **可扩展：** 使用社区生成的一系列生成、测试和部署任务，其中包括从时差到 SonarCloud 的数百个扩展。 甚至可以从其他 CI 系统（如 Jenkins）进行部署。 Web 挂钩和 REST Api 可帮助你集成。
- **免费的云托管版本：** 这些版本适用于公共和专用存储库。
- **支持部署到其他云供应商：** 供应商包括 AWS 和 Google Cloud Platform。

**参考**
- [Azure Pipelines 指南入门](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 入门](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>直接将应用程序分发到应用商店
在应用程序准备好进行生产使用并且你想要将其公开使用后，需要将其提交到应用商店，客户可在其中下载该应用。 可以通过多种方式将应用程序直接分发到应用商店。 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
通过[App Center 分发](/appcenter/distribution/stores/)，你可以将移动应用程序直接发布到应用商店。 用户准备好下载应用程序后，可以直接从 Visual Studio App Center 门户发布应用程序二进制文件。 

可以直接分发到：
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play 商店](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
在 Apple 开发和维护的应用商店中，用户可以浏览和下载为 iOS、MacOS、WatchOS 和 tvOS 设备开发的应用程序。 开发人员需要将其 iOS 应用提交到 Apple App Store 以供公开使用。

### <a name="google-play"></a>Google Play

Google Play 是适用于 Android 操作系统的官方应用商店，用户可在其中浏览和下载为通过 Google 发布的 Android 设备开发的应用程序。

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management)是一种基于云的服务，它在企业移动性管理空间中，可帮助你的员工提高工作效率，同时保护公司数据。 通过 Intune，还可以：
- 管理工作人员用来访问公司数据的移动设备和 PC。
- 管理员工使用的移动应用程序。
- 通过控制您的员工访问和分享公司信息的方式来保护公司信息。
- 确保设备和应用程序符合公司的安全要求。
    
## <a name="deploy-updates-directly-to-users-devices"></a>直接将更新部署到用户的设备

### <a name="codepush"></a>CodePush
通过 App Center 中的[CodePush](/appcenter/distribution/codepush/) ，Apache Cordova 并响应本机开发人员可以直接将移动应用程序更新部署到用户的设备。 它作为中心存储库，开发人员可以将某些更新发布到，如 JavaScript、HTML、CSS 和图像更改。 然后，应用程序可以使用提供的客户端 Sdk 从存储库查询更新。 通过这种方式，你可以在处理 bug 或添加小功能时，为你的用户提供一个更具确定性并直接参与的模型。 不需要重新生成二进制文件或通过任何公共应用商店重新分发它。

**主要功能**
- Cordova 和响应本机开发人员可以直接向用户的设备部署移动应用程序更新，而无需在商店上发布。
- 用于修复 bug 或添加和删除不要求你重新生成二进制文件并通过各自的存储重新分发它的小功能。

**参考**
- [注册 Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 中的 CodePush 入门](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)