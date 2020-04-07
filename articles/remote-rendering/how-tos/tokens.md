---
title: 获取服务访问令牌
description: 描述如何创建用于访问 ARR REST API 的令牌
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681189"
---
# <a name="get-service-access-tokens"></a>获取服务访问令牌

仅授权用户才能访问 ARR REST API。 要证明您的授权，您必须发送*访问令牌*以及 REST 请求。 这些令牌由*安全令牌服务*（STS） 颁发，以换取帐户密钥。 令牌**的生存期为 24 小时**，因此可以颁发给用户，而无需授予他们对服务的完全访问权限。

本文介绍如何创建此类访问令牌。

## <a name="prerequisites"></a>先决条件

[创建 ARR 帐户](create-an-account.md)，如果您还没有。

## <a name="token-service-rest-api"></a>令牌服务 REST API

要创建访问令牌，*安全令牌服务*提供单个 REST API。 ARR STS 服务的 URL[https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com)为 。

### <a name="get-token-request"></a>"获取令牌"请求

| URI | 方法 |
|-----------|:-----------|
| /帐户/**帐户 Id**/令牌 | GET |

| 标头 | 值 |
|--------|:------|
| 授权 | "持有人**帐户 Id**：**帐户密钥**" |

将*帐户 Id*和*帐户密钥*替换为相应的数据。

### <a name="get-token-response"></a>"获取令牌"响应

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | 访问令牌：字符串 | Success |

| 标头 | 目的 |
|--------|:------|
| MS-CV | 此值可用于跟踪服务中的调用 |

## <a name="getting-a-token-using-powershell"></a>使用 PowerShell 获取令牌

下面的 PowerShell 代码演示如何向 STS 发送必要的 REST 请求。 然后，它将令牌打印到 PowerShell 提示符。

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

脚本只需将令牌打印到输出，即可复制&粘贴它。 对于实际项目，您应该自动执行此过程。

## <a name="next-steps"></a>后续步骤

* [电源外壳脚本示例](../samples/powershell-example-scripts.md)
* [Azure 前端 API](../how-tos/frontend-apis.md)
* [会话管理 REST API](../how-tos/session-rest-api.md)
