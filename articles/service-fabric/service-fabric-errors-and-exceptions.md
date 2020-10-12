---
title: 引发的常见 FabricClient 异常
description: 描述在执行应用程序和群集管理操作时，FabricClient API 可能会引发的常见异常和错误。
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258845"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>使用 FabricClient API 时常见的异常和错误
[FabricClient](/dotnet/api/system.fabric.fabricclient) API 可让群集和应用程序管理员对 Service Fabric 造应用程序、服务或群集执行管理任务。 例如，部署、升级和删除应用程序、检查群集的运行状况或测试服务。 应用程序开发人员和群集管理员可以使用 FabricClient API 来开发用于管理 Service Fabric 群集和应用程序的工具。

使用 FabricClient 可以执行许多不同类型的操作。  由于输入错误、运行时错误或暂时性基础结构问题，每种方法都可能会引发异常或错误。  请参阅 API 参考文档，了解特定的方法会引发哪些异常。 但是，某些异常可能会由许多不同的 [FabricClient](/dotnet/api/system.fabric.fabricclient) API 引发。 下表列出了 FabricClient API 中常见的异常。

| 异常 | 引发时机 |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](/dotnet/api/system.fabric.fabricclient) 对象处于关闭状态。 释放正在使用的 [FabricClient](/dotnet/api/system.fabric.fabricclient) 对象，然后实例化新的 [FabricClient](/dotnet/api/system.fabric.fabricclient) 对象。 |
| [System.TimeoutException](/dotnet/core/api/system.timeoutexception) |操作超时。[OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) 。 |
| [System.UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |对操作的访问权限检查失败。 返回了 E_ACCESSDENIED。 |
| [System.Fabric.FabricException](/dotnet/api/system.fabric.fabricexception) |执行操作时发生运行时错误。 任何 FabricClient 方法都可能引发 [FabricException](/dotnet/api/system.fabric.fabricexception)，[ErrorCode](/dotnet/api/system.fabric.fabricexception.errorcode) 属性指明了引发异常的确切原因。 [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) 枚举中将定义错误代码。 |
| [System.Fabric.FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |由于某种暂时性错误状态，操作失败。 例如，由于副本的仲裁暂时不可访问，某项操作可能会失败。 暂时性异常对应于可重试的失败操作。 |

可能在 [FabricException](/dotnet/api/system.fabric.fabricexception) 中返回的某些常见 [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) 错误：

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
