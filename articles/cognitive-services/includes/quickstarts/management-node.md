---
title: 快速入门：适用于 Node.js 的 Azure 管理客户端库
description: 在本快速入门中，开始使用适用于 Node.js 的 Azure 管理客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 59063912b2976890976e3c27bc4f2d93b6392e37
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607608"
---
[参考文档](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [包 (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
* 最新版本的 [Node.js](https://nodejs.org/)

## <a name="create-an-azure-service-principal"></a>创建 Azure 服务主体

若要使应用程序与 Azure 帐户交互，需要使用 Azure 服务主体来管理权限。 请按照[创建 Azure 服务主体](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)中的说明操作。

创建服务主体时，你会看到它有一个机密值、一个 ID 和一个应用程序 ID。 将应用程序 ID 和机密保存到某个临时位置，以供后续步骤使用。

## <a name="create-a-resource-group"></a>创建资源组

在创建认知服务资源之前，帐户必须具有 Azure 资源组才能包含该资源。 如果还没有资源组，请在 [Azure 门户](https://ms.portal.azure.com/)中创建一个。

## <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

继续之前，请先创建一个名为 index.js 的文件。

### <a name="install-the-client-library"></a>安装客户端库

安装以下 NPM 包：

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

应用的 `package.json` 文件将使用依赖项进行更新。

### <a name="import-libraries"></a>导入库

打开 index.js 脚本并导入以下库。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>验证客户端

将以下字段添加到脚本的根目录中，并使用所创建的服务主体和 Azure 帐户信息填写其值。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

接下来，添加以下 `quickstart` 函数来处理程序的主要工作。 第一个代码块使用你在上面输入的凭据变量构造一个 CognitiveServicesManagementClient 对象。 所有 Azure 管理操作都需要此对象。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>调用管理函数

将以下代码添加到 `quickstart` 函数的末尾，以列出可用资源、创建示例资源、列出拥有的资源，然后删除示例资源。 你将在后续步骤中定义这些函数。

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

### <a name="choose-a-service-and-pricing-tier"></a>选择服务和定价层

创建新资源时，需要知道要使用的服务的种类，以及所需的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 SKU）。 创建资源时，将此信息和其他信息用作参数。 以下函数列出了可用的认知服务种类。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

请参阅下面的 SKU 和定价信息列表。 

#### <a name="multi-service"></a>多服务

| 服务                    | 种类                      |
|----------------------------|---------------------------|
| 多个服务。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)页。            | `CognitiveServices`     |


#### <a name="vision"></a>影像

| 服务                    | 种类                      |
|----------------------------|---------------------------|
| 计算机视觉            | `ComputerVision`          |
| 自定义视觉 - 预测 | `CustomVision.Prediction` |
| 自定义视觉 - 定型   | `CustomVision.Training`   |
| 人脸                       | `Face`                    |
| 表单识别器            | `FormRecognizer`          |
| 墨迹识别器             | `InkRecognizer`           |

#### <a name="search"></a>搜索

| 服务            | 种类                  |
|--------------------|-----------------------|
| 必应自动建议   | `Bing.Autosuggest.v7` |
| 必应自定义搜索 | `Bing.CustomSearch`   |
| 必应实体搜索 | `Bing.EntitySearch`   |
| 必应搜索        | `Bing.Search.v7`      |
| 必应拼写检查   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>语音

| 服务            | 种类                 |
|--------------------|----------------------|
| 语音服务    | `SpeechServices`     |
| 语音识别 | `SpeakerRecognition` |

#### <a name="language"></a>语言

| 服务            | 种类                |
|--------------------|---------------------|
| 窗体理解 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文本分析     | `TextAnalytics`     |
| 文本翻译   | `TextTranslation`   |

#### <a name="decision"></a>决策

| 服务           | 种类               |
|-------------------|--------------------|
| 异常检测器  | `AnomalyDetector`  |
| 内容审查器 | `ContentModerator` |
| 个性化体验创建服务      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>定价层和计费

定价层（以及你收到的账单金额）基于你使用身份验证信息发送的事务数。 每个定价层指定：
* 每秒允许的最大事务数 (TPS)。
* 在定价层中启用的服务功能。
* 预定义事务数的成本。 根据[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中为服务所指定的内容，超过此数字将导致额外费用。

> [!NOTE]
> 许多认知服务都有一个免费层，供你试用该服务。 若要使用免费层，请使用 `F0` 作为资源的 SKU。

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

若要创建并订阅新的认知服务资源，请使用 Create 函数。 此函数向传入的资源组添加新的可计费资源。 创建新资源时，需要知道要使用的服务的种类，以及其定价层（或 SKU）和 Azure 位置。 下面的函数使用所有这些参数并创建资源。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>查看资源

若要查看 Azure 帐户下的所有资源（跨所有资源组），请使用以下函数：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>删除资源

下面的函数从给定的资源组中删除指定的资源。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>运行应用程序

将以下代码添加到脚本底部，以调用包含错误处理的主 `quickstart` 函数。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

然后，在控制台窗口中，使用 `node` 命令运行应用程序。

```console
node index.js
```

## <a name="see-also"></a>另请参阅

* [Azure 管理 SDK 参考文档](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [什么是 Azure 认知服务？](../../Welcome.md)
* [对 Azure 认知服务的请求进行身份验证](../../authentication.md)
* [使用 Azure 门户创建新资源](../../cognitive-services-apis-create-account.md)