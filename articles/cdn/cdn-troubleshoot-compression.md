---
title: "排查 Azure CDN 中的文件压缩问题 | Microsoft Docs"
description: "排查 Azure CDN 文件压缩的问题。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5ef8a8262eb40aa827161764f03a63d031e43273
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-cdn-file-compression"></a>排查 CDN 文件压缩问题
本文将帮助你排查 [CDN 文件压缩](cdn-improve-performance.md)问题。

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并单击“**获取支持**”。

## <a name="symptom"></a>症状
已经为终结点启用了压缩，但返回的文件没有压缩。

> [!TIP]
> 要检查返回的文件是否已压缩，需要使用 [Fiddler](http://www.telerik.com/fiddler) 这类工具或浏览器的[开发人员工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)。  检查随缓存的 CDN 内容一起返回的 HTTP 响应标头。  如果存在名为 `Content-Encoding` 的标头，且其值为 **gzip**、**bzip2** 或 **deflate**，则内容已压缩。
> 
> ![Content-Encoding 标头](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>原因
有若干个可能的原因，包括：

* 所请求的内容不适合压缩。
* 请求的文件类型未启用压缩。
* HTTP 请求没有包含请求有效压缩类型的标头。

## <a name="troubleshooting-steps"></a>疑难解答步骤
> [!TIP]
> 与部署新的终结点一样，CDN 配置变更也需要一些时间才能传播到整个网络。  通常情况下会在 90 分钟内应用更改。  如果这是首次为 CDN 终结点设置压缩，应考虑等待 1-2 个小时，以确保压缩设置传播到 POP。 
> 
> 

### <a name="verify-the-request"></a>验证请求
首先，应该对请求进行一次快速的完整性检查。  可以使用浏览器的[开发人员工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)来查看正在提出的请求。

* 验证请求是发送到终结点 URL `<endpointname>.azureedge.net`，而不是源。
* 验证该请求包含 **Accept-encoding** 标头，并且该标头的值包含 **gzip**、**deflate** 或 **bzip2**。

> [!NOTE]
> **来自 Akamai 的 Azure CDN** 配置文件仅支持 **gzip** 编码。
> 
> 

![CDN 请求标头](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>验证压缩设置（标准 CDN 配置文件）
> [!NOTE]
> 仅在 CDN 配置文件为**来自 Verizon 的 Azure CDN** 或 **来自 Akamai 的 Azure CDN** 配置文件时，此步骤才适用。 
> 
> 

在 [Azure 门户](https://portal.azure.com)中导航到终结点，然后单击“**配置**”按钮。

* 验证是否已启用压缩。
* 验证待压缩内容的 MIME 类型是否已包括在压缩格式列表中。

![CDN 压缩设置](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>验证压缩设置（高级 CDN 配置文件）
> [!NOTE]
> 仅在 CDN 配置文件为**来自 Verizon 的高级 Azure CDN** 配置文件时，此步骤才适用。
> 
> 

在 [Azure 门户](https://portal.azure.com)中导航到终结点，然后单击“**管理**”按钮。  随即打开补充门户。  将鼠标悬停在“**HTTP 大**”选项卡上，然后悬停在“**缓存设置**”浮出控件。  单击“**压缩**”。 

* 验证是否已启用压缩。
* 验证“**文件类型**”列表包含以逗号分隔（无空格）的 MIME 类型列表。
* 验证待压缩内容的 MIME 类型是否已包括在压缩格式列表中。

![CDN 高级压缩设置](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>验证内容已缓存
> [!NOTE]
> 仅在 CDN 配置文件为**来自 Verizon 的 Azure CDN** 配置文件（标准或高级）时，此步骤才适用。
> 
> 

使用浏览器的开发人员工具，检查响应标头以确保文件已缓存在提出请求的区域中。

* 检查 **Server** 响应标头。  标头应具有格式**平台（POP/服务器 ID）**，如下例所示。
* 检查 **X-Cache** 响应标头。  标头应显示 **HIT**。  

![CDN 响应标头](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>验证该文件满足大小要求
> [!NOTE]
> 仅在 CDN 配置文件为**来自 Verizon 的 Azure CDN** 配置文件（标准或高级）时，此步骤才适用。
> 
> 

为了适合进行压缩，文件必须符合以下大小要求：

* 大于 128 个字节。
* 小于 1 MB。

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>在源服务器上检查请求是否包含 **Via** 标头
**Via** HTTP 标头指明了由代理服务器正在将请求传递到的 web 服务器。  默认情况下，当请求包含 **Via** 标头时，Microsoft IIS Web 服务器不会压缩响应。  要覆盖此行为，请执行以下操作：

* **IIS 6**：[在 IIS 元数据库属性中设置 HcNoCompressionForProxies ="FALSE"](https://msdn.microsoft.com/library/ms525390.aspx)
* **IIS 7 及更高版本**：[在服务器配置中将 **noCompressionForHttp10** 和 **noCompressionForProxies** 设置为 False ](http://www.iis.net/configreference/system.webserver/httpcompression)

