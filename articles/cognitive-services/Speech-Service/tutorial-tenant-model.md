---
title: 创建租户模型（预览版）- 语音服务
titleSuffix: Azure Cognitive Services
description: 自动生成一个租户模型（包含 Office 365 数据的自定义语音），该模型利用 Office 365 数据，可带来针对组织特定术语的最佳语音识别体验，并且安全合规。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: c8a2855ce9cd320be3aea8b3b4a05f3b3eb39976
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578221"
---
# <a name="create-a-tenant-model-preview"></a>创建租户模型（预览版）

租户模型（包含 Office 365 数据的自定义语音）是 Office 365 企业客户可选择加入的一种服务，它根据组织的 Office 365 数据自动生成自定义语音识别模型。 创建的模型针对技术术语、行话和人名进行了优化，所有这些都以安全且合规的方式进行。

> [!IMPORTANT]
> 如果组织注册了租户模型，语音服务可能会访问组织的语言模型，该模型是通过组织中任何人都可查看的 Office 365 公共组电子邮件和文档生成的。 组织的 Office 365 管理员可以使用 Office 365 管理门户在组织范围内启用/禁用语言模型。

本教程介绍以下操作：

> [!div class="checklist"]
> * 注册，以在 Microsoft 365 管理中心使用租户模型
> * 获取语音订阅密钥
> * 创建租户模型
> * 部署租户模型
> * 配合使用租户模型和语音 SDK

## <a name="enroll-using-the-microsoft-365-admin-center"></a>使用 Microsoft 365 管理中心注册

部署租户模型之前，首先需要使用 Microsoft 365 管理中心进行注册。 此任务只能由 Microsoft 365 管理员完成。

1. 登录 [Microsoft 365 管理中心](https://admin.microsoft.com )。
2. 在左侧面板中，选择“设置”，然后选择“应用”   。

   ![租户模型注册](media/tenant-language-model/tenant-language-model-enrollment.png)

3. 找到并选择“Azure 语音服务”  。

   ![租户模型注册 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. 单击复选框并保存。

如果需要关闭租户模型，请导航回到此屏幕，取消选中复选框，然后保存。

## <a name="get-a-speech-subscription-key"></a>获取语音订阅密钥

要配合使用租户模型和语音 SDK，需要语音资源及其关联的订阅密钥。

1. 登录到 [Azure 门户](https://aka.ms/azureportal)。
2. 选择“创建资源”。 
3. 在搜索栏中，输入：“语音”  。
4. 选择“语音”，然后单击“创建”   。
5. 按照屏幕上的说明创建资源。 请确保：
   * “位置”设置为“eastus”或“westus”    。
   * “定价层”设置为“S0”   。
6. 单击“创建”。 
7. 几分钟后，资源创建完毕。 资源的“概述”部分提供了订阅密钥  。

## <a name="create-a-model"></a>创建模型

在管理员为组织启用租户模型后，可以基于 Office365 数据创建语言模型。

1. 登录 [Speech Studio](https://speech.microsoft.com/)。
2. 在右上角找到并单击齿轮图标（“设置”），然后选择“租户模型设置”  。

   ![“设置”菜单](media/tenant-language-model/tenant-language-settings.png)

3. 此时，会看到一条消息，告知是否有资格创建租户模型。
   > [!NOTE]
   > 北美的 Office 365 企业客户有资格创建租户模型（英语）。 对于客户密码箱 (CLB)、客户密钥 (CK) 或 Office 365 政府版客户，此功能不可用。 要确定是客户密码箱还是客户密钥客户，请参照以下说明操作：
   > * [客户密码箱](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [客户密钥](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365 政府版](https://www.microsoft.com/microsoft-365/government)

4. 接下来，选择“选择加入”  。 当租户模型准备就绪时，将收到一封包含说明的电子邮件。

## <a name="deploy-your-model"></a>部署模型

租户模型准备就绪后，请按照以下步骤部署模型：

1. 单击收到的确认电子邮件中的“查看模型”按钮，或者登录 [Speech Studio](https://speech.microsoft.com/)  。
2. 在右上角找到并单击齿轮图标（“设置”），然后选择“租户模型设置”  。

   ![“设置”菜单](media/tenant-language-model/tenant-language-settings.png)

3. 单击“部署”。 
4. 部署模型后，状态将更改为“已部署”  。

## <a name="use-your-model-with-the-speech-sdk"></a>配合使用模型和语音 SDK

部署模型后，配合使用模型和语音 SDK。 在本部分中，将使用提供的示例代码通过 Azure AD 身份验证来调用语音服务。

我们来看一下用于调用 C# 中的语音 SDK 的代码。 在本例中，将使用租户模型执行语音识别。 本指南默认平台已设置。 如需获取有关设置的帮助，请参阅[快速入门：识别语音，C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)。

将以下代码复制到项目中：

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

接下来，需要在命令行下重新生成并运行项目。 在运行该命令之前，需要更新一些参数。

1. 将 `<Username>` 和 `<Password>` 替换为有效租户用户的值。
2. 将 `<Subscription-Key>` 替换为语音资源的订阅密钥。 可在 [Azure 门户](https://aka.ms/azureportal)中的语音资源的“概述”部分获取此值  。
3. 将 `<Endpoint-Uri>` 替换为以下终结点。 请确保将 `{your-region}` 替换为创建语音资源的区域。 支持以下区域：`westus`、`westus2`和 `eastus`。 可在[Azure 门户](https://aka.ms/azureportal)中的语音资源的“概述”部分获取区域信息  。
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. 运行以下命令：
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>后续步骤

* [Speech Studio](https://speech.microsoft.com/)
* [语音 SDK](speech-sdk.md)
