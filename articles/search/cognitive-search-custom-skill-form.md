---
title: 表单识别器自定义技能 （C#）
titleSuffix: Azure Cognitive Search
description: 了解如何使用 C# 和可视化工作室创建表单识别器自定义技能。
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274570"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>示例：创建表单识别器自定义技能

在此 Azure 认知搜索技能集示例中，您将了解如何使用 C# 和 Visual Studio 创建表单识别器自定义技能。 表单识别器分析文档并提取键/值对和表数据。 通过将窗体识别器包装到[自定义技能界面](cognitive-search-custom-skill-interface.md)中，可以将此功能添加为端到端扩充管道中的步骤。 然后，管道可以加载文档并执行其他转换。

## <a name="prerequisites"></a>先决条件

- [视觉工作室 2019](https://visualstudio.microsoft.com/downloads/) （任何版本）.
- 至少五种相同类型的形式。 您可以使用本指南提供的示例数据。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>训练模型

在使用此技能之前，您需要使用输入表单训练表单识别器模型。 按照[cURL 快速入门](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract)了解如何训练模型。 您可以使用该快速入门中提供的示例表单，也可以使用您自己的数据。 训练模型后，将其 ID 值复制到安全位置。

## <a name="set-up-the-custom-skill"></a>设置自定义技能

本教程在[Azure 搜索电源技能](https://github.com/Azure-Samples/azure-search-power-skills)GitHub 存储库中使用[分析窗体](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm)项目。 将此存储库克隆到本地计算机，并导航到**Vision/AnalyticsForm/** 以访问项目。 然后在视觉工作室中打开_AnalyticsForm.csproj。_ 此项目创建一个 Azure 函数资源，该资源满足[自定义技能界面](cognitive-search-custom-skill-interface.md)，并可用于 Azure 认知搜索扩充。 它将形式文档作为输入，并输出（作为文本）您指定的键/值对。

首先，添加项目级环境变量。 在左侧窗格中查找**AnalyticsForm**项目，右键单击它并选择 **"属性**"。 在"**属性"** 窗口中，单击 **"调试"** 选项卡，然后查找 **"环境变量"** 字段。 单击 **"添加**"可添加以下变量：
* `FORMS_RECOGNIZER_ENDPOINT_URL`将值设置为终结点 URL。
* `FORMS_RECOGNIZER_API_KEY`将值设置为订阅密钥。
* `FORMS_RECOGNIZER_MODEL_ID`将值设置为您训练的模型的 ID。
* `FORMS_RECOGNIZER_RETRY_DELAY`值设置为 1000。 此值是程序在重试查询之前将等待的时间（以毫秒为单位）。
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`将值设置为 100。 此值是程序在尝试成功响应时查询服务的次数。

接下来，打开_AnalyzeForm.cs_并查找引用`fieldMappings`*字段映射.json*文件的变量。 此文件（以及引用它的变量）定义要从窗体中提取的键列表以及每个键的自定义标签。 例如`{ "Address:", "address" }, { "Invoice For:", "recipient" }`，值表示脚本将仅保存`Address:`检测到的值和`Invoice For:`字段，并且它将分别用`"address"`和`"recipient"`标记这些值。

最后，请注意变量`contentType`。 此脚本在 URL 引用的远程文档上运行给定的表单识别器模型，因此内容类型为`application/json`。 如果要通过在 HTTP 请求中包括其字节流来分析本地文件，则需要将 更改为`contentType`文件的相应[MIME 类型](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types)。

## <a name="test-the-function-from-visual-studio"></a>从 Visual Studio 中测试函数

编辑项目后，请保存该项目，并将**AnalyticsForm**项目设置为 Visual Studio 中的启动项目（如果尚未设置）。 然后按**F5**在本地环境中运行该函数。 使用 REST 服务（如[Postman）](https://www.postman.com/)调用函数。

### <a name="http-request"></a>HTTP 请求

您将发出以下请求来调用函数。

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>请求正文

从下面的请求正文模板开始。

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

在这里，您需要提供与您训练的窗体类型相同的窗体的 URL。 出于测试目的，您可以使用其中一个培训表单。 如果遵循 cURL 快速启动，则窗体将位于 Azure Blob 存储帐户中。 打开 Azure 存储资源管理器，查找表单文件，右键单击它，然后选择 **"获取共享访问签名**"。 下一个对话框窗口将提供 URL 和 SAS 令牌。 分别在请求正文`"formUrl"`和`"formSasToken"`字段中输入这些字符串。

> [!div class="mx-imgBorder"]
> ![Azure 存储资源管理器;已选择 pdf 文档](media/cognitive-search-skill-form/form-sas.png)

如果要分析不在 Azure Blob 存储中的远程文档，请将其 URL 粘贴到`"formUrl"`字段中，并将`"formSasToken"`该字段留空。

> [!NOTE]
> 当技能集成到技能集中时，URL 和令牌将由认知搜索提供。

### <a name="response"></a>响应

你应该看到类似于以下示例的响应：

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>将函数发布到 Azure

当您对函数行为感到满意时，可以发布它。

1. 在可视化工作室**中的解决方案资源管理器**中，右键单击项目并选择 **"发布**"。 选择 **"创建新** > **发布**"。
1. 如果尚未将 Visual Studio 连接到 Azure 帐户，请选择“添加帐户...”****
1. 请按照屏幕上的提示操作。 为应用服务、Azure 订阅、资源组、托管计划和要使用的存储帐户指定唯一名称。 如果尚未创建新的资源组、新的托管计划和新的存储帐户，则可以创建新的资源组、新的托管计划和新的存储帐户。 完成后，选择“创建”****。
1. 部署完成后，请记下站点 URL。 此 URL 是 Azure 中函数应用的地址。 将其保存到临时位置。
1. 在[Azure 门户](https://portal.azure.com)中，导航到资源组，并查找发布的`AnalyzeForm`函数。 在“管理”部分下，应可看到主机密钥****。 复制*默认*主机密钥并将其保存到临时位置。

## <a name="connect-to-your-pipeline"></a>连接到管道

要在认知搜索管道中使用此技能，您需要向技能集添加技能定义。 以下 JSON 块是一个示例技能定义（应更新输入和输出以反映您的特定方案和技能集环境）。 替换为`AzureFunctionEndpointUrl`函数 URL，然后替换为`AzureFunctionDefaultHostKey`主机键。

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

在本指南中，您从 Azure 窗体识别器服务创建了自定义技能。 要了解有关自定义技能的更多信息，请参阅以下资源。 

* [Azure 搜索电源技能：自定义技能的存储库](https://github.com/*zure-Samples/azure-search-power-skills)
* [向 AI 扩充管道添加自定义技能](cognitive-search-custom-skill-interface.md)
* [定义技能组合](cognitive-search-defining-skillset.md)
* [创建技能集 （REST）](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [映射富集字段](cognitive-search-output-field-mapping.md)
