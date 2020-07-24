---
title: LUIS 应用的连续工作流
description: 如何为语言理解（LUIS）实现 DevOps 的 CI/CD 工作流。
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 4cedf0cbe3f9ab675c191f8e4639688bb7916ee0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072999"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>LUIS DevOps 的持续集成和持续交付工作流

正在开发语言理解（LUIS）应用的软件工程师可以在[源代码](luis-concept-devops-sourcecontrol.md)管理、[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)方面应用 DevOps 做法。 本文介绍实现 LUIS 自动生成的相关概念。

## <a name="build-automation-workflows-for-luis"></a>LUIS 的生成自动化工作流

![CI 工作流](./media/luis-concept-devops-automation/luis-automation.png)

在源代码管理（SCM）系统中，将自动生成管道配置为在以下事件上运行：

1. 引发[拉取请求](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests)（PR）时触发的**PR 工作流**。 此工作流在更新合并到主分支*之前*，验证 PR 的内容。
1. 将更新推送到主分支时触发的**CI/CD 工作流**，例如，在合并 PR 的更改时触发。 此工作流可确保主分支的所有更新的质量。

**CI/CD 工作流**合并了两个补充开发过程：

* [持续集成](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration)（CI）是经常在共享存储库中提交代码并对其执行自动生成的工程实践。 与自动[测试](luis-concept-devops-testing.md)方法配对，持续集成使我们能够验证每个更新的 LUDown 源是否仍然有效，并且可以导入到 LUIS 应用中，同时还会通过一组测试来验证训练有素的应用是否可识别解决方案所需的意图和实体。

* [持续交付](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery)（CD）进一步增强了持续集成概念，以将应用程序自动部署到可以执行更深入的测试的环境。 CD 使我们能够及早了解我们所做的更改所带来的任何无法预料的问题，还可以了解我们的测试覆盖范围内的差距。

持续集成和持续交付的目标是确保 "主服务器始终可交付"。 对于 LUIS 应用程序，这意味着我们可以（如果需要）从 master branch LUIS 应用程序获取任何版本并将其发布到生产环境中。

### <a name="tools-for-building-automation-workflows-for-luis"></a>用于为 LUIS 生成自动化工作流的工具

可以使用不同的生成自动化技术来创建生成自动化工作流。 所有这些方法都要求您使用命令行接口（CLI）或 REST 调用来编写步骤的脚本，以便它们可以在生成服务器上执行。

使用以下工具为 LUIS 生成自动化工作流：

* [机器人 Framework TOOLS LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)可用于 LUIS 应用和版本，在 LUIS 服务中进行定型、测试和发布。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)查询 azure 订阅、提取 LUIS 创作和预测密钥以及创建用于自动化身份验证的 azure[服务主体](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)。

* [NLU。](https://github.com/microsoft/NLU.DevOps)用于[测试 LUIS 应用](luis-concept-devops-testing.md)并分析测试结果的 DevOps 工具。

### <a name="the-pr-workflow"></a>PR 工作流

如上所述，将此工作流配置为当开发人员引发 PR 以将更改从功能分支合并到主分支时运行。 其目的是在将 PR 合并到主分支之前，验证其更改的质量。

此工作流应：

* 通过在 PR 中导入源来创建临时 LUIS 应用 `.lu` 。
* 训练和发布 LUIS 应用版本。
* 对其运行所有[单元测试](luis-concept-devops-testing.md)。
* 如果所有测试均通过，则传递工作流，否则会失败。
* 清理并删除临时应用。

如果 SCM 支持，请配置分支保护规则，以便必须成功完成此工作流，才能完成 PR。

### <a name="the-master-branch-cicd-workflow"></a>主分支 CI/CD 工作流

将此工作流配置为在 PR 中的更新合并到主分支后运行。 其目的是通过测试更新来使主分支的质量栏更高。 如果更新满足质量要求，则此工作流将新的 LUIS 应用版本部署到可执行更深入的测试的环境。

此工作流应：

* 使用更新后的源代码在主要 LUIS 应用（为 master 分支维护的应用）中生成新版本。

* 训练和发布 LUIS 应用版本。

  > [!NOTE]
  > 正如在[自动生成工作流中运行测试](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow)中所述，你必须发布受测的 LUIS 应用版本，以便使用 NLU 等工具。DevOps 可以访问它。 LUIS 仅支持 LUIS 应用的两个命名发布槽，*过渡*和*生产*，但你也可以[直接发布版本](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)并[按版本进行查询](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-api-v3#changes-by-slot-name-and-version-name)。 使用自动化工作流中的直接版本发布，以免限制为使用命名发布槽。

* 运行所有[单元测试](luis-concept-devops-testing.md)。

* （可选）运行[批处理测试](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing)来测量 LUIS 应用版本的质量和准确性，并将其与某些基线进行比较。

* 如果测试已成功完成：
  * 标记存储库中的源。
  * 运行持续交付（CD）作业，将 LUIS 应用程序版本部署到环境中，以便进行进一步的测试。

### <a name="continuous-delivery-cd"></a>持续交付 (CD)

CI/CD 工作流中的 CD 作业在生成和自动单元测试成功时运行。 其工作是自动将 LUIS 应用程序部署到可以执行更多测试的环境。

不建议使用最佳解决方案来部署 LUIS 应用，并且必须实现适用于项目的过程。 [LUIS DevOps 模板](https://github.com/Azure-Samples/LUIS-DevOps-Template)存储库实现了一个简单的解决方案，用于将[新的 LUIS 应用版本发布](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)到*生产*发布槽。 这适用于简单的设置。 但是，如果您需要同时支持多个不同的生产环境（例如*开发*、*过渡*和*UAT*），则每个应用程序的两个命名发布槽的限制将不足。

用于部署应用版本的其他选项包括：

* 将应用版本保留为直接版本终结点，并实施一个过程来根据需要使用直接版本终结点配置下游生产环境。
* 为每个生产环境维护不同的 LUIS 应用程序，并编写自动化步骤，将其导入 `.lu` 到目标生产环境的 LUIS 应用程序的新版本中，进行定型和发布。
* 将已测试的 LUIS 应用版本导出到[LUIS docker 容器](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto?tabs=v3)，并将 LUIS 容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。

## <a name="release-management"></a>发布管理

通常，我们建议你仅对非生产环境（例如开发和过渡）进行持续交付。 大多数团队需要手动审查和批准过程来部署到生产环境。 对于生产部署，你可能想要确保在开发团队的关键人员可用于支持或在低流量期间执行此操作。

## <a name="next-steps"></a>后续步骤

* 了解如何[通过 GitHub 为 LUIS 实现 DevOps](luis-how-to-devops-with-github.md)
* 了解如何[使用 NLU 编写 GitHub 操作工作流。DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
