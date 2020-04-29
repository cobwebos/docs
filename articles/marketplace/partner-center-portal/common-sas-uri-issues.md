---
title: 常见 SAS URI 问题和修复-Azure Marketplace
description: 使用共享访问签名时遇到的常见问题和建议的解决方法。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266235"
---
# <a name="common-sas-uri-issues-and-fixes"></a>常见的 SAS URI 问题和修补程序

> [!IMPORTANT]
> 我们正在将 Azure 虚拟机产品/服务的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移你的产品/服务之前，请遵循[常见的 SAS URI 问题和修复](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues)中的说明来管理你的产品/服务的云合作伙伴门户。

下面是在使用共享访问签名（用于标识并共享解决方案的已上传 Vhd）时遇到的常见问题，以及建议的解决方法。

| **问题** | **失败消息** | **修补程序** |
| --------- | ------------------- | ------- |
| *复制映像时失败* |  |  |
| 在 SAS URI 中找不到 "？" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建议的工具更新 SAS URI。 |
| 不在 SAS URI 中的 "st" 和 "se" 参数 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 请将 SAS URI 更新为正确的**开始日期**和**结束日期**值。 |
| "sp = rl" 不在 SAS URI 中 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新设置为`Read`和`List`的权限的 SAS URI。 |
| SAS URI 的 VHD 名称中有空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI 以删除空格。 |
| SAS URI 授权错误 | `Failure: Copying Images. Not able to download blob due to authorization error.` | 检查并更正 SAS URI 格式。 必要时重新生成。 |
| SAS URI "st" 和 "se" 参数不具有完整的日期时间规范 | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI**开始日期**和**结束日期**参数（`st`和`se`子字符串）必须具有完整的日期时间格式，如`11-02-2017T00:00:00Z`。 缩短版本无效（Azure CLI 中的某些命令可能会默认生成缩短值）。 |
|  |  |  |

有关详细信息，请参阅[使用共享访问签名（SAS）](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。
