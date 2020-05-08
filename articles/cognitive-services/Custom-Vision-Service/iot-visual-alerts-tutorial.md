---
title: 教程：IoT 视觉警报示例
titleSuffix: Azure Cognitive Services
description: 在本教程中，将配合使用自定义视觉和 IoT 设备来识别和报告照相机视频源中的视觉状态。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: d94f557ddd946d20020825e2bbb6babbd67d3af3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734325"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>教程：配合使用自定义视觉和 IoT 设备来报告视觉状态

本示例应用演示如何使用自定义视觉来训练带有照相机的设备，使其能够检测视觉对象。 可以使用导出的 ONNX 模型在 IoT 设备上运行此检测方案。

视觉状态描述图像的内容：空房间或有人的房间、空车道或有一辆卡车的车道等。 在下图中，可以看到在照相机前放置香蕉或苹果时，应用可检测出来。

![标记照相机前水果的 UI 动画](./media/iot-visual-alerts-tutorial/scoring.gif)

本教程会说明如何：
> [!div class="checklist"]
> * 配置示例应用，以使用自己的自定义视觉和 IoT 中心资源。
> * 使用应用训练自定义视觉项目。
> * 使用应用对新图像进行实时评分，并将结果发送到 Azure。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 

## <a name="prerequisites"></a>先决条件

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > 此项目应为“Compact”图像分类项目，因为之后会将模型导出到 ONNX  。
* 还需要在 Azure 上[创建 IoT 中心资源](https://ms.portal.azure.com/#create/Microsoft.IotHub)。
* [Visual Studio 2015 或更高版本](https://www.visualstudio.com/downloads/)
* （可选）运行 Windows 10 IoT 核心版 17763 或更高版本的 IoT 设备。 还可以直接在电脑上运行应用。
   * 对于 Raspberry Pi 2 和 3，可以直接在 IoT 仪表板应用中设置 Windows 10。 对于其他设备（如 DrangonBoard），需要使用 [eMMC 方法](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices)对其进行刷写。 如需获取设置新设备的帮助，请参阅 Windows IoT 文档中的[设置设备](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)。

## <a name="about-the-visual-alerts-app"></a>关于视觉警报应用

IoT 视觉警报应用以连续循环的形式运行，并根据需要在四个不同状态之间切换：

* **无模型**：无操作状态。 应用会持续休眠一秒钟，并检查照相机。
* **捕获训练图像**：在此状态下，应用将捕获图片，并将其作为训练图像上传到目标自定义视觉项目。 然后，应用会休眠 500 ms，并重复操作，直到捕获到目标图像数目为止。 然后，它将触发自定义视觉模型的训练。
* **等待训练的模型**：在此状态下，应用每秒调用自定义视觉 API，以检查目标项目是否包含训练的迭代。 找到一个训练的迭代后，会将相应的 ONNX 模型下载到本地文件，并切换到“评分”状态  。
* **评分**：在此状态下，应用根据本地 ONNX 模型使用 Windows ML 评估照相机中的单个帧。 屏幕上会显示生成的图像分类，并作为消息发送到 IoT 中心。 应用会休眠一秒钟，然后对新图像评分。

## <a name="understand-the-code-structure"></a>了解代码结构

以下文件处理应用的主要功能。

| 文件 | 说明 |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | 此文件定义 XAML 用户界面。 它承载 Web 照相机控件，并包含用于状态更新的标签。|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | 此代码控制 XAML UI 的行为。 它包含处理代码的状态机。|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | 此类是一个包装器，用于处理与自定义视觉服务的集成。|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | 此类是一个包装器，用于处理与的 Windows ML 的集成，以加载 ONNX 模型并根据该模型对图像评分。|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | 此类是一个包装器，用于处理与 IoT 中心的集成，以将评分结果上传到 Azure。|

## <a name="set-up-the-visual-alerts-app"></a>设置视觉警报应用

按照以下步骤，在电脑或 IoT 设备上运行 IoT 视觉警报应用。

1. 克隆或下载 GitHub 上的 [IoTVisualAlerts 示例](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)。
1. 在 Visual Studio 中打开解决方案“IoTVisualAlerts.sln” 
1. 集成自定义视觉项目：
    1. 在“CustomVision\CustomVisionServiceWrapper.cs”脚本中，使用训练密钥更新 `ApiKey` 变量  。
    1. 然后使用与密钥关联的终结点 URL 更新 `Endpoint` 变量。
    1. 使用目标自定义视觉项目的对应 ID 更新 `targetCVSProjectGuid` 变量。 
1. 设置 IoT 中心资源：
    1. 在“IoTHub\IotHubWrapper.cs”脚本中，使用设备的相应连接字符串更新 `s_connectionString` 变量  。 
    1. 在 Azure 门户上，加载 IoT 中心实例，在“资源管理器”下单击“IoT 设备”，选择目标设备（如果需要也可创建一个），然后找到“主连接字符串”下的连接字符串    。 此字符串将包含 IoT 中心名称、设备 ID 和共享访问密钥，其格式如下：`{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`。

## <a name="run-the-app"></a>运行应用

如果要在电脑上运行应用，请在 Visual Studio 中选择“本地计算机”作为目标设备，并选择“x64”或“x86”作为目标平台    。 然后按 F5 运行程序。 应用应启动并显示照相机的实时源和状态消息。

如果要部署到具有 ARM 处理器的 IoT 设备，则需选择“ARM”作为目标平台，并选择“远程计算机”作为目标设备   。 在系统提示时提供设备的 IP 地址（必须与 PC 位于同一网络上）。 启动设备并将其连接到网络后，便可从 Windows IoT 默认应用获取 IP 地址。 按 F5 运行程序。

如果首次运行应用，不会捕获任何视觉状态。 它会显示一条状态消息，提示无可用模型。 

## <a name="capture-training-images"></a>捕获训练图像

要设置模型，需要将应用置于“捕获训练图像”状态  。 执行以下任一步骤：
* 如果要在电脑上运行应用，请使用 UI 右上角的按钮。
* 如果要在 IoT 设备上运行应用，请通过 IoT 中心在设备上调用 `EnterLearningMode` 方法。 可通过 Azure 门户上 IoT 中心菜单中的设备项或 [IoT 中心设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp)等工具来调用。
 
当应用进入“捕获训练图像”状态时，它会每隔一秒钟捕获大约两个图像，直到达到目标图像数目  。 默认情况下，目标图像数目为 30，但可通过将所需数目作为参数传递给 `EnterLearningMode` IoT 中心方法，来设置此参数。 

当应用捕获图像时，必须将要检测的视觉状态类型（例如，空房间、有人的房间、空桌子、放有玩具卡车的桌子等）暴露在照相机下。

## <a name="train-the-custom-vision-model"></a>训练自定义视觉模型

应用完成图像捕获后，会上传图像，然后切换到“等待训练的模型”状态  。 此时，需要前往[自定义视觉门户](https://www.customvision.ai/)，基于新的训练图像生成模型。 下面的动画展示了此过程。

![动画：标记多个香蕉图像](./media/iot-visual-alerts-tutorial/labeling.gif)

在自己的方案中实现此过程：

1. 登录[自定义视觉门户](http://customvision.ai)。
1. 查找目标项目，该项目现在应包含应用上传的所有训练图像。
1. 对于需识别的每个视觉状态，请选择相应的图像并手动应用标记。
    * 例如，如果目标是区分空房间和有人的房间，建议将五个或以上有人的图像标记为新类“People”，将五个或以上没有人的图像标记为“Negative”   。 这可以帮助模型区分这两种状态。
    * 又例如，如果目标是估计书架的填满程度，则可以使用“EmptyShelf”、“PartiallyFullShelf”和“FullShelf”等标记    。
1. 完成后，选择“训练”按钮  。
1. 训练完成后，应用会检测到训练的迭代是否可用。 然后便开始将训练的模型导出到 ONNX 并下载到设备。

## <a name="use-the-trained-model"></a>使用训练的模型

下载训练的模型后，应用会切换到“评分”状态，并连续循环地对照相机中的图像评分  。

对于每个捕获的图像，应用都会在屏幕上显示顶部标记。 如果无法识别视觉状态，它将显示“无匹配项”  。 应用还会将这些消息发送到 IoT 中心，如果检测到一个类，则消息将包括标签、可信度评分和一个名为 `detectedClassAlert` 的属性，对基于属性的快速消息路由感兴趣的 IoT 中心客户端可以使用该属性。

此外，当在具有 Sense HAT 单元的 Raspberry Pi 上运行时，该示例使用 [Sense HAT 库](https://github.com/emmellsoft/RPi.SenseHat) 来检测类，因此可将该单元用作输出显示，例如在检测到一个类时将所有显示灯设置为红色，在未检测到任何类时设置为空白。

## <a name="reuse-the-app"></a>重复使用应用

如果要将应用重置回其原始状态，可以单击 UI 右上角的按钮，或者通过 IoT 中心调用 `DeleteCurrentModel` 方法。

在任何时候，都可以通过再次单击右上角的 UI 按钮或调用 `EnterLearningMode` 方法来重复执行上传训练图像的步骤。

如果在设备上运行应用并需要再次检索 IP 地址（例如，通过 [Windows IoT 远程客户端](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)建立远程连接），则可以通过 IoT 中心调用 `GetIpAddress` 方法。

## <a name="clean-up-resources"></a>清理资源

如果不再需要维护自定义视觉项目，请将其删除。 在[自定义视觉网站](https://customvision.ai)上，导航到“项目”，然后选择新项目下的垃圾桶  。

![标有“我的新项目”的面板的屏幕截图，其中包含垃圾桶图标](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>后续步骤

在本教程中，将设置并运行一个应用程序，该应用程序可在 IoT 设备上检测视觉状态信息，并将结果发送到 IoT 中心。 接下来，请进一步浏览源代码或执行以下建议修改。

> [!div class="nextstepaction"]
> [IoTVisualAlerts 示例 (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* 添加一个 IoT 中心方法，将应用直接切换到“等待训练的模型”状态  。 这样，就可以使用设备本身未捕获的图像来训练模型，然后根据命令将新模型推送到设备上。
* 参照[可视化实时传感器数据](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)教程，创建 Power BI 仪表板，用于可视化由示例发送的 IoT 中心警报。
* 参照 [IoT 远程监视](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)教程，创建一个逻辑应用，用于在检测到视觉状态时响应 IoT 中心警报。
