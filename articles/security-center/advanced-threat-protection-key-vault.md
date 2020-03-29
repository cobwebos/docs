---
title: Azure 密钥保管库的威胁保护
description: 本文介绍如何在 Azure 安全中心中为 Azure 密钥保管库设置高级威胁保护
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914800"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure 密钥保管库的威胁保护（预览版）

Azure 密钥保管库的高级威胁保护提供了额外的安全智能层。 此工具可检测访问或利用密钥保管库帐户的潜在有害尝试。 在 Azure 安全中心中使用本机高级威胁保护，无需成为安全专家即可应对威胁，无需学习其他安全监控系统。

当安全中心检测到异常活动时，将显示警报。 它还向订阅管理员发送电子邮件，提供可疑活动的详细信息以及如何调查和修复已识别的威胁的建议。

## <a name="configuring-threat-protection-from-security-center"></a>配置安全中心的威胁防护

默认情况下，当您订阅安全中心的标准定价层时，为您的所有密钥保管库帐户启用高级威胁保护。 有关详细信息，请参阅[定价](security-center-pricing.md)。

要启用或禁用特定订阅的保护，请：

1. 从安全中心中的左侧窗格中，选择**定价&设置**。

1. 选择要为其启用或禁用威胁保护的存储帐户的订阅。

1. 选择**定价层**。

1. 从 **"按资源类型组选择定价层**"中，**查找"密钥保管库"** 行并选择 **"已启用**"或 **"已禁用**"。

    [![在 Azure 安全中心为密钥保管库启用或禁用高级威胁保护](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. 选择“保存”。****


## <a name="next-steps"></a>后续步骤

在本文中，您将了解如何为 Azure 密钥保管库启用和禁用高级威胁保护。 

有关相关材料，请参阅以下文章：

- [Azure 安全中心中的威胁保护](threat-protection.md)-- 本文介绍 Azure 安全中心中的安全警报源。
- [密钥保管库安全警报](alerts-reference.md#alerts-azurekv)-- 所有 Azure 安全中心警报的参考表的密钥保管库部分