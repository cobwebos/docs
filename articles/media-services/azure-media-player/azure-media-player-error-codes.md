---
title: Azure 媒体播放器错误代码
description: Azure 媒体播放器的错误代码引用。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727250"
---
# <a name="error-codes"></a>错误代码 #

当播放无法启动或停止时，将触发错误事件，`error()`该函数将返回代码和可选消息，以帮助应用开发人员获取更多详细信息。 `error().message`不是向用户显示的消息。  向用户显示的消息基于`error().code`位 27-20，请参阅下表。

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>错误代码，位 [31-28] （4 位） ##

描述错误的区域。

- 0 - 未知
- 1 - 安培
- 2 - AzureHtml5JS
- 3 - 闪光
- 4 - 银光
- 5 - Html5
- 6 - Html5公平播放

## <a name="error-codes-bits-27-0-28-bits"></a>错误代码，位 [27-0] （28 位） ##

描述错误的详细信息，位 27-20 提供了高级别，位 19-0 提供了更多详细信息（如果可用）。


| 和错误代码。[名称] | 代码，位 [27-0] （28 位） | 说明 |
|---|---:|---|
| **MEDIA_ERR_ABORTED误差范围 （0x0100000 - 0x01FFFFF）** | | |
| 中止的未知数 | 0x010000 | 通用中止错误 |
| 中止执行 | 0x0100001 | 中止错误，未实现 |
| 中止的ErHttp混合内容被阻止 | 0x010002 | 中止错误，混合内容被阻止 - 通常发生在从`http://``https://`页面加载流时 |
| **MEDIA_ERR_NETWORK错误起始值 （0x0200000 - 0x02FFF）** | | |
| 网络Err未知 | 0x020000 | 通用网络错误 |
| 网络勘误巴德尔格式 | 0x0200190 | Http 400 错误响应 |
| 网络ErrHttpUserAuth需要 | 0x0200191 | Http 401 错误响应 |
| 网络Erhttpuser禁止 | 0x0200193 | Http 403 错误响应 |
| 网络ErHttpUrl未发现 | 0x0200194 | Http 404 错误响应 |
| 网络ErrhttpNot | 0x0200195 | Http 405 错误响应 |
| 网络埃尔HttpGone | 0x020019A | Http 410 错误响应 |
| 网络错误预置失败 | 0x020019C | Http 412 错误响应 |
| 网络ErrHttp内部服务器失败 | 0x02001F4 | Http 500 错误响应
| 网络勘误巴网关 | 0x02001F6 | Http 502 错误响应 |
| 网络ErrHttp服务不可用 | 0x02001F7 | Http 503 错误响应 |
| 网络勘误网关超时 | 0x02001F8 | Http 504 错误响应 |
| 网络勘误超时 | 0x0200258 | 网络超时错误
| 网络ErrErr | 0x0200259 | 网络连接错误响应 |
| **MEDIA_ERR_DECODE错误 （0x0300000 - 0x03FFF）** | | |
| 解码未知 | 0x030000 | 通用解码错误 |
| **MEDIA_ERR_SRC_NOT_SUPPORTED错误 （0x0400000 - 0x04FFFFF）** | | |
| srcErr 未知 | 0x040000 | 不支持的通用源错误 |
| srcErrParse 演示 | 0x0400001 | 演示文稿分析错误 |
| srcErrParse 分段 | 0x040002 | 段分析错误 |
| srcErrUn 支持演示文稿 | 0x0400003 | 不支持演示文稿 |
| srcErr 无效段 | 0x0400004 | 无效段 |
| **MEDIA_ERR_ENCRYPTED错误起始值（0x0500000 - 0x05FFF）** | | |
| 加密错误未知 | 0x050000 | 通用加密错误 | 
| 加密Er解密未发现 | 0x0500001 | 未找到解密器 |
| 加密的解密器 | 0x050002 | 解密器初始化错误 |
| 加密的解密器不受支持 | 0x0500003 | 不支持解密程序 |
| 加密ErKeyAcquire | 0x0500004 | 密钥获取失败 |
| 加密的解密 | 0x0500005 | 段解密失败 |
| 加密的埃尔许可证获取 | 0x050006 | 许可证获取失败 |
| **SRC_PLAYER_MISMATCH错误起始值（0x0600000 - 0x06FFFFF）** | | |
| srcPlayer身份不符未知 | 0x060000 | 通用没有匹配的技术播放器发挥源 |
| srcPlayer不匹配闪存未安装 | 0x0600001 |未安装闪存插件，如果安装源可能会播放。 *或*闪存 30 已安装并播放 AES 内容。  如果是这种情况，请尝试其他浏览器。 截至 6 月 7 日，闪存 30 今天不受支持。 有关详细信息，请参阅[已知问题](azure-media-player-known-issues.md)。 注意：如果 0x00600003，如果技术订单中指定，则不会安装闪存和银光。|
| srcPlayer不匹配银灯未安装 | 0x060002 | 银光插件未安装，如果安装源可能会播放。 注意：如果 0x00600003，如果技术订单中指定，则不会安装闪存和银光。 |
| | 0x00600003 | 如果技术订单中指定，则不会安装闪光灯和银光。 |
| **未知错误 （0x0FF0000）** | | |
| 错误未知 | 0xFF00000 | 未知错误 |

## <a name="user-error-messages"></a>用户错误消息 ##

显示的用户消息基于错误代码的位 27-20。

- MEDIA_ERR_ABORTED （1） - "您中止了视频播放"
- MEDIA_ERR_NETWORK （2） - "网络错误导致视频下载部分失败。
- MEDIA_ERR_DECODE （3） - "视频播放由于损坏问题或浏览器不支持的视频功能而中止。
- MEDIA_ERR_SRC_NOT_SUPPORTED （4） - "无法加载视频，因为服务器或网络出现故障，或者不支持格式。
- MEDIA_ERR_ENCRYPTED （5） - "视频已加密，我们没有解密的密钥。
- SRC_PLAYER_MISMATCH （6） - "未找到此视频的兼容源。
- MEDIA_ERR_UNKNOWN （0xFF） - "发生未知错误"。

## <a name="examples"></a>示例 ##

### <a name="0x10600001"></a>0x10600001 ##

此视频未找到兼容源。 显示给最终用户。

没有技术播放器可以播放请求的源，但如果安装了 Flash 插件，则很可能可以播放源。  

### <a name="0x20200194"></a>0x20200194 ###

网络错误导致视频下载部分失败。 显示给最终用户。

AzureHtml5JS 无法从 http 404 响应中播放。

### <a name="categorizing-errors"></a>分类错误 ###

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

以下代码仅捕获 404 个错误：

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>后续步骤 ##

- [Azure 媒体播放器快速入门](azure-media-player-quickstart.md)