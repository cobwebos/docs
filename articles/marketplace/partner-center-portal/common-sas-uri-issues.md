---
title: 常见 SAS URI 问题和修复 - Azure 市场
description: 使用共享访问签名时遇到的常见问题和建议的解决方案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 4eb3c3e893a276aed10807a13a0f2d6d3bc4e71d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87316832"
---
# <a name="common-sas-uri-issues-and-fixes"></a>常见 SAS URI 问题和修复

以下介绍使用共享访问签名（用来标识和共享解决方案的已上传 VHD）时遇到的常见问题以及建议的解决方法。

| **问题** | **失败消息** | **修补程序** |
| --------- | ------------------- | ------- |
| *复制映像时失败* |  |  |
| 在 SAS URI 中未找到“?” | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建议的工具更新 SAS URI。 |
| SAS URI 中不存在“st”和“se”参数 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用正确的“开始日期”和“结束日期”值更新 SAS URI。 |
| SAS URI 中不存在“sp=rl” | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI，将权限设置为 `Read` 和 `List`。 |
| SAS URI 的 VHD 名称中存在空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI 以删除空格。 |
| SAS URI 授权错误 | `Failure: Copying Images. Not able to download blob due to authorization error.` | 检查并更正 SAS URI 格式。 必要时重新生成。 |
| SAS URI 的“st”和“se”参数没有完整的日期时间规范 | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI 的“开始日期”和“结束日期”参数（`st` 和 `se` 子字符串）必须具有完整的日期时间格式，例如 `11-02-2017T00:00:00Z`。 缩短版本无效（默认情况下，Azure CLI 中的某些命令可能会生成缩短的值）。 |
|  |  |  |

有关详细信息，请参阅[使用共享访问签名 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。
