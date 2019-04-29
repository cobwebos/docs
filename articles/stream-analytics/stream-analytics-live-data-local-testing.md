---
title: 使用适用于 Visual Studio 的 Azure 流分析工具测试实时数据
description: 了解如何使用实时流数据在本地测试 Azure 流分析作业。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479734"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据（预览版）

借助适用于 Visual Studio 的 Azure 流分析工具，可使用 Azure 事件中心、IoT 中心和 Blob 存储中的实时事件流通过 IDE 在本地测试作业。 实时数据本地测试不能取代可在云中执行的[性能和可伸缩性测试](stream-analytics-streaming-unit-consumption.md)，但此测试可帮助在功能测试期间节省时间，因为无需在每次想要测试流分析作业时都提交到云中。 此功能处于预览状态，不应该用于生产工作负载。

## <a name="testing-options"></a>测试选项

支持以下本地测试选项：

|**输入**  |**输出**  |**作业类型**  |
|---------|---------|---------|
|本地静态数据   |  本地静态数据   |   云/边缘 |
|实时输入数据   |  本地静态数据   |   云 |
|实时输入数据   |  实时输出数据   |   云 |

## <a name="local-testing-with-live-data"></a>使用实时数据进行本地测试

1. 在 [Visual Studio 中创建 Azure 流分析云项目](stream-analytics-quick-create-vs.md)后，打开 **script.asaql**。 本地测试默认使用本地输入和本地输出。

   ![Azure 流分析 Visual Studio 本地输入和本地输出](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. 若要测试实时数据，请从下拉框中选择“使用云输入”。

   ![Azure 流分析 Visual Studio 实时云输入](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. 设置**开始时间**以定义作业何时开始处理输入数据。 作业可能需要提前读取输入数据来确保结果准确。 默认时间设置为当前时间之前的 30 分钟。

   ![Azure 流分析 Visual Studio 实时数据开始时间](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. 单击“在本地运行”。 系统将显示控制台窗口，其中包含运行进度和作业指标。 如果想要停止该进程，可以手动执行此操作。 

   ![Azure 流分析 Visual Studio 实时数据处理窗口](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   输出结果每三秒刷新一次，本地运行结果窗口中会显示前 500 行输出，输出文件则位于项目路径的 **ASALocalRun** 文件夹中。 还可以通过单击本地运行结果窗口中的“打开结果文件夹”按钮来打开输出文件。

   ![Azure 流分析 Visual Studio 实时数据打开结果文件夹](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. 如果要将结果输出到云输出接收器，请从第二个下拉框中选择“输出到云”。 Power BI 和 Azure Data Lake Storage 不是受支持的输出接收器。

   ![Azure 流分析 Visual Studio 实时数据输出到云端](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>限制

* 由于身份验证模型的限制，Power BI 和 Azure Data Lake Storage 不是受支持的输出接收器。

* 只有云输入选项具有[时间策略](stream-analytics-out-of-order-and-late-events.md)支持，本地输入选项不具备此支持。

## <a name="next-steps"></a>后续步骤

* [使用适用于 Visual Studio 的 Azure 流分析工具创建流分析作业](stream-analytics-quick-create-vs.md)
* [安装针对 Visual Studio 的 Azure 流分析工具](stream-analytics-tools-for-visual-studio-install.md)
* [使用 Visual Studio 在本地测试流分析查询](stream-analytics-vs-tools-local-run.md)
* [使用 Visual Studio 查看 Azure 流分析作业](stream-analytics-vs-tools.md)