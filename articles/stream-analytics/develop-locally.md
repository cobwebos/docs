---
title: 在本地开发和调试 Azure 流分析作业
description: 了解如何在 Azure 门户中运行 Azure 流分析作业之前，在本地计算机上开发和测试它们。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879838"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>在本地开发和调试 Azure 流分析作业

虽然您可以在 Azure 门户中创建和测试 Azure 流分析作业，但许多开发人员更喜欢本地开发体验。 流分析使使用您喜爱的代码编辑器和开发工具使用 Azure 事件中心、IoT 中心和 Blob 存储的实时事件流使用完全功能的单节点本地运行时轻松创建和测试作业。 还可以直接从本地开发环境向 Azure 提交作业。

## <a name="local-development-environments"></a>本地开发环境

在本地计算机上开发流分析作业的方式取决于您的工具首选项和功能可用性。 请参阅[Azure 流分析功能比较](feature-comparison.md)，了解每个开发环境支持哪些功能。

下表中的环境支持本地开发：

|环境                              |说明    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Visual Studio Code 的[Azure 流分析工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa)允许您使用丰富的 IntelliSense 和本机源代码控制，在本地和云中创作、管理和测试流分析作业。 支持 Linux、MacOS 和 Windows 上的开发。 要了解更多信息，请参阅[在可视化工作室代码中创建 Azure 流分析作业](quick-create-vs-code.md)。|
|[视觉工作室 2019](stream-analytics-tools-for-visual-studio-install.md) |流分析工具是可视化工作室中的 Azure 开发和数据存储和处理工作负载的一部分。 您可以使用 Visual Studio 编写自定义 C# 用户定义的函数和反序列化器。 要了解更多信息，请参阅[使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)。|
|[命令提示符或终端](stream-analytics-tools-for-visual-studio-cicd.md)|Azure 流分析 CI/CD NuGet 包为 Visual Studio 项目生成、任意计算机上的本地测试提供了工具。 Azure 流分析 CI/CD npm 包为任意计算机上的视觉工作室代码项目生成（生成 Azure 资源管理器模板）提供工具。|

## <a name="next-steps"></a>后续步骤

* [使用 Visual Studio Code 通过示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 通过实时流输入在本地测试流分析查询](visual-studio-code-local-run-live-input.md)
* [使用 Visual Studio 在本地测试流分析查询](stream-analytics-vs-tools-local-run.md)
* [使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)
