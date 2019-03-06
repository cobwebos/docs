---
title: 快速入门：使用适用于 Node.js 的自定义视觉 SDK 创建对象检测项目
titlesuffix: Azure Cognitive Services
description: 使用 Node.js SDK 创建项目、添加标记、上传图像、训练项目以及检测对象。
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: areddish
ms.openlocfilehash: 9cc1e2cd3735d8292ebca803b83351bb97de8b83
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751557"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-nodejs-sdk"></a>快速入门：使用自定义视觉 Node.js SDK 创建对象检测项目

本文提供信息和示例代码，以帮助你开始通过 Node.js 使用自定义视觉 SDK 来构建对象检测模型。 创建该项目后，可以添加标记的区域、上传图像、训练项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 Node.js 应用程序的模板。

## <a name="prerequisites"></a>先决条件

- 安装了 [Node.js 8](https://www.nodejs.org/en/download/) 或更高版本。
- 安装了 [npm](https://www.npmjs.com/)。

## <a name="install-the-custom-vision-sdk"></a>安装自定义视觉 SDK

若要安装适用于 Node.js 的自定义视觉服务 SDK，请运行以下命令：

```command
npm install azure-cognitiveservices-customvision-training
npm install azure-cognitiveservices-customvision-prediction
```

可以下载图像以及 [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)。

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>添加代码

在首选项目目录中创建名为 sample.js 的新文件。

### <a name="create-the-custom-vision-service-project"></a>创建自定义视觉服务项目

将以下代码添加到脚本中以创建新的自定义视觉服务项目。 在适当的定义中插入订阅密钥。 请注意，创建对象检测和图像分类项目之间的区别是 **create_project** 调用中指定的域。

```javascript
const util = require('util');
const TrainingApi = require("azure-cognitiveservices-customvision-training");
const PredictionApi = require("azure-cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://southcentralus.api.cognitive.microsoft.com"

const trainer = new TrainingApi.TrainingAPIClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const domains = await trainer.getDomains()
    const objDetectDomain = domains.find(domain => domain.type === "ObjectDetection");
    const sampleProject = await trainer.createProject("Sample Obj Detection Project", { domainId: objDetectDomain.id });
```

### <a name="create-tags-in-the-project"></a>在项目中创建标记

若要在项目中创建分类标记，请将以下代码添加到 sample.js 末尾：

```javascript
    const forkTag = await trainer.createTag(sampleProject.id, "Fork");
    const scissorsTag = await trainer.createTag(sampleProject.id, "Scissors");
```

### <a name="upload-and-tag-images"></a>上传和标记图像

在对象检测项目中标记图像时，需要使用标准化坐标指定每个标记对象的区域。

若要将图像、标记和区域添加到项目，请在创建标记后插入以下代码。 请注意，对于本教程，区域与代码进行内联硬编码。 区域在标准化坐标中指定边界框，坐标按以下顺序给定：左部、顶部、宽度、高度。

```javascript
    const forkImageRegions = {
        "fork_1.jpg": [0.145833328, 0.3509314, 0.5894608, 0.238562092],
        "fork_2.jpg": [0.294117659, 0.216944471, 0.534313738, 0.5980392],
        "fork_3.jpg": [0.09191177, 0.0682516545, 0.757352948, 0.6143791],
        "fork_4.jpg": [0.254901975, 0.185898721, 0.5232843, 0.594771266],
        "fork_5.jpg": [0.2365196, 0.128709182, 0.5845588, 0.71405226],
        "fork_6.jpg": [0.115196079, 0.133611143, 0.676470637, 0.6993464],
        "fork_7.jpg": [0.164215669, 0.31008172, 0.767156839, 0.410130739],
        "fork_8.jpg": [0.118872553, 0.318251669, 0.817401946, 0.225490168],
        "fork_9.jpg": [0.18259804, 0.2136765, 0.6335784, 0.643790841],
        "fork_10.jpg": [0.05269608, 0.282303959, 0.8088235, 0.452614367],
        "fork_11.jpg": [0.05759804, 0.0894935, 0.9007353, 0.3251634],
        "fork_12.jpg": [0.3345588, 0.07315363, 0.375, 0.9150327],
        "fork_13.jpg": [0.269607842, 0.194068655, 0.4093137, 0.6732026],
        "fork_14.jpg": [0.143382356, 0.218578458, 0.7977941, 0.295751631],
        "fork_15.jpg": [0.19240196, 0.0633497, 0.5710784, 0.8398692],
        "fork_16.jpg": [0.140931368, 0.480016381, 0.6838235, 0.240196079],
        "fork_17.jpg": [0.305147052, 0.2512582, 0.4791667, 0.5408496],
        "fork_18.jpg": [0.234068632, 0.445702642, 0.6127451, 0.344771236],
        "fork_19.jpg": [0.219362751, 0.141781077, 0.5919118, 0.6683006],
        "fork_20.jpg": [0.180147052, 0.239820287, 0.6887255, 0.235294119]
    };

    const scissorsImageRegions = {
        "scissors_1.jpg": [0.4007353, 0.194068655, 0.259803921, 0.6617647],
        "scissors_2.jpg": [0.426470578, 0.185898721, 0.172794119, 0.5539216],
        "scissors_3.jpg": [0.289215684, 0.259428144, 0.403186262, 0.421568632],
        "scissors_4.jpg": [0.343137264, 0.105833367, 0.332107842, 0.8055556],
        "scissors_5.jpg": [0.3125, 0.09766343, 0.435049027, 0.71405226],
        "scissors_6.jpg": [0.379901975, 0.24308826, 0.32107842, 0.5718954],
        "scissors_7.jpg": [0.341911763, 0.20714055, 0.3137255, 0.6356209],
        "scissors_8.jpg": [0.231617644, 0.08459154, 0.504901946, 0.8480392],
        "scissors_9.jpg": [0.170343131, 0.332957536, 0.767156839, 0.403594762],
        "scissors_10.jpg": [0.204656869, 0.120539248, 0.5245098, 0.743464053],
        "scissors_11.jpg": [0.05514706, 0.159754932, 0.799019635, 0.730392158],
        "scissors_12.jpg": [0.265931368, 0.169558853, 0.5061275, 0.606209159],
        "scissors_13.jpg": [0.241421565, 0.184264734, 0.448529422, 0.6830065],
        "scissors_14.jpg": [0.05759804, 0.05027781, 0.75, 0.882352948],
        "scissors_15.jpg": [0.191176474, 0.169558853, 0.6936275, 0.6748366],
        "scissors_16.jpg": [0.1004902, 0.279036, 0.6911765, 0.477124184],
        "scissors_17.jpg": [0.2720588, 0.131977156, 0.4987745, 0.6911765],
        "scissors_18.jpg": [0.180147052, 0.112369314, 0.6262255, 0.6666667],
        "scissors_19.jpg": [0.333333343, 0.0274019931, 0.443627447, 0.852941155],
        "scissors_20.jpg": [0.158088237, 0.04047389, 0.6691176, 0.843137264]
    };

    console.log("Adding images...");
    let fileUploadPromises = [];

    const forkDir = `${sampleDataRoot}/Fork`;
    const forkFiles = fs.readdirSync(forkDir);
    forkFiles.forEach(file => {
        const region = new TrainingApi.TrainingAPIModels.Region();
        region.tagId = forkTag.id;
        region.left = forkImageRegions[file][0];
        region.top = forkImageRegions[file][1];
        region.width = forkImageRegions[file][2];
        region.height = forkImageRegions[file][3];

        const entry = new TrainingApi.TrainingAPIModels.ImageFileCreateEntry();
        entry.name = file;
        entry.contents = fs.readFileSync(`${forkDir}/${file}`);
        entry.regions = [region];

        const batch = new TrainingApi.TrainingAPIModels.ImageFileCreateBatch();
        batch.images = [entry];

        fileUploadPromises.push(trainer.createImagesFromFiles(sampleProject.id, batch));
    });

    const scissorsDir = `${sampleDataRoot}/Scissors`;
    const scissorsFiles = fs.readdirSync(scissorsDir);
    scissorsFiles.forEach(file => {
        const region = new TrainingApi.TrainingAPIModels.Region();
        region.tagId = scissorsTag.id;
        region.left = scissorsImageRegions[file][0];
        region.top = scissorsImageRegions[file][1];
        region.width = scissorsImageRegions[file][2];
        region.height = scissorsImageRegions[file][3];

        const entry = new TrainingApi.TrainingAPIModels.ImageFileCreateEntry();
        entry.name = file;
        entry.contents = fs.readFileSync(`${scissorsDir}/${file}`);
        entry.regions = [region];

        const batch = new TrainingApi.TrainingAPIModels.ImageFileCreateBatch();
        batch.images = [entry];

        fileUploadPromises.push(trainer.createImagesFromFiles(sampleProject.id, batch));
    });

    await Promise.all(fileUploadPromises);
```

### <a name="train-the-project"></a>定型项目

此代码在项目中创建第一个迭代，并将其标记为默认迭代。 默认迭代反映了将响应预测请求的模型版本。 每次重新训练模型时都应更新此版本。

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

    trainingIteration.isDefault = true;
    await trainer.updateIteration(sampleProject.id, trainingIteration.id, trainingIteration);
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>获取并使用默认预测终结点

若要将图像发送到预测终结点并检索预测，请将以下代码添加到文件末尾：

```javascript
    const predictor = new PredictionApi.PredictionAPIClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_od_image.jpg`);

    const results = await predictor.predictImage(sampleProject.id, testFile, { iterationId: trainingIteration.id })

    // Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}% ${predictedResult.boundingBox.left},${predictedResult.boundingBox.top},${predictedResult.boundingBox.width},${predictedResult.boundingBox.height}`);
    });
})()
```

## <a name="run-the-application"></a>运行应用程序

运行 *sample.js*。

```PowerShell
node sample.js
```

应用程序的输出应显示在控制台中。 然后，可以验证测试图像（在 **samples/vision/images/Test** 中找到）是否已正确标记，并验证检测区域是否正确。

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。 以下指南涉及图像分类，但其原理与对象检测类似。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)