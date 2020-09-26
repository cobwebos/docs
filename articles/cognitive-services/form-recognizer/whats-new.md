---
title: 表单识别器的新增功能
titleSuffix: Azure Cognitive Services
description: 了解窗体识别器 API 的最新更改。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: 16bdb0d8ed421763895e61a6e1173fc7c5a48d4d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297940"
---
# <a name="whats-new-in-form-recognizer"></a>表单识别器的新增功能

表单识别器服务将持续更新。 使用本文可了解最新功能的增强功能、修复和文档更新。

## <a name="august-2020"></a>2020 年 8 月

### <a name="new-features"></a>新增功能

**窗体识别器2.1 版公共预览版现已推出。** Ws 2.1-已发布的 "预览版"，其中包括以下功能： 


- **REST API 引用可用** -请参阅 ws [2.1-preview. 1 引用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **除了英语以外**，还支持以下 [语言](language-support.md) ：对于 `Layout` 和 `Train Custom Model` ：英语 (`en`) ，中文 (简化)  () `zh-Hans` 、荷兰语 (`nl`) 、法语 () `fr` 、德语 (`de`) 、意大利语 (`it`) 、葡萄牙语 (`pt`) 和西班牙语 (`es`) 。
- **复选框/选择标记检测** -窗体识别器支持检测和提取选中标记（如复选框和单选按钮）。 选择标记是在中提取的 `Layout` ，你现在还可以在 "带标签的定型" 中标记和定型， `Train Custom Model`  -  _Train with Labels_以便提取选择标记的键值对。 
- **模型撰写** 允许使用单个模型 ID 编写和调用多个模型。 提交要使用组合模型 ID 分析的文档时，首先会执行分类步骤以将其路由到正确的自定义模型。 模型撰写可用于对 `Train Custom Model`  -  _标签进行定型_。
- **模型名称** 将友好名称添加到自定义模型，以便更轻松地进行管理和跟踪。
- **[新的智能卡预建模型](concept-business-cards.md)** ，用于提取英语、语言名片中的公共字段。
- 除 EN-US 外，**[预建收据的新区域设置](concept-receipts.md)** 现可用于 EN-US、en CA、EN-GB、en
- **质量改进** `Layout` ， `Train Custom Model`  -  _无标签定型_，并_为标签定型_。


v2.0**包含以下**更新：

- NET、Python、Java 和 JavaScript 的 [客户端库](quickstarts/client-library.md) 已输入公开上市。 


GitHub 上提供了**新的示例**。 
- [知识提取食谱-窗体操作手册](https://github.com/microsoft/knowledge-extraction-recipes-forms)从真实的窗体识别器客户约定中收集最佳实践，并提供用于开发这些项目的可用代码示例、清单和示例管道。 
- [示例标记工具](https://github.com/microsoft/OCR-Form-Tools)已更新，以支持新的2.1 版功能。 请参阅此 [快速入门](quickstarts/label-tool.md) ，了解如何开始学习此工具。 
- [智能展台](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md)窗体识别器示例显示了如何集成 `Analyze Receipt` 和 `Train Custom Model`  -  _定型无标签_。



## <a name="july-2020"></a>2020 年 7 月

### <a name="new-features"></a>新增功能

* **2.0 版本参考** 查看 v2.0 [API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) 和更新的适用于 [.net](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre?view=azure-dotnet)、 [Python](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)、 [Java](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview)和 [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)的 sdk。
* **表增强和提取增强** 功能包括准确性改进和表提取增强功能，尤其是在 _不带标签的自定义训练_中了解表标头和结构的功能。 

* **货币支持** 全局货币符号的检测和提取。
* **Azure Gov** 表单识别器现已在 Azure Gov 中提供。
* **增强的安全功能**： 
   * **自带密钥**  表单识别器会在保存到云时自动加密数据，以保护数据，并帮助你满足组织的安全性和符合性承诺。 默认情况下，订阅使用 Microsoft 托管的加密密钥。 你现在可以通过自己的加密密钥来管理你的订阅。 [客户管理的密钥 (CMK) ，也称为自带密钥 (BYOK) ](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest)，提供更高的灵活性来创建、轮换、禁用和撤消访问控制。 此外，你还可以审核用于保护数据的加密密钥。  
   * **专用终结点** –使你能够在虚拟网络 (VNet) [通过专用链接安全地访问数据。 ](https://docs.microsoft.com/azure/private-link/private-link-overview)


## <a name="june-2020"></a>2020 年 6 月

### <a name="new-features"></a>新增功能
* **COPYMODEL API 已添加到客户端 sdk** 你现在可以使用客户端 Sdk 将模型从一个订阅复制到另一个订阅。 有关此功能的常规信息，请参阅 [备份和恢复模型](./disaster-recovery.md) 。
* **Azure Active Directory 集成** 你现在可以使用 Azure AD 凭据来验证 Sdk 中的窗体识别器客户端对象。
* **SDK 特定的更改** 这包括次要功能添加和重大更改。 有关详细信息，请参阅 SDK 了解。
  * [C # SDK 预览版 3 changelog](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK 预览版 3 changelog](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK 预览版 3 changelog](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK 预览版 3 changelog](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-features"></a>新增功能
* **SDK 支持表单识别器 API V2.0 公共预览版** 本月，我们已将服务支持扩展为包含用于窗体识别器 v2.0 (预览) 版本的预览版 SDK。 使用以下链接开始使用你选择的语言： 
   * [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre?view=azure-dotnet)
   * [Java SDK](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview)
   * [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-python-preview)
   * [JavaScript SDK](https://docs.microsoft.com/javascript/api/overview/azure/ai-form-recognizer-readme-pre?view=azure-node-preview)

  新的 SDK 支持用于窗体识别器的 v2.0 REST API 的所有功能。 例如，你可以使用或不带标签来训练模型，并从窗体提取文本、键值对和表、从收据中提取数据以及预先生成的回执服务，并从文档中提取带有布局服务的文本和表。 可以通过 [Sdk 反馈窗体](https://aka.ms/FR_SDK_v1_feedback)在 sdk 上共享你的反馈。
 
* **复制自定义模型** 你现在可以使用新的复制自定义模型功能在区域和订阅之间复制模型。 在调用复制自定义模型 API 之前，必须首先通过对目标资源终结点调用复制授权操作来获取要复制到目标资源的授权。
   * [生成复制授权](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
   * [复制自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>安全性改进

* 客户托管的密钥现在可用于 FormRecognizer。 有关详细信息，请参阅 [窗体识别器的静态数据加密](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest)。
* 使用托管标识通过 Azure Active Directory 访问 Azure 资源。 有关详细信息，请参阅 [授予对托管标识的访问权限](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities)。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新增功能

* **用于标记的值类型** 你现在可以指定用窗体识别器示例标记工具标记的值的类型。 目前支持以下值类型和变体：
  * `string`
    * default、`no-whitespaces`、`alphanumeric`
  * `number`
    * default、`currency`
  * `date` 
    * default、`dmy`、`mdy`、`ymd`
  * `time`
  * `integer`

  若要了解如何使用此功能，请参阅 [示例标记工具](./quickstarts/label-tool.md#specify-tag-value-types) 指南。


* **表可视化** 示例标记工具现在显示在文档中识别的表。 这使您可以在标记和分析之前查看已在文档中识别和提取的表。 可以使用 "层" 选项开启/关闭此功能。

  下面是如何识别和提取表的示例：

  > [!div class="mx-imgBorder"]
  > ![使用示例标签工具的表可视化效果](./media/whats-new/formre-table-viz.png)

    提取的表在下的 JSON 输出中可用 `"pageResults"` 。

  > [!IMPORTANT]
  > 不支持标记表。 如果无法识别表并自动 extrated，则只能将它们标记为键/值对。 如果将表标记为键/值对，则将每个单元标记为唯一值。

### <a name="extraction-enhancements"></a>提取增强功能

此版本包括提取增强功能和准确性改进，特别是在同一文本行中标记和提取多个键/值对的功能。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>示例标记工具现在为开源

窗体识别器示例标记工具现在作为开源项目提供。 你可以将其集成到你的解决方案中，并根据需要进行客户特定的更改。

有关表单识别器示例标记工具的详细信息，请查看 [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)上提供的文档。

### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2

现在，将对此服务的所有 HTTP 请求强制执行 TLS 1.2。 有关详细信息，请参阅 [Azure 认知服务安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

此版本引入了窗体识别器 2.0 (预览) 。 在下面的部分中，你将找到有关新功能、增强功能和更改的详细信息。 

### <a name="new-features"></a>新增功能

* **自定义模型**
  * **标签定型** 你现在可以使用手动标记的数据来训练自定义模型。 这会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。
  * **异步 API** 可以使用异步 API 调用来训练和分析大型数据集和文件。
  * **TIFF 文件支持** 你现在可以训练和提取 TIFF 文档中的数据。
  * **提取准确性改进**

* **预生成的接收模型**
  * **Tip 数量** 你现在可以提取 tip 金额和其他手写值。
  * **行项提取** 您可以从收据中提取行项值。
  * **置信度值** 您可以查看模型对每个提取值的置信度。
  * **提取准确性改进**

* **布局提取** 现在可以使用布局 API 从窗体中提取文本数据和表数据。

### <a name="custom-model-api-changes"></a>自定义模型 API 更改

所有用于定型和使用自定义模型的 Api 都已重命名，并且一些同步方法现在是异步的。 下面是重大更改：

* 模型定型过程现在是异步的。 通过 **/Custom/models** API 调用启动培训。 此调用将返回一个操作 ID，你可以将该 ID 传递到 **自定义/模型/{modelID}** 以返回定型结果。
* 键/值提取现在由 **/Custom/models/{modelID}/analyze** API 调用启动。 此调用将返回一个操作 ID，你可以将该 ID 传递到 **自定义/模型/{modelID}/analyzeResults/{resultID}** 以返回提取结果。
* 训练操作的操作 Id 现在位于 HTTP 响应的 **Location** 标头中，而不是位于 **操作位置** 标头。

### <a name="receipt-api-changes"></a>接收 API 更改

用于读取销售收据的 Api 已重命名。

* 接收数据提取现在由 **/Prebuilt/receipt/analyze** API 调用启动。 此调用将返回一个操作 ID，你可以将该 ID 传递到 **/prebuilt/receipt/analyzeResults/{resultID}** 以返回提取结果。

### <a name="output-format-changes"></a>输出格式更改

所有 API 调用的 JSON 响应都具有新格式。 添加、删除或重命名了某些键和值。 请参阅快速入门，了解当前 JSON 格式的示例。

## <a name="next-steps"></a>后续步骤

完成 [客户端库快速入门](quickstarts/client-library.md) ，开始使用所选语言的窗体识别器编写窗体处理应用。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
