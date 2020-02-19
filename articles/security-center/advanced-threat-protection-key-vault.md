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
ms.openlocfilehash: 07924201d4208a502aa979dda476c57cf6edd749
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430544"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>为 Azure Key Vault 设置高级威胁防护（预览版）

Azure Key Vault 的高级威胁防护提供额外的安全智能层。 此工具检测到 Key Vault 帐户访问或利用潜在有害尝试。 使用 Azure 安全中心的本机高级威胁防护，无需成为安全专家，也无需学习其他安全监视系统，就能解决威胁。

当安全中心检测到异常活动时，它会显示警报。 它还向订阅管理员发送有关可疑活动的详细信息，以及如何调查和修正确定的威胁的建议。

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>设置 Azure 安全中心的高级威胁防护

默认情况下，当你订阅安全中心标准层时，将为所有 Key Vault 帐户启用高级威胁防护。 有关详细信息，请参阅[定价](security-center-pricing.md)。

若要启用或禁用对特定订阅的保护，请执行以下步骤。

1. 在安全中心的左窗格中，选择 "**定价 & 设置**"。
1. 选择包含要为其启用或禁用威胁保护的存储帐户的订阅。
1. 选择“定价层”。
1. 在 "**按资源类型组选择定价层**" 中，找到 "**密钥保管库**" 行，然后选择 "**已启用**" 或 "**禁用**"。

    [为 Azure 安全中心中的 Key Vault 启用或禁用高级威胁防护 ![](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. 选择“保存”。


## <a name="next-steps"></a>后续步骤

本文介绍了如何启用和禁用 Azure Key Vault 的高级威胁防护。 

有关其他相关材料，请参阅以下文章：

- [安全中心内 Azure 服务层的威胁检测](security-center-alerts-service-layer.md)：本文介绍了与 Azure Key Vault 的高级威胁防护相关的警报。
