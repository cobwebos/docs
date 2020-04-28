---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471483"
---
如果希望在授予访问权限之前提示用户提供第二个身份验证因素，可以配置 Azure 多重身份验证 (MFA)。 您可以基于每个用户配置 MFA，也可以通过[条件访问](../articles/active-directory/conditional-access/overview.md)来利用 mfa。

* 可以为每个用户启用 MFA，而不收费额外费用。 为每个用户启用 MFA 时，将提示用户针对绑定到 Azure AD 租户的所有应用程序进行第二因素身份验证。 有关步骤，请参阅[选项 1](#peruser)。
* 通过条件性访问，可对第二个因素的升级方式进行更精细的控制。 它可以允许仅向 VPN 分配 MFA，并排除与 Azure AD 租户关联的其他应用程序。 有关步骤，请参阅[选项 2](#conditional) 。