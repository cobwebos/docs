---
title: 自定义语音语音服务的 CI/CD
titleSuffix: Azure Cognitive Services
description: 将 DevOps 应用于自定义语音和 CI/CD 工作流。 为自己的项目实现现有的 DevOps 解决方案。
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: 463f5d5360e19fdd7f49139aea4c6dc65baf903c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081184"
---
# <a name="cicd-for-custom-speech"></a>自定义语音的 CI/CD

实现自动定型、测试和发布管理，以便在将更新应用到定型和测试数据时可以持续改进自定义语音模型。 通过有效实现 CI/CD 工作流，可以确保最佳执行自定义语音模型的端点始终可用。

[持续集成](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration)（CI）是经常在共享存储库中提交更新并对其执行自动生成的工程实践。 自定义语音的 CI 工作流可以从其数据源中为新模型定型，并对新模型执行自动测试，以确保其性能比之前的模型更好。

[持续交付](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery)（CD）从 CI 过程获取模型，并为每个改进的自定义语音模型创建一个终结点。 CD 可轻松地将终结点集成到解决方案中。

自定义 CI/CD 解决方案是可行的，但对于强大的预构建解决方案，请使用[Speech DevOps 模板存储库，该存储库](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)使用 GitHub 操作执行 CI/CD 工作流。

## <a name="cicd-workflows-for-custom-speech"></a>自定义语音的 CI/CD 工作流

这些工作流的目的是确保每个自定义语音模型具有比上一个版本更好的识别准确性。 如果对测试数据和/或定型数据的更新会提高准确性，则这些工作流将创建新的自定义语音终结点。

Git 和 Azure DevOps 等 git 服务器可以在发生特定 Git 事件时运行自动工作流，例如合并或拉取请求。 例如，将测试数据的更新推送到*主*分支时，可以触发 CI 工作流。 不同的 Git 服务器具有不同的工具，但将允许脚本命令行接口（CLI）命令，以便它们可以在生成服务器上执行。

在此过程中，工作流应命名并存储数据、测试、测试文件、模型和终结点，以便可以追溯到它们来自的提交或版本。 它还有助于命名这些资产，以便在更新测试数据和定型数据后，可以轻松地查看已创建的资产。

### <a name="ci-workflow-for-testing-data-updates"></a>用于测试数据更新的 CI 工作流

CI/CD 工作流的主要用途是使用定型数据构建一个新模型，并使用测试数据来测试该模型，以确定是否比以前的最佳执行模型（"基准模型"）对[单词错误率](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer)（WER）进行了改进。 如果新模型的性能更好，它将成为用于比较未来模型的新基准模型。

用于测试数据更新的 CI 工作流应重新测试当前的基准模型和更新的测试数据，以计算修改后的 WER。 这可确保在将新模型的 WER 与基准测试的 WER 进行比较时，这两种模型都已针对相同的测试数据进行了测试，并按类似方式进行比较。

此工作流应触发对测试数据的更新，并执行以下操作：

- 针对更新的测试数据测试基准模型。
- 使用更新后的数据来存储测试输出，其中包含用于测试的 WER。
- 这些测试的 WER 会成为新的基准 WER，以后的模型必须对其进行检测。
- CD 工作流不会对测试数据的更新执行。

### <a name="ci-workflow-for-training-data-updates"></a>用于定型数据更新的 CI 工作流

对定型数据的更新表示自定义模型的更新。

此工作流应触发对定型数据的更新，并执行以下操作：

- 使用更新的定型数据训练新模型。
- 针对测试数据测试新模型。
- 存储测试输出，其中包含 WER。
- 从基准模型将 WER 从新模型与 WER 进行比较。
- 如果 WER 未改善，则停止工作流。
- 如果 WER 提高，请执行 CD 工作流以创建自定义语音终结点。

### <a name="cd-workflow"></a>CD 工作流

在对定型数据进行更新后，将会自动执行 CD 工作流，以便为该模型创建新的终结点，并使该终结点可用于解决方案。

#### <a name="release-management"></a>发布管理

大多数团队需要手动审查和批准过程来部署到生产环境。 对于生产部署，你可能想要确保在开发团队的关键人员可用于支持或在低流量期间执行此操作。

### <a name="tools-for-custom-speech-workflows"></a>用于自定义语音工作流的工具

使用以下工具进行自定义语音的 CI/CD 自动化工作流：

- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)创建 azure 服务主体身份验证，查询 azure 订阅，并将测试结果存储在 azure Blob 中。
- [Azure 语音 CLI](https://github.com/msimecek/Azure-Speech-CLI) ，可通过命令行或自动工作流与语音服务进行交互。

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>使用 GitHub 操作的自定义语音的 DevOps 解决方案

有关自定义语音的已实现的 DevOps 解决方案，请转到[Speech DevOps 模板](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存储库。 创建模板的副本，并使用强大的 DevOps 系统（包括使用 GitHub 操作的测试、培训和版本控制）开始开发自定义模型。 存储库提供了示例测试和定型数据，以帮助安装和解释工作流。 初始安装后，请将示例数据替换为你的项目数据。

[Speech DevOps 模板](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存储库提供了基础结构和详细指导：

- 将模板存储库复制到 GitHub 帐户，并为 GitHub 操作 CI/CD 工作流创建 Azure 资源和[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。
- 演练 "[开发内部循环](https://mitchdenny.com/the-inner-loop/)"。 从功能分支更新定型和测试数据，使用临时开发模型测试更改，并引发拉取请求来建议和查看更改。
- 在向*master*的拉取请求中更新定型数据时，使用 GITHUB 操作 CI 工作流训练模型。
- 执行自动准确性测试以建立模型的[Word 错误率](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer)（WER）。 将测试结果存储在 Azure Blob 中。
- 执行 CD 工作流，以便在 WER 改善时创建终结点。

## <a name="next-steps"></a>后续步骤

详细了解 DevOps with Speech：

- 使用[Speech DevOps 模板](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存储库实现带有 GitHub 操作的自定义语音的 DevOps。
