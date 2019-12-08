---
title: 教程 - 用于 Azure 流分析云作业的自定义 .NET 反序列化程序
description: 本教程演示如何使用 Visual Studio 为 Azure 流分析云作业创建自定义 .NET 反序列化程序。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: f5fa0a4398c904113dbce5d80844b42b6e775df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702436"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>教程：为 Azure 流分析自定义 .NET 反序列化程序

Azure 流分析[内置了对三种数据格式的支持](stream-analytics-parsing-json.md)：JSON、CSV 和 Avro。 借助自定义 .NET 反序列化程序，可以从其他格式（如[协议缓冲区](https://developers.google.com/protocol-buffers/)、[绑定](https://github.com/Microsoft/bond)以及用于云和边缘作业的其他用户定义的格式）读取数据。

本教程演示如何使用 Visual Studio 为 Azure 流分析云作业创建自定义 .NET 反序列化程序。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为协议缓冲区创建自定义反序列化程序。
> * 在 Visual Studio 中创建 Azure 流分析作业。
> * 将流分析作业配置为使用自定义反序列化程序。
> * 在本地运行流分析作业以测试自定义反序列化程序。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 或 [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)。 支持 Enterprise (Ultimate/Premium)、Professional、Community 版本。 不支持 Express 版本。

* [安装用于 Visual Studio 的流分析工具](stream-analytics-tools-for-visual-studio-install.md)或更新到最新版本。 支持以下 Visual Studio 版本：
   * Visual Studio 2015
   * Visual Studio 2017

* 在 Visual Studio 中打开 Cloud Explorer，然后登录到 Azure 订阅  。

* 在 Azure 存储帐户中创建容器。
你创建的容器将用于存储与流分析作业相关的资产。 如果存储帐户已具有现有的容器，则可以使用这些容器。 如果没有，则需要[新建容器](../storage/blobs/storage-quickstart-blobs-portal.md)。

## <a name="create-a-custom-deserializer"></a>创建自定义反序列化程序

1. 打开 Visual Studio 并选择“文件”>“新建”>“项目”  。 搜索“流分析”并选择“Azure 流分析自定义反序列化程序项目(.NET)”   。 为项目指定一个名称，例如 Protobuf 反序列化程序  。

   ![创建 Visual Studio dotnet 标准类库项目](./media/custom-deserializer/create-dotnet-library-project.png)

2. 在解决方案资源管理器中，右键单击“Protobuf 反序列化程序”项目，并从菜单中选择“管理 NuGet 包”   。 然后安装 Microsoft.Azure.StreamAnalytics 和 Google.Protobuf NuGet 包   。

3. 将 [MessageBodyProto 类](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) 和 [MessageBodyDeserializer 类](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)添加到项目。

4. 生成“Protobuf 反序列化程序”项目  。

## <a name="add-an-azure-stream-analytics-project"></a>添加 Azure 流分析项目

1. 在解决方案资源管理器中，右键单击“Protobuf 反序列化程序”解决方案，然后选择“添加”>“新项目”   。 在“Azure 流分析”>“流分析”下，选择“Azure 流分析应用程序”   。 将其命名为 ProtobufCloudDeserializer 并选择“确定”   。 

2. 右键单击 Azure 流分析项目 ProtobufCloudDeserializer 下的“引用”   。 在“项目”下添加“Protobuf 反序列化程序”   。 该程序将自动填充。

## <a name="configure-a-stream-analytics-job"></a>配置流分析作业

1. 双击“JobConfig.json”  。 使用默认配置，但以下设置除外：

   |设置|建议的值|
   |-------|---------------|
   |全局存储设置资源|选择当前帐户中的数据源|
   |全局存储设置订阅| <你的订阅>|
   |全局存储设置存储帐户| <你的存储帐户>|
   |自定义代码存储设置资源|选择当前帐户中的数据源|
   |自定义代码存储设置存储帐户|<你的存储帐户>|
   |自定义代码存储设置容器|<你的存储容器>|

2. 在“输入”下双击“Input.json”   。 使用默认配置，但以下设置除外：

   |设置|建议的值|
   |-------|---------------|
   |源|Blob 存储|
   |资源|选择当前帐户中的数据源|
   |Subscription|<你的订阅>|
   |存储帐户|<你的存储帐户>|
   |容器|<你的存储容器>|
   |事件序列化格式|其他（Protobuf、XML、专有…）|
   |资源|从 ASA 项目引用或 CodeBehind 加载|
   |CSharp 程序集名称|ProtobufDeserializer.dll|
   |类名|MessageBodyProto.MessageBodyDeserializer|
   |事件压缩类型|无|

3. 将以下查询添加到 Script.asaql 文件中  。

   ```sql
   SELECT * FROM Input
   ```

4. 下载[示例 protobuf 输入文件](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)。 在“输入”文件夹中右键单击“Input.json”，然后选择“添加本地输入”    。 然后双击“local_Input.json”并配置以下设置  ：

   |设置|建议的值|
   |-------|---------------|
   |输入别名|输入|
   |源类型|数据流|
   |事件序列化格式|其他（Protobuf、XML、专有…）|
   |CSharp 程序集名称|ProtobufDeserializer.dll|
   |类名|MessageBodyProto.MessageBodyDeserializer|
   |本地输入文件路径|<已下载的示例 protobuf 输入文件的文件路径>|

## <a name="execute-the-stream-analytics-job"></a>执行流分析作业

1. 打开“Script.asaql”并选择“在本地运行”   。

2. 观察“流分析本地运行结果”中的结果  。

已成功为流分析作业实现自定义反序列化程序！ 在本教程中，已在本地测试自定义反序列化程序。 对于实际数据，可以正确配置输入和输出。 然后将作业从 Visual Studio 提交到 Azure，以使用刚刚实现的自定义反序列化程序在云中运行作业。

## <a name="debug-your-deserializer"></a>调试反序列化程序

可以在本地采用调试标准 .NET 代码的方式调试 .NET 反序列化程序。 

1. 在函数中添加断点。

2. 按 **F5** 开始调试。 程序按预期在断点处停止。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以先停止它，等到以后需要时再重启它。 如果你不打算继续使用此作业，请使用以下步骤删除本教程中创建的所有资源：

1. 在 Azure 门户的左侧菜单中选择“资源组”  ，然后选择已创建资源的名称。  

2. 在资源组页上选择“删除”，在文本框中键入要删除的资源的名称，然后选择“删除”。  

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何为协议缓冲区输入序列化实现自定义 .NET 反序列化程序。 若要了解有关创建自定义反序列化程序的详细信息，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [为 Azure 流分析作业创建不同的 .NET 反序列化程序](custom-deserializer-examples.md)
