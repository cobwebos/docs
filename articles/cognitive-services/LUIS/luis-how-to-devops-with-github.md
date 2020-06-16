---
title: 如何将 DevOps 应用于 GitHub-LUIS
titleSuffix: Azure Cognitive Services
description: 将 DevOps 与语言理解（LUIS）和 GitHub 结合使用。
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
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783757"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>使用 GitHub 操作将 DevOps 应用到 LUIS 应用开发

请参阅[LUIS DevOps 模板](https://github.com/Azure-Samples/LUIS-DevOps-Template)存储库，获取用于实现 LUIS 的 DevOps 和 software 工程最佳实践的完整解决方案。 你可以使用此模板存储库来创建自己的存储库，并提供对 CI/CD 工作流的内置支持，并为自己的[项目启用使用](luis-concept-devops-sourcecontrol.md)LUIS 的[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)。

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps 模板存储库

[LUIS DevOps 模板](https://github.com/Azure-Samples/LUIS-DevOps-Template)存储库介绍如何执行以下操作：

* **克隆模板**存储库-将模板复制到你自己的 GitHub 存储库。
* **配置 LUIS 资源**-在 Azure 中创建将由持续集成工作流使用的[LUIS 创作和预测资源](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-resources-in-azure-cli)。
* **配置 ci/cd 工作流**-配置 CI/cd 工作流的参数并将其存储在[GitHub 密码](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。
* **通过["开发内部循环"](https://mitchdenny.com/the-inner-loop/) ** ，开发人员可以在开发分支中工作时对示例 LUIS 应用进行更新，测试更新，然后引发拉取请求来提议更改并寻求审核批准。
* **执行 CI/CD 工作流**-执行[持续集成工作流，以使用 GitHub 操作生成和测试 LUIS 应用](luis-concept-devops-automation.md)。
* **执行自动测试**-对[LUIS 应用执行自动批处理测试](luis-concept-devops-testing.md)，以评估应用的质量。
* **部署 LUIS 应用**-执行[持续交付（CD）作业](luis-concept-devops-automation.md#continuous-delivery-cd)以发布 LUIS 应用。
* **使用存储库和自己的项目**-介绍如何将存储库用于自己的 LUIS 应用程序。

## <a name="next-steps"></a>后续步骤

* 使用[LUIS DevOps 模板](https://github.com/Azure-Samples/LUIS-DevOps-Template)存储库将 DevOps 应用到自己的项目。
* [LUIS 的源代码管理和分支策略](luis-concept-devops-sourcecontrol.md)
* [LUIS DevOps 的测试](luis-concept-devops-testing.md)
* [LUIS DevOps 的自动化工作流](luis-concept-devops-automation.md)
