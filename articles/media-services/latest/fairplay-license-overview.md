---
title: 媒体服务和 Apple FairPlay 许可证支持 - Azure | Microsoft Docs
description: 本主题概述 Apple FairPlay 许可证要求和配置。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d348f2696ef865616669af311477cb3a90a59a50
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995869"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay 许可要求和配置 

使用 Azure 媒体服务可通过 Apple FairPlay (AES-128 CBC) 加密 HLS 内容  。 媒体服务还提供用于交付 FairPlay 许可证的服务。 当播放器尝试播放受 FairPlay 保护的内容时，将向许可证交付服务发送请求以获取许可证。 如果许可证服务批准了该请求，则会颁发该许可证，该许可证将发送到客户端，并用来解密和播放指定的内容。

媒体服务还提供可用于配置 FairPlay 许可证的 API。 本主题讨论 FairPlay 许可证要求，并演示如何使用媒体服务 API 配置 FairPlay 许可证  。 

## <a name="requirements"></a>要求

使用媒体服务通过 Apple FairPlay 加密 HLS 内容并使用媒体服务交付 FairPlay 许可证时，必需完成以下各项  ：

* 注册 [Apple 开发计划](https://developer.apple.com/)。
* Apple 要求内容所有者获取[部署包](https://developer.apple.com/contact/fps/)。 说明已使用媒体服务实现密钥安全模块 (KSM)，以及正在请求最终 FPS 包。 最终 FPS 包中有如何生成证书和获取应用程序密钥 (ASK) 的说明。 可使用 ASK 配置 FairPlay。
* 必须在媒体服务密钥/许可证交付端上设置以下各项：

    * **应用证书 (AC)** ：这是一个包含私钥的 .pfx 文件。 创建此文件，并使用密码对其进行加密。 .pfx 文件应采用 Base64 格式。

        以下步骤介绍如何为 FairPlay 生成 .pfx 证书文件：

        1. 从 https://slproweb.com/products/Win32OpenSSL.html 安装 OpenSSL。

            转到 Apple 提供的 FairPlay 证书和其他文件所在的文件夹。
        2. 从命令行运行以下命令。 这会将 .cer 文件转换为 .pem 文件。

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. 从命令行运行以下命令。 这会将 .pem 文件转换为包含私钥的 .pfx 文件。 然后 OpenSSL 会要求提供 .pfx 文件的密码。

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **应用证书密码**：用于创建 .pfx 文件的密码。
    * **ASK**：使用 Apple 开发人员门户生成证书时会收到此密钥。 每个开发团队都会收到唯一的 ASK。 请保存一份 ASK 副本，并将其存储在安全位置。 需要将 ASK 配置为媒体服务的 FairPlayAsk。
    
* 以下事项必须通过 FPS 客户端来设置：

  * **应用证书 (AC)** ：这是一个包含公钥的 .cer/.der 文件，操作系统使用它来加密某些负载。 媒体服务需要了解它，因为播放器需要它。 密钥传送服务使用相应的私钥对其进行解密。

* 要播放 FairPlay 加密的流，需要先获取实际 ASK，然后生成实际证书。 该过程将创建所有三个部分：

  * .der 文件
  * .pfx 文件
  * .pfx 的密码

## <a name="fairplay-and-player-apps"></a>FairPlay 和播放器应用

使用 Apple FairPlay 对内容进行加密时，各视频和音频示例都使用 AES-128 CBC 模式进行加密   。 **FairPlay 流式处理** (FPS) 集成到设备操作系统，iOS 和 Apple TV 本身支持这项功能。 OS X 上的 Safari 使用加密媒体扩展 (EME) 接口支持启用 FPS。

Azure Media Player 还支持 FairPlay 播放。 有关详细信息，请参阅 [Azure Media Player 文档](https://amp.azure.net/libs/amp/latest/docs/index.html)。

可以通过使用 iOS SDK 开发自己的播放器应用。 若要能够播放 FairPlay 内容，必须实现许可证交换协议。 此协议不由 Apple 指定。 而是取决于每个应用发送密钥传送请求的方式。 媒体服务 FairPlay 密钥传送服务需要 SPC 为采用以下形式的 www-form-url 编码后消息：

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay 配置 .NET 示例

可使用媒体服务 API 来配置 FairPlay 许可证。 当播放器尝试播放受 FairPlay 保护的内容时，将向许可证交付服务发送请求以获取许可证。 如果许可证服务批准了请求，则该服务将颁发许可证。 许可证将被发送到客户端，并用于解密和播放指定的内容。

> [!NOTE]
> 通常，可能只需配置一次 FairPlay 策略选项，因为仅有一套证书和 ASK。

以下示例使用[媒体服务 .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) 配置许可证。

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>后续步骤

了解如何[使用 DRM 提供保护](protect-with-drm.md)
