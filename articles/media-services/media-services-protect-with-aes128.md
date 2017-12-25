---
title: "使用 AES-128 动态加密和密钥传送服务 | Microsoft Docs"
description: "借助 Microsoft Azure 媒体服务，可以传送使用 AES 128 位加密密钥加密的内容。 媒体服务还提供密钥传送服务，将加密密钥传送给已授权的用户。 本主题说明如何使用 AES-128 动态加密以及如何使用密钥传送服务。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: fd90c63baaf254f5086cbc99a2a22d61587ee365
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>使用 AES-128 动态加密和密钥传送服务
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> 若要获取最新版本的 Java SDK 并开始使用 Java 进行开发，请参阅[媒体服务的 Java 客户端 SDK 入门](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)。 <br/>
> 若要下载最新的媒体服务 PHP SDK，请在 [Packagist 存储库](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)中查找 0.5.7 版 Microsoft/WindowAzure 包。  

## <a name="overview"></a>概述
> [!NOTE]
> 请参阅此[博客文章](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)，了解有关使用 AES 加密内容并传送到 **macOS 上的 Safari** 的信息。
> 请参阅[此](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)视频，获取有关如何使用 AES 加密保护媒体内容的概述。
> 
> 

借助 Microsoft Azure 媒体服务，能够传送使用高级加密标准 (AES) 加密的 Http 实时流式处理 (HLS) 和平滑流（使用 128 位加密密钥）。 媒体服务还提供密钥传送服务，将加密密钥传送给已授权的用户。 如果需要媒体服务来加密资产，则需要将加密密钥与资产相关联，并配置密钥的授权策略。 当播放器请求流时，媒体服务将使用指定的密钥通过 AES 加密来动态加密内容。 为解密流，播放器从密钥传送服务请求密钥。 为了确定用户是否被授权获取密钥，服务将评估你为密钥指定的授权策略。

