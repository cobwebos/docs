---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003608"
---
## <a name="storage"></a>存储

|  |  |
|---------|---------|
| [允许的存储帐户和虚拟网络的 SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 要求存储帐户和虚拟机使用已批准的 SKU。 使用内置策略以确保使用已批准的 SKU。 指定已批准的虚拟机 SKU 数组和已批准的存储帐户 SKU 数组。 |
| [允许的存储帐户 SKU](../articles/governance/policy/samples/allowed-stor-acct-skus.md) | 要求存储帐户使用批准的 SKU。 指定一个已批准的 SKU 的数组。 |
| [拒绝存储帐户的冷访问层](../articles/governance/policy/samples/deny-cool-access-tiering.md) | 禁止为 blob 存储帐户使用冷访问层。  |
| [确保 https 流量仅限于存储帐户](../articles/governance/policy/samples/ensure-https-stor-acct.md) | 要求存储帐户使用 HTTPS 流量。  |
| [确保存储文件加密](../articles/governance/policy/samples/ensure-store-file-enc.md) | 要求对存储帐户启用文件加密。  |
| [需要存储帐户加密](../articles/governance/policy/samples/req-store-acct-enc.md) | 要求存储帐户使用 blob 加密。  |