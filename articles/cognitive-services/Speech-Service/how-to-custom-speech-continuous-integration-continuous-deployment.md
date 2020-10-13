---
title: 自定义语音识别的 CI/CD - 语音服务
titleSuffix: Azure Cognitive Services
description: 应用具有自定义语音识别和 CI/CD 工作流的 DevOps。 为自己的项目实施现有的 DevOps 解决方案。
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: 46bdc314e7aa0002937e808d7982f43c8e725d6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357465"
---
# <a name="cicd-for-custom-speech"></a>自定义语音识别的 CI/CD

实施自动定型、测试和发布管理，以便在你应用对定型数据和测试数据的更新时，自定义语音识别模型可持续改进。 通过 CI/CD 工作流的有效实施，可确保性能最佳的自定义语音识别模型的终结点始终可用。

[持续集成](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration) (CI) 是经常在共享存储库中提交更新并对其执行自动生成的工程实践。 自定义语音识别的 CI 工作流可基于其数据源定型新模型，并对新模型执行自动测试，确保其性能比之前的模型更好。

[持续交付](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery) (CD) 从 CI 过程获取模型，并为每个改进的自定义语音识别模型创建一个终结点。 CD 使终结点可轻松地集成到解决方案中。

可使用自定义 CI/CD 解决方案，但对于可靠的预生成解决方案，请使用[语音 DevOps 模板存储库](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)，它将使用 GitHub Actions 执行 CI/CD 工作流。

## <a name="cicd-workflows-for-custom-speech"></a>自定义语音识别的 CI/CD 工作流

这些工作流的目的是确保每个自定义语音识别模型具有比上一个版本更好的识别准确性。 如果对测试数据和/或定型数据的更新提高了准确性，则这些工作流将创建新的自定义语音识别终结点。

GitHub 和 Azure DevOps 等 Git 服务器可在发生特定 Git 事件（例如合并或拉取请求）时运行自动工作流。 例如，测试数据的更新被推送到主分支时，可触发 CI 工作流。 不同的 Git 服务器具有不同的工具，但允许使用脚本命令行接口 (CLI) 命令，使命令可在生成服务器上执行。

在此过程中，工作流应存储数据、测试、测试文件、模型和终结点并对它们命名，以便可以追溯到它们的源提交或源版本。 对这些资产命名也很有帮助，这样便可轻松地查看哪些资产是在更新测试数据和定型数据之后创建的。

### <a name="ci-workflow-for-testing-data-updates"></a>测试数据更新的 CI 工作流

CI/CD 工作流的主要目的是使用定型数据生成一个新模型，并使用测试数据测试该模型，确定与之前性能最佳的模型（“基准模型”）相比，[字词错误率 (WER)](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer) 是否有所改进。 如果新模型的性能更佳，它将成为新的基准模型，将来的模型将与它进行比较。

测试数据更新的 CI 工作流应使用更新的测试数据重新测试当前的基准模型，计算修改后的 WER。 这可确保在将新模型的 WER 与基准模型的 WER 进行比较时，这两种模型均已针对相同的测试数据进行了测试，并且你正在进行同类比较。

此工作流应触发对测试数据的更新，并执行以下操作：

- 针对更新的测试数据测试基准模型。
- 使用更新的数据来存储测试输出，输出中包含基准模型的 WER。
- 从这些测试得出的 WER 将成为未来模型必须超越的新基准 WER。
- 对于测试数据的更新，不执行 CD 工作流。

### <a name="ci-workflow-for-training-data-updates"></a>定型数据更新的 CI 工作流

对定型数据的更新意味着对自定义模型的更新。

此工作流应触发对定型数据的更新，并执行以下操作：

- 使用更新的定型数据定型新模型。
- 针对测试数据测试新模型。
- 存储测试输出，其中包含 WER。
- 将新模型的 WER 与基准模型的 WER 进行比较。
- 如果 WER 未改进，则停止工作流。
- 如果 WER 得到改进，则执行 CD 工作流以创建自定义语音识别终结点。

### <a name="cd-workflow"></a>CD 工作流

在定型数据的更新提高了模型的识别能力后，CD 工作流应自动执行，为该模型创建一个新的终结点，并使该终结点可用，这样它便可在解决方案中使用。

#### <a name="release-management"></a>发布管理

大多数团队需要一个手动审查和批准过程才能实现对生产环境的部署。 对于生产部署，你可能希望确保它在开发团队中的关键人员可提供支持时或在低流量时期执行。

### <a name="tools-for-custom-speech-workflows"></a>自定义语音识别工作流工具

对自定义语音识别的 CI/CD 自动化工作流使用以下工具：

- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 用于创建 Azure 服务主体身份验证、查询 Azure 订阅并将测试结果存储在 Azure Blob 中。
- [Azure 语音 CLI](spx-overview.md) 用于从命令行或自动工作流与语音服务交互。

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>使用 GitHub Actions 的 DevOps 自定义语音识别解决方案

有关已实现的 DevOps 自定义语音识别解决方案，请转到[语音 DevOps 模板存储库](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)。 创建模板的副本，并通过可靠的 DevOps 系统开始开发自定义模型，包括使用 GitHub Actions 进行测试、定型和版本控制。 存储库提供了示例测试和定型数据，帮助设置和解释工作流。 初始设置后，将示例数据替换为你的项目数据。

[语音 DevOps 模板存储库](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)为执行以下操作提供了基础结构和详细指导：

- 将模板存储库复制到 GitHub 帐户，然后为 GitHub Actions CI/CD 工作流创建 Azure 资源和[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。
- 演练[开发内部循环](https://mitchdenny.com/the-inner-loop/)。 从功能分支更新定型数据和测试数据，使用临时开发模型测试更改，并提出拉取请求以提出更改意见并对此进行审查。
- 在对主分支的拉取请求中更新定型数据后，使用 GitHub Actions CI 工作流定型模型。
- 执行自动准确性测试，确定模型的 [字词错误率](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer) (WER)。 将测试结果存储在 Azure Blob 中。
- 若 WER 得到改进，则执行 CD 工作流以创建一个终结点。

## <a name="next-steps"></a>后续步骤

详细了解具有语音识别的 DevOps：

- 使用[语音 DevOps 模板存储库](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)，通过 GitHub Actions 实现自定义语音识别 DevOps。
