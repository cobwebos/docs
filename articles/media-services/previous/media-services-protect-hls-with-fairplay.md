---
title: 使用 Microsoft PlayReady 或 Apple FairPlay 保护 HLS 内容 | Microsoft 文档
description: 本主题概括介绍并演示了如何使用 Azure 媒体服务通过 Apple FairPlay 动态加密 HTTP 实时传送视频流 (HLS) 内容。 它还演示了如何使用媒体服务许可证传送服务将 FairPlay 许可证传送到客户端。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 65188dacbb29fea5562ca5b83283861986719ce1
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866672"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>使用 Apple FairPlay 或 Microsoft PlayReady 保护 HLS 内容
使用 Azure 媒体服务，可使用以下格式动态加密 HTTP Live Streaming (HLS) 内容：  

* **AES-128 信封明文密钥**

    整个区块使用 **AES-128 CBC** 模式进行加密。 iOS 和 OS X 播放器本身支持解密流。 有关详细信息，请参阅[使用 AES-128 动态加密和密钥传递服务](media-services-protect-with-aes128.md)。
* **Apple FairPlay**

    各视频和音频示例都使用 **AES-128 CBC** 模式进行加密。 **FairPlay 流式处理** (FPS) 集成到设备操作系统，iOS 和 Apple TV 本身支持这项功能。 OS X 上的 Safari 使用加密媒体扩展 (EME) 接口支持启用 FPS。
* **Microsoft PlayReady**

下图显示了 **HLS + FairPlay 或 PlayReady 动态加密**工作流。

