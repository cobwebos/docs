---
title: 许可证自助密码重置 - Azure Active Directory
description: 了解 Azure 活动目录自助密码重置许可要求的差异
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393066"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure 活动目录自助服务密码重置的许可要求

为了减少用户无法登录到其设备或应用程序时的帮助台呼叫和工作效率损失，可以启用 Azure 活动目录 （Azure AD） 中的用户帐户进行自助服务密码重置 （SSPR）。 构成 SSPR 的功能包括密码更改、重置、解锁和写入本地目录。 Office 365 和所有 Azure AD 用户均可免费提供基本 SSPR 功能。

本文详细介绍了自助服务密码重置可以许可和使用的不同方式。 有关定价和计费的具体详细信息，请参阅[Azure AD 定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="compare-editions-and-features"></a>比较版本和功能

SSPR 是每个用户的许可。 为了保持合规性，组织需要为其用户分配适当的许可证。

下表概述了密码更改、重置或本地写回的不同 SSPR 方案，以及哪些 SKU 提供了该功能。

| Feature | Azure AD Free | Office 365 商业高级版 | Microsoft 365 商业版 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| **仅限云的用户密码更改**<br />当 Azure AD 中的用户知道其密码并希望将其更改为新密码时。 | ● | ● | ● | ● |
| **仅限云的用户密码重置**<br />当 Azure AD 中的用户忘记了其密码并需要重置密码时。 | | ● | ● | ● |
| **混合用户密码更改或重置与上前写**<br />当使用 Azure AD Connect 从本地目录同步的 Azure AD 中的用户想要更改或重置其密码，并将新密码写回 on-prem 时。 | | | ● | ● |

> [!WARNING]
> 独立 Office 365 许可计划不支持 SSPR 与本地回写。 这些 Office 365 许可计划需要 Azure AD 高级 P1、高级 P2 或 Microsoft 365 业务才能正常工作。

有关其他许可信息（包括成本），请参阅以下页面：

* [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 特性和功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [企业移动性与安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企业版](https://www.microsoft.com/microsoft-365/enterprise)
* [微软 365 业务](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>启用基于组或基于用户的许可

Azure AD 支持基于组的许可。 管理员可以将许可证批量分配给一组用户，而不是一次一个用户地分配。 有关详细信息，请参阅[分配、验证许可证和解决许可证问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

某些 Microsoft 服务并非在所有位置都可用。 在将许可证分配给用户之前，管理员必须在用户上指定 **"使用情况"位置**属性。 许可证分配可以在 Azure 门户中的 **"用户** > **配置文件** > **设置"** 部分下完成。 *使用组许可证分配时，任何没有指定使用位置的用户将继承该目录的位置。*

## <a name="next-steps"></a>后续步骤

要开始使用 SSPR，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助服务密码重置 （SSPR）](tutorial-enable-sspr.md)
