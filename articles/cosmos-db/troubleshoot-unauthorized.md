---
title: 排查 Azure Cosmos DB 未经授权的异常
description: 如何诊断和修复未经授权的异常
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294013"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>诊断并解决 Azure Cosmos DB 未经授权的异常

HTTP 401： HTTP 请求中找到的 MAC 签名与计算所得的签名不同。
如果收到以下 401 错误消息：“HTTP 请求中的 MAC 签名与计算出的签名不同。” 此错误可能是由以下情况造成的。

旧版 Sdk，异常可以显示为无效的 json 异常，而不是正确的401未经授权的异常。 较新的 Sdk 会正确处理此方案并显示有效的错误消息。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含未经授权的异常的已知原因和解决方法。

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. 密钥没有正确旋转是最常见的方案。
密钥轮换之后不久就会出现 401 MAC 签名问题，但无需进行任何更改，它最终会停止。 

#### <a name="solution"></a>解决方案：
密钥已轮换，且未遵循[最佳做法](secure-access-to-data.md#key-rotation)。 Cosmos DB 帐户密钥轮换可能需要几秒钟到几秒钟的时间，具体取决于 Cosmos DB 的帐户大小。

### <a name="2-the-key-is-misconfigured"></a>2. 密钥配置错误 
401 MAC 签名问题将保持一致，并对使用该密钥的所有调用发生

#### <a name="solution"></a>解决方案：
此密钥在应用程序上配置不正确，并且正在使用该帐户的错误密钥，或者未复制整个密钥。

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. 应用程序正在使用只读密钥进行写入操作
401 MAC 签名问题仅发生在创建或替换等写入操作时，但读取请求成功。

#### <a name="solution"></a>解决方案：
切换应用程序以使用读取/写入密钥，以允许操作成功完成。

### <a name="4-race-condition-with-create-container"></a>4. 带 create 容器的争用条件
401在创建容器后不久会出现 MAC 签名问题。 这仅在容器创建完成后才会发生。

#### <a name="solution"></a>解决方案：
创建容器时出现争用状况。 在完成容器创建之前，某个应用程序实例正在尝试访问容器。 此争用情况最常见的情况是，如果应用程序正在运行，并且删除了该容器并以相同的名称重新创建它。 SDK 将尝试使用新容器，但由于容器创建仍在进行，因此无法获得密钥。

## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则