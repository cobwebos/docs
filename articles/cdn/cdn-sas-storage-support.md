---
title: "将 Azure CDN 与 SAS 一起使用 | Microsoft Docs"
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>将 Azure CDN 与 SAS 一起使用

当你从存储帐户的存储容器提供内容时，可能需要通过授予对存储容器的私有访问权限来确保用户访问你的文件的方式。 否则，已被授予公共访问权限的存储帐户可以由任何知道其 URL 的人员访问。 为了保护允许内容交付网络 (CDN) 访问的存储帐户，可以从 Azure 存储使用共享访问签名 (SAS) 功能来授予对私有存储容器的有限访问权限。

SAS 是授予对 Azure 存储资源进行有限访问权限的 URI，而无需公开你的帐户密钥。 你可以为不信任向其提供存储帐户密钥，但需要向其委派某些存储帐户资源的访问权限的客户端提供 SAS。 通过向这些客户端分发共享访问签名 URI，可以授予它们在一段指定时间内对资源的访问权限。
 
凭借 SAS 可定义访问 blob 的各种参数，例如开始和到期时间、权限（读/写）和 IP 范围。 本文介绍如何将 SAS 与 Azure CDN 一起使用。 有关 SAS 的详细信息，包括如何创建它及其参数选项，请参阅[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>设置 Azure CDN 以用于存储 SAS
对于将 SAS 与 Azure CDN 一起使用，建议使用以下三个选项。 所有选项都假设已创建了一个有效的 SAS（请参阅先决条件）。 
 
### <a name="prerequisites"></a>先决条件
首先创建存储帐户，然后为资产生成 SAS。 可以生成两种类型的存储访问签名：服务 SAS 或帐户 SAS。 有关详细信息，请参阅[共享访问签名的类型](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures)。

生成 SAS 后，可以访问具有以下格式的 URL 的 blob 存储文件：`https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
例如：
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

有关设置参数的详细信息，请参阅 [SAS 参数注意事项](#sas-parameter-considerations)和[共享访问签名参数](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters)。

![CDN SAS 设置](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>选项 1：使用从 CDN 传递到 blob 存储的 SAS

此选项最为简单，并且仅使用从 CDN 被传递到源服务器的单个 SAS 令牌。 此选项由来自 Verizon 的 Azure CDN（适用于标准和高级配置文件）和来自 Akamai 的 Azure CDN 支持。 
 
1. 选择一个终结点，单击“缓存规则”，然后从“查询字符串缓存”列表中选择“缓存每个唯一的 URL”。

    ![CDN 缓存规则](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. 在存储帐户上设置 SAS 后，使用具有 CDN URL 的 SAS 令牌访问文件。 
   
   生成的 URL 格式为：`https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   例如：   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. 使用缓存规则或在源处添加 `Cache-Control` 标头来微调缓存持续时间。 由于 CDN 将 SAS 令牌视为普通查询字符串，因此，最佳做法是应该设置在 SAS 到期时或到期之前到期的缓存持续时间。 否则，如果文件的缓存持续时间长于 SAS 的有效时间，在 SAS 到期之后，则可从 CDN 源服务器访问此文件。 如果出现这种情况，并且你需要使缓存文件不可访问，则必须对文件执行清除操作以将其从缓存中清除。 有关对 CDN 设置缓存持续时间的信息，请参阅[使用缓存规则控制 Azure 内容交付网络缓存行为](cdn-caching-rules.md)。

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>选项 2：使用重写规则隐藏的 CDN 安全令牌
 
使用此选项，可以保护源 blob 存储，而无需 CDN 用户的 SAS 令牌。 如果不需要对文件进行特定的访问权限限制，但希望阻止用户直接访问存储源以提高 CDN 卸载时间，则需要使用此选项。 此选项仅适用于来自 Verizon 的 Azure CDN Premium 配置文件。 
 
1. 使用[规则引擎](cdn-rules-engine.md)创建 URL 重写规则。 新规则需要花 90 分钟的时间进行传播。

   ![CDN 管理按钮](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN 规则引擎按钮](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   此示例 URL 重写规则具有以下模式：
   
   源：   
   `/test/demo.jpg`
   
   目标：   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 重写规则](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. 使用以下格式访问在没有 SAS 令牌的情况下访问 CDN 上的文件：`https://<endpoint>.azureedge.net/<folder>/<file>`
 
   例如：   
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   请注意，无论是否使用 SAS 令牌，任何人员都可以访问 CDN 终结点。 

3. 使用缓存规则或在源处添加 `Cache-Control` 标头来微调缓存持续时间。 由于 CDN 将 SAS 令牌视为普通查询字符串，因此，最佳做法是应该设置在 SAS 到期时或到期之前到期的缓存持续时间。 否则，如果文件的缓存持续时间长于 SAS 的有效时间，在 SAS 到期之后，则可从 CDN 源服务器访问此文件。 如果出现这种情况，并且你需要使缓存文件不可访问，则必须对文件执行清除操作以将其从缓存中清除。 有关对 CDN 设置缓存持续时间的信息，请参阅[使用缓存规则控制 Azure 内容交付网络缓存行为](cdn-caching-rules.md)。

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>选项 3：通过重写规则使用 CDN 安全令牌身份验证

此选项最为安全且可进行自定义。 若要使用 CDN 安全令牌身份验证，必须具有来自 Verizon 的 Azure CDN Premium 配置文件。 客户端访问基于 CDN 安全令牌上的安全参数集。 但是，如果 SAS 变为无效，CDN 将无法重新从源服务器验证内容。

1. [创建 CDN 安全令牌](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication)并使用 CDN 终结点的规则引擎和用户可访问文件的路径来激活它。

   SAS URL 采用以下格式：   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   例如：   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   CDN 安全令牌身份验证的参数选项不同于 SAS 令牌的参数选项。 如果在创建 CDN 安全令牌时选择使用到期时间，请将它设置为与 SAS 令牌的到期时间相同的值。 这样可确保到期时间是可预测的。 
 
2. 使用[规则引擎](cdn-rules-engine.md)创建 URL 重写规则以启用对容器中所有 blob 的令牌访问。 新规则需要花 90 分钟的时间进行传播。

   此示例 URL 重写规则具有以下模式：
   
   源：   
   `/test/demo.jpg`
   
   目标：   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 重写规则](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. 续订 SAS 时，请更新 URL 重写规则以使用新的 SAS 令牌。 

## <a name="sas-parameter-considerations"></a>SAS 参数注意事项

由于 SAS 参数对 CDN 不可见，因此，CDN 无法根据这些参数更改自己的交付行为。 定义的参数限制仅适用于 CDN 对源服务器发出的请求，不适用于从客户端到 CDN 的请求。 在设置 SAS 参数时务必要考虑到此区别。 如果你需要这些高级功能并且正在使用[选项 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)，请对 CDN 安全令牌设置适当的限制。

| SAS 参数名称 | 说明 |
| --- | --- |
| 开始 | CDN 可以开始访问 blob 文件的时间。 由于存在时钟偏差 （当时钟信号在不同时间到达不同组件时），因此，如果希望资产立即可用，请选择一个提早 15 分钟的时间。 |
| 结束 | CDN 不可再访问 blob 文件的时间。 之前缓存在 CDN 上的文件仍可访问。 若要控制文件到期时间，请在 CDN 安全令牌上设置适当的到期时间或清除资产。 |
| 允许的 IP 地址 | 可选。 如果使用的是来自 Verizon 的 Azure CDN，可以将此参数设置为 [Azure CDN from Verizon Edge Server IP Ranges](https://msdn.microsoft.com/library/mt757330.aspx)（来自 Verizon 的 Azure CDN 边缘服务器 IP 范围）中定义的范围。 如果使用的是来自 Akamai 的 Azure CDN，则不能设置 IP 范围参数，因为这些 IP 地址不是静态的。|
| 允许的协议 | 允许为帐户 SAS 发出的请求使用的协议。 建议使用 HTTPS 设置。|

## <a name="see-also"></a>另请参阅
- [使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [共享访问签名，第 2 部分：创建 SAS 并将 SAS 用于 Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [使用令牌身份验证保护 Azure 内容交付网络资产](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
