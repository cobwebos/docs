---
title: 使用 GitHub 和 Azure 开发人员在云中托管移动应用程序源代码
description: 了解使用 Microsoft 服务在云中托管移动应用程序代码的服务。
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240964"
---
# <a name="cloud-hosted-source-code-management-services"></a>云托管源代码管理服务
如果您有多个团队成员在同一代码库上工作的开发团队，则需要找到合适的位置来托管代码。 将数据存储在云中并具有集中式存储库，允许每个人上载、编辑和管理代码文件。 他们还可以与项目上的其他开发人员进行交互。 无论您身在何处，只要您有互联网连接，代码都可以轻松访问。

云托管比本地选项容易得多。 它要求更少的硬件配置，并允许组织以更敏捷的方式完成实现过程。

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>在云中托管源代码的好处
- **集中式云存储：** 随时随地查看和管理数据。
- **更好的协作和更简洁的代码：** 跟踪团队内的代码并管理项目，以确保持续交付出色的软件。
- **更容易参与：** 轻松为项目做出贡献。
- **更快的发布周期：** 在团队中更快地工作，轻松为项目做出贡献。
- **降低成本：** 不要担心维护自己的硬件、服务器、VPN 等。

使用以下服务在云中托管应用程序数据。

## <a name="github"></a>GitHub
[GitHub](https://github.com/)是一个开源存储库托管服务，它托管各种不同编程语言的源代码项目。 GitHub 跟踪对每次迭代所做的各种更改。

**主要功能**
- 使用代码托管将所有代码保存在一个位置。 私有、公共和开源存储库都配备了工具，可帮助您托管、版本和发布代码。
- 审核代码并使用内置审阅工具使代码审阅成为任何团队流程的重要组成部分：
    - 保护分支、建议更改和请求审核。
    - 发现差异，在上下文中发表评论，并获得明确的反馈。
- 尽早协调，保持一致，并借助 GitHub 的项目管理工具完成更多工作：
    - 查看项目的大图景。
    - 使用 GitHub 中代码旁边的任务板。
    - 拖动卡片以将问题或拉取请求分配给团队成员。
    - 设置里程碑以组织和跟踪进度。
    - 编写笔记以捕获可能有用的但不属于特定问题或拉取请求的想法。
- 通过从[GitHub 应用商店](https://github.com/marketplace)购买应用程序，轻松发现并选择正确的工具，以更好地沟通和自动化工作。
- 使用： 
    - 帮助组织团队和访问权限的用户角色。
    - 讨论线程工具，使讨论主题和团队重点。
    - 社区指南，以快速设置新的团队成员与帐户。
- 浏览和明星流行的项目，以遵循他们。
- 网络，向业内其他人学习。

**引用**
- [GitHub](https://github.com/)
- [GitHub 指南](https://guides.github.com/)
- [GitHub 社区论坛](https://github.community/)
- [GitHub 市场](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/)支持[Azure 存储库](https://azure.microsoft.com/services/devops/repos/)和[团队基础版本控制 （TFVC）](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops)作为源代码管理选项。 它有无限的免费私人存储库，具有协作代码审查、高级文件管理、代码搜索和分支策略，以确保高质量的代码。 Azure Repos 非常适合需要本机 Azure 活动目录支持和高级策略的小型项目和大型组织。
    
**主要功能**
- 使用无限制的云托管 Git 源代码存储库进行公共和私有存储库：
    - 获取对任何 Git 客户端的支持。
    - 使用 Web 挂钩和 API 集成。
- 从回购拉取请求启动下一个生成：
    - 协作，通过为每个更改使用线程讨论和持续集成来构建更好的代码。
    - 设置连续集成/连续交付 （CI/CD），以便根据每个已完成的拉取请求自动触发生成、测试和部署。 您可以使用 Azure 管道或工具。
    - 使用分支策略保护您的代码质量。
- 使用团队基础版本控制（包括代码评审）维护集中式版本控制。
- 使用 Xcode、Eclipse、IntelliJ、Android 工作室、视觉工作室、视觉工作室代码等连接到您的代码。
- 使用强大的语义代码搜索。
- 从企业就绪功能中获益。 Azure DevOps 与 Azure 活动目录具有本机集成，这简化了管理对代码存储库的访问的过程。
- 通过分支策略确保代码质量，例如代码评审的最小次数、成功生成的要求以及 Git 合并策略的实现。
- 连接您最喜爱的开发环境，以访问存储库和管理工作。

**引用**
- [开始使用 Azure 存储库](https://azure.microsoft.com/services/devops/repos/) 
- [Azure 重新生成文档](/azure/devops/repos/?view=azure-devops)