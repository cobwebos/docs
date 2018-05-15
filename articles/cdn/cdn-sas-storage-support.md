---
title: 将 Azure CDN 与 SAS 一起使用 | Microsoft Docs
description: Azure CDN 支持使用共享访问签名 (SAS) 授予受限制的私有存储容器访问权限。
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: v-deasim
ms.openlocfilehash: dcae29c49035775cd9ff983bbc99bab06c7f16dc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="using-azure-cdn-with-sas"></a>将 Azure CDN 与 SAS 一起使用

当为 Azure 内容分发网络 (CDN) 设置存储帐户用来缓存内容时，默认情况下，知道存储容器 URL 的任何人都可访问上传的文件。 若要保护存储帐户中的文件，可将存储容器的访问权限从“公开”设置为“私有”。 但是，如果这样做，没人能访问你的文件。 

如果希望授予受限制的私有存储容器访问权限，可以使用 Azure 存储帐户的共享访问签名 (SAS) 功能。 SAS 是授予对 Azure 存储资源进行有限访问权限的 URI，而无需公开你的帐户密钥。 你可以为不信任向其提供存储帐户密钥，但需要向其委派某些存储帐户资源的访问权限的客户端提供 SAS。 通过向这些客户端分发共享访问签名 URI，可以授予它们在一段指定时间内对资源的访问权限。
 
