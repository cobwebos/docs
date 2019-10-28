---
title: 通过 Visual Studio App Center 和 Azure 服务自动执行应用程序的生命周期
description: 了解 App Center 的服务，这些服务可帮助为你的移动应用程序设置持续的生成和集成。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795544"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>通过持续生成和集成自动执行应用程序的生命周期

作为开发人员，你编写代码并将其签入代码存储库，但签入到存储库中的提交可能并不总是一致的。 当多个开发人员从事同一个项目时，问题就会出现在集成上，团队可能会遇到这样的情况：工作不起作用、bug 保持堆积并延迟项目开发。 开发人员必须等待，直到生成并测试了整个软件代码，以检查是否有错误，这使得处理速度变慢且迭代更少。 

通过**持续生成和集成**，开发人员不仅可以将其更改提交到源代码存储库，还可以简化代码的生成和测试，还可以将测试和验证置于生成环境中，使它们始终运行针对其代码进行测试。 对存储库进行提交时，对源代码进行的所有更改都将连续生成。 对于每次签入，CI 服务器都将验证并执行开发人员创建的任何测试。 如果测试未通过，则发送回代码以进行进一步的更改。 这允许开发人员不中断所创建的生成，也不会在其计算机上本地运行所有测试，进而提高开发人员的工作效率。 

## <a name="key-benefits"></a>主要优点
- **自动**生成、测试和部署管道。
- 尽早**检测 bug 并修复问题**，以确保更快的发布速率。
- 更频繁地**提交代码**并快速生成应用程序。
- 无需任何问题即可**灵活**地更改代码。
- **缩短投放市场的时间**可确保只有优质的代码才能实现这一功能。
- **小代码更改**，因为它允许一次集成一小部分代码。
- **提高团队透明性和责任**使你不仅可以从你的客户和你的团队那里获得**持续反馈**。

使用以下服务在移动应用中启用持续集成管道。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
通过使用安全的云基础结构， [App Center 生成](/appcenter/build/)服务可以帮助你构建团队正在处理的本机和跨平台应用程序。 你可以轻松地将你的存储库连接到 App Center，并在云中开始在云中构建你的应用程序，而无需担心在同事计算机上（而不是在你的计算机上）配置生成服务器。

随着 App Center 服务的增强功能，你可以进一步自动化工作流。 通过 App Center 测试，自动将生成发布到测试人员和公用应用商店，并在上千个实际设备和 OS 配置上运行自动 UI 测试 App Center。

**关键功能**
- 在数分钟内**设置持续集成**，并更快、更快地生成应用程序。
- 与**GitHub、BitBucket、Azure DevOps 和 GitLab**集成。
- 在托管的云托管计算机上**快速、安全地生成**。
- **启用生成以 "启动测试"** 并验证应用是否在实际的 IOS 和 Android 设备中生成。
- **本机支持和跨平台支持**-IOS、Android、MacOS、Windows、Xamarin、响应本机。
- 通过添加后期克隆、预生成和生成后脚本**来自定义生成**。

**参考**
- [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 生成入门](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是 Azure DevOps 中的一项服务，它是一个功能齐全的持续集成（CI）和持续交付（CD）服务，可与首选的 Git 提供程序结合使用，并可部署到大多数主要的云服务（包括 Azure）。 你可以在 GitHub、GitHub Enterprise Server、GitLab、Bitbucket 云和 Azure Repos 上开始编写代码。 然后，可以自动化构建和测试代码并将其部署到 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services 。

**关键功能**
- 用于为本机（Android、iOS 和 Windows）和跨平台（Xamarin、Cordova 和响应本机）移动应用程序以及基于 Microsoft 和非 Microsoft （基于 node.js、Java）的服务器设置 CI 服务器的基于任务的简单体验结合.
- **任何语言、平台和云**生成、测试和部署 node.js、Python、Java、PHP、Ruby、中转、C/C++、 C#、Android 和 iOS 应用程序。 在 Linux、macOS 和 Windows 上并行运行。 部署到 Azure、AWS 和 GCP 之类的云提供程序。 通过 beta 通道和应用商店分发移动应用程序。
- **本机容器支持**-轻松创建新容器，并将其推送到任何注册表。 将容器部署到独立的主机或 Kubernetes。
- **高级工作流**-简单的生成链接和多阶段生成。 支持 YAML、测试集成、发布门、报告等。
- **可扩展**-使用社区生成的一系列生成、测试和部署任务–从时差到 SonarCloud 的数百个扩展。 甚至可以从其他 CI 系统（如 Jenkins）进行部署，并使用 webhook 和 REST Api 帮助你集成
- 为公共和专用存储库**免费提供云托管的版本**。
- **支持部署到其他云供应商**，如 Amazon Web Services、Google Cloud Platform 等。

**参考**
- [Azure Pipelines 指南入门](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps 入门](https://app.vsaex.visualstudio.com/signup/) 
- [快速入门](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

若要为应用程序生成选择正确的服务，请遵循比较[App Center 版本与 Azure Pipelines](/appcenter/build/choose-between-services)的文章。
