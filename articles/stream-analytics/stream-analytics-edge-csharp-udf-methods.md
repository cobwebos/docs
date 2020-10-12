---
title: 为 Azure 流分析作业开发 .NET Standard 函数（预览版）
description: '了解如何为流分析作业编写 c # 用户定义函数。'
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/10/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9cf929a3a6f5b3752b030f449b3b24b2bdc941a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907317"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>为 Azure 流分析作业开发 .NET Standard 用户定义函数（预览版）

Azure 流分析提供类似 SQL 的查询语言，用于对事件数据流执行转换和计算。 其中有许多内置函数，但在某些复杂的情况下需要更大的灵活性。 通过使用 .NET Standard 用户定义函数 (UDF)，可以调用以任何 .NET Standard 语言（C#、F# 等）编写的自己的函数来扩展流分析查询语言。 UDF 可用于执行复杂的数学计算，使用 ML.NET 导入自定义 ML 模型，并对丢失的数据使用自定义插补逻辑。 流分析作业的 UDF 功能目前处于预览状态，不应在生产工作负荷中使用。

适用于云作业的 .NET 用户定义功能在中提供：
* 美国中西部
* 北欧
* 美国东部
* 美国西部
* 美国东部 2
* 西欧

如果你有兴趣在任何其他区域中使用此功能，则可以 [请求访问](https://aka.ms/ccodereqregion)。

## <a name="package-path"></a>包路径

任何 UDF 包的格式都具有路径 `/UserCustomCode/CLR/*`。 动态链接库 (DLL) 和资源被复制到 `/UserCustomCode/CLR/*` 文件夹下，有助于将系统中的用户 DLL 与 Azure 流分析 DLL 隔离开来。 无论使用何种方法来利用这些函数，此包路径都用于所有函数。

## <a name="supported-types-and-mapping"></a>支持的类型和映射

若要在 c # 中使用 Azure 流分析值，需要将其从一个环境封送到另一个环境。 针对 UDF 的所有输入参数进行封送处理。 每个 Azure 流分析类型在 c # 中都有对应的类型，如下表所示：

|**Azure 流分析类型** |**C# 类型** |
|---------|---------|
|bigint | long |
|FLOAT | double |
|nvarchar(max) | 字符串 |
|datetime | DateTime |
|记录 | Dictionary\<string, object> |
|Array | Object [] |

当需要将数据从 c # 封送到 Azure 流分析时，就会出现这种情况。 下表显示了受支持的类型：

|**C# 类型**  |**Azure 流分析类型**  |
|---------|---------|
|long  |  bigint   |
|double  |  FLOAT   |
|字符串  |  nvarchar(max)   |
|DateTime  |  dateTime   |
|struct  |  记录   |
|object  |  记录   |
|Object []  |  Array   |
|Dictionary\<string, object>  |  记录   |

## <a name="develop-a-udf-in-visual-studio-code"></a>在 Visual Studio Code 中开发 UDF

利用[Azure 流分析 Visual Studio Code 工具](quick-create-visual-studio-code.md)，可以轻松地编写 udf、在本地测试作业 (甚至脱机) ，以及将流分析作业发布到 Azure。

可通过两种方法在 Visual Studio Code 工具中实现 .NET standard Udf。

* 本地 Dll 中的 UDF
* 来自本地项目的 UDF

### <a name="local-project"></a>本地项目

可在 Azure 流分析查询稍后引用的程序集中编写用户定义的函数。 这是复杂函数的推荐选项，它需要 .NET Standard 语言在其表达式语言之外的全部功能，例如过程逻辑或递归。 当需要在多个 Azure 流分析查询之间共享函数逻辑时，也可能会使用本地项目中的 UDF。 向本地项目添加 Udf 使你能够在本地调试和测试函数。

引用本地项目：

1. 在本地计算机上创建新的 .NET standard 类库。
2. 在类中编写代码。 注意必须将类定义为公共，并且必须将对象定义为静态公共****。
3. 在 Azure 流分析项目中添加新的 CSharp 函数配置文件并引用 CSharp 类库项目。
4. 在作业配置文件 "CustomCodeStorage" 部分中配置程序集路径 `JobConfig.json` 。 **CustomCodeStorage**本地测试不需要执行此步骤。

### <a name="local-dlls"></a>本地 Dll

还可以引用包含用户定义函数的本地 Dll。

### <a name="example"></a>示例

在此示例中， **CSharpUDFProject** 是一个 c # 类库项目， **ASAUDFDemo** 是 Azure 流分析项目，它将引用 **CSharpUDFProject**。

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-demo.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 " **添加项**"。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

2. 向 Azure 流分析项目添加 c # 函数 **SquareFunction** 。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-2.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-name.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 " **方法** "，从下拉列表中选择相关的类和方法名称。 要在流分析查询中引用方法、类型和函数，必须将这些类定义为公共，并且必须将对象定义为静态公共****。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-project.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 " **方法** "，从下拉列表中选择相关的类和方法名称。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-dll.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 " **从你的订阅中选择** "，选择你的订阅，并从下拉列表中选择存储帐户和容器名称。 将 **路径** 保留为默认值。 本地测试不需要执行此步骤。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-configure-storage-account.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

## <a name="develop-a-udf-in-visual-studio"></a>在 Visual Studio 中开发 UDF

可以通过三种方法在 Visual Studio 工具中实现 Udf。

* ASA 项目中的 CodeBehind 文件
* 来自本地项目的 UDF
* Azure 存储帐户中现有的包

### <a name="codebehind"></a>CodeBehind

可以在 Script.sql CodeBehind 中编写用户定义的函数****。 Visual Studio 工具会自动将 CodeBehind 文件编译为程序集文件。 将作业提交到 Azure 时，程序集将打包为 zip 文件并上传到存储帐户。 可以根据[流分析 Edge 作业的 UDF](stream-analytics-edge-csharp-udf.md) 教程执行操作，了解如何使用 CodeBehind 编写 C# UDF。 

### <a name="local-project"></a>本地项目

在 Visual Studio 中引用本地项目：

1. 在解决方案中创建新的 .NET standard 类库
2. 在类中编写代码。 注意必须将类定义为公共，并且必须将对象定义为静态公共****。 
3. 生成你的项目。 工具会将 bin 文件夹中的所有项目打包为 zip 文件，并将该 zip 文件上传到存储帐户。 对于外部引用，请使用程序集引用而不是 NuGet 包。
4. 引用 Azure 流分析项目中的新类。
5. 在 Azure 流分析项目中添加一个新的函数。
6. 在作业配置文件 `JobConfig.json` 中配置程序集路径。 将程序集路径设置为“本地项目引用或 CodeBehind”****。
7. 重新生成函数项目和 Azure 流分析项目。  

### <a name="example"></a>示例

在本示例中，UDFTest 是一个 C# 类库项目，ASAUDFDemo 是 Azure 流分析项目，它将引用 UDFTest************。

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

1. 生成 C# 项目，通过该项目，可以从 Azure 流分析查询中添加对 C# UDF 的引用。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

2. 在 ASA 项目中添加对 C# 项目的引用。 右键单击“引用”节点，然后选择“添加引用”。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

3. 从列表中选择 C# 项目名称。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

4. 你会在“解决方案资源管理器”中看到“引用”下方列出的 UDFTest************。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

5. 右键单击“Functions”文件夹，然后选择“新项”********。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

6. 将 C# 函数“SquareFunction.json”添加到 Azure 流分析项目中****。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

7. 双击“解决方案资源管理器”中的函数，以打开配置对话框****。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

8. 在 C# 函数配置中，选择“从 ASA 项目引用中加载”，并从下拉列表中选择相关的程序集、类和方法名称****。 要在流分析查询中引用方法、类型和函数，必须将这些类定义为公共，并且必须将对象定义为静态公共****。

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png" alt-text="Visual Studio Code 中的 Azure 流分析项目&quot;:::

下面的 UDF 包含一个函数，该函数将整数相乘以生成整数的平方。 类必须定义为 *公共* ，并且必须将对象定义为 *静态公共*。

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

以下步骤演示如何将 c # UDF 函数添加到流分析项目。

1. 右键单击 &quot; **函数** &quot; 文件夹，然后选择 ":::

## <a name="existing-packages"></a>现有包

可以在所选的任何 IDE 中编写 .NET Standard UDF，并从 Azure 流分析查询中调用它们。 首先编译代码并打包所有 DLL。 包的格式具有路径 `/UserCustomCode/CLR/*`。 然后，将 `UserCustomCode.zip` 上传到 Azure存储帐户中容器的根目录。

将程序集 zip 包上传到 Azure 存储帐户后，便可使用 Azure 流分析查询中的函数。 你只需将存储信息包含到流分析作业配置中即可。 无法使用此选项在本地测试该函数，因为 Visual Studio 工具不会下载你的包。 包路径直接解析至服务。 

若要在作业配置文件 `JobConfig.json` 中配置程序集路径，请执行以下操作：

展开“用户定义的代码配置”部分，并使用以下建议值填写配置：

   |**设置**|建议的值|
   |-------|---------------|
   |全局存储设置资源|选择当前帐户中的数据源|
   |全局存储设置订阅| <你的订阅>|
   |全局存储设置存储帐户| <你的存储帐户>|
   |自定义代码存储设置资源|选择当前帐户中的数据源|
   |自定义代码存储设置存储帐户|<你的存储帐户>|
   |自定义代码存储设置容器|<你的存储容器>|
   |自定义代码程序集源|云中的现有程序集包|
   |自定义代码程序集源|UserCustomCode.zip|

## <a name="user-logging"></a>用户日志记录

使用日志记录机制，可以在作业运行时捕获自定义信息。 您可以使用日志数据实时调试或评估自定义代码的正确性。

`StreamingContext`类可让你使用函数发布诊断信息 `StreamingDiagnostics.WriteError` 。 下面的代码显示了由 Azure 流分析公开的接口。

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` 作为输入参数传递到 UDF 方法，并可在 UDF 中使用以发布自定义日志信息。 在下面的示例中， `MyUdfMethod` 定义一个由查询提供的 **数据** 输入，并定义一个作为的 **上下文** 输入 `StreamingContext` ，由运行时引擎提供。 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

此 `StreamingContext` 值不需要由 SQL 查询传入。 如果存在输入参数，Azure 流分析会自动提供上下文对象。 不 `MyUdfMethod` 会更改的使用，如下面的查询所示：

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

可以通过 [诊断日志](data-errors.md)访问日志消息。

## <a name="limitations"></a>限制

UDF 预览目前有以下限制：

* 只能在 Visual Studio Code 或 Visual Studio 中创作 .NET Standard Udf，并将其发布到 Azure。 可以在 Azure 门户中的“Functions”下查看只读版本的 .NET Standard UDF****。 Azure 门户中不支持创建 .NET Standard 函数。

* 在门户中使用 .NET Standard UDF 时，Azure 门户查询编辑器显示一条错误。 

* 由于自定义代码与 Azure 流分析引擎共享上下文，因此自定义代码无法引用 namespace/dll_name 与 Azure 流分析代码发生冲突的任何内容。 例如，不能引用 Newtonsoft Json**。

* 项目中包含的支持文件会复制到用户自定义代码 zip 文件中，该文件在你将作业发布到云中时使用。 解压缩时，子文件夹中的所有文件都将直接复制到云中的用户自定义代码文件夹的根目录中。 解压缩时，zip 为 "平展"。

* 用户自定义代码不支持空文件夹。 不要将空文件夹添加到项目中的支持文件。

## <a name="next-steps"></a>后续步骤

* [教程：为 Azure 流分析作业编写 c # 用户定义函数 (预览) ](stream-analytics-edge-csharp-udf.md)
* [教程：Azure 流分析 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)