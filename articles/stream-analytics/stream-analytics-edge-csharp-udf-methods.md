---
title: 为 Azure 流分析 Edge 作业（预览版）开发 .NET Standard 函数
description: 了解如何为流分析 Edge 作业编写 c# 用户定义函数。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 40035b946d0f2b09929f8c7f1ac27231546e6746
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692918"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>为 Azure 流分析 Edge 作业开发 .NET Standard 用户定义函数（预览版）

Azure 流分析提供类似 SQL 的查询语言，用于对事件数据流执行转换和计算。 其中有许多内置函数，但在某些复杂的情况下需要更大的灵活性。 通过使用 .NET Standard 用户定义函数 (UDF)，可以调用以任何 .NET Standard 语言（C#、F# 等）编写的自己的函数来扩展流分析查询语言。 UDF 可用于执行复杂的数学计算，使用 ML.NET 导入自定义 ML 模型，并对丢失的数据使用自定义插补逻辑。 流分析 Edge 作业的 UDF 功能目前处于预览状态，不应在生产工作负荷中使用。

## <a name="overview"></a>概述
Azure 流分析的 Visual Studio 工具可用于轻松编写 UDF、在本地（甚至脱机）测试作业，并将流分析作业发布到 Azure。 发布到 Azure 后，可以使用 IoT 中心将作业部署到 IoT 设备。

可通过三种方式实现 UDF：

* ASA 项目中的 CodeBehind 文件
* 来自本地项目的 UDF
* Azure 存储帐户中现有的包

## <a name="package-path"></a>包路径

任何 UDF 包的格式都具有路径 `/UserCustomCode/CLR/*`。 动态链接库 (DLL) 和资源被复制到 `/UserCustomCode/CLR/*` 文件夹下，有助于将系统中的用户 DLL 与 Azure 流分析 DLL 隔离开来。 无论使用何种方法来利用这些函数，此包路径都用于所有函数。

## <a name="supported-types-and-mapping"></a>支持的类型和映射

