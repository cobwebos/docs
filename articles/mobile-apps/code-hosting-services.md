---
title: 用 GitHub 和 Azure DevOps 在云中托管你的移动应用程序源代码
description: 了解如何通过 Microsoft 服务在云中托管移动应用程序代码。
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795622"
---
# <a name="cloud-hosted-source-code-management-services"></a>云托管的源代码管理服务
如果有多个团队成员在同一基本代码上工作的开发团队，则需要找到合适的位置来托管代码。 将数据存储在云中，使用集中式存储库，每个人都可以上传、编辑和管理代码文件，与其他开发人员进行交互，并使代码随时可随时访问，只要你具有 internet 连接。

云托管比本地选项要容易得多，因为它需要较少的硬件配置，并使组织能够以更灵活的方式完成实现过程。

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>在云中托管源代码的优点
- **集中式云存储**，可从任何位置查看和管理数据。
- 在团队中实现**更好的协作和更简洁的代码**，以跟踪代码并管理项目，以确保实现优秀软件的持续交付。
- **参与**并使你能够**轻松地加入**你的项目。
- **更快的发布周期**，因为团队可以更快、更**轻松地参与**项目。
- **降低成本**，无需担心如何维护自己的硬件、服务器、VPN 等。

使用以下服务在云中托管你的应用程序数据。

## <a name="github"></a>GitHub
[GitHub](https://github.com/)是一个开源存储库宿主服务，该服务承载多种不同编程语言的源代码项目，并跟踪对每个迭代进行的各种更改。

**关键功能**
- 在一个位置通过所有代码**托管代码**。 "专用"、"公共" 或 "开放源"，所有存储库都配备有工具来帮助你托管、版本和发布代码。
- 无缝**代码评审**和内置评审工具使代码评审成为任何团队过程的基本组成部分。
    - 保护分支、建议更改和请求评审。
    - 查看上下文中的差异和备注，并获得明确的反馈。
- 提前协调、保持一致，并通过 GitHub 的**项目管理工具**完成更多操作。
    - 查看项目的大图。
    - GitHub 中的代码旁边的**任务板**。
    - **拖放卡**，使你可以将问题或拉取请求分配给团队成员。
    - 用于组织和跟踪进度的**里程碑**。
    - 用于捕获可能有用但不属于特定问题或拉取请求的观点的**注释**。
- 通过从 **[GitHub Marketplace](https://github.com/marketplace)** 购买应用程序，可以轻松发现和选择合适的工具以实现更好的通信和自动化工作。
- **团队管理**，可轻松管理和扩大团队、**用户角色**，以帮助组织团队和访问权限、**讨论线索**工具来保持对话和团队重点，并使用**社区指导**快速设置使用帐户创建新的团队成员。
- **浏览**和**星型**项目以遵循这些项目。
- **网络，并**从行业中的其他人那里学习。

**参考**
- [GitHub](https://github.com/)
- [GitHub 指南](https://guides.github.com/)
- [GitHub 社区论坛](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/)支持[Azure Repos](https://azure.microsoft.com/services/devops/repos/)和[Team Foundation 版本控制（TFVC）](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops)作为源代码管理选项。 它拥有无限制的免费专用存储库，并提供协作代码评审、高级文件管理、代码搜索和分支策略，以确保高质量代码。 Azure Repos 非常适合小型项目以及需要本机 AAD 支持和高级策略的大型组织。
    
**关键功能**
- 公有和私有存储库的**云托管 Git 源代码存储库无限制**
    - 支持任何 Git 客户端。
    - Web 挂钩和 API 集成。
- 从存储库拉取请求开始**下一次生成**
    - 通过对每个更改使用线程化讨论和持续集成，协作生成更好的代码。
    - 使用 Azure Pipelines 或你的工具通过每个完成的拉取请求设置持续集成/持续交付 (CI/CD)，自动触发生成、测试和部署。
    - 通过分支策略保护代码质量。
- **具有 Team Foundation 版本控制的集中式版本控制**，包括代码评审。
- 使用 Xcode、Eclipse、IntelliJ、Android Studio、Visual Studio、Visual Studio Code 等**连接到你的代码**。
- **强大的语义代码搜索**。
- **企业就绪**，因为它与 AZURE ACTIVE DIRECTORY （AD）的本机集成，从而简化了管理对代码存储库的访问的过程。
- 通过分支策略**确保代码质量**，如最小代码评审数、需要成功的生成，以及强制 Git 合并策略。
- **连接你最喜爱的开发环境**以访问存储库和管理工作。

**参考**
- [Azure Repos 入门](https://azure.microsoft.com/services/devops/repos/) 
- [Azure Repos 文档](/azure/devops/repos/?view=azure-devops)