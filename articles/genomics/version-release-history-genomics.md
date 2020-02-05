---
title: 版本发行历史记录
titleSuffix: Microsoft Genomics
description: Microsoft 基因组学 Python 客户端更新的发布历史记录，用于修补程序和新功能。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991076"
---
# <a name="version-release-history"></a>版本发行历史记录
Microsoft 基因组学团队定期更新 Microsoft 基因组学 Python 客户端，以便提供修补程序和新的功能。 

## <a name="latest-release"></a>最新发布
当前 Python 客户端为版本0.9.0。 它已于 6 2019 年2月发布，并支持通过 GATK 3.5 和 GATK4 运行工作流。 它支持 gVCF 输出，并可接受用于输出压缩的可选参数。


## <a name="release-history"></a>版本历史记录 
Microsoft 基因组学 Python 客户端的新版本每年发布一次。 当 Microsoft 基因组学 Python 客户端发布新版本时，此处会更新修补程序和功能列表。 发布新版本后，对早期版本的支持应至少持续 90 日。 当早期版本不再受支持时，此页面将进行说明。 

### <a name="version-090"></a>版本0.9。0
版本0.9.0 包括对输出压缩的支持。 这等效于运行 `-bgzip`，然后 `-tabix` .vcf 或 gvcf 输出。 有关详细信息，请参阅[常见问题解答](frequently-asked-questions-genomics.md)。 

### <a name="version-081"></a>版本 0.8.1
版本 0.8.1 包括小 bug 修复。  

### <a name="version-080"></a>版本 0.8.0
版本 0.8.0 包括对 GATK4 和输出 gVCF 的支持。  

### <a name="version-074"></a>版本 0.7.4
版本 0.7.4 包括对于接受 `config.txt` 输入中的 SAS 令牌（而不是帐户密钥）的支持。 有关详细信息，请参阅[输入 SAS 令牌快速入门](quickstart-input-sas.md)。 

### <a name="version-073"></a>版本 0.7.3
版本 0.7.3 包括小 bug 修复。

### <a name="version-072"></a>版本 0.7.2
版本 0.7.2 是初始版本。 它已于 2017 年 11 月 1 发布。
