---
title: Azure Key Vault 的威胁防护
description: 本文介绍如何在 Azure 安全中心中为 Azure Key Vault 设置高级威胁防护
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77914800"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault 的威胁防护（预览版）

Azure Key Vault 的高级威胁防护提供了额外的安全情报层。 此工具可检测访问或利用 Key Vault 帐户的潜在有害的尝试。 使用 Azure 安全中心中的本机高级威胁防护，无需成为安全专家，也无需学习其他安全监视系统就可以解决威胁。

安全中心检测到异常活动时会显示警报。 它还会向订阅管理员发送电子邮件，提供可疑活动的详细信息，以及如何调查和修正已识别威胁的建议。

## <a name="configuring-threat-protection-from-security-center"></a>配置安全中心的威胁防护

默认情况下，当你订阅安全中心的标准定价层时，将为你的所有 Key Vault 帐户启用高级威胁防护。 有关详细信息，请参阅[定价](security-center-pricing.md)。

启用或禁用对特定订阅的保护：

1. 在安全中心的左窗格中，选择“定价和设置”。

1. 选择具有要为其启用或禁用威胁防护的存储帐户的订阅。

1. 选择“定价层”。

1. 在“按资源类型选择定价层”组中，找到“Key Vault”行，然后选择“启用”或“禁用”   。

    [![在 Azure 安全中心中启用或禁用 Key Vault 的高级威胁防护](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. 选择“保存” 。


## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何为 Azure Key Vault 启用和禁用高级威胁防护。 

有关相关材料，请参阅以下文章：

- [Azure 安全中心中的威胁防护](threat-protection.md) - 这篇文章介绍了 Azure 安全中心中安全警报的来源。
- [Key Vault 安全警报](alerts-reference.md#alerts-azurekv) - 所有 Azure 安全中心警报的引用表的 Key Vault 部分