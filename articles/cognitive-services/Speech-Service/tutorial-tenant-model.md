---
title: 创建租户模型（预览版）- 语音服务
titleSuffix: Azure Cognitive Services
description: 自动生成一个安全且合规的租户模型（包含 Office 365 数据的自定义语音），该模型使用 Office 365 数据，可带来针对组织特定术语的最佳语音识别体验。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a83ed5c9cec994c1bc4cadd5cf6208c159823658
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469011"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>教程：创建租户模型（预览版）

租户模型（包含 Office 365 数据的自定义语音）是 Office 365 企业客户可选择加入的一种服务，它根据组织的 Office 365 数据自动生成自定义语音识别模型。 此模型针对技术术语、行话和人名进行了优化，所有这些都以安全且合规的方式进行。

> [!IMPORTANT]
> 如果组织使用租户模型服务进行了注册，语音服务可能会访问组织的语言模型。 该模型是通过组织中任何人都可查看的 Office 365 公共组电子邮件和文档生成的。 组织的 Office 365 管理员可以通过 Office 365 管理门户在组织范围内启用/禁用语言模型。

本教程介绍以下操作：

> [!div class="checklist"]
> * 通过 Microsoft 365 管理中心注册租户模型
> * 获取语音订阅密钥
> * 创建租户模型
> * 部署租户模型
> * 配合使用租户模型和语音 SDK

## <a name="enroll-in-the-tenant-model-service"></a>注册租户模型服务

部署租户模型之前，需注册租户模型服务。 注册在 Microsoft 365 管理中心完成，只能由你的 Microsoft 365 管理员执行。

1. 登录 [Microsoft 365 管理中心](https://admin.microsoft.com)。

1. 在左窗格中，选择“设置”  ，然后从嵌套菜单中选择“设置”  ，然后从主窗口中选择“Azure 语音服务”  。

   ![“服务和加载项”窗格](media/tenant-language-model/tenant-language-model-enrollment.png)

1. 选中“允许组织范围内的语言模型”复选框，然后选择“保存更改”。  

   ![“Azure 语音服务”窗格](media/tenant-language-model/tenant-language-model-enrollment-2.png)

若要关闭租户模型实例，请执行以下操作：
1. 重复前面的步骤 1 和 2。
1. 清除“允许组织范围内的语言模型”复选框，然后选择“保存更改”。  

## <a name="get-a-speech-subscription-key"></a>获取语音订阅密钥

要配合使用租户模型和语音 SDK，需要语音资源及其关联的订阅密钥。

1. 登录 [Azure 门户](https://aka.ms/azureportal)。
1. 选择“创建资源”。 
1. 在“搜索”框中，键入“语音”   。
1. 在结果列表中，选择“语音”，然后选择“创建”。  
1. 按照屏幕上的说明创建资源。 请确保：
   * “位置”设置为“eastus”或“westus”    。
   * “定价层”设置为“S0”   。
1. 选择“创建”  。

   几分钟后，资源创建完毕。 资源的“概述”部分提供了订阅密钥  。

## <a name="create-a-language-model"></a>创建语言模型

在管理员为组织启用租户模型后，可以基于 Office 365 数据创建语言模型。

1. 登录 [Speech Studio](https://speech.microsoft.com/)。
1. 在右上角选择“设置”（齿轮图标），然后选择“租户模型设置”。  

   ![“租户模型设置”链接](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio 会显示一条消息，告知你是否有权创建租户模型。

   > [!NOTE]
   > 北美的 Office 365 企业客户有资格创建租户模型（英语）。 对于客户密码箱、客户密钥或 Office 365 政府版客户，此功能不可用。 若要确定自己是客户密码箱客户还是客户密钥客户，请参阅：
   > * [客户密码箱](/microsoft-365/compliance/customer-lockbox-requests)
   > * [客户密钥](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 政府版](https://www.microsoft.com/microsoft-365/government)

1. 选择“选择加入”  。

   当租户模型准备就绪时，你会收到一封确认电子邮件，其中包含更多说明。

## <a name="deploy-your-tenant-model"></a>部署租户模型

租户模型实例准备就绪后，请执行以下操作来部署它：

1. 在确认电子邮件中，选择“查看模型”按钮。  或者，登录 [Speech Studio](https://speech.microsoft.com/)。
1. 在右上角选择“设置”（齿轮图标），然后选择“租户模型设置”。  

   ![“租户模型设置”链接](media/tenant-language-model/tenant-language-settings.png)

1. 选择“部署”。 

   部署模型后，状态会更改为“已部署”  。

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>配合使用租户模型和语音 SDK

部署模型后，配合使用模型和语音 SDK。 在本部分中，我们使用示例代码通过 Azure Active Directory (Azure AD) 身份验证来调用语音服务。

我们来看一下用于调用 C# 中的语音 SDK 的代码。 在本例中，我们使用租户模型执行语音识别。 本指南默认平台已设置。 如果需要设置帮助，请参阅[快速入门：识别语音，C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)。

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

    // ServiceApplicationId is a fixed value. No need to change it.

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

接下来，需要在命令行下重新生成并运行项目。 在运行该命令之前，请通过以下操作更新一些参数：

1. 将 `<Username>` 和 `<Password>` 替换为有效租户用户的值。
1. 将 `<Subscription-Key>` 替换为语音资源的订阅密钥。 可在 [Azure 门户](https://aka.ms/azureportal)中的语音资源的“概述”部分获取此值  。
1. 将 `<Endpoint-Uri>` 替换为以下终结点。 请确保将 `{your region}` 替换为创建语音资源的区域。 支持以下区域：`westus`、`westus2`和 `eastus`。 可在[Azure 门户](https://aka.ms/azureportal)中的语音资源的“概览”部分获取区域信息  。
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. 运行以下命令：

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

在本教程中，你已了解如何使用 Office 365 数据创建自定义语音识别模型、如何部署它，以及如何将其与语音 SDK 一起使用。

## <a name="next-steps"></a>后续步骤

* [Speech Studio](https://speech.microsoft.com/)
* [语音 SDK](speech-sdk.md)
