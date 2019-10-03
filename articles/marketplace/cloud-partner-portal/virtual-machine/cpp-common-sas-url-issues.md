---
title: 常见的 SAS URL 问题和 Azure Marketplace 的修补程序
description: 列出了使用共享访问签名 URI 时的常见问题和可能的解决方案。
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 4f2770312624e1ca4c939ade458a451eb03f9d20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938378"
---
# <a name="common-sas-url-issues-and-fixes"></a>常见的 SAS URL 问题和修复

下表列出了使用共享访问签名（它们用来标识和共享解决方案的已上传 VHD）时遇到的一些常见问题以及建议的解决方法。

| **问题** | **失败消息** | **修补程序** | 
| --------- | ------------------- | ------- | 
| &emsp;  *复制映像时失败* |  |  |
| 在 SAS URL中未找到 "?" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建议的工具更新 SAS URL。 |
| SAS URL 中不存在“st”和“se”参数 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URL，在其中设置正确的**开始日期**和**结束日期**值。 | 
| SAS URL 中不存在“sp=rl” | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | 更新 SAS URL，将权限设置为 `Read` 和 `List`。 | 
| SAS URL 中的 VHD 名称中存在空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URL 以删除空格。 |
| SAS URL 授权错误 | `Failure: Copying Images. Not able to download blob due to authorization error` | 检查并更正 SAS URI 格式。 必要时重新生成。 |
| SAS URL“st”和“se”参数没有指定完整的日期时间 | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS URL **Start Date**和**结束日期**参数 (`st`并`se`子字符串) 都需要完整的日期时间格式，如`11-02-2017T00:00:00Z`。 缩短的版本无效。 （默认情况下，Azure CLI 中的某些命令可能会生成缩短的值。） | 
|  |  |  |

有关详细信息，请参阅[使用共享访问签名 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。
