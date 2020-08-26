---
title: 排查 Azure Cosmos DB 未经授权的异常
description: 了解如何诊断和修复未经授权的异常。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a4f51b641ca38b2b6f74bb77928537270d12f1e8
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870844"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>诊断并解决 Azure Cosmos DB 未经授权的异常

HTTP 401：在 HTTP 请求中找到的 MAC 签名与计算所得的签名不同。
如果收到401错误消息 "HTTP 请求中找到的 MAC 签名与计算的签名不同"，则可能是由下列方案导致的。

对于较旧的 Sdk，异常可能会显示为无效的 JSON 异常，而不是正确的401未经授权的异常。 较新的 SDK 可以正确处理此情况，并提供有效的错误消息。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含未经授权的异常的已知原因和解决方法。

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>密钥未正确旋转是最常见的方案
在密钥轮换之后不久就会出现 401 MAC 签名，最终会在不进行任何更改的情况下停止。 

#### <a name="solution"></a>解决方案：
密钥已轮换，未遵循 [最佳做法](secure-access-to-data.md#key-rotation)。 Azure Cosmos DB 帐户密钥轮换可能需要几秒钟到几秒钟的时间，具体取决于 Azure Cosmos DB 的帐户大小。

### <a name="the-key-is-misconfigured"></a>密钥配置错误 
401 MAC 签名问题将保持一致，并对使用该密钥的所有调用发生。

#### <a name="solution"></a>解决方案：
此密钥在应用程序上配置错误，并对该帐户使用错误的密钥，或者未复制整个密钥。

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>应用程序正在使用只读密钥进行写入操作
401 MAC 签名问题仅发生在创建或替换等写入操作时，但读取请求成功。

#### <a name="solution"></a>解决方案：
切换应用程序以使用读/写密钥，从而允许操作成功完成。

### <a name="race-condition-with-create-container"></a>带 create 容器的争用条件
创建容器后，不久会出现 401 MAC 签名问题。 此问题仅在容器创建完成后才会出现。

#### <a name="solution"></a>解决方案：
创建容器时出现争用情况。 在容器创建完成之前，应用程序实例正在尝试访问容器。 此争用情况最常见的情况是，如果应用程序正在运行，并且删除了容器并重新创建了同一名称。 SDK 会尝试使用新的容器，但仍在进行容器创建，因此它没有密钥。

## <a name="next-steps"></a>后续步骤
* [诊断并解决](troubleshoot-dot-net-sdk.md) 使用 AZURE COSMOS DB .net SDK 时遇到的问题。
* 了解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的性能准则。