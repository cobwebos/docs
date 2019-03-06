---
title: 教程：使用自定义徽标检测器识别 Azure 服务 - 自定义视觉
titlesuffix: Azure Cognitive Services
description: 本教程逐步介绍一个在徽标检测方案中使用 Azure 自定义视觉的示例应用。 了解如何将自定义视觉与其他组件配合使用，以提供端到端的应用程序。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 5b749a85295e85ecde8d283ca02066a31be33666
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673063"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>教程：识别照片中的 Azure 服务徽标

在本教程中，你会了解在大型方案中使用 Azure 自定义视觉的示例应用。 AI Visual Provision 应用（适用于移动平台的 Xamarin.Forms 应用）将分析 Azure 服务徽标的照片，然后将实际服务部署到用户的 Azure 帐户。 你将在本文中了解如何配合其他组件使用自定义视觉来提供有用的端到端应用程序。 自行运行整个应用方案，或者，只是完成设置中的自定义视觉部分，然后再了解该应用如何使用自定义视觉。

本教程会说明如何：

> [!div class="checklist"]
> - 创建自定义对象检测器用于识别 Azure 服务徽标。
> - 将应用连接到 Azure 的计算机视觉和自定义视觉。
> - 创建一个 Azure 服务主体帐户以从应用部署 Azure 服务。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 

## <a name="prerequisites"></a>先决条件

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- 适用于 Visual Studio 的 Xamarin 工作负荷（请参阅 [安装 Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows)）
- 适用于 Visual Studio 的 iOS 或 Android 仿真器
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)（可选）

## <a name="get-the-source-code"></a>获取源代码

如果要使用提供的 Web 应用，请从 GitHub 上的 [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) 存储库克隆或下载该应用的源代码。 在 Visual Studio 中打开 *Source/VisualProvision.sln* 文件。 以后会编辑某些项目文件，以便可以运行应用。

## <a name="create-an-object-detector"></a>创建对象检测器

