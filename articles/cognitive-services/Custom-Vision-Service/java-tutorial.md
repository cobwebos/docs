---
title: 教程：使用适用于 Java 的自定义视觉 SDK 创建图像分类项目
titlesuffix: Azure Cognitive Services
description: 创建项目、添加标记、上载图像、定型你的项目并使用默认终结点进行预测。
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: e302fc580d9c83d269f0deedd051a3ea23bd274e
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957202"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>教程：使用适用于 Java 的自定义视觉 SDK 创建图像分类项目

了解如何使用 Java 通过自定义影像服务创建图像分类项目。 创建该项目后，你可以添加标记、上传图像、定型项目、获取项目的默认预测终结点 URL 并将其用于以编程方式测试图像。 将此开源示例用作通过使用自定义视觉 API 构建自己的应用的模板。

## <a name="prerequisites"></a>先决条件

- 已安装 JDK 7 或 8。
- 已安装 Maven。

## <a name="get-the-training-and-prediction-keys"></a>获取定型密钥和预测密钥

若要获取此示例中使用的密钥，请访问[自定义影像网页](https://customvision.ai)并选择右上角齿轮图标。 在“帐户”部分中，复制“定型密钥”和“预测密钥”字段中的值。

![密钥 UI 的图像](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>安装自定义影像服务 SDK

可从 maven 中央存储库安装自定义影像 SDK：
* [定型 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [预测 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>了解代码

完整的项目，包括映像，可从 [Java 存储库的自定义影像 Azure 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master)中获取。 

使用你最喜欢的 Java IDE 打开 `Vision/CustomVision` 项目。 

此应用程序使用之前检索到的定型密钥创建一个名为“示例 Java 项目”的新项目。 然后，它上传图像以定型和测试分类器。 分类器标识树是 Hemlock 还是 Japanese Cherry。

以下代码片段实现了本示例的主要功能：

## <a name="create-a-custom-vision-service-project"></a>创建自定义影像服务项目

> [!IMPORTANT]
> 将 `trainingApiKey` 设置为先前检索到的定型密钥值。

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>将标记添加到你的项目

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>将图像上载到项目

该示例已设置为在最终包中包含图像。 从 jar 的资源部分读取图像，并上传到该服务。

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

上一段代码使用两个帮助程序函数，以资源流的形式检索图像并将其上传到该服务。

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>定型项目

这会在项目中创建第一次迭代，并将此迭代标记为默认迭代。 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>获取并使用默认预测终结点

> [!IMPORTANT]
> 将 `predictionApiKey` 设置为先前检索到的预测密钥值。

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>运行示例

若要编译并运行该解决方案，请使用 maven ：

```
mvn compile exec:java
```

应用程序的输出类似于以下文本：

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```