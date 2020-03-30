---
title: 使用可视化工作室应用中心和 Azure 服务自动实现应用的生命周期
description: 了解帮助为移动应用程序设置连续构建和集成的应用中心等服务。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240925"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>通过持续构建和集成实现应用生命周期的自动化

作为开发人员，您编写代码并将其签入代码存储库，但签入存储库中的提交可能并不总是一致的。 当多个开发人员处理同一个项目时，可能会遇到集成问题。 团队可能会遇到事情不工作、Bug 堆积和项目开发延迟的情况。 开发人员必须等到构建和测试整个软件代码以检查错误，这使得过程变慢，迭代更少。 

通过持续构建和集成，开发人员可以通过将更改提交到源代码存储库并将测试和验证放入生成环境来简化生成并测试其代码。 这样，他们总是针对代码运行测试。 每当向存储库提交时，对源代码所做的所有更改都会持续生成。 每次签入时，持续集成 （CI） 服务器都会验证并执行开发人员创建的任何测试。 如果测试未通过，代码将发送回以进行进一步更改。 这样，开发人员就不会破坏创建的生成。 他们也不必在其计算机上本地运行所有测试，从而提高了开发人员的工作效率。 

## <a name="key-benefits"></a>主要优点
- 自动执行管道的生成、测试和部署。
- 及早检测 Bug 并修复问题，以确保更快的发布速度。
- 更频繁地提交代码并快速生成应用程序。
- 灵活快速更改代码，无需任何问题。
- 获得更快的上市时间，以便只有高质量的代码才能一直贯穿其中。
- 由于一次集成了小块代码，因此可以更高效地进行小代码更改。
- 提高团队透明度和问责制，以便不断获得客户和团队的反馈。

使用以下服务在移动应用中启用连续集成管道。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[应用中心构建](/appcenter/build/)可帮助您使用安全的云基础结构构建您的团队正在处理的本机和跨平台应用程序。 您可以在 Visual Studio 应用中心轻松连接您的回购，并在每次提交时开始在云中构建应用。 您不必担心在本地配置生成服务器、复杂的配置和基于同事的计算机而不是您的代码的代码。

借助 Visual Studio 应用中心服务的强大功能，您可以进一步自动化您的工作流程。 您可以使用应用中心分发自动向测试人员和公共应用商店发布生成。 您还可以通过应用中心测试在云中的数千个真实设备和操作系统配置上运行自动 UI 测试。

**主要功能**
- 在几分钟内设置持续集成，并更频繁、更快速地构建应用程序。
- 与 GitHub、位存储桶、Azure 开发人员和 GitLab 集成。
- 在托管的云托管计算机上创建快速安全的构建。
- 使生成能够启动测试，并验证应用是否在真实 iOS 和 Android 设备中生成。
- 获得针对 iOS、Android、macOS、Windows、Xamarin 和反应本机的本机和跨平台支持。
- 通过添加克隆后、预生成和生成后脚本自定义生成。

**引用**
- [使用可视化工作室应用中心注册](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [开始使用应用中心生成](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是 Azure DevOps 中的一项服务，是一项功能齐全的连续集成和连续传递 （CD） 服务，可与首选 Git 提供程序配合使用。 它可以部署到大多数主要云服务，其中包括 Azure。 您可以在 GitHub、GitHub 企业服务器、GitLab、Bitbucket 云或 Azure 存储库上使用代码开始。 然后，您可以将代码的生成、测试和部署自动化到 Microsoft Azure、Google 云平台或亚马逊 Web 服务 （AWS）。

**主要功能**
- **简化基于任务的 CI 服务器设置体验：** 除了微软和非微软（Node.js，Java）的服务器技术外，为本机（安卓、iOS 和 Windows）和跨平台（Xamarin、Cordova 和 React Native）移动应用程序设置 CI 服务器。
- **任何语言、平台和云：** 构建、测试和部署 Node.js、Python、Java、PHP、Ruby、Go、C/C++、C#、Android 和 iOS 应用程序。 在 Linux、macOS 和 Windows 上并行运行。 部署到 Azure、AWS 和 Google 云平台等云提供商。 通过测试版渠道和应用商店分发移动应用程序。
- **本机容器支持：** 轻松创建新容器，并将它们推送到任何注册表。 将容器部署到独立的主机或库伯奈斯。
- **高级工作流：** 轻松创建生成链和多阶段生成。 获得对 YAML、测试集成、释放门、报告等的支持。
- **可扩展：** 使用社区构建的一系列生成、测试和部署任务，其中包括从 Slack 到声纳云的数百个扩展。 您甚至可以从其他 CI 系统（如 Jenkins）进行部署。 网络挂钩和 REST API 可以帮助您进行集成。
- **免费云托管版本：** 这些生成可用于公共和私有存储库。
- **支持部署到其他云供应商：** 供应商包括 AWS 和 Google 云平台。

**引用**
- [开始使用 Azure 管道指南](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 入门](https://app.vsaex.visualstudio.com/signup/) 
- [快速入门](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

为了帮助为应用程序生成选择正确的服务，请参阅将[应用中心生成与 Azure 管道](/appcenter/build/choose-between-services)进行比较的文章。
