---
title: 获取服务访问令牌
description: 介绍如何创建用于访问 ARR REST Api 的令牌
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e8e2f3f9dd49693faa26eaaab309fcad58f6f9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076151"
---
# <a name="get-service-access-tokens"></a>获取服务访问令牌

仅向授权用户授予对 ARR REST Api 的访问权限。 若要证明你的授权，你必须将 *访问令牌* 与 REST 请求一起发送。 这些令牌由 *安全令牌服务* 颁发， (STS) 用于帐户密钥的 exchange 中。 令牌的 **生存期为24小时** ，因此，可以将其颁发给用户，而无需授予其对服务的完全访问权限。

本文介绍如何创建此类访问令牌。

## <a name="prerequisites"></a>必备条件

如果还没有[ARR 帐户](create-an-account.md)，请创建一个。

## <a name="token-service-rest-api"></a>令牌服务 REST API

为创建访问令牌， *安全令牌服务* 提供单个 REST API。 ARR STS 服务的 URL 是 https： \/ /sts.mixedreality.azure.com。

### <a name="get-token-request"></a>"获取令牌" 请求

| URI | 方法 |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| 标头 | 值 |
|--------|:------|
| 授权 | "持有者 **accountId**：**accountKey**" |

将 *accountId* 和 *accountKey* 替换为相应的数据。

### <a name="get-token-response"></a>"获取令牌" 响应

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | AccessToken：字符串 | 成功 |

| Header | 目的 |
|--------|:------|
| MS-CV | 此值可用于跟踪服务中的调用 |

## <a name="getting-a-token-using-powershell"></a>使用 PowerShell 获取令牌

下面的 PowerShell 代码演示如何向 STS 发送必要的 REST 请求。 然后，它会将令牌打印到 PowerShell 提示符。

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

此脚本只是将令牌打印到输出，从中复制 & 粘贴。 对于实际项目，应自动执行此过程。

## <a name="next-steps"></a>后续步骤

* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
* [Azure 前端 Api](../how-tos/frontend-apis.md)
* [会话管理 REST API](../how-tos/session-rest-api.md)
