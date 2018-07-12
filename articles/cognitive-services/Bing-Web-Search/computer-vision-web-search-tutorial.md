---
title: 视觉搜索移动应用教程
description: 使用认知服务计算机视觉 API、必应 Web 搜索 API 和 Xamarin.Forms 跨平台框架实现视觉搜索的开源 C# 应用程序。
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365772"
---
# <a name="visual-search-mobile-app-tutorial"></a>视觉搜索移动应用教程

## <a name="introduction"></a>介绍  
本教程探讨了[计算机视觉 API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) 终结点和[必应 Web 搜索 API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 终结点，并展示了如何使用这些终结点通过 [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/) 生成基本的视觉搜索应用程序。  总的来说，本教程涵盖以下主题： 

> [!div class="checklist"]
> * 设置 Visual Studio 以开发 Xamarin.Forms 应用程序
> * 使用 [Xamarin 媒体插件](https://github.com/jamesmontemagno/MediaPlugin)捕获和导入图像数据
> * 使用计算机视觉 API 根据图像分析文本
> * 将搜索请求发送到必应 Web 搜索 API
> * 通过 [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)（使用 LINQ 和模型对象反序列化）分析这两个 API 的 JSON 响应
> * 为视觉搜索创建跨平台的 Xamarin.Forms 用户界面 

## <a name="prerequisites"></a>先决条件

此示例是在 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 中使用 Xamarin.Forms 开发出来的。 可以使用 Visual Studio 2017 Community Edition。 有关如何在 Visual Studio 中使用 Xamarin 的详细信息，请参阅 [Xamarin 文档](https://developer.xamarin.com/guides/cross-platform/getting_started/)。

此示例使用以下 NuGet 包：

> [!div class="checklist"]
> * [Xamarin 媒体插件](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

此应用程序使用以下认知服务 API：

> [!div class="checklist"]
> * [必应 Web 搜索 API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [计算机视觉 API](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

若要获取这些 API 的 30 天试用密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/)。 有关获取商业用途密钥的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/calculator/)。

## <a name="setup-for-development"></a>开发设置  

### <a name="installing-xamarin-on-windows"></a>在 Windows 上安装 Xamarin
在安装任意版本的 Visual Studio 2017 之后，打开 Visual Studio 安装程序，选择与 Visual Studio 安装关联的汉堡菜单，然后选择“修改”。  

![Visual Studio 安装程序](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

向下滚动到“移动与游戏”，并启用“使用 .NET 的移动开发”（如果尚未启用）。

![选中了 Xamarin.Forms 的 Visual Studio 安装程序](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

最后，单击窗口右下角的“修改”，等待安装 Xamarin。

### <a name="installing-xamarin-on-macos"></a>在 macOS 上安装 Xamarin
Visual Studio for Mac 预装了 Xamarin。 无需进行任何安装。

## <a name="building-and-running-the-app"></a>生成并运行应用

可以从[包含认知服务的视觉搜索应用](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/)中下载视觉搜索应用程序的 Visual Studio 解决方案文件 *(.sln)*。 可以使用 Web 浏览器下载 ZIP 存档，从 GitHub 将其克隆到工作站，或使用 Visual Studio 下载。

若要开始使用该示例，请在 Visual Studio 中打开 `VisualSearchApp.sln`。  初始化所需组件可能需要一些时间。

该应用程序需要两个第三方库：**Json.NET** 和 **Xamarin 媒体插件**。 可以使用 NuGet 包管理器直接在 Visual Studio 中安装这些库。 为解决方案选择“工具”>“NuGet 包管理器”>“管理 NuGet 包”，或在解决方案资源管理器中右键单击解决方案，然后从上下文菜单中选择“管理 NuGet 包”。

在 NuGet 窗口中，搜索并安装 **Xamarin 媒体插件** (Xam.Plugin.Media) 版本 2.6.2 和 **Json.NET** (Newtonsoft.Json) 10.0.3。 安装时一定要选择所有项目。

若要生成面向所有可用平台的应用程序，请按 **Ctrl + Shift + B** 或单击功能区菜单上的“生成”，然后选择“生成解决方案”。  若要从 Windows 系统编译和测试 iOS 代码，请参阅[该指南](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/)获得帮助。

在运行应用程序之前，必须选择目标配置、平台和项目。  Xamarin.Forms 应用程序编译为 Windows、Android 和 iOS 的本机代码。 本教程包含 Windows 版本示例的屏幕截图，但所有版本在功能上都是等效的。 本指南所用的部署设置如下所示。  

![Visual Studio 配置为针对 Android 手机进行编译](./media/computer-vision-web-search-tutorial/configuration-selection.png)

在生成过程成功完成并且你选择了目标平台后，便单击工具栏中的“开始”按钮或按 **F5**。 Visual Studio 会将解决方案部署到目标平台并启动它。

随即显示“添加密钥”页面。 （此页面在 `AddKeysPage.xaml` 中定义。）  

![“添加密钥”页面的图像](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

在此处粘贴计算机视觉和必应 Web 搜索 API 密钥。 计算机视觉 API 还要求选择托管终结点的服务器。

> [!TIP]
> 若要跳过此页面，请在 `App.xaml.cs` 中的相应位置输入此信息。 

应用程序通过执行测试查询来验证密钥，然后转到“OCR 选择”页面（在 `OcrSelectPage.xaml` 中定义）。
   
![“OCR 选择”页面的图像](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

在此屏幕的顶部，可以选择是打印还是手写要识别的文本。

> [!TIP]
> 使要尝试识别其文本的项目尽量保持水平，确保其均匀受光且无反光现象。 我们发现手写 OCR 有时更适用于脚本字体或其他“花哨”文本。

接下来，单击“拍照”或“导入照片”，以使用设备摄像头拍摄照片或选择存储在设备上的照片。

拍摄或选择照片后，图像将传递到计算机视觉 API。 “找到的词语”页面（在 `OcrResultsPage.xaml` 中定义）会显示在图像中识别出的任何词语。

![“OCR 结果”页面的图像](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> 我们用于这些结果的图像以 `SamplePhotos\TestImage.jpg` 的形式存储在源存储库中。

单击“找到的词语”页面上的某个项目时，将显示“Web 结果”页面 (`WebResultsPage.xaml`)，其中显示了该搜索最匹配的必应结果。

![“Web 结果”页面的图像](./media/computer-vision-web-search-tutorial/web-results-page.png)  
最后，在“Web 结果”页面上选择一个项目，以在嵌入式 Web 视图中打开链接。 （或向后导航以选择不同的结果。）

![“Web 视图”页面的图像](./media/computer-vision-web-search-tutorial/web-view-page.png)  

可以像在任何 Web 浏览器中一样与该页面交互，或导航回“Web 结果”页面。 

## <a name="review-and-learn"></a>温故知新

你已经试用过视觉搜索应用了，现在，让我们探究一下如何使用这两个 Microsoft 认知服务 API。  无论是将此示例作为自己应用程序的起点，还是仅仅作为 API 的操作说明，都可以逐屏查看该应用程序，确切了解其工作原理。

### <a name="add-keys-page"></a>“添加密钥”页面
“添加密钥”页面 UI 在 `AddKeysPage.xaml` 中定义，其主要逻辑在 `AddKeysPage.xaml.cs` 中定义。 由于已通过发出测试请求对密钥进行了验证，因此，确定如何在 C# 中使用认知服务终结点的时机已经成熟。  

这种交互的基本结构如下： 

1. 从 *System.Net.Http* 实例化 *HttpResponseMessage* 和 *HttpClient* 对象
2. 将所需的任何标头（在每个终结点的 API 参考中定义）附加到 *HttpClient* 对象
3. 发送包含数据的 GET 或 POST 请求，以向终结点 URI 添加所有必要的参数
4. 验证响应是否成功
5. 传递响应以进行进一步处理

检查必应搜索 API 密钥有效性的函数是 `CheckBingSearchKey()`，如下所示。

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

类似的函数 `CheckComputerVisionKey()` 用于验证计算机视觉密钥。

### <a name="ocr-select-page"></a>“OCR 选择”页面

“OCR 选择”页面 (`OcrSelectPage.xaml`) 有两个重要角色。 首先，它确定对目标照片执行何种 OCR。 其次，它允许用户捕获或打开他们希望处理的图像。

在跨平台应用程序中执行第二项任务一直以来都很麻烦，因为每个平台都需要不同的代码。 Xamarin 媒体插件只需几行代码便可执行该任务。

下面的函数包含使用 Xamarin 媒体插件捕获照片的示例。  在其中，我们：

1. 确保当前设备上有可用的摄像头
2. 实例化 `StoreCameraMediaOptions` 对象以指定保存捕获图像的位置
3. 捕获图像并获取包含图像数据的 `MediaFile` 对象
4. 将 `MediaFile` 解压缩为字节数组
5. 返回字节数组以进行进一步处理

下面就是使用 Xamarin 媒体插件捕获照片的 `TakePhoto()` 函数。  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
照片导入实用工具采用类似的工作原理。 这两个功能都可以在 `OcrSelectPage.xaml.cs` 中找到。 
 
> [!NOTE]
> 手写 OCR 终结点只能处理小于 4 MB 的照片。 为避免文件大小问题，我们通过在 `StoreCameraMediaOptions` 对象上设置选项 `PhotoSize = PhotoSize.Medium`，将照片缩小到原始大小的 50%。  如果设备拍摄的照片质量非常高，并因此出现错误，则可以尝试使用 `PhotoSize = PhotoSize.Small`。

下面是用于将 *MediaFile* 转换为字节数组的实用工具函数。

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>“OCR 结果”页面

“OCR 结果”页面使用 **Json.NET** [SelectToken 方法](http://www.newtonsoft.com/json/help/html/SelectToken.htm)显示从图像中提取的文本。  这两个 OCR 终结点的工作方式略有不同，因此都值得探讨。  

由于计算机视觉 API 仅托管在少数服务器位置中，因此必须在运行时动态构造其终结点。 函数 `SetOcrLocation`（在 `AppConstants.cs` 中找到的静态 *AppConstants* 类的一部分）用于设置 URI 终结点的位置。 它对应于用户在“添加密钥”页面上的选择或使用 `App.xaml.cs` 中设置的值。 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

我们来仔细看看这些 API 请求。 计算机视觉 OCR API 能够分析语言不确定的文本，但我们指示它搜索英文文本以改善结果。 我们还让该 API 为我们检测文本方向。 使用固定方向可能会改善我们的分析结果，但方向检测在移动应用程序中很有用。

可以从[打印光学字符识别 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc)中详细了解此终结点可用的参数。  

手写 OCR API 仍处于预览状态，目前仅适用于英文文本。  出于这个原因，其唯一的参数当前是一个标志，用于指示是否要分析手写文本。 手写 OCR API 可以分析机器打印文本和手写文本，但 `handwriting=false` 会对打印文本生成更好的结果。 

由于此应用程序是英文的，我们本来可以只在此示例中使用手写 OCR，并根据用户要我们识别的内容设置 `handwriting` 标志。  但为了便于说明，我们使用了两个终结点。  

可以从[手写光学字符识别 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)中详细了解此终结点可用的参数。

现在，让我们看一下调用这些 API 的函数。

`FetchPrintedWordList()` 使用打印 OCR 终结点来分析图像中的打印文本。 该 HTTP 请求采用与“添加密钥”页面中用于验证密钥的调用相似的结构，但我们需要发送照片。 照片太大，无法传入查询字符串，因此我们必须使用 HTTP POST 请求，而不是 GET 请求。 我们需要将照片（以字节数组的形式存储在内存中）编码成 `ByteArrayContent` 对象，并为此对象添加一个标头，以定义我们要发送的数据类型。 

可以在[计算机视觉 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)中了解可接受的内容类型。  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> 注意我们如何使用 **Json.NET** [SelectToken 方法](http://www.newtonsoft.com/json/help/html/SelectToken.htm)从响应对象中提取文本。 当我们只需要 JSON 响应的特定部分时，`SelectToken` 是理想之选，我们稍后可以将其传递给下一个函数。 在代码的其他部分，我们将 JSON 响应反序列化为 `ModelObjects.cs` 中定义的模型对象。

打印 OCR 请求与手写 OCR 请求的主要区别在于，打印 OCR 服务将识别出的文本作为 HTTP 响应的一部分返回，而手写 OCR 服务需要发出额外的请求来获取信息。 初始手写 OCR 请求返回 HTTP 202 状态，该状态仅表示已开始处理。 此响应包含一个终结点，客户端必须检查该终结点以获取完成的响应（如果可用）。 请参阅 `FetchHandwrittenWordList()` 以了解其工作原理。

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` 是手写 OCR 过程的第二部分。 它对从初始响应的元数据中提取的 URI 执行 ping 操作，直到获得结果或函数超时。在此函数自己的线程上对其进行异步调用非常重要。 否则，此方法将锁定用户界面，直到完成处理。

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>“Web 结果”页面
在用户选择了“OCR 结果”页面上显示的搜索词后，我们继续前往“Web 结果”页面。  在这里，我们将构造一个[必应 Web 搜索 API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 请求，将其发送到服务的终结点，并使用 **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) 方法反序列化 JSON 响应。  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

当你对用户可能需要的内容提供尽可能多的信息时，必应 Web 搜索 API 最有效。 特别是，在几乎所有情况下，你都应该使用 `mkt` 和 `setLang` 参数（此处设置为美国英语）来标识用户的位置和首选语言。

> [!NOTE]
> 我们保持了必应 Web 搜索查询的简洁性，确保源代码易于理解。  在实际应用程序中，应将以下标头添加到 HTTP 请求中，以改善结果。 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> 可以在[必应 Web 搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)中详细了解这些标头值

## <a name="next-steps"></a>后续步骤
Microsoft 认知服务提供了许多可以轻松集成到此应用程序中的附加服务。  例如，可以：

* 添加[必应实体搜索](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/)以扩充 Web 搜索结果
* 用[必应自定义搜索](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/)代替必应 Web 搜索
* 使用[必应图像搜索](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)图像洞察功能详细了解已捕获的图像，并在 Web 上查找类似图像
* 使用[必应拼写检查](https://azure.microsoft.com/services/cognitive-services/spell-check/)进一步提高已分析文本的质量
* 集成 [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) 以查看以不同语言提取的文本
* 混合并匹配[认知服务门户](https://azure.microsoft.com/services/cognitive-services/)中的其他服务；没有任何限制！
