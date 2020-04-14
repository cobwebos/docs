---
title: 常见的 SAS URI 问题和修复 - Azure 应用商店
description: 使用共享访问签名时遇到的常见问题和建议的解决方案。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266235"
---
# <a name="common-sas-uri-issues-and-fixes"></a>常见的 SAS URI 问题和修复

> [!IMPORTANT]
> 我们将 Azure 虚拟机产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照["常见 SAS URI 问题和](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues)云合作伙伴门户修补程序"中的说明进行操作，以管理您的优惠。

以下是使用共享访问签名（用于标识和共享已上载的 VHD 用于解决方案）时遇到的常见问题，以及建议的解决方案。

| **问题** | **失败消息** | **修补程序** |
| --------- | ------------------- | ------- |
| *复制映像时失败* |  |  |
| "？" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用推荐的工具更新 SAS URI。 |
| 不在 SAS URI 中的"st"和"se"参数 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用正确的**开始日期**和**结束日期**值更新 SAS URI。 |
| 不在 SAS URI 中的"sp_rl" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 将权限设置为`Read`和`List`更新 SAS URI。 |
| SAS URI 具有 VHD 名称中的空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI 以删除空格。 |
| SAS URI 授权错误 | `Failure: Copying Images. Not able to download blob due to authorization error.` | 检查并更正 SAS URI 格式。 必要时重新生成。 |
| SAS URI"st"和"se"参数没有完整的日期时间规范 | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI**开始日期**和**结束日期**参数`st`（`se`和子字符串）必须具有完整的日期时间格式，如`11-02-2017T00:00:00Z`。 缩短的版本无效（默认情况下，Azure CLI 中的某些命令可能会生成缩短的值）。 |
|  |  |  |

有关详细信息，请参阅[使用共享访问签名 （SAS）。](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
