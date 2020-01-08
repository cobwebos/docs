---
title: 用 GitHub 和 Azure DevOps 在云中托管你的移动应用程序源代码
description: 了解如何通过 Microsoft 服务在云中托管移动应用程序代码。
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8662b9f77614339ff514fa4fcf97dc1ee8fc7417
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454486"
---
# <a name="cloud-hosted-source-code-management-services"></a>云托管的源代码管理服务
如果你的开发团队有多个使用同一基本代码的团队成员，则需要找到合适的位置来托管你的代码。 将数据存储在云中，使用集中式存储库，每个人都可以上传、编辑和管理代码文件。 它们还可以与其他项目开发人员交互。 只要有 internet 连接，就可以随时访问该代码，无论您身处何处。

云托管比本地选项要容易得多。 它需要更少的硬件配置，并使组织能够以更灵活的方式完成实现过程。

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>在云中托管源代码的优点
- **集中式云存储：** 随时随地查看和管理数据。
- **更好的协作和更简洁的代码：** 跟踪团队中的代码和管理项目，以确保实现优秀软件的持续交付。
- **更易于参与：** 轻松参与项目。
- **更快的发布周期：** 更快地在团队中工作，并轻松地参与项目。
- **降低成本：** 不必担心如何维护自己的硬件、服务器、VPN 等。

使用以下服务在云中托管你的应用程序数据。

## <a name="github"></a>GitHub
[GitHub](https://github.com/)是一个开源存储库宿主服务，它承载各种不同编程语言的源代码项目。 GitHub 跟踪对每个迭代进行的各种更改。

**主要功能**
- 使用代码宿主将所有代码保存在一个位置。 私有、公共和开源存储库都具备可帮助您托管、版本和发布代码的工具。
- 查看代码，并使用内置的评审工具使代码查看任何团队过程的基本组成部分：
    - 保护分支、建议更改和请求评审。
    - 发现差异，在上下文中注释，并获得明确的反馈。
- 提前协调、保持一致，并通过 GitHub 的项目管理工具完成更多操作：
    - 查看项目的大图。
    - 使用 GitHub 中你的代码旁边的任务板。
    - 拖动卡以将问题或拉取请求分配给团队成员。
    - 设置里程碑以组织和跟踪进度。
    - 编写注释来捕获可能有用但不属于特定问题或拉取请求的创意。
- 通过从[GitHub Marketplace](https://github.com/marketplace)购买应用程序，可以轻松发现和选择合适的工具以实现更好的通信和自动化工作。
- 使用以下内容管理和增长团队： 
    - 用于帮助组织团队和访问权限的用户角色。
    - 讨论线程工具，用于保持主题和团队的对话。
    - 社区指导原则，使用帐户快速设置新的团队成员。
- 浏览和星型项目以遵循这些项目。
- 网络，并从行业中的其他人那里学习。

**参考**
- [GitHub](https://github.com/)
- [GitHub 指南](https://guides.github.com/)
- [GitHub 社区论坛](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/)支持[Azure Repos](https://azure.microsoft.com/services/devops/repos/)和[Team Foundation 版本控制（TFVC）](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops)作为源代码管理选项。 它拥有无限制的免费专用存储库，并提供协作代码评审、高级文件管理、代码搜索和分支策略，以确保高质量代码。 对于需要本机 Azure Active Directory 支持和高级策略的小型项目和大型组织而言，Azure Repos 非常有用。
    
**主要功能**
- 为公有和私有存储库使用不限云托管的 Git 源代码存储库：
    - 获取对任何 Git 客户端的支持。
    - 使用 web 挂钩和 API 集成。
- 从存储库拉取请求开始下一次生成：
    - 通过对每个更改使用线程化讨论和持续集成，协作生成更好的代码。
    - 设置持续集成/持续交付（CI/CD），以通过每个已完成的拉取请求自动触发生成、测试和部署。 你可以使用 Azure Pipelines 或工具。
    - 通过分支策略保护代码质量。
- 维护带有 Team Foundation 版本控制的集中式版本控制，其中包括代码评审。
- 使用 Xcode、Eclipse、IntelliJ、Android Studio、Visual Studio、Visual Studio Code 等连接到你的代码。
- 使用功能强大的语义代码搜索。
- 利用企业就绪功能获得好处。 Azure DevOps 与 Azure Active Directory 的本机集成，这简化了管理对代码存储库的访问的过程。
- 使用分支策略确保代码质量，例如最小数量的代码评审、成功生成的要求以及 Git 合并策略的强制执行。
- 连接你最喜爱的开发环境以访问存储库和管理工作。

**参考**
- [Azure Repos 入门](https://azure.microsoft.com/services/devops/repos/) 
- [Azure Repos 文档](/azure/devops/repos/?view=azure-devops)