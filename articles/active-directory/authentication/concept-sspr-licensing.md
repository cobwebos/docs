---
title: 许可证自助密码重置 - Azure Active Directory
description: 了解 Azure Active Directory 自助服务密码重置许可要求之间的差异
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393066"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 自助服务密码重置的许可要求

若要在用户无法登录到其设备或应用程序时减少技术支持呼叫和生产力损失，可为自助服务密码重置（SSPR）启用 Azure Active Directory （Azure AD）中的用户帐户。 构成 SSPR 的功能包括密码更改、重置、解锁和写回到本地目录。 基本 SSPR 功能适用于 Office 365 和所有 Azure AD 用户。

本文详细介绍了自助服务密码重置可以获得许可和使用的不同方法。 有关定价和计费的具体详细信息，请参阅[Azure AD 定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="compare-editions-and-features"></a>比较版本和功能

SSPR 按用户授予许可。 为了保持符合性，组织需要为其用户分配适当的许可证。

下表概述了用于更改密码、重置或本地写回的不同 SSPR 方案，以及哪些 Sku 提供此功能。

| Feature | Azure AD Free | Office 365 商业高级版 | Microsoft 365 商业版 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| **仅限云的用户密码更改**<br />Azure AD 中的用户知道其密码并想要将其更改为新内容时。 | ● | ● | ● | ● |
| **仅限云的用户密码重置**<br />Azure AD 中的用户忘记了自己的密码，需要对其进行重置。 | | ● | ● | ● |
| **混合用户密码更改或重置本地写回**<br />如果 Azure AD 中的用户使用 Azure AD Connect 从本地目录同步，则需要更改或重置其密码，同时将新密码写回本地。 | | | ● | ● |

> [!WARNING]
> 独立 Office 365 许可计划不支持 SSPR 与本地写回。 这些 Office 365 许可计划需要 Azure AD Premium P1、高级 P2 或 Microsoft 365 商业版才能使此功能生效。

有关其他许可信息（包括成本），请参阅以下页面：

* [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 特性和功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [企业移动性 + 安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企业版](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 商业版](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>启用基于组或基于用户的许可

Azure AD 支持基于组的许可。 管理员可以将许可证批量分配给一组用户，而不是一次一个用户地分配。 有关详细信息，请参阅[分配、验证许可证和解决许可证问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

某些 Microsoft 服务在所有位置都不可用。 在将许可证分配给用户之前，管理员必须为用户指定 "**使用位置**" 属性。 可以在 Azure 门户的 "**用户** > **配置文件** > **设置**" 部分下完成分配许可证。 *使用组许可证分配时，任何没有指定使用位置的用户将继承该目录的位置。*

## <a name="next-steps"></a>后续步骤

若要开始 SSPR，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助服务密码重置（SSPR）](tutorial-enable-sspr.md)
