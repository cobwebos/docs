---
title: 如何使用 GitHub 应用 DevOps - LUIS
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 和 GitHub 应用 DevOps。
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 6df65040277ac61cca5fb4bf7fce5b5a7b2f3afe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84783757"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>使用 GitHub Actions 将 DevOps 应用于 LUIS 应用开发

请转到 [LUIS DevOps 模板存储库](https://github.com/Azure-Samples/LUIS-DevOps-Template)，以获取针对 LUIS 实现 DevOps 和软件工程最佳做法的完整解决方案。 可以使用此模板存储库创建自己的存储库，其中对于通过 LUIS 为自己的项目实现[源代码管理](luis-concept-devops-sourcecontrol.md)、[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)的 CI/CD 工作流和做法提供内置支持。

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps 模板存储库

[LUIS DevOps 模板存储库](https://github.com/Azure-Samples/LUIS-DevOps-Template)演练如何执行以下操作：

* 克隆模板存储库 - 将模板复制到自己的 GitHub 存储库。
* 配置 LUIS 资源 - [在 Azure 中创建 LUIS 创作和预测资源](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-resources-in-azure-cli)，这些资源将由持续集成工作流使用。
* 配置 CI/CD 工作流 - 配置 CI/CD 工作流的参数并将它们存储在 [GitHub 机密](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。
* 演练[“开发内部循环”](https://mitchdenny.com/the-inner-loop/) - 开发人员在开发分支中工作时对示例 LUIS 应用进行更新，测试更新，然后引发拉取请求来提议更改并寻求审批。
* 执行 CI/CD 工作流 - 使用 GitHub Actions 执行[持续集成工作流以生成和测试 LUIS 应用](luis-concept-devops-automation.md)。
* 执行自动测试 - 执行 [LUIS 应用的自动批处理测试](luis-concept-devops-testing.md)以评估应用的质量。
* 部署 LUIS 应用 - 执行[持续交付 (CD) 作业](luis-concept-devops-automation.md#continuous-delivery-cd)以发布 LUIS 应用。
* 将存储库用于自己的项目 - 说明如何将存储库用于自己的 LUIS 应用程序。

## <a name="next-steps"></a>后续步骤

* 使用 [LUIS DevOps 模板存储库](https://github.com/Azure-Samples/LUIS-DevOps-Template)将 DevOps 应用于自己的项目。
* [LUIS 的源代码管理和分支策略](luis-concept-devops-sourcecontrol.md)
* [LUIS DevOps 测试](luis-concept-devops-testing.md)
* [LUIS DevOps 的自动化工作流](luis-concept-devops-automation.md)
