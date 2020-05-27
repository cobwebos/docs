---
title: Azure 事件网格安全和身份验证
description: 本文介绍了对事件网格资源（WebHook、订阅、自定义主题）的访问进行身份验证的不同方法
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 71d47c83586f7e5e31b148714e2804686422326a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588252"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>对 Azure 事件网格资源的访问进行身份验证
本文提供了关于以下方案的信息：  

- 使用共享访问签名 (SAS) 或密钥对将事件发布到 Azure 事件网格主题的客户端进行身份验证。 
- 使用 Azure Active Directory (Azure AD) 保护 Webhook 终结点，以向事件网格进行身份验证，从而将事件传递到终结点。

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>使用 SAS 或密钥对发布客户端进行身份验证
自定义主题使用共享访问签名 (SAS) 或密钥身份验证。 建议使用 SAS，但密钥身份验证提供简单的编程，并与多个现有 webhook 发布服务器兼容。

HTTP 标头中包括身份验证值。 对于 SAS，使用 aeg-sas-token 作为标头值。 对于密钥身份验证，使用 aeg-sas-key 作为标头值。

### <a name="key-authentication"></a>密钥身份验证

密钥身份验证是最简单的身份验证形式。 在邮件头中使用格式 `aeg-sas-key: <your key>`。

例如，使用以下项传递密钥：

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

还可以将 `aeg-sas-key` 指定为查询参数。 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 令牌

事件网格的 SAS 令牌包括资源、过期时间和签名。 SAS 令牌的格式是：`r={resource}&e={expiration}&s={signature}`。

资源是要将事件发送到的事件网格主题的路径。 例如，有效的资源路径是 `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`。 若要查看所有受支持的 API 版本，请参阅 [Microsoft.EventGrid 资源类型](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)。 

从密钥生成签名。

例如，有效的 aeg-sas-token 值是：

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

以下示例会创建用于事件网格的 SAS 令牌：

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>静态加密

由事件网格服务写入到磁盘的所有事件或数据都使用 Microsoft 托管密钥进行加密，同时确保进行的是静态加密。 此外，根据[事件网格重试策略](delivery-and-retry.md)，事件或数据的最长保留期为 24 小时。 事件网格将在 24 小时或事件生存时间到期（无论哪个在先）后自动删除所有事件或数据。

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>对 Webhook 终结点的事件传递进行身份验证
下面各部分介绍了如何对 Webhook 终结点的事件传递进行身份验证。 无论使用何种方法，都需要使用验证握手机制。 有关详细信息，请参阅 [Webhook 事件传递](webhook-event-delivery.md)。 

### <a name="using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD)
可以保护 Webhook 终结点，具体方法为使用 Azure Active Directory (Azure AD) 向事件网格进行身份验证和授权，以将事件传递到终结点。 需要创建 Azure AD 应用程序，并在授权事件网格的应用程序中创建角色和服务主体，同时还需要将事件订阅配置为使用 Azure AD 应用程序。 [了解如何为事件网格配置 Azure Active Directory](secure-webhook-delivery.md)。

### <a name="using-client-secret-as-a-query-parameter"></a>使用客户端密码作为查询参数
在创建事件订阅时，可以通过向 Webhook URL 中添加查询参数来保护 Webhook 终结点。 将这些查询参数之一设置为客户端密码，如[访问令牌](https://en.wikipedia.org/wiki/Access_token)或共享密码。 Webhook 可以使用该机密来识别事件是否来自具有有效权限的事件网格。 事件网格会在前往 Webhook 的每个事件传递中包括这些查询参数。 如果更新了客户端密码，还需要更新事件订阅。 为了避免在此密码轮换期间出现传递失败，让 Webhook 在有限的时间内同时接受新旧密码。 

由于查询参数可能包含客户端密码，因此需要格外小心地处理它们。 它们以加密的形式存储，不可由服务操作员访问。 它们不作为服务日志/跟踪的一部分进行记录。 编辑事件订阅时，除非在 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 中使用了 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 参数，否则，不会显示或返回查询参数。

若要详细了解如何将事件传递到 Webhook，请参阅 [Webhook 事件传递](webhook-event-delivery.md)

> [!IMPORTANT]
Azure 事件网格只支持 HTTPS Webhook 终结点。 

## <a name="next-steps"></a>后续步骤

- 有关事件网格的介绍，请参阅[关于事件网格](overview.md)
