---
title: 人脸 API C# 教程 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本教程创建一个 Windows 应用，以便使用认知服务人脸服务来检测和定格图像中的人脸。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: e4f2192c40f0b650b31ed59642dee89e42eca703
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125912"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>教程：创建一个用于检测和定格图像中人脸的 WPF 应用

本教程创建一个 Windows Presentation Framework (WPF) 应用程序，以便通过 .NET 客户端库使用人脸服务。 该应用检测图像中的人脸，围绕每张脸绘制一个框架，并在状态栏上显示人脸描述。 完整的示例代码在 GitHub 上的[在 Windows 上检测和定格图像中的人脸](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)中提供。

![显示使用矩形将检测到的人脸定格的屏幕截图](../Images/getting-started-cs-detected.png)

本教程演示如何：

> [!div class="checklist"]
> - 创建 WPF 应用程序
> - 安装人脸服务客户端库
> - 使用客户端库检测图像中的人脸
> - 围绕每个检测到的人脸绘制一个框架
> - 在状态栏上显示人脸描述

## <a name="prerequisites"></a>先决条件

- 需要一个订阅密钥来运行此示例。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。 如果使用 Visual Studio 2017，则 .NET 桌面应用程序开发工作负荷是必需的。 本教程使用 Visual Studio 2017 Community Edition。
- [Microsoft.Azure.CognitiveServices.Vision.Face 2.0.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.0.0-preview) 客户端库 NuGet 包。 不需下载程序包。 下面提供了安装说明。

## <a name="create-the-visual-studio-solution"></a>创建 Visual Studio 解决方案

执行以下步骤，以便创建 Windows WPF 应用程序项目。

1. 打开 Visual Studio，在“文件”菜单中单击“新建”，然后单击“项目”。
   - 在 Visual Studio 2017 中展开“已安装”，然后展开“其他语言”。 选择“Visual C#”，然后选择“WPF 应用(.NET Framework)”。
   - 在 Visual Studio 2015 中展开“已安装”，然后展开“模板”。 选择“Visual C#”，然后选择“WPF 应用程序”。
1. 将应用程序命名为“FaceTutorial”，然后单击“确定”。

## <a name="install-the-face-service-client-library"></a>安装人脸服务客户端库

按照以下说明安装客户端库。

1. 在“工具”菜单中选择“NuGet 包管理器”，然后选择“包管理器控制台”。
1. 在“包管理器控制台”中粘贴以下内容，然后按 **Enter**。

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.0.0-preview`

## <a name="add-the-initial-code"></a>添加初始代码

### <a name="mainwindowxaml"></a>MainWindow.xaml

打开 *MainWindow.xaml*（提示：使用**向上/向下箭头图标**来切换窗格），将其中的内容替换为以下代码。 此 xaml 代码用于创建 UI 窗口。 记下事件处理程序 `FacePhoto_MouseMove` 和 `BrowseButton_Click`。

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

展开 *MainWindow.xaml*，然后打开 *MainWindow.xaml.cs*，将其中的内容替换为以下代码。 忽略红色的波浪下划线；它们会在第一次生成操作之后消失。

头两行用于导入客户端库命名空间。 接下来创建 `FaceClient`，传入订阅密钥，并在 `MainWindow` 构造函数中设置 Azure 区域。 `BrowseButton_Click` 和 `FacePhoto_MouseMove` 方法对应于在 *MainWindow.xaml* 中声明的事件处理程序。

`BrowseButton_Click` 创建一个 `OpenFileDialog`，供用户选择 jpg 图像。 图像在主窗口中读取和显示。 `BrowseButton_Click` 的剩余代码以及 `FacePhoto_MouseMove` 的代码在后续步骤中插入。

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string baseUri =
            "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(baseUri, UriKind.Absolute))
            {
                faceClient.BaseUri = new Uri(baseUri);
            }
            else
            {
                MessageBox.Show(baseUri,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>插入订阅密钥并验证或更改区域

- 在 *MainWindow.xaml.cs* 中找到以下行，将 `<Subscription Key>` 替换为人脸 API 订阅密钥：

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- 在 *MainWindow.xaml.cs* 中找到以下行，然后替换或验证与订阅密钥关联的 Azure 区域：

    ```csharp
    private const string baseUri =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
    ```

    确保位置与获取订阅密钥时所处的位置一致。 例如，如果是从 **westus** 区域获取的订阅密钥，则将 `Westcentralus` 替换为 `Westus`。

    如果是使用免费试用版收到的订阅密钥，则密钥所在区域为 **westcentralus**，因此无需进行任何更改。

### <a name="test-the-app"></a>测试应用程序

按菜单上的“启动”，对应用进行测试。 窗口打开后，单击左下角的“浏览”。 此时会显示一个“打开文件”对话框，你可以在其中浏览并选择照片，然后该照片就会显示在窗口中。

![显示未修改的人脸图像的屏幕截图](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>上传用于检测人脸的图像

检测人脸最直接的方式是调用 `FaceClient.Face.DetectWithStreamAsync` 方法，该方法包装了用于上传本地图像的[检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 方法。

在 `MainWindow` 类中 `FacePhoto_MouseMove` 方法下面插入以下方法。

此时会创建一系列用于分析的人脸特性，并会将提交的图像文件读取到 `Stream` 中。 二者都会传递给 `DetectWithStreamAsync` 调用。

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>在每个人脸周围绘制矩形

添加用于在图像中每个检测到的人脸周围绘制矩形的代码。

在 *MainWindow.xaml.cs* 中，将 `async` 修饰符添加到 `BrowseButton_Click` 方法。

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

在 `BrowseButton_Click` 方法末尾 `FacePhoto.Source = bitmapSource` 行之后插入以下代码。

检测到的人脸的列表通过调用 `UploadAndDetectFaces` 来填充。 接下来，围绕每个人脸绘制一个矩形，修改的图像显示在主窗口中。

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>描述图像中的人脸

将以下方法追加到 `MainWindow` 类的 `UploadAndDetectFaces` 方法下面。

此方法将人脸特性转换成描述人脸的字符串。 当鼠标指针悬停在人脸矩形上时，会显示此字符串。

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>显示人脸描述

将 `FacePhoto_MouseMove` 方法替换为以下代码。

当鼠标指针悬停在人脸矩形上时，此事件处理程序会显示人脸描述字符串。

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>运行应用

运行此应用程序并通过浏览方式查找包含人脸的图像。 等待几秒钟，以便人脸服务响应。 然后，你会在图像中的人脸上看到一个红色矩形。 将鼠标移到人脸矩形上，状态栏会显示该人脸的描述。

![显示使用矩形将检测到的人脸定格的屏幕截图](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>摘要

本教程介绍了人脸服务客户端库的基本使用过程，并创建了一个应用程序来显示图像中的已定格人脸。

## <a name="next-steps"></a>后续步骤

了解如何检测并使用人脸特征点。

> [!div class="nextstepaction"]
> [如何检测图像中的人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
