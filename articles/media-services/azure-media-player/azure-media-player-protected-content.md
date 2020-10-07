---
title: Azure Media Player 的受保护内容
description: Azure Media Player 当前支持 AES-128 位信封加密内容和通用加密内容。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91329680"
---
# <a name="protected-content"></a>受保护内容 #

Azure Media Player 当前支持 AES-128 位信封加密内容和通用加密内容（通过 PlayReady 和 Widevine 加密）或通过 FairPlay 加密的内容。 为了正确播放受保护内容，你必须向 Azure Media Player 告知 `protectionInfo`。 此信息每个源都存在，可以通过 `data-setup` 直接在 `<source>` 标记中添加。  如果动态设置源，则还可以直接将 `protectionInfo` 添加为参数。

`protectionInfo` 接受 JSON 对象并包括：

- `type`：`AES`、`PlayReady`、`Widevine` 或 `FairPlay`
- `certificateUrl`：这应当是指向托管 FairPlay 证书的直接链接

- `authenticationToken`：这是用于添加未编码的身份验证令牌的一个选项字段

> [!IMPORTANT]
> 只有 FairPlay DRM 需要“certificateUrl”  对象。***
>[!NOTE]
> 默认的 techOrder 已更改，以适应新技术 - `html5FairPlayHLS`，该技术专门用来在支持 FairPlay 内容的浏览器（OSX 8+ 上的 Safari）上以本机方式播放 FairPlay 内容。 如果你有 FairPlay 内容要播放，并且  你将默认的 techOrder 更改为应用程序中的自定义技术，则需要将此新技术添加到 techOrder 对象中。 建议在 silverlightSS 之前包含它，使内容不会通过 PlayReady 播放。

## <a name="code-sample"></a>代码示例 ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

或者，使用多个 DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> 并非所有浏览器/平台都能够播放受保护内容。 有关支持的功能的详细信息，请参阅[播放技术](azure-media-player-playback-technology.md)部分。
> [!IMPORTANT]
> 传递给播放器的令牌用于受保护内容，并且仅用于经身份验证的用户。 假设应用程序使用了 SSL 或某种其他形式的安全措施。 而且，最终用户被认为是可信的，不会滥用令牌；如果不是这样，请让你的安全专家参与。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)