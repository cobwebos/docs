---
title: 使用 Visual Studio Code 通过示例数据在本地测试 Azure 流分析作业
description: 本文介绍如何使用适用于 Visual Studio Code 的 Azure 流分析工具在本地测试查询。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486464"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>使用 Visual Studio Code 通过示例数据在本地测试流分析查询

可以使用 Azure 流分析工具 Visual Studio Code 来使用示例数据在本地测试流分析作业。 你可以在项目的**LocalRunOutputs**文件夹中找到 JSON 文件中的查询结果。

## <a name="prerequisites"></a>必备组件

* 安装[.net CORE SDK](https://dotnet.microsoft.com/download)并重新启动 Visual Studio Code。

* 使用本[快速入门](quick-create-vs-code.md)教程了解如何使用 Visual Studio Code 创建流分析作业。

## <a name="prepare-sample-data"></a>准备示例数据

你需要先准备示例输入数据文件。 如果计算机上已有一些示例数据文件，则可以跳过此步骤并转到下一步。

1. 单击顶部行中的 "预览输入配置文件中的**数据**"。 某些输入数据将从 IoT 中心提取并显示在预览窗口中。 请注意，这可能需要一段时间。

2. 显示数据后，单击 "**另存为**" 将数据保存到本地文件。

 ![预览实时输入](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>定义本地输入

1. 在流分析项目的 "输入" 文件夹下，单击 "**输入"。** 然后从顶部行中选择 "**添加本地输入**"。

    ![从项目添加本地输入](./media/quick-create-vs-code/add-input-from-project.png)

    还可以使用**Ctrl + Shift + P**打开命令面板，并输入**ASA： Add Input**。

   ![在 VS Code 中添加流分析输入](./media/quick-create-vs-code/add-input.png)

2. 选择 "**本地输入**"。

    ![在 Visual Studio code 中添加 ASA 本地输入](./media/vscode-local-run/add-local-input.png)

3. 选择 " **+ 新本地输入**"。

    ![在 Visual Studio code 中添加新的 ASA 本地输入](./media/vscode-local-run/add-new-local-input.png)

4. 输入在查询中使用的相同输入别名。

    ![添加新的 ASA 本地输入别名](./media/vscode-local-run/new-local-input-alias.png)

5. 在新生成的**LocalInput_Input json**文件中，输入本地数据文件所在的文件路径。

    ![在 Visual Studio 中输入本地文件路径](./media/vscode-local-run/local-file-path.png)

6. 选择 "**预览数据**"，预览输入数据。 如果数据的 JSON 或 CSV，则会自动检测数据的序列化类型。 使用选择器以**表**或**原始**格式查看数据。 下表是**表格式**的数据示例：

     ![预览表格式的本地数据](./media/vscode-local-run/local-file-preview-table.png)

    下表是**原始格式**的数据示例：

    ![预览原始格式的本地数据](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>在本地运行查询

返回到查询编辑器，选择 "**本地运行**"。 然后从下拉列表中选择 "**使用本地输入**"。

![在查询编辑器中选择 "本地运行"](./media/vscode-local-run/run-locally.png)

![使用本地输入](./media/vscode-local-run/run-locally-use-local-input.png)

结果将显示在右侧的窗口中。 您可以单击 "**运行**" 以再次测试。 还可以选择 "**在文件夹中打开**"，以便在文件资源管理器中查看结果文件，并将其与其他工具一起打开。 请注意，结果文件仅以 JSON 格式提供。

![查看本地运行结果](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>后续步骤

* [使用 Visual Studio Code 在本地测试 Azure 流分析作业使用实时输入](visual-studio-code-local-run-live-input.md)

* [浏览 Visual Studio Code 的 Azure 流分析作业（预览）](visual-studio-code-explore-jobs.md)