媒体服务支持通过多种方式对发出密钥请求的用户进行身份验证。 内容密钥授权策略可能受到一种或多种授权限制：开放或令牌限制。 令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用[简单 Web 令牌](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) 格式和 [JSON Web 令牌](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 格式的令牌。 有关详细信息，请参阅[配置内容密钥授权策略](media-services-protect-with-aes128.md#configure_key_auth_policy)。

为了充分利用动态加密，资产必须包含一组多码率 MP4 文件或多码率平滑流源文件。 还需要为资产配置传送策略（在本文后面部分介绍）。 然后，根据在流式处理 URL 中指定的格式，按需流式处理服务器会确保使用选定的协议来传送流。 因此，只需以单一存储格式存储文件并为其付费，然后媒体服务服务就会基于客户端的请求构建并提供相应响应。

本文适合开发受保护媒体传送应用程序的开发人员。 本文介绍如何使用授权策略来配置密钥传送服务，确保只有经过授权的客户端才能接收加密密钥。 此外还介绍如何使用动态加密。


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 动态加密和密钥传送服务工作流

下面是使用 AES 加密资产时需要执行的常规步骤，这些步骤使用媒体服务密钥传送服务，也使用动态加密。

1. [创建资产并将文件上传到资产](media-services-protect-with-aes128.md#create_asset)。
2. [将包含文件的资产编码为自适应比特率 MP4 集](media-services-protect-with-aes128.md#encode_asset)。
3. [创建内容密钥并将其与编码资产相关联](media-services-protect-with-aes128.md#create_contentkey)。 在媒体服务中，内容密钥包含资产的加密密钥。
4. [配置内容密钥授权策略](media-services-protect-with-aes128.md#configure_key_auth_policy)。 必须配置内容密钥授权策略，客户端必须遵守该策略，才能将内容密钥传送到客户端。
5. [为资产配置传送策略](media-services-protect-with-aes128.md#configure_asset_delivery_policy)。 传送策略配置包括：密钥获取 URL 和初始化向量 (IV)（进行加密和解密时，AES 128 要求提供同一个初始化向量）、传送协议（例如 MPEG DASH、HLS、平滑流式处理或全部）、动态加密类型（例如信封或无动态加密）。

    可以将不同的策略应用到同一资产上的每个协议。 例如，可以将 PlayReady 加密应用到平滑流/DASH，将 AES 信封应用到 HLS。 将阻止流式处理传送策略中未定义的任何协议（例如，添加仅将 HLS 指定为协议的单个策略）。 如果根本没有定义任何传送策略，则情况不是这样。 此时，允许所有明文形式的协议。

6. [创建 OnDemand 定位符](media-services-protect-with-aes128.md#create_locator)以获取流式处理 URL。

本文还说明了[客户端应用程序如何从密钥传送服务请求密钥](media-services-protect-with-aes128.md#client_request)。

可以在文章末尾找到完整的 .NET [示例](media-services-protect-with-aes128.md#example)。

下图演示了上述工作流。 在图中，使用令牌进行了身份验证。

![使用 AES-128 提供保护](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

本文的余下部分提供了详细说明、代码示例和主题链接，向你演示如何完成上述任务。

## <a name="current-limitations"></a>当前限制
如果添加或更新资产的传送策略，则必须删除现有定位符（如果有）并创建新定位符。

## <a id="create_asset"></a>创建资产并将文件上传到资产
为了对视频进行管理、编码和流式处理，必须首先将内容上传到 Microsoft Azure 媒体服务中。 上传完成后，相关内容即安全地存储在云中供后续处理和流式处理。 

有关详细信息，请参阅 [将文件上传到媒体服务帐户](media-services-dotnet-upload-files.md)。

## <a id="encode_asset"></a>将包含文件的资产编码为自适应比特率 MP4 集
使用动态加密时，只需创建包含一组多码率 MP4 文件或多比特率平滑流式处理源文件的资产。 然后，按需流式处理服务器会确保以选定的协议按清单或分段请求中的指定格式接收流。 因此，只需以单一存储格式存储文件并为其付费，媒体服务服务就会基于客户端的请求构建并提供相应响应。 有关详细信息，请参阅[动态打包概述](media-services-dynamic-packaging-overview.md)一文。

>[!NOTE]
>创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。 
>
>此外，要使用动态打包和动态加密，资产必须包含一组自适应比特率 MP4 或自适应比特率平滑流式处理文件。

有关如何编码的说明，请参阅 [如何使用 Media Encoder Standard 对资产进行编码](media-services-dotnet-encode-with-media-encoder-standard.md)。

## <a id="create_contentkey"></a>创建内容密钥并将其与编码资产相关联
在媒体服务中，内容密钥包含用于加密资产的密钥。

有关详细信息，请参阅 [创建内容密钥](media-services-dotnet-create-contentkey.md)。

## <a id="configure_key_auth_policy"></a>配置内容密钥授权策略
媒体服务支持通过多种方式对发出密钥请求的用户进行身份验证。 必须配置内容密钥授权策略，客户端（播放器）必须遵守该策略，才能将密钥传送到客户端。 内容密钥授权策略可能受到一种或多种授权限制：开放、令牌限制或 IP 限制。

有关详细信息，请参阅[配置内容密钥授权策略](media-services-dotnet-configure-content-key-auth-policy.md)。

## <a id="configure_asset_delivery_policy"></a>配置资产传送策略
为资产配置传送策略。 资产传送策略配置包括：

* 密钥获取 URL。 
* 用于信封加密的初始化向量 (IV)。 进行加密和解密时，AES - 128 要求提供同一个 IV。 
* 资产传送协议（例如 MPEG DASH、HLS、平滑流式处理或全部）。
* 动态加密类型（例如 AES 信封）或无动态加密。 

有关详细信息，请参阅[配置资产传送策略](media-services-dotnet-configure-asset-delivery-policy.md)。

## <a id="create_locator"></a>创建 OnDemand 流定位符以获取流 URL
需要为用户提供平滑流、DASH 或 HLS 的流式处理 URL。

> [!NOTE]
> 如果添加或更新资产的传送策略，则必须删除现有定位符（如果有）并创建新定位符。
> 
> 

有关如何发布资产和生成流 URL 的说明，请参阅 [生成流 URL](media-services-deliver-streaming-content.md)。

## <a name="get-a-test-token"></a>获取测试令牌
获取用于密钥授权策略的基于令牌限制的测试令牌。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

可以使用 [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 来测试流。

## <a id="client_request"></a>客户端如何从密钥传送服务请求密钥？
在上一步骤中，构造了指向清单文件的 URL。 客户端需要从流清单文件提取必要的信息，以便向密钥传送服务发出请求。

### <a name="manifest-files"></a>清单文件
客户端需要从清单文件提取 URL（也包含内容密钥 ID (kid)）值。 然后，客户端将尝试从密钥传送服务获取加密密钥。 客户端还需要提取 IV 值，并使用该值来解密流。 下面的代码片段演示平滑流式处理清单的 <Protection> 元素。

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

对于 HLS，根清单将划分成段文件。 

例如，根清单为 http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) 并且包含段文件名的列表。

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

如果在文本编辑器中打开其中一个片段文件（例如，http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)），则应包含 #EXT-X-KEY，表示该文件已加密。

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>如果打算在 Safari 中播放 AES 加密的 HLS，请参阅[此博客](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

### <a name="request-the-key-from-the-key-delivery-service"></a>从密钥传送服务请求密钥

以下代码演示如何使用密钥传送 URI（从清单提取）和令牌（本文不讨论如何从安全令牌服务获取简单 Web 令牌），向媒体服务密钥传送服务发送请求。

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-using-net"></a>在 .NET 中使用 AES-128 保护内容

### <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

1. 设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。 
2. 将以下元素添加到 app.config 文件中定义的 **appSettings**：

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>示例

使用本部分中所示的代码覆盖 Program.cs 文件中的代码。
 
>[!NOTE]
>不同 AMS 策略的策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的日期/访问权限，则应使用相同的策略 ID，例如，用于要长期就地保留的定位符的策略（非上传策略）。 有关详细信息，请参阅[此](media-services-dotnet-manage-entities.md#limit-access-policies)文章。

请务必将变量更新为指向输入文件所在的文件夹。

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

