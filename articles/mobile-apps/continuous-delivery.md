---
title: 通过 Visual Studio App Center 和 Azure 服务自动部署和发布移动应用程序
description: 了解 App Center 的服务，这些服务可帮助为你的移动应用程序设置持续交付管道。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795596"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>通过持续交付服务自动部署和发布移动应用程序

作为开发人员，你编写代码并将其签入代码存储库，但签入到存储库中的提交可能不是始终一致的。 当多个开发人员从事同一个项目时，问题就会出现在集成上，团队可能会遇到这样的情况：工作不起作用、bug 保持堆积并延迟项目开发。 开发人员必须等待整个软件代码生成并测试，以检查是否有错误，这使得处理速度缓慢且迭代更少。

通过**持续交付**，无论是要将应用程序分发到一组测试人员还是公司员工（对于测试版），还是应用商店（用于生产），都可以自动部署和发布移动应用程序。 它使部署更不具风险，鼓励快速迭代，并使你能够以连续方式向客户发布新的更改。

## <a name="distribute-application-binaries-to-beta-testers"></a>将应用程序二进制文件分发给 beta 版测试人员
测试您的移动应用程序的测试是应用程序开发过程中的关键步骤之一。 它可帮助你及早查找应用程序中的错误和问题，并提供反馈，提高应用程序的质量，使其可供生产之用。

使用以下服务在移动应用中启用持续交付管道。

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 分发](/appcenter/distribution/)是一种工具，可供开发人员快速将版本发布到最终用户设备。 通过完整的安装门户体验，分发不仅是适用于 beta 应用程序测试程序分发的强大解决方案，还是通过公共应用商店分发的一种便利方法。 开发人员还可以通过 App Center 生成和公用应用商店集成，进一步自动化其分发工作流。

**关键功能**
- 将**你的应用分发给 beta 版测试人员和用户**，并确保所有测试人员都在最新版本的应用程序上。
- 向**测试人员通知新版本**，而无需测试人员再次经历下载流。
- 管理不同版本的应用程序的**通讯组**。
- **分发到存储** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **平台支持**-IOS、Android、MacOS、TvOS、Xamarin、响应本机、Unity 和 Cordova。
- 自动将 iOS 设备注册到预配配置文件。

**参考**
- [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 分发入门](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是一种功能全面的持续集成（CI）和持续交付（CD）服务，可与首选的 Git 提供程序一起使用，并且可以部署到大多数主要的云服务，包括 Azure 服务。 你可以在 GitHub、GitHub Enterprise Server、GitLab、Bitbucket 云和 Azure Repos 上开始编写代码。 然后，可以自动化构建和测试代码并将其部署到 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services 。

**关键功能**
- 简化的基于任务的体验，用于为**本机（Android、iOS 和 Windows）和跨平台（Xamarin、Cordova 和响应本机）移动应用程序**设置 CI 服务器。
- **任何语言、平台和云**生成、测试和部署 node.js、Python、Java、PHP、Ruby、中转、C/C++、 C#、Android 和 iOS 应用。 在 Linux、macOS 和 Windows 上并行运行。 部署到 Azure、AWS 和 GCP 之类的云提供程序。 通过 beta 通道和应用商店分发移动应用程序。
- **本机容器支持**-轻松创建新容器，并将其推送到任何注册表。 将容器部署到独立的主机或 Kubernetes。
- **高级工作流和功能**-简单的生成链接和多阶段生成。 支持 YAML、测试集成、发布门、报告等。
- **可扩展**-使用社区生成的一系列生成、测试和部署任务–从时差到 SonarCloud 的数百个扩展。 甚至可以从其他 CI 系统（如 Jenkins）进行部署。 Webhook 和 REST Api 可帮助你集成
- 为公共和专用存储库**免费提供云托管的版本**。
- **支持部署到其他云供应商**，如 AWS、GCP 等。

**参考**
- [Azure Pipelines 指南入门](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 入门](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>直接将应用程序分发到应用商店
一旦你的应用程序可供生产使用并且你想要公开使用它，就需要将它提交到客户可供客户下载的应用商店。 可以通过多种方式将应用程序直接分发到应用商店。 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 分发](/appcenter/distribution/stores/)服务，你可以将移动应用程序直接发布到应用商店。 最终用户准备好下载应用程序后，可以直接从 App Center 门户发布应用程序二进制文件。  

可以直接分发到：
- [Apple 应用商店](/appcenter/distribution/stores/apple)
- [Google Play 商店](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple 应用商店
Apple 开发和维护的应用商店允许用户浏览和下载为 iOS、MacOS、WatchOS 和 tvOS 设备开发的应用程序。 开发人员需要将其 iOS 应用提交到 Apple Store 以供公开使用。

### <a name="google-play"></a>Google Play

Google Play 是适用于 Android 操作系统的官方应用商店，允许用户浏览和下载为 Android 设备开发并通过 Google 发布的应用程序。

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management)是企业移动性管理（EMM）领域中基于云的服务，可帮助员工提高工作效率，同时保护公司数据。 借助 Intune，你可以 
- 管理你的员工用于访问公司数据的移动设备和电脑。
- 管理员工使用的移动应用程序。
- 通过控制您的员工访问和分享公司信息的方式来保护公司信息。
- 确保设备和应用程序符合公司的安全要求。
    
## <a name="deploy-updates-directly-to-users-devices"></a>直接将更新部署到用户的设备

### <a name="codepush"></a>CodePush
App Center 中的[CodePush](/appcenter/distribution/codepush/)服务将启用 Apache Cordova 并响应本机开发人员，以将移动应用程序更新直接部署到用户的设备。 它的工作原理是，开发人员可以将某些更新发布到（例如，JS、HTML、CSS 和图像更改）。 然后，应用程序可以使用提供的客户端 Sdk 从存储库查询更新。 这样，你就可以为最终用户提供更具确定性的直接参与模型，同时解决 bug 或添加小功能，而无需重新生成二进制文件或通过任何公共应用商店重新分发它。

**关键功能**
- 允许 Cordova 和响应本机开发人员将移动应用程序更新直接部署到用户的设备，而无需在商店上发布。
- 用于修复 bug 或添加/移除不需要通过各自的存储重新分发二进制文件并重新分发的小功能。

**参考**
- [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [开始在 App Center 中推送代码](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)