|**UDF 类型 (C#)**  |**Azure 流分析类型**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|字符串  |  nvarchar(max)   |
|dateTime  |  dateTime   |
|struct  |  IRecord   |
|对象  |  IRecord   |
|Array\<object>  |  IArray   |
|dictionary<string, object>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
可以在 Script.sql CodeBehind 中编写用户定义的函数  。 Visual Studio 工具会自动将 CodeBehind 文件编译为程序集文件。 将作业提交到 Azure 时，程序集将打包为 zip 文件并上传到存储帐户。 可以根据[流分析 Edge 作业的 UDF](stream-analytics-edge-csharp-udf.md) 教程执行操作，了解如何使用 CodeBehind 编写 C# UDF。 

## <a name="local-project"></a>本地项目
可在 Azure 流分析查询稍后引用的程序集中编写用户定义的函数。 这是复杂函数的推荐选项，它需要 .NET Standard 语言在其表达式语言之外的全部功能，例如过程逻辑或递归。 当需要在多个 Azure 流分析查询之间共享函数逻辑时，也可能会使用本地项目中的 UDF。 通过将 UDF 添加到本地项目，你可以从 Visual Studio 本地调试和测试函数。

引用本地项目：

1. 在解决方案中创建一个新的类库。
2. 在类中编写代码。 注意必须将类定义为公共，并且必须将对象定义为静态公共   。 
3. 生成项目。 工具会将 bin 文件夹中的所有项目打包为 zip 文件，并将该 zip 文件上传到存储帐户。 对于外部引用，请使用程序集引用而不是 NuGet 包。
4. 引用 Azure 流分析项目中的新类。
5. 在 Azure 流分析项目中添加一个新的函数。
6. 在作业配置文件 `JobConfig.json` 中配置程序集路径。 将程序集路径设置为“本地项目引用或 CodeBehind”  。
7. 重新生成函数项目和 Azure 流分析项目。  

### <a name="example"></a>示例

在本示例中，UDFTest 是一个 C# 类库项目，ASAEdgeUDFDemo 是 Azure 流分析 Edge 项目，它将引用 UDFTest    。

![Visual Studio 中的 Azure 流分析 IoT Edge 项目](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. 生成 C# 项目，通过该项目，可以从 Azure 流分析查询中添加对 C# UDF 的引用。
    
   ![在 Visual Studio 中生成 Azure 流分析 IoT Edge 项目](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. 在 ASA Edge 项目中添加对 C# 项目的引用。 右键单击“引用”节点，然后选择“添加引用”。

   ![在 Visual Studio 中添加对 C# 项目的引用](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. 从列表中选择 C# 项目名称。 
    
   ![从引用列表中选择你的 C# 项目名称。](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. 你会在“解决方案资源管理器”中看到“引用”下方列出的 UDFTest    。

   ![在解决方案资源管理器中查看用户定义的函数引用](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. 右键单击“Functions”文件夹，然后选择“新项”   。

   ![在 Azure 流分析 Edge 解决方案中向 Functions 添加新项](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. 将 C# 函数“SquareFunction.json”添加到 Azure 流分析项目中  。

   ![从 Visual Studio 的流分析 Edge 项选择 CSharp 函数](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. 双击“解决方案资源管理器”中的函数，以打开配置对话框  。

   ![Visual Studio 中的 C sharp 函数配置](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. 在 C# 函数配置中，选择“从 ASA 项目引用中加载”，并从下拉列表中选择相关的程序集、类和方法名称  。 要在流分析 Edge 查询中引用方法、类型和函数，必须将这些类定义为公共，并且必须将对象定义为静态公共   。

   ![流分析 C sharp 函数配置](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>现有包

可以在所选的任何 IDE 中编写 .NET Standard UDF，并从 Azure 流分析查询中调用它们。 首先编译代码并打包所有 DLL。 包的格式具有路径 `/UserCustomCode/CLR/*`。 然后，将 `UserCustomCode.zip` 上传到 Azure存储帐户中容器的根目录。

将程序集 zip 包上传到 Azure 存储帐户后，便可使用 Azure 流分析查询中的函数。 你只需将存储信息包含到流分析 Edge 作业配置中即可。 无法使用此选项在本地测试该函数，因为 Visual Studio 工具不会下载你的包。 包路径直接解析至服务。 

若要在作业配置文件 `JobConfig.json` 中配置程序集路径，请执行以下操作：

展开“用户定义的代码配置”部分，并使用以下建议值填写配置  ：

 |**设置**  |**建议的值**  |
 |---------|---------|
 |程序集源  | 云中的现有程序集包    |
 |Resource  |  选择当前帐户中的数据   |
 |订阅  |  选择订阅。   |
 |存储帐户  |  选择存储帐户。   |
 |容器  |  选择在存储帐户中创建的容器。   |

![Visual Studio 中的 Azure 流分析 Edge 作业配置](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>限制
UDF 预览目前有以下限制：

* .NET Standard 语言仅用于 Azure IoT Edge 流分析。 对于云作业，可以编写 JavaScript 用户定义的函数。 要了解详情，请访问 [Azure 流分析 JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) 教程。

* 只能在 Visual Studio 中编写 .NET Standard UDF 并将其发布到 Azure。 可以在 Azure 门户中的“Functions”下查看只读版本的 .NET Standard UDF  。 Azure 门户中不支持创建 .NET Standard 函数。

* 在门户中使用 .NET Standard UDF 时，Azure 门户查询编辑器显示一条错误。 

* 由于自定义代码与 Azure 流分析引擎共享上下文，因此自定义代码无法引用 namespace/dll_name 与 Azure 流分析代码发生冲突的任何内容。 例如，不能引用 Newtonsoft Json  。

## <a name="next-steps"></a>后续步骤

* [教程：为 Azure 流分析 Edge 作业（预览版）编写 C# 用户定义函数](stream-analytics-edge-csharp-udf.md)
* [教程：Azure 流分析 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [使用 Visual Studio 查看 Azure 流分析作业](stream-analytics-vs-tools.md)