![动态加密工作流的图示](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

本文演示如何使用媒体服务通过 Apple FairPlay 动态加密 HLS 内容。 它还演示了如何使用媒体服务许可证传送服务将 FairPlay 许可证传送到客户端。

> [!NOTE]
> 如果还想使用 PlayReady 加密 HLS 内容，则需要创建通用的内容密钥并将其与资产相关联。 还需要配置内容密钥的授权策略，如[使用 PlayReady 动态通用加密](media-services-protect-with-playready-widevine.md)中所述。
>
>

## <a name="requirements-and-considerations"></a>要求和注意事项

在使用媒体服务传送通过 FairPlay 加密的 HLS 和传送 FairPlay 许可证时，需要以下各项：

  * 一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。
  * 一个媒体服务帐户。 若要创建媒体服务帐户，请参阅[使用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。
  * 注册 [Apple 开发计划](https://developer.apple.com/)。
  * Apple 要求内容所有者获取[部署包](https://developer.apple.com/contact/fps/)。 说明已使用媒体服务实现密钥安全模块 (KSM)，以及正在请求最终 FPS 包。 最终 FPS 包中有如何生成证书和获取应用程序密钥 (ASK) 的说明。 可使用 ASK 配置 FairPlay。
  * Azure 媒体服务 .NET SDK **3.6.0** 版本或更高版本。

必须在媒体服务密钥传送端上设置以下各项：

  * **应用证书 (AC)**：这是一个包含私钥的 .pfx 文件。 创建此文件，并使用密码对其进行加密。

       配置密钥传送策略时，必须提供该密码和 Base64 格式的 .pfx 文件。

      以下步骤介绍如何为 FairPlay 生成 .pfx 证书文件：

    1. 从 https://slproweb.com/products/Win32OpenSSL.html 安装 OpenSSL。

        转到 Apple 提供的 FairPlay 证书和其他文件所在的文件夹。
    2. 从命令行运行以下命令。 这会将 .cer 文件转换为 .pem 文件。

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. 从命令行运行以下命令。 这会将 .pem 文件转换为包含私钥的 .pfx 文件。 然后 OpenSSL 会要求提供 .pfx 文件的密码。

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **应用证书密码**：用于创建 .pfx 文件的密码。
  * **应用证书密码 ID**：必须上传密码，其方式与上传其他媒体服务密钥类似。 使用 **ContentKeyType.FairPlayPfxPassword** 枚举值获取媒体服务 ID。 需要在密钥传送策略选项中使用此 ID。
  * **iv**：这是 16 字节的随机值。 该值必须与资产传送策略中的 iv 相匹配。 生成 iv 并将其放入以下两个位置：资产传送策略和密钥传送策略选项。
  * **ASK**：使用 Apple 开发人员门户生成证书时会收到此密钥。 每个开发团队都会收到唯一的 ASK。 请保存一份 ASK 副本，并将其存储在安全位置。 稍后需要将 ASK 作为 FairPlayAsk 配置到媒体服务。
  * **ASK ID**：将 ASK 上传到媒体服务中时，将获取此 ID。 必须使用 **ContentKeyType.FairPlayAsk** 枚举值上传 ASK。 因此，将返回媒体服务 ID，在设置密钥传送策略选项时应使用此 ID。

以下事项必须通过 FPS 客户端来设置：

  * **应用证书 (AC)**：这是一个包含公钥的 .cer/.der 文件，操作系统使用它来加密某些负载。 媒体服务需要了解它，因为播放器需要它。 密钥传送服务使用相应的私钥对其进行解密。

要播放 FairPlay 加密的流，需要先获取实际 ASK，然后生成实际证书。 该过程将创建所有三个部分：

  * .der 文件
  * .pfx 文件
  * .pfx 的密码

以下客户端支持使用 **AES-128 CBC** 加密的 HLS：OS X 上的 Safari、Apple TV、iOS。

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>配置 FairPlay 动态加密和许可证传送服务
下面是使用 FairPlay 保护资产的常规步骤，这些步骤使用媒体服务许可证传送服务，也使用动态加密。

1. 创建资产并将文件上传到资产。
2. 将包含文件的资产编码为自适应比特率 MP4 集。
3. 创建内容密钥并将其与编码资产相关联。  
4. 配置内容密钥授权策略。 指定以下项：

   * 传送方法（在本例中为 FairPlay）。
   * FairPlay 策略选项配置。 有关如何配置 FairPlay 的详细信息，请参阅以下示例中的 **ConfigureFairPlayPolicyOptions()** 方法。

     > [!NOTE]
     > 通常，可能只需配置一次 FairPlay 策略选项，因为仅有一套证书和 ASK。
     >
     >
   * 限制（开放或令牌）。
   * 特定于密钥传送类型的信息，用于定义将密钥传送到客户端的方式。
5. 配置资产传送策略。 传送策略配置包括：

   * 传送协议 (HLS)。
   * 动态加密类型（常用的 CBC 加密）。
   * 许可证获取 URL。

     > [!NOTE]
     > 如果要传送使用 FairPlay 和其他数字版权管理 (DRM) 系统加密的流，则必须配置单独的传送策略：
     >
     > * 一个 IAssetDeliveryPolicy，用于使用通用加密 (CENC) (PlayReady + Widevine) 和 Smooth with PlayReady 配置 HTTP 上的动态自适应流式处理 (DASH)
     > * 另一个 IAssetDeliveryPolicy 用来配置 HLS 的 FairPlay
     >
     >
6. 创建 OnDemand 定位符以获取流式处理 URL。

## <a name="use-fairplay-key-delivery-by-player-apps"></a>使用播放器应用执行的 FairPlay 密钥传送
可以通过使用 iOS SDK 开发播放器应用。 若要能够播放 FairPlay 内容，必须实现许可证交换协议。 此协议不由 Apple 指定。 而是取决于每个应用发送密钥传送请求的方式。 媒体服务 FairPlay 密钥传送服务需要 SPC 为采用以下形式的 www-form-url 编码后消息：

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player 支持 FairPlay 播放。 请参阅 [Azure Media Player 文档](https://amp.azure.net/libs/amp/latest/docs/index.html)了解详细信息。
>
>

## <a name="streaming-urls"></a>流 URL
如果使用了多个 DRM 加密资产，则应在流式处理 URL 中使用加密标记：(format='m3u8-aapl', encryption='xxx')。

请注意以下事项：

* 仅可以指定零个或一个加密类型。
* 如果资产仅应用了一种加密，则无需在 URL 中指定加密类型。
* 加密类型不区分大小写。
* 可以指定以下加密类型：  
  * **cenc**：通用加密（PlayReady 或 Widevine）
  * **cbcs-aapl**：FairPlay
  * **cbc**：AES 信封加密

## <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

1. 设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。 
2. 将以下元素添加到 app.config 文件中定义的 **appSettings**：

    ```xml
            <add key="Issuer" value="http://testacs.com"/>
            <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>示例

以下示例演示使用媒体服务传送使用 FairPlay 加密的内容的能力。 适用于 .NET 的 Azure 媒体服务 SDK 版本 3.6.0 中引入了此功能。 

使用本部分中所示的代码覆盖 Program.cs 文件中的代码。

>[!NOTE]
>不同 AMS 策略的策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的日期/访问权限，则应使用相同的策略 ID，例如，用于要长期就地保留的定位符的策略（非上传策略）。 有关详细信息，请参阅[此](media-services-dotnet-manage-entities.md#limit-access-policies)文章。

请务必将变量更新为指向输入文件所在的文件夹。

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="next-steps-media-services-learning-paths"></a>后续步骤：媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
