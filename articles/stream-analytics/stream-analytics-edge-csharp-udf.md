---
title: 在 Visual Studio 中为 Azure 流分析作业编写 C# 用户定义函数（预览）
description: 了解如何在 Visual Studio 中为流分析作业编写 C# 用户定义函数。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: d6cf420c8baceb243e8c4d70c8bcbc95ec626c3a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990245"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>教程：为 Azure 流分析作业编写 C# 用户定义函数（预览）

使用 Visual Studio 中创建的 C# 用户定义函数 (UDF)，使你能够使用自己的函数扩展 Azure 流分析查询语言。 可以重复使用现有代码（包括 DLL），并对 C# 使用数学或复杂逻辑。 可通过三种方式实现 UDF：流分析项目中的 CodeBehind 文件、本地 C# 项目的 UDF 或存储帐户中现有包的 UDF。 本教程使用 CodeBehind 方法来实现基本的 C# 函数。 流分析作业的 UDF 功能目前处于预览状态，不应在生产工作负荷中使用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 CodeBehind 创建 C# 用户定义函数。
> * 在本地测试流分析作业。
> * 将作业发布到 Azure。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已完成以下先决条件：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安装[适用于 Visual Studio 的流分析工具](stream-analytics-tools-for-visual-studio-install.md)以及“Azure 开发”或“数据存储或处理”工作负荷   。
* 如果要构建 IoT Edge 作业 (stream-analytics-tools-for-visual-studio-edge-jobs.md)，请参阅现有的 [流分析 Edge 开发指南]。

## <a name="create-a-container-in-your-azure-storage-account"></a>在 Azure 存储帐户中创建容器

创建的容器将用于存储编译的 C# 包。 如果创建 Edge 作业，此存储帐户也将用于将包部署到 IoT Edge 设备。 对每个流分析作业使用专用容器。 不支持对多个流分析 Edge 作业重复使用相同的容器。 如果存储帐户已具有现有的容器，则可以使用这些容器。 如果没有，则需要[新建容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>在 Visual Studio 中创建流分析项目

1. 启动 Visual Studio。

2. 选择“文件”>“新建”>“项目”  。

3. 在左侧的模板列表中，选择“流分析”，然后选择“Azure 流分析 Edge 应用程序”或“Azure 流分析应用程序”    。

4.  输入项目的**名称**、**位置**和**解决方案名称**，然后选择“确定”  。

    ![在 Visual Studio 中创建 Azure 流分析 Edge 项目](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>配置程序集包路径

1. 打开 Visual Studio 并导航到“解决方案资源管理器”  。

2. 双击作业配置文件 `EdgeJobConfig.json`。

3. 展开“用户定义的代码配置”部分，并使用以下建议值填写配置  ：

   |**设置**|建议的值 |
   |-------|---------------|
   |全局存储设置资源|选择当前帐户中的数据源|
   |全局存储设置订阅| <你的订阅>|
   |全局存储设置存储帐户| <你的存储帐户>|
   |自定义代码存储设置资源|选择当前帐户中的数据源|
   |自定义代码存储设置存储帐户|<你的存储帐户>|
   |自定义代码存储设置容器|<你的存储容器>|


## <a name="write-a-c-udf-with-codebehind"></a>使用 CodeBehind 编写 C# UDF
CodeBehind 文件是与单个 ASA 查询脚本关联的 C# 文件。 Visual Studio 工具会自动压缩 CodeBehind 文件并在提交后将其上传到 Azure 存储帐户。 必须将所有类定义为公共，并且必须将所有对象定义为静态公共   。

1. 在“解决方案资源管理器”中，展开 Script.asql 以查找 Script.asaql.cs CodeBehind 文件    。

2. 将此代码替换为以下示例：

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>实现 UDF

1. 在“解决方案资源管理器”中，打开 Script.asaql 文件   。

2. 将现有查询替换为以下内容：

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>本地测试

1. 下载[温度模拟器示例数据文件](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json)。

2. 在“解决方案资源管理器”中，展开“输入”，右键单击 Input.json，然后选择“添加本地输入”     。

   ![在 Visual Studio 中向流分析作业添加本地输入](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. 为下载的示例数据指定本地输入文件路径，然后单击“保存”  。

    ![Visual Studio 中流分析作业的本地输入配置](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. 在脚本编辑器中单击“本地运行”  。 本地运行成功保存输出结果后，按任意键即可以表格形式查看结果。 

    ![使用 Visual Studio 在本地运行 Azure 流分析作业](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. 还可选择“打开结果文件夹”，查看 JSON 和 CSV 格式的原始文件  。

    ![使用 Visual Studio 查看本地 Azure 流分析作业的结果](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>调试 UDF
可以在本地采用调试标准 C# 代码的方式调试 C# UDF。 

1. 在 C# 函数中添加断点。

    ![在 Visual Studio 中向流分析用户定义函数添加断点](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. 按 **F5** 开始调试。 程序按预期在断点处停止。

    ![查看流分析用户定义的函数调试结果](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>将作业发布到 Azure
在本地测试查询后，选择脚本编辑器中的“提交到 Azure”，将作业发布到 Azure  。

![将流分析 Edge 作业从 Visual Studio 中提交到 Azure](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>部署到 IoT Edge 设备
如果选择构建流分析 Edge 作业，现可将其部署为 IoT Edge 模块。 请按照 [IoT Edge 快速入门](https://docs.microsoft.com/azure/iot-edge/quickstart)创建 IoT 中心、注册 IoT Edge 设备并在设备上安装和启动 IoT Edge 运行时。 然后按照[部署作业](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job)教程进行操作，将流分析作业部署为 IoT Edge 模块。 

## <a name="next-steps"></a>后续步骤

在本教程中，你使用 CodeBehind 创建了一个简单的 C# 用户定义函数、将作业发布到了 Azure，并将作业部署到了 Azure 或 IoT Edge 设备。 

若要详细了解对流分析作业使用 C# 用户定义函数的不同方法，请继续阅读此文：

> [!div class="nextstepaction"]
> [为 Azure 流分析编写 C# 函数](stream-analytics-edge-csharp-udf-methods.md)
