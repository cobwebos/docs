---
title: 更改、训练应用，C# - LUIS
titleSuffix: Azure Cognitive Services
description: 此 C# 快速入门将示例话语添加到家庭自动化应用并训练该应用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 55251a553921e947e7c6522677e51e4eb1d3fa5b
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619951"
---
# <a name="quickstart-change-model-using-c"></a>快速入门：使用 C# 更改模型

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* 最新的 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)。
* C# 编程语言已安装。
* [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) 和 [CommandLine](https://www.nuget.org/packages/CommandLineParser/) NuGet 包

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>创建快速入门代码 

在 Visual Studio 中，使用 .NET Framework 创建新的 **Windows 经典桌面控制台**应用。 将项目命名为 `ConsoleApp1`。

![Visual Studio 项目类型](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>添加 System.Web 依赖项

Visual Studio 项目需要 **System.Web**。 在解决方案资源管理器中，右键单击“引用”并从“程序集”部分中选择“添加引用”。  

![添加 System.web 引用](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>添加其他依赖项

Visual Studio 项目需要 **JsonFormatterPlus** 和 **CommandLineParser**。 在“解决方案资源管理器”中，右键单击“引用”，并选择“管理 NuGet 包...”   。浏览并添加这两个包中的每一个包。 

![添加第三方依赖项](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>编写 C# 代码
**Program.cs** 文件应为：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

更新依赖项，以便：

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


将 LUIS ID 和字符串添加到 **Program** 类。

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

向 **Program** 类添加用于管理命令行参数的类。

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

将 GET 请求方法添加到 **Program** 类。

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


将 POST 请求方法添加到 **Program** 类。 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

将示例话语从文件方法添加到 **Program** 类。

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

将更改应用到模型以后，训练该模型。 将方法添加到 **Program** 类。

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

训练可能不会立刻完成，请通过查看状态来验证训练是否已完成。 将方法添加到 **Program** 类。

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

若要管理命令行参数，请添加 main 代码。 将方法添加到 **Program** 类。

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>将 utterances.json 复制到输出目录

在解决方案资源管理器中，通过右键单击解决方案资源管理器的项目名称，然后依次选择“添加”  、“现有项”  ，添加 `utterances.json`。 选择 `utterances.json` 文件。 这会将文件添加到项目。 然后需要将它添加到输出目录。 右键单击 `utterances.json` 并选择“属性”  。 在属性窗口中，标记 `Content` 的“生成操作”，并标记 `Copy Always` 的“复制到输出目录”。    

![将 JSON 文件标记为内容](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>生成代码

在 Visual Studio 中生成代码。 

## <a name="run-code"></a>运行代码

在项目的 /bin/Debug 目录中，从命令行运行应用程序。 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

此命令行显示调用“添加陈述”API 的结果。 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>清理资源
完成本快速入门后，删除在本快速入门中创建的所有文件。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [以编程方式生成 LUIS 应用](luis-tutorial-node-import-utterances-csv.md) 
