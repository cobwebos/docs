---
title: 适用于 Key Vault 的 Azure Defender-权益和功能
description: 了解适用于 Key Vault 的 Azure Defender 的优势和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 78a522922f8580003e2d2ff588cbf10dbf5fff9d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301765"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Azure Defender for Key Vault 简介

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

为适用于 Azure 的 azure **Defender Key Vault** 提供适用于 Azure Key Vault 的高级威胁防护，同时提供额外的安全智能层。 

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|**Azure Defender for Key Vault**按[定价页面上的](security-center-pricing.md)说明计费|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Azure Defender 对 Key Vault 有哪些好处？

Azure Defender 检测到 Key Vault 帐户访问或利用异常的潜在有害尝试。 借助此保护层，无需成为安全专家，也无需管理第三方安全监视系统，即可应对威胁。  

当发生异常活动时，Azure Defender 会显示警报，还可以选择通过电子邮件将其发送到组织相关成员。 这些警报包含可疑活动的详细信息，以及有关如何调查和修正威胁的建议。 

## <a name="azure-defender-for-key-vault-alerts"></a>用于 Key Vault 警报的 Azure Defender
当你从 Azure Defender 收到 Key Vault 的警报时，建议你根据对 [Key Vault 的 Azure defender 的响应](defender-for-key-vault-usage.md)中所述调查和响应警报。 Azure Defender for Key Vault 保护应用程序和凭据，因此即使你熟悉触发警报的应用程序或用户，也必须检查每个警报的情况。

警报显示在 Key Vault 的 " **安全** " 页、"Azure Defender" 面板和 "安全中心" 的 "警报" 页中。

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault 的 "安全" 页":::


> [!TIP]
> 可以按照在 [Azure 安全中心验证 Azure Key Vault 威胁检测](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)中的说明来模拟 azure Defender Key Vault 警报。


## <a name="next-steps"></a>后续步骤

本文介绍了有关 Key Vault 的 Azure Defender。

如需相关材料，请参阅以下文章： 

- [Key Vault 安全警报](alerts-reference.md#alerts-azurekv) - 所有 Azure 安全中心警报的引用表的 Key Vault 部分
- [将警报导出到 SIEM](continuous-export.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)