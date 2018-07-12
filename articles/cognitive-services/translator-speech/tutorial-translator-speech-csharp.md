---
title: 语音翻译教程 (C#) | Microsoft Docs
titleSuffix: Cognitive Services
description: 了解如何使用语音翻译服务实时翻译文本。
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: e82c5c5ccfa6b7de8a9ec111140dad1a40ad44f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365696"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>教程：C# 中的 Microsoft Translator WPF 应用程序

本教程介绍使用 Microsoft 语音翻译服务的交互式语音翻译工具，该服务是 Azure 中 Microsoft 认知服务的一部分。 学习如何：

> [!div class="checklist"]
> * 请求服务支持的语言列表
> * 捕获音频并将其传输到服务
> * 以文本形式接收并显示语音翻译
> * （可选）播放翻译的语音（文本转语音）版本

可在 [GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator) 上获取此应用程序的 Visual Studio 解决方案文件。

## <a name="prerequisites"></a>先决条件

对于本教程，需要任意版本的 Visual Studio 2017，包括社区版。 

Visual Studio 解决方案还会生成应用程序安装程序。 需要 [WiX 工具集](http://wixtoolset.org/)和 [WiX 工具集 Visual Studio 扩展](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension)才能支持此功能。

还需要语音翻译服务的订阅密钥，可从 Microsoft Azure 仪表板获取。 可使用免费定价层进行翻译，免费定价层每个月免费提供 10 小时的语音翻译服务。 此层对本教程而言就已足够。

还需要第三方 [JSON.Net 库](https://www.newtonsoft.com/json)（来自 Newtonsoft）。 如果在 Visual Studio 选项中启用了两个“包还原”复选框，则此程序集由 NuGet 自动安装。

## <a name="trying-the-translation-app"></a>试用翻译应用

在 Visual STudio 中打开 Microsoft 语音翻译工具解决方案 (`SpeechTranslator.sln`) 后，按 F5 生成并启动该应用程序。  程序的主窗口随即显示。

![[语音翻译工具主窗口]](media/speech-translator-main-window.png)

第一次运行时，从“设置”菜单选择“帐户设置”，打开以下所示的窗口。

![[语音翻译工具主窗口]](media/speech-translator-settings-window.png)

将 Microsoft 语音翻译订阅密钥粘贴到此窗口中，然后单击“保存”。 在多次运行间，密钥得以保存。

返回主窗口，选择要使用的音频输入和输出设备以及源语言和目标语言。 如果需要听翻译的音频，请确保选中“TTS”（文本转语音）选项。 如果需要在说话时看到部分推测性翻译，请启用“部分结果”选项。

最后，单击“开始”开始翻译。 讲出希望翻译的内容并查看窗口中显示的已识别文本和翻译。 如果已启用 TTS 选项，还会听到翻译。

## <a name="obtaining-supported-languages"></a>获取支持的语言

在撰写本文时，Microsoft Translator 服务支持六十多种语言进行文本翻译。 语音翻译支持的语言数量较少。 这些语言要求同时支持听录（语音识别）和合成（用于文本转语音输出）。

换言之，对于语音翻译，源语言必须是支持听录的语言。 假设需要文本结果，则输出语言可以是支持文本翻译的任意语言。 如需语音输出，则只能翻译成支持文本转语音的语言。

Microsoft 可能会不时地添加对新语言的支持。 为此，不应在应用程序中对受支持语言的任何知识进行硬编码。 相反，语音翻译 API 提供了语言终结点，使你可以在运行时检索受支持的语言。 可以选择接收一个或多个语言列表： 

| | |
|-|-|
|`speech`|支持语音听录的语言。 可以是语音翻译的源语言。|
|`text`|支持文本转文本翻译的语言。 可以是使用文本输出时语音翻译的目标语言。|
|`tts`|支持语音合成的声音，每个声音与特定语言相关联。 可以是使用文本到语音时语音翻译的目标语言。 一种给定语言可能受多个声音支持。|

语言终结点不需要订阅密匙，并且其使用情况不会影响配额。 其 URI 为 `https://dev.microsofttranslator.com/languages`，并且以 JSON 格式返回其结果。

此处显示的 `MainWindow.xaml.cs` 中的 `UpdateLanguageSettingsAsync()` 方法调用语言终结点，获取受支持语言列表。 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

此方法首先构造发送给语言终结点的 HTTP 请求，用于请求语言的所有三个列表（`text`、`speech` 和 `tts`）。

语言终结点使用请求的 `Accept-Languages` 标头确定用于表述语言名称的语言。 例如，英语使用者称为“German”的语言在德语中称为“Deutsch”，在西班牙语中称为“Alemán”，语言列表会反映这些差异。 此标头使用系统的默认语言。

发送请求并收到 JSON 响应后，系统会将响应解析为内部数据结构。 然后使用这些结构进行源语言和目标语言菜单的构建。 

由于提供的声音取决于用户选择的目标语言，因此目前无法设置“声音”菜单。 但会存储每种语言的可用声音，供以后使用。 同一源文件中的 `ToLanguage_SelectionChanged` 处理程序稍后在用户选择目标语言时通过调用 `UpdateVoiceComboBox()` 更新“声音”菜单。 

为提高趣味性，如果用户之前未运行该应用程序，则随机选择目标语言。 （菜单设置在会话之间进行存储。）

## <a name="authenticating-requests"></a>对请求进行身份验证

要对 Microsoft 语音翻译服务进行身份验证，需要在连接请求中以 `Ocp-Apim-Subscription-Key` 的值的形式发送标头中的 Azure 订阅密钥。

## <a name="translation-overview"></a>翻译概述

翻译 API（WebSockets 终结点 `wss://dev.microsofttranslator.com/speech/translate`）接受以单声道、16 kHz、16 位带符号 WAVE 的格式转换的音频。 该服务返回包含已识别文本和已翻译文本的一个或多个 JSON 响应。 如果已请求文本转语音，则发送音频文件。

用户使用“麦克风/文件输入”菜单选择音频源。 音频可能来自音频设备（例如麦克风）或 `.WAV` 文件。

用户单击“开始”按钮时，系统将调用 `StartListening_Click` 方法。 反过来，此事件处理程序调用 `Connect()`，开始将音频发送到服务 API 终结点的过程。 `Connect()` 方法执行以下任务：


> [!div class="checklist"]
> * 从主窗口获取用户设置并进行验证
> * 初始化音频输入和输出流
> * 调用 `ConnectAsync()` 处理其余的工作

而 `ConnectAsync()` 处理以下任务：

> [!div class="checklist"]
> * 在标头 `Ocp-Apim-Subscription-Key` 中使用 Azure 订阅密钥进行身份验证
> * 创建 `SpeechClient` 实例（可在 `SpeechClient.cs` 中找到），与服务进行通信
> * 初始化 `TextMessageDecoder` 和 `BinaryMessageDecoder` 实例（请参阅 `SpeechResponseDecoder.cs`），处理响应
> * 通过 `SpeechClient` 实例将音频发送到语音翻译服务
> * 接收并处理翻译结果

`SpeechClient` 的任务更少：

> [!div class="checklist"]
> * 建立与语音翻译服务的 WebSocket 连接
> * 通过套接字发送音频数据并接收响应

## <a name="a-closer-look"></a>详细了解

现在应该更清楚地了解应用程序的各部分如何协同工作，执行翻译请求。 让我们来看一些代码，重点关注相关部分。

以下是 `Connect()` 的部分版本，显示音频流设置：

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

`Connect()` 的很大一部分涉及 `SpeechClientOptions` 实例（请参阅 `SpeechClientOptions.cs`）的创建，用于保存翻译选项。 选项包括连接到服务所需的信息（例如身份验证密钥和主机名）以及用于翻译的功能。 此处的字段映射到[语音翻译 API](http://docs.microsofttranslator.com/speech-translate.html) 公开的标头字段和 HTTP 参数。

`Connect()` 还创建并初始化音频输入设备（变量 `sampleProvider`），该设备用作待翻译的语音源。 此设备为硬件输入设备（如麦克风）或包含 WAVE 音频数据的文件。

以下是 `ConnectAsync()` 方法，该方法实例化 `speechClient` 类并连接到匿名函数，以处理来自服务的文本和二进制响应。

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

进行身份验证后，该方法创建 `SpeechClient` 实例。 `SpeechClient.cs` 中的 `SpeechClient` 类在收到二进制和文本数据后调用事件处理程序。 连接失败或断开连接时，将调用其他处理程序。

二进制数据是在启用 TTS 时由服务发送的音频（文本转语音输出）。 文本数据是语音文本的部分或完整翻译。 因此，在实例化之后，该方法将连接函数处理这些消息：对于音频，存储供以后回放，对于文本，将其显示在窗口中。

## <a name="next-steps"></a>后续步骤

此代码示例是一个功能丰富的应用程序，演示了语音翻译 API 的用法。 因此，需要了解相当多的活动部件。 你已了解最重要的部分。 对其他部分而言，在 Visual Studio 中设置几个断点并完成翻译过程可能很有帮助。 了解示例应用程序后，即可在自己的应用程序中使用语音翻译服务。

> [!div class="nextstepaction"]
> [Microsoft 语音翻译 API 参考](http://docs.microsofttranslator.com/speech-translate.html)
