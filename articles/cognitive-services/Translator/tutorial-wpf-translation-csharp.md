---
title: 教程：在 C# 中使用 WPF 创建翻译应用 - 翻译
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将创建一个 WPF 应用，通过单个订阅密钥来执行文本翻译、语言检测和拼写检查。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 0d500a7c24538adb139a42924134f784973f496b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588515"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>教程：使用 WPF 创建翻译应用

在本教程中，你将生成一个使用 Azure 认知服务通过单个订阅密钥进行文本翻译、语言检测和拼写检查的 [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2019) 应用。 具体而言，该应用将调用“翻译”和[必应拼写检查](https://azure.microsoft.com/services/cognitive-services/spell-check/)中的 API。

什么是 WPF？ WPF 是可以创建桌面客户端应用的 UI 框架。 WPF 开发平台支持广泛的应用开发功能，包括应用模型、资源、控件、图形、布局、数据绑定、文档和安全性。 它是 .NET Framework 的一个子集，因此，如果你以前曾经在 .NET Framework 中使用 ASP.NET 或 Windows 窗体生成过应用，则应会熟悉该编程体验。 WPF 使用可扩展应用标记语言 (XAML) 为应用编程提供声明性模型，后续部分将提供相关介绍。

本教程介绍以下操作：

> [!div class="checklist"]
> * 在 Visual Studio 中创建 WPF 项目
> * 将程序集和 NuGet 包添加到项目
> * 使用 XAML 创建应用 UI
> * 使用“翻译”获取语言、翻译文本以及检测源语言
> * 使用必应拼写检查 API 验证输入和提高翻译准确度
> * 运行 WPF 应用

### <a name="cognitive-services-used-in-this-tutorial"></a>本教程中使用的认知服务

以下列表包含本教程中使用的认知服务。 请单击链接浏览每项功能的 API 参考。

| 服务 | Feature | 说明 |
|---------|---------|-------------|
| 转换器 | [获取语言](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | 检索文本翻译支持的语言的完整列表。 |
| 转换器 | [Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | 将文本翻译成 60 多种语言。 |
| 转换器 | [Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | 检测输入文本的语言。 包含检测置信度评分。 |
| 必应拼写检查 | [拼写检查](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | 更正拼写错误以提高翻译准确度。 |

## <a name="prerequisites"></a>先决条件

在继续之前，需要准备好以下各项：

* 一个 Azure 认知服务订阅。 [获取认知服务密钥](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#create-a-new-azure-cognitive-services-resource)。
* 一台 Windows 计算机
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) - Community 或 Enterprise 版

> [!NOTE]
> 对于本教程，我们建议在美国西部区域创建订阅。 否则，在完成本练习的过程中，需要在代码中更改终结点和区域。  

## <a name="create-a-wpf-app-in-visual-studio"></a>在 Visual Studio 中创建 WPF 应用

要做的第一件事是在 Visual Studio 中设置项目。

1. 打开 Visual Studio。 选择“创建新项目”。
1. 在“创建新项目”中，找到并选择“WPF 应用(.NET Framework)”。  可以在“语言”中选择“C#”，缩小选项范围。
1. 选择“下一步”，然后将项目命名为 `MSTranslatorDemo`。
1. 将框架版本设置为“.NET Framework 4.7.2”或更高版本，然后选择“创建”。 
   ![在 Visual Studio 中输入名称和框架版本](media/name-wpf-project-visual-studio.png)

现已创建项目。 你会注意到，有两个选项卡已打开：`MainWindow.xaml` 和 `MainWindow.xaml.cs`。 在整个教程中，我们会将代码添加到这两个文件。 我们将修改应用的用户界面的 `MainWindow.xaml`。 我们将修改用于调用“翻译”和“必应拼写检查”的 `MainWindow.xaml.cs`。
   ![检查环境](media/blank-wpf-project.png)

在下一部分，我们会将程序集和 NuGet 包添加到项目以实现附加的功能，例如 JSON 分析。

## <a name="add-references-and-nuget-packages-to-your-project"></a>将引用和 NuGet 包添加到项目

我们的项目需要几个 .NET Framework 程序集，以及要使用 NuGet 包管理器安装的 NewtonSoft.Json。

### <a name="add-net-framework-assemblies"></a>添加 .NET Framework 程序集

将程序集添加到项目，以序列化和反序列化对象，以及管理 HTTP 请求和响应。

1. 在 Visual Studio 的解决方案资源管理器中找到你的项目。 右键单击项目，然后选择“添加”>“引用”，打开“引用管理器”。 
1. “程序集”选项卡列出了可供引用的所有 .NET Framework 程序集。 使用右上方的搜索栏来搜索引用。
   ![添加程序集引用](media/add-assemblies-2019.png)
1. 为项目选择以下引用：
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * System.Web.Extensions
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
1. 添加对项目的这些引用后，可以单击“确定”关闭“引用管理器”。 

> [!NOTE]
> 若要详细了解程序集引用，请参阅[如何：使用引用管理器添加或删除引用](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019)。

### <a name="install-newtonsoftjson"></a>安装 NewtonSoft.Json

我们的应用将使用 NewtonSoft.Json 来反序列化 JSON 对象。 遵照以下说明安装相应的包。

1. 在 Visual Studio 的解决方案资源管理器中找到你的项目，并右键单击该项目。 选择“管理 NuGet 包”。
1. 找到并选择“浏览”选项卡。
1. 在搜索栏中输入 [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)。

    ![找到并安装 NewtonSoft.Json](media/nuget-package-manager.png)

1. 选择该包并单击“安装”。
1. 安装完成后，关闭该选项卡。

## <a name="create-a-wpf-form-using-xaml"></a>使用 XAML 创建 WPF 窗体

若要使用该应用，需要创建一个用户界面。 我们将使用 XAML 创建一个窗体，使用户能够选择输入和翻译语言、输入要翻译的文本，并显示翻译输出。

让我们看看所要生成的对象。

![WPF XAML 用户界面](media/translator-text-csharp-xaml.png)

用户界面包括以下组件：

| 名称 | 类型 | 说明 |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | 显示 Microsoft 文本翻译支持的语言列表。 用户选择他们要进行翻译的语言。 |
| `ToLanguageComboBox` | ComboBox | 显示与 `FromComboBox` 相同的语言列表，但用于选择用户要翻译到的语言。 |
| `TextToTranslate` | TextBox | 让用户输入要翻译的文本。 |
| `TranslateButton` | 按钮 | 使用此按钮翻译文本。 |
| `TranslatedTextLabel` | Label | 显示翻译。 |
| `DetectedLanguageLabel` | Label | 显示检测到的待翻译文本 (`TextToTranslate`) 语言。 |

> [!NOTE]
> 我们将使用 XAML 源代码创建此窗体，但是，你也可以使用 Visual Studio 中的编辑器创建此窗体。

将代码添加到项目。

1. 在 Visual Studio 中，选择 `MainWindow.xaml` 对应的选项卡。
1. 将以下代码复制到项目中，然后选择“文件”>“保存 MainWindow.xaml”以保存所做的更改。
   ```xaml
   <Window x:Class="MSTranslatorDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
现在，Visual Studio 中应会显示该应用的用户界面预览， 如上图所示。

窗体现已准备就绪。 接下来，让我们编写一些代码来使用文本翻译和必应拼写检查。

> [!NOTE]
> 可以随意调整此窗体或创建自己的窗体。

## <a name="create-your-app"></a>创建应用

`MainWindow.xaml.cs` 包含用于控制应用的代码。 在接下来的几个部分，我们将添加代码以填充下拉菜单，并调用“翻译”和“必应拼写检查”公开的几个 API。

* 启动程序并实例化 `MainWindow` 时，将调用“翻译”的 `Languages` 方法来检索和填充语言选择下拉菜单。 此操作只会在每个会话开始时发生一次。
* 单击“翻译”按钮时，将检索用户选择的语言和文本、针对输入执行拼写检查，然后向用户显示翻译内容和检测到的语言。
  * 调用“翻译”的 `Translate` 方法来翻译 `TextToTranslate` 中的文本。 此调用还包括使用下拉菜单选择的 `to` 和 `from` 语言。
  * 调用“翻译”的 `Detect` 方法来确定 `TextToTranslate` 的文本语言。
  * 使用必应拼写检查来验证 `TextToTranslate` 并调整拼写错误。

整个项目封装在 `MainWindow : Window` 类中。 让我们先添加代码以设置订阅密钥、为“翻译”和“必应拼写检查”声明终结点，并初始化应用。

1. 在 Visual Studio 中，选择 `MainWindow.xaml.cs` 对应的选项卡。
1. 将预先填充的 `using` 语句替换为以下内容。  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. 找到 `MainWindow : Window` 类，并将其替换为以下代码：
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. 添加认知服务订阅密钥并保存。

在此代码块中，我们已声明两个成员变量，其中包含有关可用翻译语言的信息：

| 变量 | 类型 | 说明 |
|----------|------|-------------|
|`languageCodes` | 字符串数组 |缓存的语言代码。 Translator 服务使用短代码（例如英语为 `en`）来标识语言。 |
|`languageCodesAndTitles` | 排序的字典 | 将用户界面中的“友好”名称映射回 API 中使用的短代码。 始终按字母顺序排序，不考虑大小写。 |

然后，在 `MainWindow` 构造函数中，我们添加了使用 `HandleExceptions` 的错误处理机制。 此错误处理可以确保在异常未经处理时提供警报。 然后运行检查，以确认提供的订阅密钥长度是否为 32 个字符。 如果该密钥小于/大于 32 个字符，将引发错误。

如果密钥至少长度正确，则 `InitializeComponent()` 调用通过定位、加载和实例化主应用窗口的 XAML 说明来运转用户界面。

最后，我们添加了用于调用相应方法的代码，以检索要翻译的语言，以及填充应用用户界面的下拉菜单。 不要担心，我们稍后就会剖析这些调用背后的代码。

## <a name="get-supported-languages"></a>获取支持的语言

“翻译”目前支持 60 多种语言。 由于支持的新语言会不断增加，建议调用“翻译”公开的语言资源，而不要对应用中的语言列表进行硬编码。

在本部分，我们将对语言资源创建 `GET` 请求，并指定我们希望提供可用于翻译的语言列表。

> [!NOTE]
> 使用语言资源可以结合以下查询参数筛选语言支持：音译、字典和翻译。 有关详细信息，请参阅 [API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)。

在继续之前，让我们查看调用语言资源后的示例输出：

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

在此输出中，可以提取特定语言的语言代码和 `name`。 我们的应用将使用 NewtonSoft.Json 来反序列化 JSON 对象 ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm))。

从最后一部分离开的位置着手，添加一个方法用于获取应用支持的语言。

1. 在 Visual Studio 中，打开 `MainWindow.xaml.cs` 对应的选项卡。
2. 将以下代码添加到项目：
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

`GetLanguagesForTranslate()` 方法创建 HTTP GET 请求，并使用 `scope=translation` 查询字符串参数来限制支持的翻译语言的请求范围。 将添加值为 `en` 的 `Accept-Language` 标头，以便以英语返回受支持的语言。

分析 JSON 响应并将其转换为字典。 然后将语言代码添加到 `languageCodes` 成员变量。 将循环访问包含语言代码和友好语言名称的键/值对，并将它们添加到 `languageCodesAndTitles` 成员变量中。 窗体中的下拉菜单显示友好名称，但需要使用代码来请求翻译。

## <a name="populate-language-drop-down-menus"></a>填充语言下拉菜单

用户界面是使用 XAML 定义的，因此除了调用 `InitializeComponent()` 以外，不需要执行过多的操作即可对其进行设置。 需要做的一件事是将友好的语言名称添加到“翻译源语言”和“翻译目标语言”下拉菜单。  `PopulateLanguageMenus()` 方法用于添加这些名称。

1. 在 Visual Studio 中，打开 `MainWindow.xaml.cs` 对应的选项卡。
2. 将以下代码添加到项目中的 `GetLanguagesForTranslate()` 方法下面：
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

此方法循环访问 `languageCodesAndTitles` 字典并将每个密钥添加到两个菜单。 填充菜单后，默认的源语言和目标语言将分别设置为“检测”和“英语”。 

> [!TIP]
> 如果没有为菜单设置默认选择，则用户可以单击“翻译”而无需选择“目标语言”或“源语言”。 默认设置消除了处理此问题的需要。

初始化 `MainWindow` 并创建用户界面后，只有在单击“翻译”按钮之后，此代码才会运行。

## <a name="detect-language-of-source-text"></a>检测源文本的语言

现在，我们将创建一个方法用于通过“翻译”检测源文本（在文本区域中输入的文本）的语言。 此请求返回的值将在稍后的翻译请求中使用。

1. 在 Visual Studio 中，打开 `MainWindow.xaml.cs` 对应的选项卡。
2. 将以下代码添加到项目中的 `PopulateLanguageMenus()` 方法下面：
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

此方法对“检测”资源创建 HTTP `POST` 请求。 此方法采用单个参数 `text`，该参数作为请求正文连同请求一起传递。 稍后当我们创建翻译请求时，在 UI 中输入的文本将传递到此方法用于语言检测。

此外，此方法会评估响应的置信度评分。 如果评分大于 `0.5`，则在用户界面中显示检测到的语言。

## <a name="spell-check-the-source-text"></a>对源文本执行拼写检查

现在，我们将创建一个方法，用于通过必应拼写检查 API 对源文本执行拼写检查。 拼写检查可以确保“翻译”返回准确的翻译。 单击“翻译”按钮时，对源文本所做的任何更正将连同翻译请求一起传递。

1. 在 Visual Studio 中，打开 `MainWindow.xaml.cs` 对应的选项卡。
2. 将以下代码添加到项目中的 `DetectLanguage()` 方法下面：

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>单击后翻译文本

要做的最后一件事是，创建一个在用户界面中单击“翻译”按钮时要调用的方法。

1. 在 Visual Studio 中，打开 `MainWindow.xaml.cs` 对应的选项卡。
1. 将以下代码添加到项目中的 `CorrectSpelling()` 方法下面并保存：  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

第一步是获取“源”和“目标”语言，以及用户在窗体中输入的文本。 如果源语言设置为“检测”，则调用 `DetectLanguage()` 来确定源文本的语言。 文本可能采用了“翻译”不支持的语言。 在这种情况下，会显示一条消息来通知用户，并返回且不翻译文本。

如果源语言是英语（无论是指定的还是检测到的），将使用 `CorrectSpelling()` 进行文本拼写检查并应用任何更正。 更正的文本将添加回到文本区域，使用户能够看到已做出更正。

你应该很熟悉用于翻译文本的代码：生成 URI、创建请求、发送请求，并分析响应。 JSON 数组可以包含多个要翻译的对象，但是，我们的应用只需要一个对象。

成功请求之后，`TranslatedTextLabel.Content` 将替换为 `translation`，这会更新用户界面以显示翻译的文本。

## <a name="run-your-wpf-app"></a>运行 WPF 应用

现已使用 WPF 生成了一个可正常运行的翻译应用。 若要运行该应用，请在 Visual Studio 中单击“启动”按钮。

## <a name="source-code"></a>源代码

此项目的源代码已在 GitHub 中提供。

* [浏览源代码](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Microsoft 翻译参考](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
