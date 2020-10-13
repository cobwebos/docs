---
title: 使用 Visual Studio Code 通过示例数据在本地测试 Azure 流分析作业
description: 本文介绍了如何使用适用于 Visual Studio Code 的 Azure 流分析工具通过示例数据在本地测试查询。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: 0122e67714efb201584a52f1cb763528dbb0b925
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903791"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>使用 Visual Studio Code 通过示例数据在本地测试流分析查询

可使用针对 Visual Studio Code 的 Azure 流分析工具通过示例数据在本地测试流分析作业。 可以在项目的 **LocalRunOutputs** 文件夹中的 JSON 文件中找到查询结果。

## <a name="prerequisites"></a>先决条件

* 安装 [.NET core SDK](https://dotnet.microsoft.com/download) 并重启 Visual Studio Code。

* 使用此[快速入门](quick-create-visual-studio-code.md)了解如何使用 Visual Studio Code 创建流分析作业。

## <a name="prepare-sample-data"></a>准备示例数据

你需要先准备示例输入数据文件。 如果计算机上已有一些示例数据文件，则可以跳过此步骤并转到下一步。

1. 在你的输入配置文件中单击顶部行中的“预览数据”  。 将会从 IoT 中心提取一些输入数据，并将其显示在预览窗口中。 注意，这可能需要一些时间。

2. 在数据显示后，单击“另存为”  将数据保存到一个本地文件。

 ![预览实时输入](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>定义本地输入

1. 右键单击流分析项目中“输入”文件夹下的 **input.json**。 然后从顶部行中选择“添加本地输入”。 

    ![从项目中添加本地输入](./media/quick-create-visual-studio-code/add-input-from-project.png)

    你还可以使用 **Ctrl+Shift+P** 打开命令面板，然后输入 **ASA:Add Input**。

   ![在 VS Code 中添加流分析输入](./media/quick-create-visual-studio-code/add-input.png)

2. 选择“本地输入”  。

    ![在 Visual Studio code 中添加 ASA 本地输入](./media/vscode-local-run/add-local-input.png)

3. 选择“+ 新建本地输入”  。

    ![在 Visual Studio code 中添加新的 ASA 本地输入](./media/vscode-local-run/add-new-local-input.png)

4. 输入在查询中使用的相同输入别名。

    ![添加新的 ASA 本地输入别名](./media/vscode-local-run/new-local-input-alias.png)

5. 在新生成的 **LocalInput_Input.json** 文件中，输入本地数据文件所在的文件路径。

    ![在 Visual Studio 中输入本地文件路径](./media/vscode-local-run/local-file-path.png)

6. 选择“预览数据”  以预览输入数据。 如果数据的序列化类型为 JSON 或 CSV，系统会自动检测到该类型。 使用选择器查看“表”或“原始”格式的数据。   下表显示了采用“表”格式的数据示例： 

     ![以表格式预览本地数据](./media/vscode-local-run/local-file-preview-table.png)

    下表显示了“原始”格式的数据示例： 

    ![以原始格式预览本地数据](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>在本地运行查询

返回到查询编辑器，并选择“在本地运行”  。 然后从下拉列表中选择“使用本地输入”  。

![在查询编辑器中选择“在本地运行”](./media/vscode-local-run/run-locally.png)

![使用本地输入](./media/vscode-local-run/run-locally-use-local-input.png)

结果将显示在右侧的窗口中。 你可以单击“运行”  以再次测试。 还可以选择“在文件夹中打开”  以在文件资源管理器中查看结果文件，并进一步使用其他工具将其打开。 请注意，结果文件仅以 JSON 格式提供。

![查看本地运行结果](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>后续步骤

* [通过 Visual Studio Code，使用实时输入在本地测试 Azure 流分析作业](visual-studio-code-local-run-live-input.md)

* [使用 Visual Studio Code（预览版）浏览 Azure 流分析作业](visual-studio-code-explore-jobs.md)
