---
title: Azure Media Player 错误代码
description: Azure Media Player 的错误代码引用。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81727250"
---
# <a name="error-codes"></a>错误代码 #

播放无法启动或停止时，将激发错误事件，并且该 `error()` 函数将返回一个代码和可选消息，帮助应用开发人员获取更多详细信息。 `error().message` 不是向用户显示的消息。  向用户显示的消息基于 `error().code` bits 27-20，请参阅下表。

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>错误代码，位 [31-28] (4 位)  ##

描述错误的区域。

- 0 - 未知
- 1-AMP
- 2-AzureHtml5JS
- 3-FlashSS
- 4-SilverlightSS
- 5-Html5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>错误代码，位 [27-0] (28 位)  ##

描述错误的详细信息，bits 27-20 提供高级别，bits 19-0 提供更多详细信息（如果可用）。


| 错误代码。路径名 | 代码，位 [27-0] (28 位)  | 说明 |
|---|---:|---|
| **MEDIA_ERR_ABORTED 错误范围 (0x0100000-0x01FFFFF) ** | | |
| abortedErrUnknown | 0x0100000 | 一般中止错误 |
| abortedErrNotImplemented | 0x0100001 | 中止错误，未实现 |
| abortedErrHttpMixedContentBlocked | 0x0100002 | 中止错误，阻止了混合内容-一般在 `http://` 从页面加载流时 `https://` 出现 |
| **MEDIA_ERR_NETWORK 错误开始值 (0x0200000-0x02FFFFF) ** | | |
| networkErrUnknown | 0x0200000 | 一般网络错误 |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 错误响应 |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401 错误响应 |
| networkErrHttpUserForbidden | 0x0200193 | Http 403 错误响应 |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404 错误响应 |
| networkErrHttpNotAllowed | 0x0200195 | Http 405 错误响应 |
| networkErrHttpGone | 0x020019A | Http 410 错误响应 |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412 错误响应 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500 错误响应
| networkErrHttpBadGateway | 0x02001F6 | Http 502 错误响应 |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503 错误响应 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504 错误响应 |
| networkErrTimeout | 0x0200258 | 网络超时错误
| networkErrErr | 0x0200259 | 网络连接错误响应 |
| **MEDIA_ERR_DECODE 错误 (0x0300000-0x03FFFFF) ** | | |
| decodeErrUnknown | 0x0300000 | 一般解码错误 |
| **MEDIA_ERR_SRC_NOT_SUPPORTED 错误 (0x0400000-0x04FFFFF) ** | | |
| srcErrUnknown | 0x0400000 | 不支持通用源错误 |
| srcErrParsePresentation | 0x0400001 | 演示分析错误 |
| srcErrParseSegment | 0x0400002 | 段分析错误 |
| srcErrUnsupportedPresentation | 0x0400003 | 不支持演示 |
| srcErrInvalidSegment | 0x0400004 | 段无效 |
| **MEDIA_ERR_ENCRYPTED 错误开始值 (0x0500000-0x05FFFFF) ** | | |
| encryptErrUnknown | 0x0500000 | 一般加密错误 | 
| encryptErrDecrypterNotFound | 0x0500001 | 找不到 Decrypter |
| encryptErrDecrypterInit | 0x0500002 | Decrypter 初始化错误 |
| encryptErrDecrypterNotSupported | 0x0500003 | 不支持 Decrypter |
| encryptErrKeyAcquire | 0x0500004 | 密钥获取失败 |
| encryptErrDecryption | 0x0500005 | 解密段失败 |
| encryptErrLicenseAcquire | 0x0500006 | 许可证获取失败 |
| **SRC_PLAYER_MISMATCH 错误开始值 (0x0600000-0x06FFFFF) ** | | |
| srcPlayerMismatchUnknown | 0x0600000 | 一般无匹配的技术玩家播放源 |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |如果已安装，则不会安装闪存插件。 *或* 安装了 Flash 30，并播放了 AES 内容。  如果是这种情况，请尝试使用其他浏览器。 截至6月7日，目前不支持闪存30。 有关更多详细信息，请参阅 [已知问题](azure-media-player-known-issues.md) 。 注意：如果0x00600003，则不会安装闪存和 Silverlight （如果在 techOrder 中指定了）。|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | 如果安装了源，则不会安装 Silverlight 插件。 注意：如果0x00600003，则不会安装闪存和 Silverlight （如果在 techOrder 中指定了）。 |
| | 0x00600003 | 如果在 techOrder 中指定了 Flash 和 Silverlight，则不会安装。 |
| **0x0FF00000)  (未知错误 ** | | |
| errUnknown | 0xFF00000 | 未知错误 |

## <a name="user-error-messages"></a>用户错误消息 ##

显示的用户消息基于错误代码的27-20 位。

- MEDIA_ERR_ABORTED (1) -"中止播放视频"
- MEDIA_ERR_NETWORK (2) -"网络错误导致视频下载失败。"
- MEDIA_ERR_DECODE (3) -"由于损坏问题或浏览器不支持的视频，视频播放被中止。"
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) -"由于服务器或网络失败或者不支持格式，无法加载视频。"
- MEDIA_ERR_ENCRYPTED (5) -"已加密视频，我们没有解密密钥。"
- SRC_PLAYER_MISMATCH (6) -"找不到此视频的兼容源"。
- MEDIA_ERR_UNKNOWN (0xFF) -"发生未知错误"。

## <a name="examples"></a>示例 ##

### <a name="0x10600001"></a>0x10600001 ##

"找不到此视频的兼容源。" 向最终用户显示。

没有可以播放请求的源的技术玩家，但如果安装了闪存插件，则可能会播放某个源。  

### <a name="0x20200194"></a>0x20200194 ###

"网络错误导致视频下载失败。" 向最终用户显示。

AzureHtml5JS 无法从 http 404 响应中播放。

### <a name="categorizing-errors"></a>错误分类 ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>捕获特定错误 ###

下面的代码只捕获404个错误：

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)