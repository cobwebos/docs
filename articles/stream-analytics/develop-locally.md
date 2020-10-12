---
title: 在本地开发和调试 Azure 流分析作业
description: 了解如何在本地计算机上开发和测试 Azure 流分析作业，然后在 Azure 门户中运行它们。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 797577ce976f71f41ece0d103bd0145775d48b56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885583"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>在本地开发和调试 Azure 流分析作业

尽管你可以在 Azure 门户中创建和测试 Azure 流分析作业，但许多开发人员更偏爱本地开发体验。 使用流分析，可以轻松地使用你最喜欢的代码编辑器和开发工具，使用完全正常运行的单节点本地运行时，通过 Azure 事件中心、IoT 中心和 Blob 存储中的实时事件流来创建和测试作业。 还可以直接从本地开发环境将作业提交到 Azure。

## <a name="local-development-environments"></a>本地开发环境

在本地计算机上开发流分析作业的方式取决于你的工具首选项和功能可用性。 请参阅 [Azure 流分析功能比较](feature-comparison.md)，了解每个开发环境支持哪些功能。

下表中的环境支持本地开发：

|环境                              |说明    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| 利用适用于 Visual Studio Code 的 [Azure 流分析工具扩展](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) ，可以通过丰富的 IntelliSense 和本机源代码管理，在本地和云中创作、管理和测试流分析作业。 支持在 Linux、MacOS 和 Windows 上进行开发。 若要了解详细信息，请参阅[在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)。 扩展还支持 [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) ，它是云托管的开发环境。|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |流分析工具是 Visual Studio 中 Azure 开发以及数据存储和处理工作负载的一部分。 可以使用 Visual Studio 编写自定义 C# 用户定义函数和反序列化程序。 若要了解详细信息，请参阅[使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)。|
|[命令提示符或终端](stream-analytics-tools-for-visual-studio-cicd.md)|Azure 流分析 CI/CD NuGet 包提供了用于在任意计算机上进行 Visual Studio 项目生成、本地测试的工具。 Azure 流分析 CI/CD npm 包提供了用于在任意计算机上进行 Visual Studio Code 项目生成（将生成 Azure 资源管理器模板）的工具。|

## <a name="next-steps"></a>后续步骤

* [通过 Visual Studio Code 使用示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 通过实时流输入在本地测试流分析查询](visual-studio-code-local-run-live-input.md)
* [使用 Visual Studio 在本地测试流分析查询](stream-analytics-vs-tools-local-run.md)
* [使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)
