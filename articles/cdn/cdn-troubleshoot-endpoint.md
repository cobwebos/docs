---
title: "排查 Azure CDN 终结点返回 404 状态的问题 | Microsoft Docs"
description: "排查 Azure CDN 终结点的 404 响应代码。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f59fbd18413fb44026d8c92b7f6940ed2f8a00a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>排查 CDN 终结点返回 404 状态的问题
本文将帮助你排查 [CDN 终结点](cdn-create-new-endpoint.md)返回 404 的问题。

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)，并单击“**获取支持**”。

## <a name="symptom"></a>症状
已经创建 CDN 配置文件和终结点，但内容似乎没有出现在 CDN 上。  尝试通过 CDN URL 访问内容的用户收到 HTTP 404 状态代码。 

## <a name="cause"></a>原因
有若干个可能的原因，包括：

* CDN 不知道文件的来源
* 终结点配置不正确，导致 CDN 在错误的位置中进行查找
* 主机拒绝来自 CDN 的主机标头
* 终结点没有足够时间传播到整个 CDN

## <a name="troubleshooting-steps"></a>疑难解答步骤
> [!IMPORTANT]
> 创建 CDN 终结点后，终结点无法立即使用，因为注册需要时间来通过 CDN 进行传播。  对于<b>来自 Akamai 的 Azure CDN</b> 配置文件，传播通常可在一分钟内完成。  对于<b>来自 Verizon 的 Azure CDN</b> 配置文件，传播通常会在 90 分钟内完成，但某些情况下可能更长。  如果完成了本文档中的步骤，但仍然收到 404 响应，请考虑在创建支持票证之前，等待几小时后重新检查一次。
> 
> 

### <a name="check-the-origin-file"></a>检查源文件
首先，应该验证想要缓存的文件已在源上可用，并且可以公开访问。  执行此操作的最快方法是，在一个 In-Private 或 Incognito 会话中打开浏览器，并直接浏览该文件。  只需键入 URL 或将该 URL 粘贴到地址框，然后查看文件中的结果是否与预期一致。  在此示例中，将使用 Azure 存储帐户中的一个文件（可从 `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt` 访问）。  如你所见，文件成功通过了测试。

