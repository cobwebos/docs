---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: cd2bb595df91597059131b1dd7274e1a7ba36066
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875411"
---
本快速入门介绍使用语音 SDK 进行说话人识别的基本设计模式，其中包括：

* 依赖于文本和独立于文本的验证
* 用于识别一组语音中的语音样本的说话人识别
* 删除语音配置文件

若要深入了解语音识别概念，请参阅[概述](../../../speaker-recognition-overview.md)一文。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 目前仅 `westus` 区域中创建的 Azure 语音资源支持说话人识别。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，使用以下说明：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>导入依赖项

若要运行本文中的示例，请在脚本的最前面包含以下 `using` 语句。

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。 在此示例中，你将使用订阅密钥和区域创建一个 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。 此外，你将创建一些基本的样板代码，在本文的余下部分，你将修改这些代码以进行不同的自定义操作。

请注意，区域设置为 `westus`，因为它是该服务唯一支持的区域。

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>依赖于文本的验证

说话人验证是确认说话人与已知或已注册的语音匹配的操作。 第一步是注册语音配置文件，以便该服务有可与将来的语音样本进行比较的内容。 在此示例中，使用依赖于文本的策略注册配置文件，该策略需要用于注册和验证的特定通行短语。 有关支持的通行短语的列表，请参阅[参考文档](https://docs.microsoft.com/rest/api/speakerrecognition/)。

首先在 `Program` 类中创建以下函数以注册语音配置文件。

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

在此函数中，`await client.CreateProfileAsync()` 实际会创建新的语音配置文件。 创建后，指定音频示例的输入方式，使用本示例中的 `AudioConfig.FromDefaultMicrophoneInput()` 捕获来自默认输入设备的音频。 接下来，注册 `while` 循环中的音频示例，该循环跟踪注册所必须的剩余的示例数。 在每次迭代中，`client.EnrollProfileAsync(profile, audioInput)` 都会提示你对着麦克风说出通行短语，并将该示例添加到语音配置文件。

注册完成后，调用 `await SpeakerVerify(config, profile, profileMapping)` 以针对刚创建的配置文件进行验证。 添加另一个函数以定义 `SpeakerVerify`。

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

在此函数中，传递刚创建的 `VoiceProfile` 对象以初始化要验证的模型。 接下来，`await speakerRecognizer.RecognizeOnceAsync(model)` 提示你再次说出通行短语，但这次将针对语音配置文件进行验证，并返回范围在 0.0-1.0 之间的相似性分数。 `result` 对象还会根据通行短语是否匹配返回 `Accept` 或 `Reject`。

接下来，修改 `Main()` 函数以调用你创建的新函数。 此外，请注意，创建 `Dictionary<string, string>` 以通过函数调用传递引用。 出现这种情况的原因是，该服务不允许使用已创建的 `VoiceProfile` 存储用户可读的名称，并且出于隐私目的仅存储 ID 号。 在 `VerificationEnroll` 函数中，使用新创建的 ID 以及文本名称向此字典添加一个条目。 在需要显示用户可读名称的应用程序开发方案中，必须将此映射存储到某个位置，该服务无法对其进行存储。

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

运行该脚本，系统会提示你说出短语“我的语音是我的 passport，验证我”三次进行注册，再说一次进行验证。 返回的结果是相似性分数，可用于创建自己的自定义阈值进行验证。

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>独立于文本的验证

与依赖于文本的验证不同，独立于文本的验证 ：

* 不需要说出某个通行短语，可以说任何内容
* 不需要三个音频样本，但需要总共 20 秒的音频

对 `VerificationEnroll` 函数进行一些简单的更改，以便切换到独立于文本的验证。 首先，将验证类型更改为 `VoiceProfileType.TextIndependentVerification`。 接下来，更改 `while` 循环以跟踪 `result.RemainingEnrollmentsSpeechLength`，这将继续提示你说话，直到捕获 20 秒的音频。

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

再次运行该程序，并在验证阶段说任何内容，因为无需通行短语。 同样，将返回相似性分数。

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>说话人识别

说话人识别用于确定谁正在一组给定的注册语音中说话。 此过程与独立于文本的验证非常相似，主要区别在于前者能够一次针对多个语音配置文件进行验证，而不是针对单个配置文件进行验证。

创建函数 `IdentificationEnroll` 以注册多个语音配置文件。 每个配置文件的注册过程与独立于文本的验证的注册过程相同，并且每个配置文件需要 20 秒的音频。 此函数接受字符串 `profileNames` 的列表，并且将为列表中的每个名称创建新的语音配置文件。 此函数返回 `VoiceProfile` 对象的列表，这些对象在下一个函数中用于识别说话人。

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

创建以下函数 `SpeakerIdentification` 以提交识别请求。 与说话人验证请求相比，此函数中的主要差异在于使用 `SpeakerIdentificationModel.FromProfiles()`，它接受 `VoiceProfile` 对象的列表。 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

将 `Main()` 函数更改为以下函数。 创建字符串 `profileNames` 的列表，将这些字符串传递到 `IdentificationEnroll()` 函数。 这会提示你为此列表中的每个名称创建新的语音配置文件，因此可以添加更多名称，为好友或同事创建其他配置文件。

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

运行该脚本，系统会提示你说话以注册第一个配置文件的语音示例。 注册完成后，系统会提示你对列表 `profileNames` 中的每个名称重复此过程。 每个注册完成后，系统会提示你让任何人说话，该服务将尝试从已注册的语音配置文件中识别此人。

此示例仅返回最接近的匹配项，即相似性分数，但可以通过将 `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` 添加到 `SpeakerIdentification` 函数，获取包含前五个相似性分数的完整响应。

## <a name="changing-audio-input-type"></a>更改音频输入类型

本文中的示例使用默认设备麦克风作为音频示例的输入。 但是，在需要使用音频文件而不是麦克风输入的情况下，只需将 `AudioConfig.FromDefaultMicrophoneInput()` 的任何实例更改为 `AudioConfig.FromWavFileInput(path/to/your/file.wav)` 即可切换到文件输入。 还可以具有混合输入，例如，使用麦克风进行注册，使用文件进行验证。

## <a name="deleting-voice-profile-enrollments"></a>删除语音配置文件注册

若要删除已注册的配置文件，请对 `VoiceProfileClient` 对象使用 `DeleteProfileAsync()` 函数。 以下示例函数演示如何从已知的语音配置文件 ID 中删除语音配置文件。

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```
