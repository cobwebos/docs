---
title: 为 Azure Key Vault 设置高级威胁防护
description: 本文介绍如何在 Azure 安全中心为 Azure Key Vault 设置高级威胁防护
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 3ca3026a259e7f23bbfa01398a96cbdbfae1321c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623983"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault 的威胁防护（预览版）

Azure Key Vault 的高级威胁防护提供额外的安全智能层。 此工具检测到 Key Vault 帐户访问或利用潜在有害尝试。 使用 Azure 安全中心的本机高级威胁防护，无需成为安全专家，也无需学习其他安全监视系统，就能解决威胁。

当安全中心检测到异常活动时，它会显示警报。 它还向订阅管理员发送有关可疑活动的详细信息，以及如何调查和修正确定的威胁的建议。

## <a name="configuring-threat-protection-from-security-center"></a>配置安全中心的威胁防护

默认情况下，当你订阅安全中心的标准定价层时，将为所有 Key Vault 帐户启用高级威胁防护。 有关详细信息，请参阅[定价](security-center-pricing.md)。

若要启用或禁用对特定订阅的保护：

1. 在安全中心的左窗格中，选择 "**定价 & 设置**"。

1. 选择包含要为其启用或禁用威胁保护的存储帐户的订阅。

1. 选择“定价层”。

1. 在 "**按资源类型组选择定价层**" 中，找到 "**密钥保管库**" 行，然后选择 "**已启用**" 或 "**禁用**"。

    [为 Azure 安全中心中的 Key Vault 启用或禁用高级威胁防护 ![](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. 选择“保存”。


## <a name="next-steps"></a>后续步骤

本文介绍了如何启用和禁用 Azure Key Vault 的高级威胁防护。 

有关相关材料，请参阅以下文章：

- [Azure 安全中心的威胁防护](threat-protection.md)-本文介绍了 Azure 安全中心的安全警报源。
- [Key Vault 安全警报](alerts-reference.md#alerts-azurekv)-所有 Azure 安全中心警报的引用表的 Key Vault 部分