---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 04/14/2020
ms.openlocfilehash: 0546645bc496f6e8918f937305ac6cad6a4428ed
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837929"
---
本文介绍如何开始通过 Node.js 使用自定义视觉 SDK 来构建图像分类模型。 创建该项目后，可以添加标记、上传图像、训练项目、获取项目的已发布预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 Node.js 应用程序的模板。 若要在不使用代码的情况下了解生成和使用分类模型的过程，  请改为查看[基于浏览器的指南](../../getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>先决条件

- 安装了 [Node.js 8](https://www.nodejs.org/en/download/) 或更高版本。
- 安装了 [npm](https://www.npmjs.com/)。
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>安装自定义视觉 SDK

若要安装适用于 Node.js 的自定义视觉服务 SDK，请在 PowerShell 中运行以下命令：

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [node-get-images](../../includes/node-get-images.md)]

## <a name="add-the-code"></a>添加代码

在首选项目目录中创建名为 sample.js  的新文件。

### <a name="create-the-custom-vision-service-project"></a>创建自定义视觉服务项目

将以下代码添加到脚本中以创建新的自定义视觉服务项目。 在相应的定义中插入订阅密钥，并将 sampleDataRoot 路径值设置为图像文件夹路径。 请确保终结点值与你在 [Customvision.ai](https://www.customvision.ai/) 创建的训练和预测终结点匹配。 请注意，创建对象检测和图像分类项目之间的区别是 **createProject** 调用中指定的域。

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");
const msRest = require("@azure/ms-rest-js");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const credentials = new msRest.ApiKeyCredentials({ inHeader: { "Training-key": trainingKey } });
const trainer = new TrainingApi.TrainingAPIClient(credentials, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>在项目中创建标记

若要在项目中创建分类标记，请将以下代码添加到 sample.js  末尾：

```javascript
const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>上传和标记图像

要将示例图像添加到项目，请在创建标记后插入以下代码。 此代码会上传具有相应标记的每个图像。 最多可以在单个批次中上传 64 个图像。

> [!NOTE]
> 需根据此前下载认知服务 Node.js SDK 示例项目的位置将 sampleDataRoot  更改为图像的路径。

```javascript
console.log("Adding images...");
let fileUploadPromises = [];

const hemlockDir = `${sampleDataRoot}/Hemlock`;
const hemlockFiles = fs.readdirSync(hemlockDir);
hemlockFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
});

const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
const japaneseCherryFiles = fs.readdirSync(cherryDir);
japaneseCherryFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
});

await Promise.all(fileUploadPromises);
```

### <a name="train-the-classifier-and-publish"></a>训练分类器和发布

此代码创建预测模型的第一个迭代，然后将该迭代发布到预测终结点。 为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

```javascript
console.log("Training...");
let trainingIteration = await trainer.trainProject(sampleProject.id);

// Wait for training to complete
console.log("Training started...");
while (trainingIteration.status == "Training") {
    console.log("Training status: " + trainingIteration.status);
    await setTimeoutPromise(1000, null);
    trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
}
console.log("Training status: " + trainingIteration.status);

// Publish the iteration to the end point
await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>获取并使用预测终结点上发布的迭代

若要将图像发送到预测终结点并检索预测，请将以下代码添加到文件末尾：

```javascript
    const predictor_credentials = new msRest.ApiKeyCredentials({ inHeader: { "Prediction-key": predictionKey } });
    const predictor = new PredictionApi.PredictionAPIClient(predictor_credentials, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>运行应用程序

运行 *sample.js*。

```shell
node sample.js
```

应用程序的输出应类似于以下文本：

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

然后，可以验证测试图像（在 **<base_image_url>/Images/Test/** 中找到）是否已正确标记。 也可返回到[自定义视觉网站](https://customvision.ai)，查看新创建项目的当前状态。

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)
