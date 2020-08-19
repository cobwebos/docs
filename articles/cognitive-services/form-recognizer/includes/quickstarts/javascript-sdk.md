---
title: 快速入门：适用于 JavaScript 的表单识别器客户端库
description: 在本快速入门中，开始使用适用于 JavaScript 的表单识别器客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.custom: devx-track-javascript
ms.openlocfilehash: 17b0ede8d917ea24bf6fa6fbcda49b1860ef83b9
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246156"
---
[参考文档](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/) | [库源代码](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [包 (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 包含一组训练数据的 Azure 存储 Blob。 有关整理训练数据集的提示和选项，请参阅[为自定义模型生成训练数据集](../../build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Train** 文件夹下的文件。
* 最新版本的 [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>设置

### <a name="create-a-form-recognizer-azure-resource"></a>创建表单识别器 Azure 资源

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>创建环境变量

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

创建一个名为 `index.js` 的文件，将其打开，并导入以下库：

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

同时加载环境变量文件。

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>安装客户端库

安装 `ai-form-recognizer` 和 `dotenv` NPM 包:

```console
npm install @azure/ai-form-recognizer dotenv
```

应用的 `package.json` 文件将使用依赖项进行更新。


<!-- 
    Object model
-->

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 JavaScript 的表单识别器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [识别表单内容](#recognize-form-content)
* [识别回执](#recognize-receipts)
* [训练自定义模型](#train-a-custom-model)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [管理自定义模型](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>验证客户端

在 `main` 函数中，为该资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭编辑器、IDE 或 shell，然后再重新打开才能访问该变量。

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

然后，使用定义的订阅变量对客户端对象进行身份验证。 你将使用 AzureKeyCredential 对象，因此，如果需要，你可以更新 API 密钥而无需创建新的客户端对象。 你还将创建训练客户端对象。

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

### <a name="call-client-specific-functions"></a>调用特定于客户端的函数

`main` 中的下一个代码块使用客户端对象来调用表单识别器 SDK 中的每个主要任务的函数。 稍后将定义这些函数。

还需要为训练和测试数据添加对 URL 的引用。
* 若要检索自定义模型训练数据的 SAS URL，请打开 Microsoft Azure 存储资源管理器，右键单击容器，然后选择“获取共享访问签名”。 确保选中“读取”和“列表”权限，然后单击“创建”。 然后复制 **URL** 部分中的值。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。
* 若要获取要测试的表单的 URL，可以使用上述步骤获取 blob 存储中单个文档的 SAS URL。 或者获取位于其他位置的文档的 URL。
* 使用上述方法还可获取回执图像的 URL，或者使用提供的示例图像 URL。

> [!NOTE]
> 本指南中的代码片段使用通过 URL 访问的远程表单。 如果要改为处理本地表单文档，请参阅[参考文档](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)中的相关方法。


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>识别表单内容

可以使用表单识别器识别文档中的表格、线条和单词，而无需训练模型。 若要识别位于给定 URI 的文件的内容，请使用 beginRecognizeContentFromUrl 方法。

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

返回的值是 FormPage 对象的集合：提交的文档中的每一页对应一个对象。 下面的代码循环访问这些对象，并输出提取的键/值对和表数据。

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>识别回执

本部分演示如何使用预先训练的回执模型识别和提取美国回执中的常见字段。

若要从 URI 识别回执，请使用 beginRecognizeReceiptsFromUrl 方法。 返回的值是 RecognizedReceipt 对象的集合：提交的文档中的每一页对应一个对象。 下面的代码处理给定 URI 的回执，并将主字段和值输出到控制台。

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const receipt = receipts[0];
    console.log("First receipt:");
    // For supported fields recognized by the service, please refer to https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult.
    const receiptTypeField = receipt.recognizedForm.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.recognizedForm.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.recognizedForm.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
```

接下来的代码块会循环访问在回执上检测到的各个项，并将其详细信息输出到控制台。

```javascript
    const itemsField = receipt.recognizedForm.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
}
```


## <a name="train-a-custom-model"></a>训练自定义模型

本部分演示如何使用自己的数据训练模型。 训练的模型可以输出结构化数据，其中包含原始形式的文档中的键/值关系。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

> [!NOTE]
> 你还可以使用图形用户界面（例如[表单识别器示例标记工具](../../quickstarts/label-tool.md)）来训练模型。

### <a name="train-a-model-without-labels"></a>不使用标签训练模型

训练自定义模型可以识别在自定义表单中找到的所有字段和值，而无需手动标记训练文档。

下面的函数使用给定文档集训练模型，并将该模型的状态输出到控制台。 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.requestedOn}`);
    console.log(`Last modified: ${response.completedOn}`);
```

返回的 CustomFormModel 对象包含该模型可以识别的表单类型以及它可以从每种表单类型中提取的字段的相关信息。 下面的代码块将此信息输出到控制台。

```javascript
    if (response.submodels) {
        for (const submodel of response.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

最后，此方法返回模型的唯一 ID。

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>使用标签训练模型

你还可以通过手动标记训练文档来训练自定义模型。 在某些情况下，使用标签训练可改善效果。 若要使用标签训练，你需要在 blob 存储容器中添加特殊标签信息文件 (\<filename\>.pdf.labels.json) 和训练文档。 [表单识别器示例标记工具](../../quickstarts/label-tool.md)提供了可帮助你创建这些标签文件的 UI。 获得它们后，可以调用 beginTraining 方法，并将 uselabels 参数设置为 `true`。

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自定义模型分析表单

本部分演示如何通过你使用自己的表单训练的模型，从自定义表单类型中提取键/值信息和其他内容。

> [!IMPORTANT]
> 若要实现此方案，必须已训练好一个模型，以便可以将其 ID 传递到下面的方法中。 请参阅[训练模型](#train-a-model-without-labels)部分。

你将使用 beginRecognizeCustomFormsFromUrl 方法。 返回的值是 RecognizedForm 对象的集合：提交的文档中的每一页对应一个对象。

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeCustomFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

以下代码将分析结果输出到控制台。 它将输出每个识别的字段和相应的值以及置信度分数。

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>管理自定义模型

本部分演示如何管理帐户中存储的自定义模型。 例如，下面的代码在单个函数中执行所有模型管理任务。 首先，复制以下函数签名：

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>检查 FormRecognizer 资源帐户中的模型数

下面的代码块会检查表单识别器帐户中保存的模型数，并将其与帐户限制进行比较。

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.customModelCount} custom models, and we can have at most ${accountProperties.customModelLimit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出资源帐户中当前存储的模型

下面的代码块会列出帐户中的当前模型，并将这些模型的详细信息输出到控制台。 它还保存对第一个模型的引用。

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listCustomModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>使用模型 ID 获取特定模型

下面的代码块使用上一节中保存的模型 ID 并使用它来检索有关模型的详细信息。

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>从资源帐户中删除模型

还可以通过引用模型的 ID 从帐户中删除该模型。 此代码删除在上一节中使用的模型。

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑难解答

### <a name="enable-logs"></a>启用日志
使用此库时，可以设置以下环境变量来查看调试日志。

```
export DEBUG=azure*
```

有关如何启用日志的详细说明，请参阅 [@azure/logger 包文档](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用表单识别器 Python 客户端库以不同的方式训练模型和分析表单。 接下来，请了解如何创建更好的训练数据集并生成更准确的模型。

> [!div class="nextstepaction"]
> [生成训练数据集](../../build-training-data-set.md)

* [什么是表单识别器？](../../overview.md)
* 在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上可以找到本指南中使用的示例代码（以及更多代码）。