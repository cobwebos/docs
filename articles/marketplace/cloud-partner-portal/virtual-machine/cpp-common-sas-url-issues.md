---
title: Azure 应用商店的常见 SAS URL 问题和修补程序
description: 列出了使用共享访问签名 URI 时的常见问题和可能的解决方案。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: c575389538230218e1e6e4f172ebcfbee8ee51dc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273454"
---
# <a name="common-sas-url-issues-and-fixes"></a>常见的 SAS URL 问题和修复

> [!IMPORTANT]
> 从 2020 年 4 月 13 日开始，我们将开始将 Azure 虚拟机产品的管理移到合作伙伴中心。 迁移后，您将在合作伙伴中心创建和管理您的优惠。 按照[常见 SAS URL 问题和修补程序](https://aka.ms/AzureSAS_URL_FAQ)中的说明进行操作，以管理迁移的优惠。

下表列出了使用共享访问签名（它们用来标识和共享解决方案的已上传 VHD）时遇到的一些常见问题以及建议的解决方法。

| **问题** | **失败消息** | **修补程序** | 
| --------- | ------------------- | ------- | 
| &emsp;  *复制映像时失败* |  |  |
| 在 SAS URL中未找到 "?" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建议的工具更新 SAS URL。 |
| 不在 SAS URL 中的"st"和"se"参数 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URL，在其中设置正确的**开始日期**和**结束日期**值。 | 
| 不在 SAS URL 中的"sp_rl" | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | 更新 SAS URL，将权限设置为 `Read` 和 `List`。 | 
| SAS URL 中的 VHD 名称中存在空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URL 以删除空格。 |
| SAS URL 授权错误 | `Failure: Copying Images. Not able to download blob due to authorization error` | 检查并更正 SAS URI 格式。 必要时重新生成。 |
| SAS URL“st”和“se”参数没有指定完整的日期时间 | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS URL**开始日期**和**结束日期**参数`st`（`se`和子字符串）需要具有完整的日期时间格式，如`11-02-2017T00:00:00Z`。 缩短的版本无效。 （默认情况下，Azure CLI 中的某些命令可能会生成缩短的值。） | 
|  |  |  |

有关详细信息，请参阅[使用共享访问签名 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。
