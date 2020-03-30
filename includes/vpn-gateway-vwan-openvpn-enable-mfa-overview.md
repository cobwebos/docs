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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471483"
---
如果希望在授予访问权限之前提示用户进行第二个身份验证因素，则可以配置 Azure 多重身份验证 （MFA）。 您可以根据每个用户配置 MFA，也可以通过[条件访问](../articles/active-directory/conditional-access/overview.md)利用 MFA。

* 每个用户的 MFA 可以不加付费。 当每个用户启用 MFA 时，将提示用户对绑定到 Azure AD 租户的所有应用程序进行第二因子身份验证。 有关步骤，请参阅[选项 1。](#peruser)
* 条件访问允许对如何提升第二个因素进行更细粒度的控制。 它可以允许仅将 MFA 分配给 VPN，并排除绑定到 Azure AD 租户的其他应用程序。 有关步骤，请参阅[选项 2。](#conditional)