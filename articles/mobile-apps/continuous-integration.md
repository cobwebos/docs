---
title: 通过 Visual Studio App Center 和 Azure 服务自动执行应用程序的生命周期
description: 了解 App Center 的服务，这些服务可帮助为你的移动应用程序设置持续的生成和集成。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 9b0a9e10a88836ce83e636db20180c3692ab4429
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453192"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>通过持续生成和集成自动执行应用程序的生命周期

作为开发人员，你编写代码并将其签入代码存储库，但签入到存储库中的提交可能并不总是一致的。 当多个开发人员处理同一个项目时，可能会出现问题。 团队可能会遇到这样的情况：工作不起作用、错误堆积以及项目开发已延迟。 开发人员必须等待，直到生成并测试整个软件代码，以检查是否有错误，这会使处理速度变慢且迭代更少。 

通过持续生成和集成，开发人员可以通过将其更改提交到源代码存储库，并将测试和验证置于生成环境中，来简化生成和测试代码。 通过这种方式，它们始终对其代码运行测试。 每次对存储库进行提交时，都会连续生成对源代码所做的所有更改。 对于每次签入，持续集成（CI）服务器都会验证并执行开发人员创建的任何测试。 如果测试未通过，则发送回代码以进行进一步的更改。 通过这种方式，开发人员不会中断创建的生成。 它们还不必在计算机上本地运行所有测试，这会提高开发人员的工作效率。 

## <a name="key-benefits"></a>主要优点
- 自动执行管道的生成、测试和部署。
- 尽早检测 bug 并修复问题，以确保更快的发布速率。
- 更频繁地提交代码并快速生成应用程序。
- 无需任何问题即可灵活地更改代码。
- 获得更快的上市时间，以便只有优质的代码才能实现此目的。
- 更有效地进行较小的代码更改，因为一次集成了小部分代码。
- 提高团队透明性和责任，使你能够从客户和你的团队获得持续的反馈。

使用以下服务在移动应用中启用持续集成管道。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 生成](/appcenter/build/)可帮助你使用安全的云基础结构构建团队正在处理的本机和跨平台应用程序。 你可以轻松地将你的存储库连接到 Visual Studio App Center，并在每次提交时开始在云中构建你的应用程序。 您无需担心如何在本地配置生成服务器、复杂的配置以及在同事计算机上构建的代码，而不是在您的计算机上进行配置。

随着 Visual Studio App Center 服务的增强功能，你可以进一步自动化工作流。 可以通过 App Center 分发，自动将生成发布到测试人员和公用应用商店。 还可以通过 App Center 测试，在云中的数千个实际设备和 OS 配置上运行自动 UI 测试。

**主要功能**
- 在数分钟内设置持续集成，并更快、更快地生成应用程序。
- 与 GitHub、BitBucket、Azure DevOps 和 GitLab 集成。
- 在托管的云托管计算机上创建快速、安全的内部版本。
- 启用生成以启动测试，并验证应用是否在实际的 iOS 和 Android 设备中生成。
- 为 iOS、Android、macOS、Windows、Xamarin 和响应本机获取本机支持和跨平台支持。
- 通过添加后期克隆、预生成和生成后脚本来自定义生成。

**参考**
- [注册 Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 生成入门](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是 Azure DevOps 中的一项服务，它是一个功能齐全的持续集成和持续交付（CD）服务，可与首选的 Git 提供程序结合使用。 它可以部署到最主要的云服务，其中包括 Azure。 你可以在 GitHub、GitHub Enterprise Server、GitLab、Bitbucket 云和 Azure Repos 上开始编写代码。 然后，可以将代码的生成、测试和部署自动化到 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services （AWS）。

**主要功能**
- **简化了设置 CI 服务器的基于任务的体验：** 除了 Microsoft 和非 Microsoft （基于 node.js、Java）的服务器技术外，还为本机（Android、iOS 和 Windows）和跨平台（Xamarin、Cordova 和响应本机）移动应用程序设置 CI 服务器。
- **任何语言、平台和云：** 生成、测试和部署 node.js、Python、Java、PHP、Ruby、中转、C/C++、 C#、Android 和 iOS 应用程序。 在 Linux、macOS 和 Windows 上并行运行。 部署到 Azure、AWS 和 Google Cloud Platform 等云提供程序。 通过 beta 通道和应用商店分发移动应用程序。
- **本机容器支持：** 轻松创建新容器，并将其推送到任何注册表。 将容器部署到独立的主机或 Kubernetes。
- **高级工作流：** 轻松创建生成链和 multiphased 生成。 获取对 YAML、测试集成、发布门、报表等的支持。
- **可扩展：** 使用社区生成的一系列生成、测试和部署任务，其中包括从时差到 SonarCloud 的数百个扩展。 甚至可以从其他 CI 系统（如 Jenkins）进行部署。 Web 挂钩和 REST Api 可帮助你集成。
- **免费的云托管版本：** 这些版本适用于公共和专用存储库。
- **支持部署到其他云供应商：** 供应商包括 AWS 和 Google Cloud Platform。

**参考**
- [Azure Pipelines 指南入门](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 入门](https://app.vsaex.visualstudio.com/signup/) 
- [快速入门](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

若要帮助您为应用程序生成选择正确的服务，请参阅与 Azure Pipelines 进行比较[App Center 版本与](/appcenter/build/choose-between-services)。
