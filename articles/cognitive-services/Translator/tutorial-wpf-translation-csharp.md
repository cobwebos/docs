---
title: 如何使用 C#、Azure 认知服务编写 Microsoft Translator WPF 应用程序 | Microsoft Docs
description: 了解如何使用文本翻译服务翻译文本，获取受支持语言的本地化列表及更多内容。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365801"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>如何在 C# 中编写 Microsoft Translator WPF 应用程序

在本教程中，我们将使用 Microsoft 文本翻译 API (V3) 构建交互式文本翻译工具，该 API 是 Azure 中 Microsoft 认知服务的一部分。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 请求服务支持的语言短代码列表
> * 检索与这些语言代码对应的本地化语言名称列表
> * 获取从一种语言到另一种语言的用户输入文本的翻译版本

此应用程序还具有与其他两项 Microsoft 认知服务集成的功能。

|||
|-|-|
|[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|用于根据需要自动检测要翻译的文本的源语言|
|[必应拼写检查](https://azure.microsoft.com/services/cognitive-services/spell-check/)|对于英语源文本，用于更正拼写错误，使翻译更准确

![[运行的教程程序]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要任意版本的 Visual Studio 2017，包括社区版。

你还需要该程序中使用的三项 Azure 服务的订阅密钥。 你可以从 Azure 仪表板获取文本翻译服务的密钥。 提供免费定价层，使你每个月能够免费翻译最多 200 万个字符。

文本分析和必应拼写检查服务均提供免费试用，你可以在[试用认知服务](https://azure.microsoft.com/try/cognitive-services/)上注册。 你还可以通过 Azure 仪表板为任一服务创建订阅。 文本分析具有免费层。

本教程的源代码如下所示。 订阅密钥必须作为 `MainWindow.xaml.cs` 中的 `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` 等变量复制到源代码中。

> [!IMPORTANT]
> 文本分析服务在多个地区提供。 教程源代码中的 URI 位于 `westus` 区域，该区域是用于免费试用的区域。 如果你在其他地区有订阅，请相应地更新此 URI。

## <a name="source-code"></a>源代码

这是 Microsoft 文本翻译 API 的源代码。 要运行该应用，请将源代码复制到 Visual Studio 中新 WPF 项目的相应文件中。

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

这是提供应用程序功能的代码隐藏文件。

```cs
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog when we get an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
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

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
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
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

此文件定义应用程序的用户界面，即 WPF 表单。 如果要设计自己的表单版本，则不需要此 XAML。

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
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
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>服务终结点

Microsoft Translator 服务有许多端点，可提供各种翻译功能。 我们在本教程中使用的功能是：

|||
|-|-|
|`Languages`|返回文本翻译 API 的其他操作当前支持的语言集。|
|`Translate`|给定源文本、源语言代码和目标语言代码，返回源文本到目标语言的转换。|

## <a name="the-translation-app"></a>翻译应用

翻译应用程序的用户界面使用 Windows Presentation Foundation (WPF) 构建。 按照以下步骤在 Visual Studio 中创建一个新的 WPF 项目。

* 从“文件”菜单上，单击“新建”>“项目”。
* 在“新建项目”窗口中，打开“已安装”>“模板”>“Visual C#”。 可用项目模板的列表显示在对话框的中心。
* 确保在项目模板列表上方的下拉菜单中选择“NET Framework 4.5.2”。
* 单击项目模板列表中的“WPF 应用(.NET Framework)”。
* 使用对话框底部的字段命名新项目以及包含它的解决方案。
* 单击“确定”以创建新项目和解决方案。

![[在 Visual Studio 中创建新的 WPF 应用程序]](media/translator-text-csharp-new-project.png)

将以下 .NET 框架程序集的引用添加到项目中。

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

此外，将 NuGet 包 `Newtonsoft.Json` 安装到你的项目。

现在在解决方案资源管理器中找到 `MainWindow.xaml` 文件并将其打开。 它最初为空。 这是完成的用户界面应有的外观，用蓝色控件的名称批注。 对用户界面中的控件使用相同的名称，因为它们也出现在代码中。

![[Visual Studio 设计器中主窗口的批注视图]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> 本教程的源代码包含此表单的 XAML 源。 你可以将其粘贴到项目中，而不是在 Visual Studio 中生成表单。

* `FromLanguageComboBox` (ComboBox) - 显示 Microsoft 文本翻译支持的语言列表。 用户选择他们要进行翻译的语言。
* `ToLanguageComboBox` (ComboBox) - 显示与 `FromComboBox` 相同的语言列表，但用于选择用户要翻译到的语言。
* `TextToTranslate` (TextBox) - 用户在此处输入要翻译的文本。
* `TranslateButton` (Button) - 用户单击此按钮（或按 Enter 键）以翻译文本。
* `TranslatedTextLabel` (Label) - 此处显示用户文本的翻译。

如果你正在对此表单制作你自己的版本，则无需完全照搬我们的说明。 但要确保语言下拉菜单足够宽，以避免切断语言名称。

## <a name="the-mainwindow-class"></a>MainWindow 类

代码隐藏文件 `MainWindow.xaml.cs` 是代码运行的地方，使程序完成它的工作。 两次发生此项工作：

* 程序启动时。 当实例化 `MainWindow` 时，我们使用 Translator 的 `GetLanguagesForTranslate` 和 `GetLanguageNames` API 检索语言列表，并用它们填充下拉菜单。 此任务在每个会话的开头执行一次。

* 当用户单击“翻译”按钮时，我们将检索用户的语言选择和他们输入的文本。 然后我们调用 `Translate` API 来执行翻译。 我们还可以调用其他函数来确定文本的语言并在翻译之前更正其拼写。

我们了解一下如何开始使用类：

```cs
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog when we get an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

此处声明的两个成员变量包含有关语言的信息：

|||
|-|-|
|`languageCodes`<br>字符串数组|缓存的语言代码。 Translator 服务使用短代码（例如英语为 `en`）来标识语言。|
|`languageCodesAndTitles`<br>SortedDictionary|将用户界面中的“友好”名称映射回 API 中使用的短代码。 始终按字母顺序排序，不考虑大小写。|

应用程序执行的第一个代码是 `MainWindow` 构造函数。 首先，我们将方法 `HandleExceptions` 设置为全局错误处理程序。 这样，如果没有处理任何异常，我们至少会收到错误警报。

接下来，我们检查以确保 API 订阅密钥长度都是 32 个字符。 如果不是，最可能的原因是某人未粘贴在其 API 密钥 (tsk) 中。 在这种情况下，我们会显示错误消息并进行纾困。（通过此测试当然并不表示密钥有效。）

如果至少密钥长度正确，则 `InitializeComponent()` 调用通过定位、加载和实例化主应用程序窗口的 XAML 描述来获取用户界面。

最后，我们设置了语言下拉菜单。 此任务需要三个单独的方法调用。 以下部分详细介绍这三种方法。

## <a name="get-supported-languages"></a>获取支持的语言

Microsoft Translator 服务在进行此类撰写时总共支持 61 种语言，并且可能会不时添加更多语言。 因此，最好不要在程序中对支持的语言进行硬编码。 相反，请询问 Translator 服务它支持哪种语言。 任何支持的语言都可以翻译成任何其他支持的语言。

调用 `Languages` API 以获取支持的语言列表。

`Languages` API 采用可选的 GET 查询参数 scope。 scope 可以包含以下三个值之一：`translation`、`transliteration` 和 `dictionary`。 我们将使用值 `translation`。

`Languages` API 还采用可选的 HTTP 标头 `Accept-Language`。 此标头的值确定在其中返回支持的语言名称的语言。 该值应该是格式标准的 BCP 47 语言标记。 我们将使用值 `en` 来获取英语的语言名称。

`Languages` API 返回如下所示的 JSON 响应。

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
    },
...
}
```

我们想要提取语言代码（例如，`af`）和语言名称（例如，`Afrikaans`）。 我们使用 NewtonSoft.Json 方法 [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) 来执行此操作。

有了这些背景知识，我们创建以下方法来检索语言代码及其名称。

```cs
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
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
```

`GetLanguagesForTranslate()` 首先创建 HTTP 请求。 `scope=translation` 查询字符串参数指定我们需要支持文本翻译的语言。 我们将文本翻译 API 订阅密钥添加到请求标头中。 我们还添加值为 `en` 的 `Accept-Language` 标头，以表示我们希望以英语返回支持的语言。

请求完成后，我们解析 JSON 响应并将其转换为字典。 我们将语言代码添加到 `languageCodes` 成员变量中。 然后，我们循环访问包含语言代码和友好语言名称的键/值对，并将它们添加到 `languageCodesAndTitles` 成员变量中。 （表单中的下拉菜单显示友好名称，但需要代码来请求翻译。）

## <a name="populate-the-language-menus"></a>填充语言菜单

大部分用户界面都是在 XAML 中定义的，因此除了调用 `InitializeComponent()` 之外，我们不需要执行太多操作即可对其进行设置。 我们需要做的其他唯一一件事情是将友好的语言名称添加到“目标语言”和“源语言”下拉列表中，这在 `PopulateLanguageMenus()` 中完成。

```cs
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

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

填充菜单是一个简单的事情，即迭代 `languageCodesAndTitles` 字典并将每个键（“友好”名称）添加到两个菜单中。 填充菜单后，我们将默认的“目标语言”和“源语言”设置为 “检测”（自动检测语言）和英语。

> [!TIP]
> 如果没有为菜单设置默认选择，则用户可以单击“翻译”而无需选择“目标语言”或“源语言”。 默认设置消除了处理此问题的需要。

现在已初始化 `MainWindow`，可创建用户界面。 在用户单击“翻译”按钮之前，我们无法再次获得控制权限。

## <a name="perform-translation"></a>执行翻译

当用户单击“翻译”时，WPF 会调用此处显示的 `TranslateButton_Click()` 事件处理程序。

```cs
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
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

在这里，我们首先从表单中检索“目标语言”和“源语言”以及用户输入的文本。

如果源语言设置为“检测”，我们调用 `DetectLanguage()` 来确定文本语言。 文本可能是 Translator API 不支持的语言（可检测到的语言比可翻译的语言多得多），或者文本分析 API 可能无法检测到它。 在这种情况下，系统会显示一条消息，通知用户并返回，而不进行翻译。

如果源语言是英语（无论是指定的还是检测到的），我们使用 `CorrectSpelling()` 进行文本拼写检查并应用任何更正。 更正的文本被填充回输入字段，因此用户知道已进行更正。 （用户可以在要翻译的文本之前添加连字符，以禁止拼写更正。）

如果用户未输入任何文本，或者“目标语言”和“源语言”相同，则无需翻译。 在这种情况下，我们避免提出请求。

执行翻译请求的代码看起来应很熟悉。 我们生成 URI、创建请求、发送请求并解析响应。 要显示文本，我们将其存储在 `TranslatedTextLabel` 控件中。

我们将文本传递给 POST 请求正文中序列化 JSON 数组中的 `Translate` API。 JSON 数组可以包含多个要翻译的文本，但在这里我们只包含一个。

名为 `X-ClientTraceId` 的 HTTP 标头是可选的。 该值应是一个 GUID。 当事情无法按预期方式执行时，客户端提供的跟踪 ID 可用于跟踪请求。 但是，为了有用处，客户端必须记录 X-ClientTraceID 的值。 客户端跟踪 ID 和请求日期可以帮助 Microsoft 诊断可能发生的问题。

> [!NOTE]
> 本教程重点介绍 Microsoft Translator 服务，因此我们不会详细介绍 `DetectLanguage()` 和 `CorrectSpelling()` 方法。 文本分析和必应拼写检查服务以 JSON 格式而不是 XML 提供响应，文本分析要求也将请求格式化为 JSON。 这些特征占我们已了解的方法的代码差异的大部分。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Microsoft 文本翻译 API 参考](http://docs.microsofttranslator.com/text-translate.html)