![成功！](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> 虽然这是验证文件是否已公开可用的最快且最简单的方法，组织中的一些网络配置可能会给你一个错觉，即文件虽然已公开可用，但实际上只有网络（即使它在 Azure 中托管）用户才能看到此文件。  如果有一个外部浏览器可以测试，例如未连接到组织网络的移动设备或 Azure 中的虚拟机，那就最好了。
> 
> 

### <a name="check-the-origin-settings"></a>检查源设置
至此，我们已经验证该文件在 Internet 上公开可用，接下来我们应验证我们的源设置。  在 [Azure 门户](https://portal.azure.com)中，浏览到 CDN 配置文件，并单击要进行故障排除的终结点。  在随后出现的“**终结点**”边栏选项卡，单击源。  

![具有源的“终结点”边栏选项卡将突出显示](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

“**源**”边栏选项卡随即显示。 

![源边栏选项卡](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>源类型和主机名
验证**源类型**是否正确，并验证**源主机名**。  在这个示例中，`https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`，URL 的主机名部分是 `cdndocdemo.blob.core.windows.net`。  如屏幕快照中所示，主机名是正确的。  对于 Azure 存储、Web 应用和云服务源，“**源主机名**”字段是一个下拉列表，因此无需担心拼写正确问题。  但是，如果要使用自定义源，则正确拼写主机名*非常关键*！

#### <a name="http-and-https-ports"></a>HTTP 和 HTTPS 端口
其他需要检查的是 **HTTP** 和 **HTTPS 端口**。  在大多数情况下，80 和 443 都正确，无需任何更改。  但是，如果源服务器正在侦听其他端口，则应在此处表示出来。  如果不能确定，请查看源文件的 URL。  HTTP 和 HTTPS 规范指定默认端口为 80 和 443。 在我的 URL `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt` 中未指定端口，因此会假定 443 为默认端口，我的设置是正确的。  

但是，如果之前测试的源文件的 URL 为 `http://www.contoso.com:8080/file.txt`，  请注意主机名段结尾的 `:8080`。  这会告诉浏览器使用端口 `8080` 来连接位于 `www.contoso.com` 的 Web 服务器，因此，需要在“**HTTP 端口**”字段中输入 8080。  请务必注意，这些端口设置只会影响该终结点用来从源中检索信息的那些端口。

> [!NOTE]
> **来自 Akamai 的 Azure CDN** 终结点不允许原点的完整 TCP 端口范围。  有关不被允许的原点端口列表，请参阅 [来自 Akamai 的 Azure CDN 受允许原点端口](https://msdn.microsoft.com/library/mt757337.aspx)。  
> 
> 

### <a name="check-the-endpoint-settings"></a>检查终结点设置
返回到“**终结点**”边栏选项卡，单击“**配置**”按钮。

![“终结点”边栏选项卡将随配置按钮突出显示](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

终结点的“**配置**”边栏选项卡出现。

![配置边栏选项卡](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>协议
对于**协议**，请验证是否选择了客户端所使用的协议。  客户端使用的协议与用来访问源的协议相同，因此，在前一节中正确配置源的端口非常重要。  终结点仅侦听默认 HTTP 和 HTTPS 端口（80 和 443），而不考虑源端口。

让我们返回到假设示例，`http://www.contoso.com:8080/file.txt`。  会记得，Contoso 指定 `8080` 作为其 HTTP 端口，但我们还假定它们指定 `44300` 作为其 HTTPS 端口。  如果创建名为 `contoso` 的终结点，其 CDN 终结点主机名将是 `contoso.azureedge.net`。  对 `http://contoso.azureedge.net/file.txt` 的请求是一个 HTTP 请求，因此，终结点将使用端口 8080 上的 HTTP 来检索源。  通过 HTTPS 的安全请求，`https://contoso.azureedge.net/file.txt`，可能会导致终结点在从源检索文件时使用端口 44300 上的 HTTPS。

#### <a name="origin-host-header"></a>源主机标头
**源主机标头**是随着每个请求发送到源的主机标头值。  在大多数情况下，应与前面部分验证的**源主机名**相同。  此字段中的错误值通常不会导致 404 状态，但有可能会导致其他 4xx 状态，具体取决于源期望的值。

#### <a name="origin-path"></a>源路径
最后，我们应该验证**源路径**。  默认情况下为空。  只有在希望缩小在 CDN 上使用源托管的资源范围时，才应使用此字段。  

例如，在我的终结点中，我希望我的存储帐户上的所有资源都可用，所以我将**源路径**保留为空。  这意味着，对 `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` 的请求，将导致我的终结点连接至请求 `/publicblob/lorem.txt` 的 `cdndocdemo.core.windows.net`。  同样，对 `https://cdndocdemo.azureedge.net/donotcache/status.png` 的请求将导致终结点从源请求 `/donotcache/status.png`。

但是，如果我不想在我的源上的每个路径使用 CDN 呢？  例如，我只想要公开 `publicblob` 路径。  如果我在“**源路径**”字段中输入 */publicblob*，这会导致终结点在对源提出的每个请求前面插入 */publicblob*。  这意味着，对 `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` 的请求现在实际上将接受 URL `/publicblob/lorem.txt` 的请求部分，并在将 `/publicblob` 附加到开始部分。 这会导致从源对 `/publicblob/publicblob/lorem.txt` 进行请求。  如果该路径未解析实际文件，源将返回 404 状态。  在此示例中，用来检索 lorem.txt 的正确 URL 实际上是 `https://cdndocdemo.azureedge.net/lorem.txt`。  请注意，我们完全没有包含 */publicblob* 路径，因为该 URL 的请求部分是 `/lorem.txt`，并且终结点添加了 `/publicblob`，这会导致 `/publicblob/lorem.txt` 将请求传递到源。

