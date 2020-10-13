---
title: 如何响应 Azure Defender Key Vault 警报
description: 了解响应 Azure Defender 中 Key Vault 的警报所需的步骤。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: e3eb6b750c805951a67a2e869c862f1285a342a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448333"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>响应适用于 Key Vault 的 Azure Defender 警报
收到来自 Azure Defender for Key Vault 的警报时，建议你按下述方式调查和响应警报。 Azure Defender for Key Vault 保护应用程序和凭据，因此即使你熟悉触发警报的应用程序或用户，也必须验证每个警报的情况。  

来自 Azure Defender for Key Vault 的每个警报都包含以下元素：

- 对象 ID
- 可疑资源的用户主体名称或 IP 地址

> [!TIP]
> 根据发生的访问 *类型* ，某些字段可能不可用。 例如，如果你的密钥保管库已被应用程序访问，你将看不到关联的用户主体名称。 如果流量来自 Azure 外部，则不会看到对象 ID。

## <a name="step-1-contact"></a>步骤 1。 联系人

1. 验证流量是否源自 Azure 租户。 如果密钥保管库防火墙已启用，则很可能是你已提供对触发此警报的用户或应用程序的访问权限。
1. 如果无法验证流量的来源，请继续执行 [步骤2。立即缓解](#step-2-immediate-mitigation)。
1. 如果可以标识租户中的流量源，请与应用程序的用户或所有者联系。 

> [!CAUTION]
> 适用于 Key Vault 的 Azure Defender 旨在帮助确定由盗窃的凭据引起的可疑活动。 **不要** 只是因为您识别了用户或应用程序而不能消除警报。 与应用程序或用户的所有者联系，并验证活动是否合法。 如果需要，可以创建抑制规则以消除干扰。 有关详细信息，请参阅 [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)。


## <a name="step-2-immediate-mitigation"></a>步骤 2. 即时缓解 
如果你不能识别用户或应用程序，或者你认为不应授予访问权限：

- 如果流量来自无法识别的 IP 地址：
    1. 如 [配置 Azure Key Vault 防火墙和虚拟网络](../key-vault/general/network-security.md)中所述，启用 Azure Key Vault 的防火墙。
    1. 将防火墙配置为受信任的资源和虚拟网络。

- 如果警报的源是未授权的应用程序或可疑用户：
    1. 打开密钥保管库的访问策略设置。
    1. 删除相应的安全主体，或限制安全主体可执行的操作。  

- 如果警报的源在你的租户中具有 Azure Active Directory 角色：
    1. 请与您的管理员联系。
    1. 确定是否需要减少或撤消 Azure Active Directory 权限。

## <a name="step-3-identify-impact"></a>步骤 3. 确定影响 
缓解了影响后，调查密钥保管库中受影响的机密：
1. 打开 Azure Key Vault 上的 "安全性" 页并查看触发的警报。
1. 选择触发的特定警报。
    查看已访问的机密列表和时间戳。
1. 或者，如果已启用密钥保管库诊断日志，请查看前面的操作，以获取相应的调用方 IP、用户主体或对象 ID。  

## <a name="step-4-take-action"></a>步骤 4. 执行操作 
在编译了可疑用户或应用程序访问的机密、密钥和证书列表后，应立即轮替这些对象。

1. 应禁用或删除密钥保管库中受影响的机密。
1. 如果凭据用于特定的应用程序：
    1. 与应用程序的管理员联系，并要求他们审核其环境，使其能够对泄露的凭据进行任何使用，因为它们受到威胁。
    1. 如果使用了泄露的凭据，则应用程序所有者应该确定已访问的信息并减轻影响。


## <a name="next-steps"></a>后续步骤

此页介绍了从 Azure Defender Key Vault 的警报的响应过程。 有关相关信息，请参阅以下页面：

- [用于 Key Vault 的 Azure Defender 简介](defender-for-key-vault-introduction.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)
- [导出安全警报](continuous-export.md)