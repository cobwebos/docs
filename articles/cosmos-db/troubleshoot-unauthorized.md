---
title: 排查 Azure Cosmos DB 未经授权异常
description: 了解如何诊断和修复未经授权异常。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a4f51b641ca38b2b6f74bb77928537270d12f1e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870844"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>诊断和排查 Azure Cosmos DB 未经授权异常

HTTP 401：HTTP 请求中的 MAC 签名与计算出的签名不同。
如果收到 401 错误消息“HTTP 请求中的 MAC 签名与计算出的签名不同”，这可能是由以下情况所致。

对于较旧的 SDK，异常可能显示为无效的 JSON 异常，而不是正确的 401 未经授权异常。 较新的 SDK 可以正确处理此情况，并提供有效的错误消息。

## <a name="troubleshooting-steps"></a>疑难解答步骤
以下列表包含未经授权异常的已知原因和解决方案。

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>密钥未正确轮换是最常见的情况
密钥轮换后不久就会出现 401 MAC 签名问题，但无需进行任何更改，它最终会停止。 

#### <a name="solution"></a>解决方案：
密钥已轮换，且没有遵循[最佳做法](secure-access-to-data.md#key-rotation)。 完成 Azure Cosmos DB 帐户密钥轮换耗时几秒到几天不等，具体取决于 Azure Cosmos DB 帐户大小。

### <a name="the-key-is-misconfigured"></a>密钥配置错误 
401 MAC 签名问题持续出现并在使用该密钥的所有调用中发生。

#### <a name="solution"></a>解决方案：
密钥在应用程序上配置错误，且帐户使用了错误密钥或未复制整个密钥。

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>应用程序使用只读密钥进行写入操作
401 MAC 签名问题仅发生在创建或替换等写入操作中，但读取请求会成功。

#### <a name="solution"></a>解决方案：
切换应用程序以使用读/写密钥，从而允许操作成功完成。

### <a name="race-condition-with-create-container"></a>创建容器时出现争用情况
创建容器后不久会看到 401 MAC 签名问题。 此问题仅会在容器创建完成前发生。

#### <a name="solution"></a>解决方案：
创建容器时出现争用状况。 在容器创建完成之前，某个应用程序实例正在尝试访问容器。 出现此争用条件的最常见情况是，应用程序正在运行就删除了容器，并重新创建了同名的容器。 SDK 尝试使用新容器，但由于容器创建仍在进行，因此无法获得密钥。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。