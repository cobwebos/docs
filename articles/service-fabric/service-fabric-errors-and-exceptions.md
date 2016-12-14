---
title: "引发的常见 FabricClient 异常 | Microsoft 文档"
description: "描述在执行应用程序和群集管理操作时，FabricClient API 可能会引发的常见异常和错误。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 491f61afe899c746c193f0d3703d3212f9258e63
ms.openlocfilehash: 578046d6939b90cb58d457087bb465006630b4c1


---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>使用 FabricClient API 时常见的异常和错误
借助 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) API，群集和应用程序管理员可以对 Service Fabric 应用程序、服务或群集执行管理任务。 例如，部署、升级和删除应用程序、检查群集的运行状况或测试服务。 应用程序开发人员和群集管理员可以使用 FabricClient API 来开发用于管理 Service Fabric 群集和应用程序的工具。

使用 FabricClient 可以执行许多不同类型的操作。  由于输入错误、运行时错误或暂时性基础结构问题，每种方法可能会引发异常或错误。  请参阅 API 参考文档，以查找特定的方法会引发哪些异常。 不过，某些异常可能会由多个不同的 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) API 引发。 下表列出了 FabricClient API 引发的常见异常。

| 异常 | 引发时机 |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) 对象处于关闭状态。 释放正在使用的 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) 对象，然后实例化新的 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) 对象。 |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |操作超时。 如果完成操作所需的时间超过 MaxOperationTimeout，则返回 [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode)。 |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |对操作的访问权限检查失败。 返回了 E_ACCESSDENIED。 |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |执行操作时发生运行时错误。 任何 FabricClient 方法都可能引发 [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException)，[ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) 属性指明了引发异常的确切原因。 [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) 枚举中定义了错误代码。 |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |由于出现某种暂时性错误状态，操作失败。 例如，由于副本的仲裁暂时不可访问，某项操作可能会失败。 暂时性异常对应于可重试的失败操作。 |

可能在 [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) 中返回的某些常见 [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) 错误：

| 错误 | 条件 |
| --- |:--- |
| CommunicationError |通信错误导致操作失败，请重试操作。 |
| InvalidCredentialType |凭据类型无效。 |
| InvalidX509FindType |X509FindType 无效。 |
| InvalidX509StoreLocation |X509 存储位置无效。 |
| InvalidX509StoreName |X509 存储名称无效。 |
| InvalidX509Thumbprint |X509 证书指纹字符串无效。 |
| InvalidProtectionLevel |保护级别无效。 |
| InvalidX509Store |无法打开 X509 证书存储。 |
| InvalidSubjectName |使用者名称无效。 |
| InvalidAllowedCommonNameList |公用名字符串列表的格式无效。 应为逗号分隔的列表。 |




<!--HONumber=Nov16_HO3-->


