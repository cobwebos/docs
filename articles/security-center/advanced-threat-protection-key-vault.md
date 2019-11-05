---
title: 如何为 Azure Key Vault 设置高级威胁防护 |Microsoft Docs
description: 本文介绍如何在 Azure 安全中心为 Azure Key Vault 设置高级威胁防护
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521873"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>如何为 Azure Key Vault 设置高级威胁防护（预览版）

Azure Key Vault 的高级威胁防护提供额外的安全智能层。 此工具检测到 Key Vault 帐户访问或利用潜在有害尝试。 使用安全中心的本机高级威胁防护，无需成为安全专家，也无需学习其他安全监视系统，就能解决威胁。

当安全中心检测到异常活动时，它会显示警报。 它还向订阅管理员发送有关可疑活动的详细信息，以及如何调查和修正确定的威胁的建议。 

> [!NOTE]
> Azure Key Vault 的高级威胁防护目前仅适用于北美区域。

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>设置 Azure 安全中心的高级威胁防护

默认情况下，当你订阅安全中心的标准层时，将为所有 Key Vault 帐户启用高级威胁防护（请参阅[定价](security-center-pricing.md)）。 

若要启用或禁用对特定订阅的保护：

1. 在安全中心的边栏中，单击 "**定价 & 设置**"。
1. 选择包含要为其启用或禁用威胁保护的存储帐户的订阅。
1. 单击“定价层”。
1. 在 "**按资源类型组选择定价层**" 中，找到 "密钥保管库" 行，然后单击 "**启用**" 或 "**禁用**"。
    [![在 Azure 安全中心中启用或禁用 Key Vault 的高级威胁防护](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. 单击“保存”。


## <a name="next-steps"></a>后续步骤

本文介绍了如何启用和禁用 Azure Key Vault 的高级威胁防护。 

有关其他相关材料，请参阅以下文章：

- [安全中心内 Azure 服务层的威胁检测](security-center-alerts-service-layer.md)-本文介绍了与高级威胁防护相关的警报 Azure Key Vault