使用 SAS 可以定义各种 blob 访问权限参数，例如开始和到期时间、权限（读/写）和 IP 范围。 本文介绍如何将 SAS 与 Azure CDN 一起使用。 有关 SAS 的详细信息，包括如何创建它及其参数选项，请参阅[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>设置 Azure CDN 以用于存储 SAS
对于将 SAS 与 Azure CDN 一起使用，建议使用以下三个选项。 所有选项都假设已创建了一个有效的 SAS（请参阅先决条件）。 
 
### <a name="prerequisites"></a>先决条件
首先创建存储帐户，然后为资产生成 SAS。 可以生成两种类型的存储访问签名：服务 SAS 或帐户 SAS。 有关详细信息，请参阅[共享访问签名的类型](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures)。

生成 SAS 令牌后，可将 `?sv=<SAS token>` 追加到 URL，访问 blob 存储文件。 此 URL 格式如下： 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
例如：
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

有关设置参数的详细信息，请参阅 [SAS 参数注意事项](#sas-parameter-considerations)和[共享访问签名参数](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters)。

![CDN SAS 设置](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>选项 1：使用从 Azure CDN 传递到 blob 存储的 SAS

此选项最为简单，并且仅使用从 Azure CDN 传递到源服务器的单个 SAS 令牌。 它受 **Verizon 推出的 Azure CDN 标准版**和 **Akamai 推出的 Azure CDN 标准版**配置文件的支持。 
 
1. 选择一个终结点，选择“缓存规则”，然后从“查询字符串缓存”列表中选择“缓存每个唯一的 URL”。

    ![CDN 缓存规则](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. 在存储帐户上设置 SAS 后，若要访问文件，必须将 SAS 令牌与 CDN 终结点和源服务器 URL 配合使用。 
   
   生成的 CDN 终结点 URL 的格式为：`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   例如：   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. 使用缓存规则或在源服务器处添加 `Cache-Control` 标头来微调缓存持续时间。 由于 Azure CDN 将 SAS 令牌视为普通查询字符串，因此，最佳做法是应该设置在 SAS 到期时或到期之前到期的缓存持续时间。 否则，如果文件的缓存持续时间长于 SAS 的有效时间，在 SAS 到期之后，则可从 Azure CDN 源服务器访问此文件。 如果出现这种情况，并且你需要使缓存文件不可访问，则必须对文件执行清除操作以将其从缓存中清除。 有关对 Azure CDN 设置缓存持续时间的信息，请参阅[使用缓存规则控制 Azure CDN 缓存行为](cdn-caching-rules.md)。

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>选项 2：使用重写规则隐藏的 CDN SAS 令牌
 
此选项仅适用于来自 Verizon 的 Azure CDN Premium 配置文件。 使用此选项，可以在源服务器上保护 Blob 存储。 如果不需要对文件进行特定的访问权限限制，但希望阻止用户直接访问存储源以提高 Azure CDN 卸载时间，则需要使用此选项。 若要在源服务器的指定容器中访问文件，必须使用对用户来说不可知的 SAS 令牌。 不过，由于 URL 重写规则的缘故，CDN 终结点上不需要 SAS 令牌。
 
1. 使用[规则引擎](cdn-rules-engine.md)创建 URL 重写规则。 新规则需要花 90 分钟的时间进行传播。

   ![CDN 管理按钮](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN 规则引擎按钮](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   以下示例 URL 重写规则使用包含捕获组和名为 storagedemo 的终结点的正则表达式模式：
   
   源：   
   `(/test/.*)`
   
   目标：   
   ```
   $1?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![CDN URL 重写规则](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-2.png)

2. 新规则激活以后，任何人都可以访问 CDN 终结点上指定容器中的文件，不管这些人是否在 URL 中使用 SAS 令牌。 格式为：`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   例如：   
   `https://demoendpoint.azureedge.net/container1/demo.jpg`
       

3. 使用缓存规则或在源服务器处添加 `Cache-Control` 标头来微调缓存持续时间。 由于 Azure CDN 将 SAS 令牌视为普通查询字符串，因此，最佳做法是应该设置在 SAS 到期时或到期之前到期的缓存持续时间。 否则，如果文件的缓存持续时间长于 SAS 的有效时间，在 SAS 到期之后，则可从 Azure CDN 源服务器访问此文件。 如果出现这种情况，并且你需要使缓存文件不可访问，则必须对文件执行清除操作以将其从缓存中清除。 有关对 Azure CDN 设置缓存持续时间的信息，请参阅[使用缓存规则控制 Azure CDN 缓存行为](cdn-caching-rules.md)。

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>选项 3：通过重写规则使用 CDN 安全令牌身份验证

若要使用 Azure CDN 安全令牌身份验证，必须具有来自 Verizon 的 Azure CDN Premium 配置文件。 此选项最为安全且可进行自定义。 客户端访问基于安全令牌上设置的安全参数。 创建并设置安全令牌以后，所有 CDN 终结点 URL 上都需要该令牌。 不过，由于 URL 重写规则的缘故，CDN 终结点上不需要 SAS 令牌。 如果 SAS 令牌稍后变为无效，Azure CDN 将再也不能通过源服务器来重新验证内容。

1. [创建 Azure CDN 安全令牌](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication)并使用 CDN 终结点的规则引擎和用户可访问文件的路径来激活它。

   安全令牌终结点 URL 采用以下格式：   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   例如：   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   安全令牌身份验证的参数选项不同于 SAS 令牌的参数选项。 如果在创建安全令牌时选择使用到期时间，则应将它设置为与 SAS 令牌的到期时间相同的值。 这样可确保到期时间是可预测的。 
 
2. 使用[规则引擎](cdn-rules-engine.md)创建 URL 重写规则以启用对容器中所有 blob 的 SAS 令牌访问。 新规则需要花 90 分钟的时间进行传播。

   以下示例 URL 重写规则使用包含捕获组和名为 storagedemo 的终结点的正则表达式模式：
   
   源：   
   `(/test/.*)`
   
   目标：   
   ```
   $1&sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![CDN URL 重写规则](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-3.png)

3. 如果续订 SAS，请确保使用新的 SAS 令牌更新 URL 重写规则。 

## <a name="sas-parameter-considerations"></a>SAS 参数注意事项

由于 SAS 参数对 Azure CDN 不可见，因此，Azure CDN 无法根据这些参数更改其交付行为。 定义的参数限制仅适用于 Azure CDN 对源服务器发出的请求，不适用于从客户端到 Azure CDN 的请求。 在设置 SAS 参数时务必要考虑到此区别。 如果需要这些高级功能并且正在使用[选项 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)，请对 Azure CDN 安全令牌设置适当的限制。

| SAS 参数名称 | 说明 |
| --- | --- |
| 开始 | Azure CDN 可以开始访问 blob 文件的时间。 由于存在时钟偏差 （当时钟信号在不同时间到达不同组件时），因此，如果希望资产立即可用，请选择一个提早 15 分钟的时间。 |
| 结束 | Azure CDN 不可再访问 blob 文件的时间。 之前缓存在 Azure CDN 上的文件仍可访问。 若要控制文件到期时间，请在 Azure CDN 安全令牌上设置适当的到期时间或清除资产。 |
| 允许的 IP 地址 | 可选。 如果使用的是来自 Verizon 的 Azure CDN，可以将此参数设置为 [Azure CDN from Verizon Edge Server IP Ranges](https://msdn.microsoft.com/library/mt757330.aspx)（来自 Verizon 的 Azure CDN 边缘服务器 IP 范围）中定义的范围。 如果使用的是来自 Akamai 的 Azure CDN，则不能设置 IP 范围参数，因为这些 IP 地址不是静态的。|
| 允许的协议 | 允许为帐户 SAS 发出的请求使用的协议。 建议使用 HTTPS 设置。|

## <a name="see-also"></a>另请参阅
- [使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [共享访问签名，第 2 部分：创建 SAS 并将 SAS 用于 Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- 
  [使用令牌身份验证保护 Azure 内容分发网络资产](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
