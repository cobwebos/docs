---
title: 用于 Key Vault 的 Azure Defender - 优点和功能
description: 了解用于 Key Vault 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 6649a8d470a75922aac423bf0b411163bdd79f71
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449087"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>用于 Key Vault 的 Azure Defender 简介

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

启用适用于 Key Vault 的 Azure Defender，这是针对 Azure Key Vault 的 Azure 原生高级威胁防护，提供额外的安全情报层。 

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|用于 Key Vault 的 Azure Defender 按[定价页中](security-center-pricing.md)的定价计费|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>适用于 Key Vault 的 Azure Defender 有哪些优点？

Azure Defender 会检测以非寻常和可能有害方式访问或恶意利用密钥保管库帐户的企图。 借助此保护层，无需成为安全专家，也无需管理第三方安全监视系统，即可应对威胁。  

当发生异常活动时，Azure Defender 会显示警报，并选择性地通过电子邮件将其发送给组织中的相关成员。 这些警报包含可疑活动的详细信息，以及有关如何调查和修正威胁的建议。 

## <a name="azure-defender-for-key-vault-alerts"></a>适用于 Key Vault 的 Azure Defender 警报
当收到来自适用于 Key Vault 的 Azure Defender 的警报时，建议按[响应适用于 Key Vault 的 Azure Defender](defender-for-key-vault-usage.md) 中的说明，调查和响应警报。 适用于 Key Vault 的 Azure Defender 旨在为应用程序和凭据提供保护，因此即使你熟悉触发警报的应用程序或用户，也请基于每个警报检查相关情况。

警报显示在 Key Vault 的“安全”页、Azure Defender 仪表板和安全中心的“警报”页中。

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault 的“安全”页":::


> [!TIP]
> 可以按照[验证 Azure 安全中心中的 Azure Key Vault 威胁检测](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)中的说明，模拟适用于 Key Vault 的 Azure Defender 警报。


## <a name="next-steps"></a>后续步骤

本文介绍了适用于 Key Vault 的 Azure Defender。

如需相关材料，请参阅以下文章： 

- [Key Vault 安全警报](alerts-reference.md#alerts-azurekv) - 所有 Azure 安全中心警报的引用表的 Key Vault 部分
- [将警报导出到 SIEM](continuous-export.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)