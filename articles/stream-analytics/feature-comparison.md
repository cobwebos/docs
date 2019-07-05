---
title: Azure Stream Analytics 功能比较
description: 本文比较了 Azure Stream Analytics 云连接和 Azure 门户、 Visual Studio 和 Visual Studio Code 中的 IoT Edge 作业支持的功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509776"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics 功能比较

借助 Azure Stream Analytics，你可以创建流式处理解决方案在云中和在使用 IoT Edge [Azure 门户](stream-analytics-quick-create-portal.md)， [Visual Studio](stream-analytics-quick-create-vs.md)，并[Visual Studio Code](quick-create-vs-code.md)。 这篇文章中的表显示这两种作业类型的每个平台支持的功能。

## <a name="cloud-job-features"></a>云作业功能


|Feature  |门户  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|跨平台     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|脚本创作     |是         |是         |是         |
|脚本 Intellisense     |语法突出显示         |语法突出显示</br>代码完成功能</br>错误标记         |语法突出显示</br>代码完成功能</br>错误标记         |
|定义输入、 输出和作业配置     |是         |是         |是         |
|Blob 输出分区     |是         |是         |是         |
|Power BI 作为输出     |是         |是         |否         |
|SQL 数据库引用数据     |是         |是         |是         |
|自定义消息属性     |是         |否         |否         |
|共享跨多个查询的输入和输出     |否         |是         |是         |
|JavaScript UDF 和 UDA     |是         |是         |仅 Windows         |
|机器学习的标注     |是的但查询无法进行测试        |是的但不能本地测试         |否         |
|兼容性级别     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|内置的基于机器学习的异常情况检测函数     |是         |是         |是         |
|内置地理空间函数     |是         |是         |是         |
|查询测试了一个示例文件     |是         |是         |是         |
|实时数据本地测试     |否         |是         |否         |
|列出作业并查看作业实体     |是         |是         |是         |
|将作业导出到本地项目     |否         |是         |是         |
|提交、 启动和停止作业     |是         |是         |是         |
|源代码管理     |否         |是         |是         |
|CI/CD 支持     |部分         |是         |是         |
|查看作业指标和关系图     |是         |是         |在门户中打开         |
|查看作业运行时错误     |是         |是         |否         |
|诊断日志     |是         |否         |否         |


## <a name="iot-edge-job-features"></a>IoT Edge 作业功能

|Feature  |门户  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|创作的作业     |是         |是         |否         |
|源代码管理     |否         |是         |否         |
|将作业导出到本地项目     |否         |是         |否         |
|查询测试了一个示例文件     |是         |是         |否         |
|共享跨多个查询的输入和输出     |否         |是         |否         |
|C# UDF     |否         |是         |否         |
|提交、 启动和停止作业     |是         |是         |否         |
|列出作业并查看作业实体     |是         |是         |否         |
|查看作业指标和关系图     |是         |部分         |否         |
|查看作业运行时错误     |是         |部分         |否         |
|CI/CD 支持     |否         |否         |否         |


## <a name="next-steps"></a>后续步骤

* [Azure IoT Edge 流分析](stream-analytics-edge.md)
* [教程：编写C#Azure Stream Analytics IoT Edge 作业 （预览版） 的用户定义函数](stream-analytics-edge-csharp-udf.md)
* [开发使用 Visual Studio 工具的 Stream Analytics IoT Edge 作业](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 Visual Studio 查看 Azure 流分析作业](stream-analytics-vs-tools.md)
* [了解 Azure Stream Analytics 使用 Visual Studio Code （预览版）](vscode-explore-jobs.md)


