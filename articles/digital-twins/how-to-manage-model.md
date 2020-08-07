---
title: 管理自定义模型
titleSuffix: Azure Digital Twins
description: 请参阅如何创建、编辑和删除 Azure 数字孪生中的模型。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 64c7db4223fcb703272749b0bf8d5b1583fbb818
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987318"
---
# <a name="manage-azure-digital-twins-models"></a>管理 Azure 数字孪生模型

你可以使用[**DigitalTwinsModels api**](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[Azure 数字孪生 CLI](how-to-use-cli.md)来管理 azure 数字孪生实例知道的[模型](concepts-models.md)。 

管理操作包括上载、验证、检索和删除模型。 

## <a name="create-models"></a>创建模型

Azure 数字孪生的模型以 DTDL 编写，并保存为*json*文件。 还有一个可用于[Visual Studio Code](https://code.visualstudio.com/)的[DTDL 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)，提供语法验证和其他功能，便于编写 DTDL 文档。

假设某个医院要以数字方式表示其房间。 每个空间都包含一个智能 soap 分配器用于监视 washing，而传感器用于监视通过房间的流量。

解决该问题的第一步是创建模型来表示医院的各个方面。 此方案中的患者聊天室可能如下所述：

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> 这是一个 json 文件的示例正文，其中定义并保存了模型，以便作为客户端项目的一部分进行上载。 另一方面，REST API 调用采用类似于上面 (的模型定义的数组，该数组 `IEnumerable<string>` 在 .NET SDK) 中映射到。 因此，若要直接在 REST API 中使用此模型，请将其括在括号中。

此模型定义了患者房间的名称和唯一 ID，并定义了用于表示访问者计数和手动冲蚀状态 (这些计数器将从运动传感器和智能 soap dispensers 更新的属性，并将一起用于计算*handwash 百分比*属性) 。 该模型还定义了一个关系*hasDevices*，用于将基于此*房间*模型的任何[数字孪生](concepts-twins-graph.md)连接到实际设备。

按照此方法，你可以继续为医院的 wards、区域或医院本身定义模型。

### <a name="validate-syntax"></a>验证语法

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>利用 Api 管理模型。

以下部分说明了如何使用[Azure 数字孪生 api 和 sdk](how-to-use-apis-sdks.md)完成不同的模型管理操作。

> [!NOTE]
> 对于简洁起见，下面的示例不包含错误处理。 不过，强烈建议你在项目中将服务调用包装在 try/catch 块中。

> [!TIP] 
> 请记住，所有 SDK 方法均为同步和异步版本。 对于分页调用，异步方法会在 `AsyncPageable<T>` 同步版本返回时返回 `Pageable<T>` 。

### <a name="upload-models"></a>上载模型

创建模型后，可以将其上传到 Azure 数字孪生实例。

> [!TIP]
> 建议在将模型上传到 Azure 数字孪生实例之前，先对其进行验证。 你可以使用 "[*操作方法：分析和验证模型*](how-to-parse-models.md)" 中所述的[DTDL 客户端分析器库](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)和[DTDL 验证器示例](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)来检查模型，然后将它们上载到服务中。

准备好上载模型时，可以使用以下代码片段：

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

请注意，该 `CreateModels` 方法接受单个事务中的多个文件。 下面是一个示例，说明：

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

模型文件可以包含多个模型。 在这种情况下，需要将模型放入 JSON 数组。 例如：

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
上传时，模型文件由服务进行验证。

### <a name="retrieve-models"></a>检索模型

可以列出和检索存储在 Azure 数字孪生实例上的模型。 

下面是此选项的选项：
* 检索所有模型
* 检索单个模型
* 检索具有依赖关系的单个模型
* 检索模型的元数据

下面是一些示例调用：

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

用于检索模型所有返回对象的 API 调用 `ModelData` 。 `ModelData`包含有关存储在 Azure 数字孪生实例中的模型的元数据，例如名称、DTMI 和模型的创建日期。 `ModelData`对象也可以选择包含模型本身。 因此，根据参数，您可以使用检索调用来仅检索元数据，这在您想要显示可用工具的 UI 列表的情况下很有用 (例如) 或整个模型。

`RetrieveModelWithDependencies`调用不仅返回所请求的模型，而且还返回请求的模型所依赖的所有模型。

模型不一定精确地以其上传到的文档形式返回。 Azure 数字孪生仅保证返回窗体在语义上是等效的。 

### <a name="remove-models"></a>删除模型

还可以通过以下两种方式之一从服务中删除模型：
* **退役**：一旦模型停止使用后，就不能再使用它创建新的数字孪生。 已使用此模型的现有数字孪生不会受到影响，因此你仍可以使用属性更改、添加或删除关系等功能对其进行更新。
* **删除**：这将从解决方案中完全删除该模型。 使用此模型的任何孪生不再与任何有效模型关联，因此它们被视为根本没有模型。 你仍可以阅读这些孪生，但在将它们重新分配到另一个模型之前，将无法对其进行任何更新。

这些是单独的功能，它们不会相互影响，但它们可以一起使用来逐步删除模型。 

### <a name="decommissioning"></a>停止

下面是用于停止模型的代码：

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

模型的解除授权状态包含在 `ModelData` 模型检索 api 返回的记录中。

#### <a name="deletion"></a>删除

您可以一次删除实例中的所有模型，也可以单独执行此操作。

有关如何删除所有模型的示例，请下载教程中使用的示例应用[*：使用示例客户端应用浏览基础知识*](tutorial-command-line-app.md)。 *CommandLoop.cs*文件在函数中执行此 `CommandDeleteAllModels` 功能。

本部分的其余部分将删除模型删除详细信息，并演示如何针对单个模型执行此操作。

##### <a name="before-deletion-deletion-requirements"></a>删除之前：删除要求

通常，可以随时删除模型。

例外情况是其他模型依赖的模型，无论是使用 `extends` 关系还是组件。 例如，如果*ConferenceRoom*模型扩展了一个*房间*模型，并将*ACUnit*模型作为一个组件，则在*ConferenceRoom*删除这些引用之前，不能删除*房间*或*ACUnit* 。 

为此，可以更新依赖模型以删除依赖项，或者完全删除依赖模型。

##### <a name="during-deletion-deletion-process"></a>删除期间：删除过程

即使模型满足立即删除它的要求，您也可能需要先执行几个步骤，以避免孪生遗留的意外后果。 下面是一些可帮助你管理过程的步骤：
1. 首先，停止模型
2. 请等待几分钟，以确保服务已处理完停止前发送的任何最后一分钟的克隆创建请求
3. 按模型查询孪生，以查看所有正在使用现已停止使用的模型的孪生
4. 如果不再需要孪生，请将其删除，或根据需要将其修补为新模型。 您还可以选择保留它们，在这种情况下，在删除该模型后，它们就会变为孪生。 有关此状态的含义，请参阅下一节。
5. 等待几分钟，确保更改已 percolated
6. 删除模型 

若要删除模型，请使用此调用：
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>删除后：没有模型的孪生

删除某一模型后，使用该模型的任何数字孪生现在都被视为没有模型。 请注意，没有可为您提供此状态的所有孪生的列表的查询，不过您仍*可以*通过删除的模型来查询孪生，以了解受影响的孪生。

下面概述了在没有模型的情况孪生的情况。

你**可以**执行的操作：
* 查询克隆
* 读取属性
* 读取传出关系
* 添加和删除传入关系 (如中所示，其他孪生仍可形成*与*此克隆的关系) 
  - `target`关系定义中的仍可反映已删除模型的 DTMI。 没有定义的目标的关系也可以在此处工作。
* 删除关系
* 删除克隆

不**能**执行的操作：
* 编辑与 (相同的传出关系，并将*其与其他*孪生) 进行关系
* 编辑属性

##### <a name="after-deletion-re-uploading-a-model"></a>删除后：重新上传模型

在删除某一模型后，您可以稍后再决定使用与您删除的模型相同的 ID 上传一个新模型。 下面是在这种情况下会发生的情况。
* 从解决方案商店的角度来看，这与上传全新的模型相同。 该服务不记得已经上传过的旧服务。   
* 如果图表中存在引用已删除模型的任何剩余孪生，则它们将不再孤立;此模型 ID 再次有效，并具有新的定义。 但是，如果模型的新定义不同于已删除的模型定义，则这些孪生可能具有与删除的定义相匹配的属性和关系，并且与新的定义无效。

Azure 数字孪生不会阻止此状态，因此请小心地修补孪生，以确保它们通过模型定义开关保持有效。

## <a name="manage-models-with-cli"></a>用 CLI 管理模型

还可以使用 Azure 数字孪生 CLI 来管理模型。 有关命令，请参阅[*操作方法：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)。

## <a name="next-steps"></a>后续步骤

请参阅如何基于模型创建和管理数字孪生：
* [*操作说明：管理数字孪生*](how-to-manage-twin.md)