登录到[自定义视觉网站](https://customvision.ai/)并创建新项目。 指定对象检测项目，并使用“徽标”域；这可以让服务使用针对徽标检测进行优化的算法。 

![Chrome 浏览器中自定义视觉网站上的新建项目窗口](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>上传和标记图像

接下来，通过上传 Azure 服务徽标图像并手动标记这些图像来训练徽标检测算法。 AIVisualProvision 存储库包含一组可用的训练图像。 在网站上，选择“训练图像”选项卡上的“添加图像”按钮。然后转到存储库的 Documents/Images/Training_DataSet 文件夹。 需要手动标记每个图像中的徽标，因此，如果只是测试此项目，则可能只要上传一部分图像。 上传计划使用的每个标记的至少 15 个实例。

上传训练图像之后，选择屏幕上的第一个图像。 此时会打开标记窗口。 针对每个图像中的每个徽标绘制方框并分配标记。 

![自定义视觉网站上的徽标标记](media/azure-logo-tutorial/tag-logos.png)

应用配置为使用特定标记字符串。 你会在 Source\VisualProvision\Services\Recognition\RecognitionService.cs 文件中找到定义：

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

标记图像之后，转到右侧以标记下一个图像。 完成后，关闭标记窗口。

## <a name="train-the-object-detector"></a>训练对象检测器

在左窗格中，将“标记”开关设置为“已标记”以显示图像。 然后选择页面顶部的绿色按钮以训练模型。 这样做可让算法知道如何识别新图像中的相同标记。 它还会基于现有的某些图像测试模型，以生成准确度评分。

![自定义视觉网站的“训练图像”选项卡。在此屏幕截图中，框出了“训练”按钮](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>获取预测 URL

训练模型之后，可将其集成到应用中。 为此，需要获取终结点 URL（应用将要查询的模型地址）和预测密钥（向应用授予预测请求的访问权限）。 在“性能”选项卡中，选择页面顶部的“预测 URL”按钮。

![自定义视觉网站，显示了“预测 API”窗口，其中显示 URL 地址和 API 密钥](media/azure-logo-tutorial/cusvis-endpoint.png)

将图像文件 URL 和 Prediction-Key 值复制到 Source\VisualProvision\AppSettings.cs 文件中的相应字段：

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>了解自定义视觉的用法

打开 Source/VisualProvision/Services/Recognition/CustomVisionService.cs 文件，以查看应用如何使用自定义视觉密钥和终结点 URL。 **PredictImageContentsAsync** 方法采用图像文件的字节流以及取消标记（用于异步任务管理），调用自定义视觉预测 API，然后返回预测结果。 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

此结果采用 **PredictionResult** 实例的格式，该实例本身包含**预测**实例的列表。 **预测**包含检测到的标记及其边框在图像中的位置。

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

若要了解有关应用如何处理此数据的详细信息，请从 GetResourcesAsync 方法开始。 此方法在 Source/VisualProvision/Services/Recognition/RecognitionService.cs 文件中定义。  

## <a name="add-computer-vision"></a>添加计算机视觉

本教程的自定义视觉部分已完成。 如果要运行应用，还需要集成计算机视觉服务。 应用使用计算机视觉文本识别功能对徽标检测过程进行补充。 Azure 徽标可以通过其外观或其附近打印的文本进行识别。 与自定义视觉模型不同，计算机视觉经过预先训练，可针对图像或视频执行特定的操作。

订阅计算机视觉服务即可获取密钥和终结点 URL。 有关此步骤的帮助，请参阅[如何获取订阅密钥](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe)。

![Azure 门户中的计算机视觉服务，其中选择了“快速启动”菜单。 密钥的链接会列出，API 终结点 URL 也是如此](media/azure-logo-tutorial/comvis-keys.png)

接下来，打开 Source\VisualProvision\AppSettings.cs 文件，并使用正确的值填充 `ComputerVisionEndpoint` 和 `ComputerVisionKey` 变量。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>创建服务主体

应用需要通过一个 Azure 服务主体帐户将服务部署到 Azure 订阅。 使用服务主体可以通过基于角色的访问控制向应用委托特定的权限。 若要了解详细信息，请参阅[服务主体指南](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals)。

可以使用 Azure Cloud Shell 或 Azure CLI 创建服务主体（如下所示）。 若要开始，请登录并选择要使用的订阅。

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

然后创建服务主体。 （此过程可能需要一些时间才能完成。）

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

成功完成后，应会看到以下包括所需凭据的 JSON 输出。

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
记下 `clientId` 和 `tenantId` 值。 将其添加到 *Source\VisualProvision\AppSettings.cs* 文件中的相应字段。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>运行应用

此时，已向应用授予以下对象的访问权限：
* 训练的自定义视觉模型
* 计算机视觉服务
* 服务主体帐户 

遵循以下步骤运行应用：

1. 在 Visual Studio 解决方案资源管理器中，选择 VisualProvision.Android 项目或 VisualProvision.iOS 项目。 从主工具栏上的下拉菜单中选择对应的仿真器或连接的移动设备。 然后运行应用。

    > [!NOTE]
    > 需要 MacOS 设备来运行 iOS 仿真器。

1. 在第一个屏幕中，输入服务主体客户端 ID、租户 ID 和密码。 选择“登录”按钮。

    > [!NOTE]
    > 在某些仿真器上，执行此步骤时，“登录”按钮可能未激活。 如果发生这种情况，请停止应用，打开 Source/VisualProvision/Pages/LoginPage.xaml 文件，找到标有 LOGIN BUTTON 的 `Button` 元素，删除以下行，然后再次运行应用。
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![显示服务主体凭据字段的应用屏幕](media/azure-logo-tutorial/app-credentials.png)

1. 在下一个屏幕上，从下拉菜单中选择 Azure 订阅。 （此菜单应包含所有服务主体有权访问的所有订阅。）选择“继续”按钮。 此时，应用可能会提示你向设备授予对相机和照片存储的访问权限。 授予访问权限。

    ![显示目标 Azure 订阅下拉菜单字段的应用屏幕](media/azure-logo-tutorial/app-az-subscription.png)

    

1. 设备上的相机将会激活。 拍摄所训练的某个 Azure 服务徽标的照片。 部署窗口应会提示你选择新服务的区域和资源组（就像从 Azure 门户部署时一样）。 

    ![智能手机相机屏幕，侧重于两个 Azure 徽标剪纸贴画](media/azure-logo-tutorial/app-camera-capture.png)

    ![显示部署区域和资源组字段的应用屏幕](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>清理资源 

如果已遵循本方案的所有步骤并已使用该应用将 Azure 服务部署到帐户，请转到 [Azure 门户](https://ms.portal.azure.com/)。 在其中取消不想使用的服务。

如果你计划使用自定义视觉创建自己的对象检测项目，则可能要删除在本教程中创建的徽标检测项目。 自定义视觉免费试用版只允许创建两个项目。 若要删除徽标检测项目，请在[自定义视觉网站](https://customvision.ai)上，打开“项目”，然后选择“我的新项目”下的回收站图标。

## <a name="next-steps"></a>后续步骤

在本教程中，你已设置并探索了一个全功能的 Xamarin.Forms 应用，该应用使用自定义视觉服务检测手机相机图像中的徽标。 接下来，了解有关生成自定义视觉模型的最佳做法，以便可以为自己的应用创建强大且准确的模型。

> [!div class="nextstepaction"]
> [如何改进分类器](getting-started-improving-your-classifier.md)
