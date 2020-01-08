---
title: 生成安全令牌以访问 IoT 即插即用预览存储库 |Microsoft Docs
description: 在以编程方式访问 IoT 即插即用预览模型存储库时，生成要使用的共享访问签名令牌。
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 2530c5b3561ad90eac0556770a8a356cfaa6a52c
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531281"
---
# <a name="generate-sas-token"></a>生成 SAS 令牌

本操作方法指南演示了如何以编程方式生成用于 IoT 即插即用预览模型存储库 Api 的共享访问签名（SAS）令牌。

## <a name="python"></a>Python

以下代码片段演示如何使用 Python 生成 SAS 令牌：

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

以下代码片段演示如何使用 C\#生成 SAS 令牌：

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>使用 SAS 令牌

生成 SAS 令牌后，可以使用它发出 HTTP POST 请求。 例如：

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

如果为客户端分配 SAS 令牌，则客户端不会具有该资源的主键，并且不能反向哈希来获取它。 SAS 令牌使你可以控制客户端可以访问的内容以及多长时间。 当你更改策略中的主密钥时，从该密钥创建的所有 SAS 令牌都将失效。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何生成用于访问模型 IoT 的安全令牌即插即用预览模型存储库，建议下一步是在[IoT 即插即用预览版建模开发人员指南](concepts-developer-guide.md)中了解详细信息。
