---
title: 使用 Visual Studio 应用中心和 Azure 服务自动部署和发布移动应用程序
description: 了解帮助为移动应用程序设置连续交付管道的应用中心等服务。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235357"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>通过连续交付服务自动部署和发布移动应用程序

作为开发人员，您编写代码并将其签入代码存储库，但签入存储库中的提交可能并不总是一致的。 当多个开发人员处理同一个项目时，可能会遇到集成问题。 团队可能会遇到事情不工作、Bug 堆积和项目开发延迟的情况。 开发人员必须等到构建和测试整个软件代码以检查错误，这使得过程变慢，迭代更少。

通过持续交付，您可以自动部署和释放移动应用程序。 无论您是将应用程序分发给一组测试人员还是公司员工（用于 beta 测试）还是分发到应用商店（用于生产）并不重要。 持续交付使部署风险降低，并鼓励快速迭代。 您还可以持续向客户发布新的更改。

## <a name="distribute-application-binaries-to-beta-testers"></a>将应用程序二进制文件分发给 beta 测试人员
Beta 测试移动应用程序是应用程序开发过程中的关键步骤之一。 它有助于在早期查找应用程序中的 Bug 和问题。 当您准备好用于生产时，反馈会提高应用程序质量。

使用以下服务在移动应用中启用连续交付管道。

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[应用中心分发](/appcenter/distribution/)是开发人员快速向设备发布生成的工具。 凭借完整的安装门户体验，应用中心分发是测试版应用测试器分发的强大解决方案。 这也是通过公共应用商店分发的便捷替代方案。 开发人员可以通过应用中心构建和公共应用程序存储集成进一步自动化其分发工作流。

**主要功能**
- 将应用分发给 beta 测试人员和用户，并确保所有测试人员都位于应用程序的最新版本上。
- 通知测试人员新版本，无需测试人员再次遍通下载流程。
- 管理应用程序不同版本的通讯组。
- 分销到商店： 
    - [苹果](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- 获得对 iOS、Android、macOS、tvOS、Xamarin、反应原生、团结和科尔多瓦的平台支持。
- 自动将 iOS 设备注册到预配配置文件。

**引用**
- [使用可视化工作室应用中心注册](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [开始应用中心分发](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是一项功能齐全的连续集成 （CI） 和连续交付 （CD） 服务，可与首选 Git 提供商配合使用。 Azure 管道可以部署到大多数主要云服务，如 Azure 服务。 您可以在 GitHub、GitHub 企业服务器、GitLab、Bitbucket 云或 Azure 存储库上使用代码开始。 然后，您可以将代码的生成、测试和部署自动化到 Microsoft Azure、Google 云平台或亚马逊 Web 服务 （AWS）。

**主要功能**
- **简化基于任务的 CI 服务器设置体验：** 为本机（安卓、iOS 和 Windows）和跨平台（Xamarin、Cordova 和 React 本机）移动应用程序设置 CI 服务器。
- **任何语言、平台和云：** 生成、测试和部署 Node.js、Python、Java、PHP、Ruby、Go、C/C++、C#、Android 和 iOS 应用。 在 Linux、macOS 和 Windows 上并行运行。 部署到 Azure、AWS 和 Google 云平台等云提供商。 通过测试版渠道和应用商店分发移动应用程序。
- **本机容器支持：** 轻松创建新容器，并将它们推送到任何注册表。 将容器部署到独立的主机或库伯奈斯。
- **高级工作流和功能：** 轻松创建生成链和多阶段生成。 获得对 YAML、测试集成、释放门、报告等的支持。
- **可扩展：** 使用社区构建的一系列生成、测试和部署任务，其中包括从 Slack 到声纳云的数百个扩展。 您甚至可以从其他 CI 系统（如 Jenkins）进行部署。 网络挂钩和 REST API 可以帮助您进行集成。
- **免费云托管版本：** 这些生成可用于公共和私有存储库。
- **支持部署到其他云供应商：** 供应商包括 AWS 和 Google 云平台。

**引用**
- [开始使用 Azure 管道指南](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 入门](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>将应用程序直接分发到应用商店
应用程序已准备好用于生产，并且希望公开使用后，需要将其提交到客户可以下载的应用商店。 有多种方法可以将应用程序直接分发到应用商店。 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
使用[应用中心分发](/appcenter/distribution/stores/)，您可以将移动应用程序直接发布到应用商店。 应用程序准备好由用户下载后，可以直接从可视化 Studio 应用中心门户发布应用程序二进制文件。 

您可以直接分发到：
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
在 Apple 开发和维护的应用商店中，用户可以浏览和下载为 iOS、MacOS、WatchOS 和 tvOS 设备开发的应用程序。 开发人员需要将其 iOS 应用程序提交到 Apple 应用商店供公众使用。

### <a name="google-play"></a>Google Play

Google Play 是 Android 操作系统的官方应用商店，用户可以在这里浏览和下载为通过 Google 发布的 Android 设备开发的应用程序。

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management)是企业移动管理领域的基于云的服务，可帮助您的员工提高工作效率，同时保护公司数据。 通过 Intune，还可以：
- 管理工作人员用来访问公司数据的移动设备和 PC。
- 管理员工使用的移动应用程序。
- 通过控制员工访问和共享公司信息的方式来保护公司信息。
- 确保设备和应用程序符合公司安全要求。
    
## <a name="deploy-updates-directly-to-users-devices"></a>将更新直接部署到用户的设备

### <a name="codepush"></a>代码推送
借助 AppCenter 中的[CodePush，Apache](/appcenter/distribution/codepush/) Cordova 和 React 原生开发人员可以直接将移动应用程序更新部署到其用户的设备上。 它充当开发人员可以发布某些更新（如 JavaScript、HTML、CSS 和映像更改）的中央存储库。 然后，应用程序可以使用提供的客户端 SDK 查询存储库中的更新。 通过这种方式，您可以在解决 Bug 或添加小功能时，与用户建立更具确定性和直接参与模型。 您不需要重建二进制文件或通过任何公共应用商店重新分发它。

**主要功能**
- Cordova 和 React Native 开发人员可以直接将移动应用程序更新部署到用户的设备上，而无需在存储中发布。
- 可用于修复 Bug 或添加和删除不需要您重新生成二进制文件并通过相应存储重新分发的小功能。

**引用**
- [使用可视化工作室应用中心注册](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [在应用中心开始使用代码推送](/appcenter/distribution/codepush/)
- [代码推送 CLI](/appcenter/distribution/codepush/cli)